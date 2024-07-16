# 聊天室示例

This application shows how to use the
[websocket](https://github.com/gorilla/websocket) package to implement a simple
web chat application.

主要是通道+线程+websocket
很巧妙的设计，让人收获颇丰

## 运行

go环境准备 [Getting
Started](http://golang.org/doc/install) 

使用如下命令

    $ go run *.go

> 可选参数 --addr=:8080
指定运行端口号 默认8080

如果不行再用下面的

    $ go get github.com/gorilla/websocket
    $ cd `go list -f '{{.Dir}}' github.com/gorilla/websocket/examples/chat`
    $ go run *.go

快捷打开 http://localhost:8080/ 

## Server

服务定义了两个不同的类型`Client`和`Hub`

服务为每一个websocket链接都会创建一个`Client`实例

`Client`实例是websocket链接和Hub类型的桥梁

`Hub`维护一组被注册的客户并向客户组广播消息

该应用为`Hub`运行一个线程，为`Client`运行两个线程

线程之间通过通道通信

`Hub`持有注册客户端/取消注册客户端/广播消息的通道

每一个客户端都持有一个输出缓冲通道，客户端的其中一个线程读取输出缓冲通道的消息并向websocket写信息

其他客户端的线程从websocket读信息并发送给`Hub`


### Hub 

`main`函数新建线程运行`hub`的run方法

客户端使用register或unregister或broadcast通道向Hub发送请求

`hub`通过将客户端指针作为一个key添加到`clients`map中来注册客户端

map永真

取消注册代码稍微复杂一点

除了从`clients`map中删除客户端指针，hub关闭客户端的`send`通道去通知客户端不会有更多消息被发往该客户端

hub循环通知已经注册了的客户端并且向客户端的`send`通道发送消息

如果客户端的`send`缓冲已满，那么hub就声明该客户端死亡/阻塞

在这种情况下，hub就取消注册并关闭这个websocket



### Client

在`main`函数中`serveWs`函数被注册为一个HTTP请求处理程序

处理程序将HTTP链接升级为WebSocket协议，创建一个客户端，用hub注册客户端并且规划取消注册客户端（使用defer语句）

接下来，HTTP处理程序以线程开启`writePump`方法

这个方法将消息从客户端发送通道发往websocket连接

当通道被关闭/出现错误，这个写方法退出

最后，HTTP处理程序调用`readPump`方法

这个方法从websocket向hub发送入站消息

websocket[支持一个同步读和一个同步写](https://godoc.org/github.com/gorilla/websocket#hdr-Concurrency)

这个应用通过从`readPump`执行所有读和从`writePump`执行所有写确保了同步需求

为了改善高负载下的效率，`writePump`方法将send通道中待处理的待发送消息合并成单个WebSocket消息，此举不仅减少了系统调用还减少了通过网络发送的数据量

## Frontend

当document加载好后，脚本检查websocket功能。如果正常，则打开一个连接并注册一个回调处理消息的回调。该回调酱消息附加到聊天框（chat log）中

为了允许用户手动滚动浏览聊天记录而不会被新消息打断，`appendLog` 函数会在添加新内容之前检查滚动位置。如果聊天记录滚动到底部，则函数会在添加内容后将新内容滚动到视图中。否则，滚动位置不会改变。

表单处理程序将用户输入写入 websocket 并清除输入字段。