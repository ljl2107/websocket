# Gorilla WebSocket

[MDN中关于WebSockets推荐](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSockets_API)

[![GoDoc](https://godoc.org/github.com/gorilla/websocket?status.svg)](https://godoc.org/github.com/gorilla/websocket)
[![CircleCI](https://circleci.com/gh/gorilla/websocket.svg?style=svg)](https://circleci.com/gh/gorilla/websocket)

Gorilla WebSocket 是一个[WebSocket](http://www.rfc-editor.org/rfc/rfc6455.txt) 协议的[Go](http://golang.org/) 实现


### 文档

* [API Reference](https://pkg.go.dev/github.com/gorilla/websocket?tab=doc)

使用示例
* [Chat example](https://github.com/gorilla/websocket/tree/main/examples/chat)
* [Command example](https://github.com/gorilla/websocket/tree/main/examples/command)
* [Client and server example](https://github.com/gorilla/websocket/tree/main/examples/echo)
* [File watch example](https://github.com/gorilla/websocket/tree/main/examples/filewatch)

### Status

该包提供了完整且被测试过的[WebSocket](http://www.rfc-editor.org/rfc/rfc6455.txt)实现

并且该包是稳定的

### 安装

    go get github.com/gorilla/websocket

### 协议合规性

通过[Autobahn Test
Suite](https://github.com/crossbario/autobahn-testsuite) 的[examples/autobahn
subdirectory](https://github.com/gorilla/websocket/tree/main/examples/autobahn)测试

