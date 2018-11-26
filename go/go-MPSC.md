1. ������ȷ�����Ͱ�ȫ�����������ٶȽ����� 
2. ������context��������ʹ�˳��źţ���channel����δ������Ϣʱ����Ȼ�кܴ��ʼ���ִ�м���ҵ����������� select-case ���������ɵģ����ǵ� select-case ����������𣩡� 
3. goroutinue���ڴ濪����

��ô��go��goroutine+chan ����ģʽ֮ǰ���첽����ͨ��������δ����أ��Ƚϳ�������ͨ��bufferRing���ṩһ�����������Ķ��У���MPSC���ṩ���������Ƶķ�ʽ��MPSC��ȫ����Multi-Produce Single-Consumer���Ƿǳ���Ч�����������Ķ��̲߳�������������ܶ�ʱ���� MPSC ��������ָ�����ͨ����go��������ԭ�Ӳ�����ʵ�ַǳ��򵥣�

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

// ���һ���µ���Ϣ�����е�ĩβ
func (q *Queue) Push(x interface{}) {
    n := &node{val: x}
    prev := (*node)(atomic.SwapPointer((*unsafe.Pointer)(unsafe.Pointer(&q.head)), unsafe.Pointer(n)))
    prev.next = n
}

// �Ӷ�������ȡһ����Ϣ������������
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

// ��ն���
func (q *Queue) Empty() bool {
    tail := q.tail
    next := (*node)(atomic.LoadPointer((*unsafe.Pointer)(unsafe.Pointer(&tail.next))))
    return next == nil
}

������ǵ� Queue ��װ����mpsc����ô������ for ѭ�������ж��Ƿ�رգ�Ȼ�����Pop����������ݣ�����ý����ߡ�������ϵͳ���˳���Ӧ�ͻ�ǳ��򵥣�

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
       //�Ƿ������ر�
        if atomic.LoadInt32(&queue.closed) == _CLOSED {
            return
        }
        //�������Ϣ�����ٹر�ʱ��ϵͳ����poisonPill
        if msg = queue.userQueue.Pop(); msg != nil && msg != poisonPill {
            queue.invoker(msg)
        } else {
            return
        }
    }
}

������δ���û�п��Ǵ������ҵ��ʱ��cpu ����(runtime.Gosched())������������ҵ����õĴ��롣���ǿ��Կ����� 
1. ����ҪselectЭ�̣������Ͽ�����С 
2. ���Ƹ���������ҵ����Ϣ��ͬʱ�����ܷǳ��������Ӧ������Ϣ��ֹͣ����ͣ�ȣ���

�ġ��ܽ�
�͹۵�˵��go���Ե�goroutine+chan������ CSP ���Էǳ�ֱ�۵����ҵ��֮������ݴ��͹�ϵ����������Ψһ�⣬������������Ӧ���У��������� atomic+�������ݽṹ�Ĺ��߿⣬���ǳ���Ĺ��̻�ѡ����ԭ���ǣ� 
- chan���ʺϵ�������+��/�������ߡ�һ���Ƕ�������ߣ��ʹ����ظ��رյ������� 
- ͨ�������ص����첽���лص����Ǹ���ȫ�Ļ�������ı��뷽ʽ�� 
- ���� CSPʵ�ֵĶ��У��ڽ���ϵͳ����ʱ������ stop����ʱ�Բ��� mpsc��ͳ��bufferRing/List�����ݽṹ�� 
- ȡ��Э�̣����ٶ�����ڴ濪���� 
- �����������״̬������Ҫ���ж�����ҵ����Ϣ��ϵͳ��Ϣ��Ӧʱ��mpsc�Ǹ�Ϊ�ձ�Ŀ���ָ��Ľṹ�� 
�����֮��goroutine+channel ��Ϊһ�����˵�С��ģ�����ڴ������ⲻ�󡣵���Ϊ�Ŷӵĳ���ҵ����г������������ǽ���ԭʼ�Ļ����⣬����û�иߴ��ϵ����ƣ��޷����� show�������ܽ�����⣡
--------------------- 
���ߣ�alex_023 
��Դ��CSDN 
ԭ�ģ�https://blog.csdn.net/qq_26981997/article/details/78773487 
��Ȩ����������Ϊ����ԭ�����£�ת���븽�ϲ������ӣ�