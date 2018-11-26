QOR - Go语言写的模块化电商系统和CMS
https://github.com/qor/qor
---------------------------------------------------------
！！NATS消息中间件
https://www.slideshare.net/nats_io/writing-networking-clients-in-go-gophercon-2017-talk
---------------------------------------------------------
https://github.com/elves/elvish
---------------------------------------------------------

https://github.com/golang/groupcache/
---------------------------------------------------------
小瑞
https://github.com/rfyiamcool/share_ppt
http://xiaorui.cc/2018/04/09/%E6%8A%80%E6%9C%AF%E5%88%86%E4%BA%AB%E4%B9%8B%E3%80%8Agolang%E9%AB%98%E6%80%A7%E8%83%BD%E5%AE%9E%E6%88%98%E3%80%8B/
---------------------------------------------------------
视频播放MP4
https://blog.csdn.net/wangshubo1989/article/details/78053856
package main

import (
    "fmt"

    "github.com/nareix/joy4/av"
    "github.com/nareix/joy4/av/avutil"
    "github.com/nareix/joy4/format"
)

func init() {
    format.RegisterAll()
}

func main() {
    file, _ := avutil.Open("test.mp4")
    streams, _ := file.Streams()
    for _, stream := range streams {
        if stream.Type().IsAudio() {
            astream := stream.(av.AudioCodecData)
            fmt.Println(astream.Type(), astream.SampleRate(), astream.SampleFormat(), astream.ChannelLayout())
        } else if stream.Type().IsVideo() {
            vstream := stream.(av.VideoCodecData)
            fmt.Println(vstream.Type(), vstream.Width(), vstream.Height())
        }
    }

    file.Close()
}
--------------------- 
作者：一蓑烟雨1989 
来源：CSDN 
原文：https://blog.csdn.net/wangshubo1989/article/details/78053856 
版权声明：本文为博主原创文章，转载请附上博文链接！
实现静态文件服务器(文件查看，文件上传，文件下载)
https://blog.csdn.net/wangshubo1989/article/details/77319537?locationNum=10&fps=1
---------------------------------------------------------
360千亿级连接
https://blog.csdn.net/GV7lZB0y87u7C/article/details/80269673
---------------------------------------------------------
Golang 中文学习资料
https://go.wuhaolin.cn/
---------------------------------------------------------

---------------------------------------------------------

---------------------------------------------------------

---------------------------------------------------------

---------------------------------------------------------

---------------------------------------------------------

---------------------------------------------------------

---------------------------------------------------------

---------------------------------------------------------

---------------------------------------------------------

---------------------------------------------------------


---------------------------------------------------------

---------------------------------------------------------

---------------------------------------------------------

---------------------------------------------------------