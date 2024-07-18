# File Watch example.

此示例每当文件被修改时就将该文件发送到浏览器客户端进行显示。

注意

    $ go run main.go README-CN.md

需要指定观测的文件名，否则报错`filename not specified`

可以通过`--addr=`指定端口

    $ go get github.com/gorilla/websocket
    $ cd `go list -f '{{.Dir}}' github.com/gorilla/websocket/examples/filewatch`
    $ go run main.go <name of file to watch>
    # Open http://localhost:8080/ .
    # Modify the file to see it update in the browser.

http://localhost:8080/ 

## html

0. 获取数据展示块
1. 开启ws链接
2. 绑定断开回调（前端展示“链接关闭”）
3. 绑定获取消息回调