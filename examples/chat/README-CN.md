# 聊天室示例

This application shows how to use the
[websocket](https://github.com/gorilla/websocket) package to implement a simple
web chat application.

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

在main函数中serveWs函数被注册为一个HTTP请求处理程序

处理程序将HTTP链接升级为WebSocket协议，创建一个客户端，用hub注册客户端并且规划取消注册客户端（使用defer语句）




Next, the HTTP handler starts the client's `writePump` method as a goroutine.
This method transfers messages from the client's send channel to the websocket
connection. The writer method exits when the channel is closed by the hub or
there's an error writing to the websocket connection.

Finally, the HTTP handler calls the client's `readPump` method. This method
transfers inbound messages from the websocket to the hub.

WebSocket connections [support one concurrent reader and one concurrent
writer](https://godoc.org/github.com/gorilla/websocket#hdr-Concurrency). The
application ensures that these concurrency requirements are met by executing
all reads from the `readPump` goroutine and all writes from the `writePump`
goroutine.

To improve efficiency under high load, the `writePump` function coalesces
pending chat messages in the `send` channel to a single WebSocket message. This
reduces the number of system calls and the amount of data sent over the
network.

## Frontend



On document load, the script checks for websocket functionality in the browser.
If websocket functionality is available, then the script opens a connection to
the server and registers a callback to handle messages from the server. The
callback appends the message to the chat log using the appendLog function.

To allow the user to manually scroll through the chat log without interruption
from new messages, the `appendLog` function checks the scroll position before
adding new content. If the chat log is scrolled to the bottom, then the
function scrolls new content into view after adding the content. Otherwise, the
scroll position is not changed.

The form handler writes the user input to the websocket and clears the input
field.
