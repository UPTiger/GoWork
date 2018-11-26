1. 引入锁确保推送安全，导致推送速度较慢。 
2. 引入了context包来处理即使退出信号，但channel存在未处理消息时，依然有很大几率继续执行几次业务操作，这是 select-case 的随机性造成的（还记得 select-case 生成随机数吗）。 
3. goroutinue的内存开销大。

那么，go的goroutine+chan 这种模式之前，异步队列通信又是如何处理呢？比较常见的是通过bufferRing来提供一定缓冲数量的队列，而MPSC则提供无数量限制的方式。MPSC的全称是Multi-Produce Single-Consumer，是非常高效的无锁并发的多线程并发解决方案，很多时候用 MPSC 来做控制指令队列通道。go语言利用原子操作，实现非常简单：

type node struct {
    next *node
    val  interface{}
}

type Queue struct {
    head, tail *node
}

func New() *Queue {
    q := &Queue{}
    stub := &node{}
    q.head = stub
    q.tail = stub
    return q
}

// 添加一条新的消息到队列的末尾
func (q *Queue) Push(x interface{}) {
    n := &node{val: x}
    prev := (*node)(atomic.SwapPointer((*unsafe.Pointer)(unsafe.Pointer(&q.head)), unsafe.Pointer(n)))
    prev.next = n
}

// 从队列中提取一条消息交付给消费者
func (q *Queue) Pop() interface{} {
    tail := q.tail
    next := (*node)(atomic.LoadPointer((*unsafe.Pointer)(unsafe.Pointer(&tail.next)))) // acquire
    if next != nil {
        q.tail = next
        v := next.val
        next.val = nil
        return v
    }
    return nil
}

// 清空队列
func (q *Queue) Empty() bool {
    tail := q.tail
    next := (*node)(atomic.LoadPointer((*unsafe.Pointer)(unsafe.Pointer(&tail.next))))
    return next == nil
}

如果我们的 Queue 封装的是mpsc，那么就是在 for 循环中先判断是否关闭，然后操作Pop，如果有数据，则调用接受者。这样，系统的退出响应就会非常简单：

func (queue *queueMPSC) run() {
    var msg interface{}

    defer func() {
        defer func() {
            if err := recover(); err != nil {
                log.Printf("[queue_mpsc] recovering reason is %+v. More detail:", err)
                log.Println(string(debug.Stack()))
            }
        }()
    }()
    for  {
       //是否立即关闭
        if atomic.LoadInt32(&queue.closed) == _CLOSED {
            return
        }
        //当完成消息处理再关闭时，系统发出poisonPill
        if msg = queue.userQueue.Pop(); msg != nil && msg != poisonPill {
            queue.invoker(msg)
        } else {
            return
        }
    }
}

尽管这段代码没有考虑处理大量业务时的cpu 调度(runtime.Gosched())，但基本上是业务可用的代码。我们可以看到： 
1. 不需要select协程，整体上开销更小 
2. 控制更加灵活，处理业务消息的同时，还能非常方便的响应控制消息（停止、暂停等）。

四、总结
客观的说，go语言的goroutine+chan来构建 CSP 可以非常直观的理解业务之间的数据传送关系。但并不是唯一解，尤其是在中型应用中，考虑利用 atomic+基础数据结构的工具库，更是成熟的工程化选择，其原因是： 
- chan更适合单生产者+单/多消费者。一旦是多个生产者，就存在重复关闭的隐患。 
- 通过函数回调，异步队列回调，是更安全的基础组件的编码方式。 
- 采用 CSP实现的队列，在进行系统控制时，比如 stop，及时性不如 mpsc或传统的bufferRing/List等数据结构。 
- 取消协程，减少额外的内存开销。 
- 消费者如果带状态，且需要进行独立于业务消息的系统信息响应时，mpsc是更为普遍的控制指令的结构。 
简而言之，goroutine+channel 作为一两个人的小规模、短期代码问题不大。但作为团队的长期业务进行持续开发，还是建议原始的基础库，尽管没有高大上的名称，无法技术 show，但更能解决问题！
--------------------- 
作者：alex_023 
来源：CSDN 
原文：https://blog.csdn.net/qq_26981997/article/details/78773487 
版权声明：本文为博主原创文章，转载请附上博文链接！