title: 基于nodejs(含express)实现SSE（Server-Sent Event）与WebSocket
date: 2016-07-29 10:45:52
comments: true
categories: TECH NOTE
tags:
- Server-Sent Event
- nodejs
- express
- comet
- WebSocket
- leanEngine
- 部署
---
* 引言
* SSE
* WebSocket

<!-- more -->

## 引言
关于SSE和WebSocket的区别及适用场景（服务端、传输频率等），优缺点，兼容性这里不做相关解释，均可以通过检索得到。本文旨在分析一个基本实例的结构思想。

## SSE
[什么是SSE](https://github.com/waylau/Jersey-2.x-User-Guide/blob/master/Chapter%2015.%20Server-Sent%20Events%20(SSE&#41;%20Support/15.1.%20What%20are%20Server-Sent%20Events.md)
### 所需条件
**参照 [前端通信进阶](https://segmentfault.com/a/1190000004682473#articleHeader3)，总结如下**
客户端：
1.创建EventSource对象，指定url（即与服务端建立联系）
``var source = new EventSource('/chat');``
2.监听事件
默认的open，message，error（可自定义事件）
两种形式``source.onmessage = function(event){};``和``source.addEventListener('message', callback)``
3.event属性
data，origin，lastEventId
4.发送数据格式（一般建立连接后，通过一个ajax事件上传数据）
指定Content-Type为``text/plain;charset=UTF-8``
(试过将content-type设置为json格式的，但服务器接收的依然是string)

服务端：
1.设置响应头
```
{'Content-Type': "text/event-stream",
 'Cache-Control': 'no-cache',
 'Connection': 'keep-alive'}
```
2.返回数据格式（可选）
空类型（即注释），event（默认为message），data，id，retry

**用res.write()向客户端发送数据，以'\n\n'结尾来发送一次事件流，彻底断开连接用res.end()**
举例：
```
//一次事件流
res.write("id: " + num + "\n");
res.write("event: " + str + "\n");
res.write("data: " + datastr + "\n\n");

//如果一个request断开连接，则结束对应的response
res.end();
```

### 一个示例
一个基本的SSE结构应该满足于客户端与服务端建立联系，客户端再次向服务端发送请求（一般为post）从而触发服务端向（多个）客户端推送数据。本示例基于《javascript权威指南（第六版）》的代码进行更改简化。

#### 思路
1.客户端先请求一个页面（url为'/'）
2.服务端返回一个chat.html页面，载入完成后在客户端创建一个EventSource对象（url为'/chat'）
3.服务端设置相关路由，在url为'/chat'的前提下，如果方法为post，即客户端上传数据，此时处理数据并将要返回的数据通过forEach()广播给每个客户端（clients数组）；否则即客户端在请求一个新的信息流（可以视为新用户），服务端为其设置相关响应头，并将其相应对象push进clients数组中（以便未来推送消息）
4.当一个请求关闭连接之后，要将其对应的响应对象从clients数组中删除

#### 截图
{% asset_img 1.png %}

#### 代码
```
chatserver.js
var http = require('http');  // NodeJS HTTP server API

// The HTML file for the chat client. Used below.
var clientui = require('fs').readFileSync("chat.html");

// An array of ServerResponse objects that we're going to send events to
var clients = [];

// Create a new server
var server = new http.Server();  

// When the server gets a new request, run this function
server.on("request", function (request, response) {
    // Parse the requested URL
    var url = require('url').parse(request.url);

    // If the request was for "/", send the client-side chat UI.
    if (url.pathname === "/") {  // A request for the chat UI
        response.writeHead(200, {"Content-Type": "text/html"});
        response.write(clientui);
        response.end();
        return;
    }
    // Send 404 for any request other than "/chat"
    else if (url.pathname !== "/chat") {
        response.writeHead(404);
        response.end();
        return;
    }
    // If the request was a post, then a client is posting a new message
    if (request.method === "POST") {
        var body = "";
        // When we get a chunk of data, add it to the body     
        request.on("data", function(chunk) { body += chunk; });

        // When the request is done, send an empty response 
        // and broadcast the message to all listening clients.
        request.on("end", function() {
            response.writeHead(200);   // Respond to the request
            response.end();

            // Format the message in text/event-stream format
            // Make sure each line is prefixed with "data:" and that it is
            // terminated with two newlines.
            message = 'data: ' + body.replace('\n', '\ndata: ') + "\r\n\r\n";
            // Now send this message to all listening clients
            //console.log('post:' + clients);
            clients.forEach(function(client) { client.write(message); });
        });
    }
    // Otherwise, a client is requesting a stream of messages
    else {
        // Set the content type and send an initial message event 
        response.writeHead(200, {'Content-Type': "text/event-stream",
                                 'Cache-Control': 'no-cache',
                                 'Connection': 'keep-alive'});
        response.write("data: Connected\n\n");
        // If the client closes the connection, remove the corresponding
        // response object from the array of active clients
        request.connection.on("end", function() {
            clients.splice(clients.indexOf(response), 1);
            response.end();
        });

        // Remember the response object so we can send future messages to it
        clients.push(response);
    }
});

// Run the server on port 8000. Connect to http://localhost:8000/ to use it.
server.listen(8000);

chat.html
<body>
    <h1>聊天室</h1>
    <div></div>
    <input type="text"><button id="btn">发送</button>

    <script src="http://cdn.bootcss.com/jquery/3.1.0/jquery.min.js"></script>
    <script>
        $(function(){
            var nick = prompt("输入你的昵称");
            
            var source = new EventSource('/chat');
            source.addEventListener('message', msg);

            function msg(event){
                var p = $("<p></p>").text(event.data);
                $('div').append(p);
                $('input')[0].scrollIntoView();
            }

            $('#btn').click(function(event) {
                //var v = nick + ': ' + $('input').val();
                var v = {name: nick,
                         msg: $('input').val()};
                vjson = JSON.stringify(v);
                $('input').val('');
                $.ajax({contentType: 'text/plain;charset=UTF-8',
                        data: vjson,
                        url: '/chat',
                        method: 'POST'});
            });
        })
    </script>
</body>
```

## WebSocket
WebSocket的概念不过多描述，这里有一篇阮一峰老师写的文章介绍的很清晰：[WebSocket解析](http://javascript.ruanyifeng.com/htmlapi/websocket.html)

了解计算机网络的分层模型应该对socket（套接字）并不陌生。我们知道，网络上的两台主机能够在知道双方IP地址的情况下交换数据，但是当数据传到主机时，数据究竟是要传给哪个程序（进程）就不得而知了。这时候传输层的作用就显现出来了。传输层提供一个参数，即端口。它指定了一个数据包到底供哪个程序（进程）使用。应用程序会被随机分配一个端口，这个端口与服务器的端口相联系。传输层的功能，即是建立端口到端口的通信。理解了这个，WebSocket也即很容易理解了。

### 一个简单的实现(原生)
即在客户端通过WebSocket()构造函数创建一个套接字，通过message事件监听服务端响应，通过send()方法向服务端发送数据。
```
var socket = new WebSocket('ws://www.example.com:8080/test');
socket.onopen = function(e){};
socket.onclose = function(e){};
socket.onerror = function(e){};
socket.onmessage = function(e){ e.data };

//发送数据
socket.send('Hello, Server');
```

而在服务器端，我们也需要相应的写一套API来实现接收和发送数据。下面通过第三方库来实现。

### Socket.io + nodejs(express)
Socket.io在阮一峰老师的那篇文章中也有相关介绍，这里是官方文档：[Socket Doc](http://socket.io/docs/)

它提供了一套简单的客户端和服务端API，能够让我们方便的建立socket通信。其核心就是emit()和on()，一个负责发送事件消息，一个负责接收事件消息。支持自定义事件。

具体的一个Demo相信根据文档和socket.io在github上example/chat文件，自己很容易理解并修改。

### 部署到leanEngine
这里分析一下调试或部署中碰到的问题：
1.客户端建立连接时，通过
```
var socket = io.connect();
```
connect()默认是当前域名，如果要给connect传递参数，建议补充上端口号（适用于本地调试），默认是80（即监听端口为80时，不用补充端口号）。但是一般部署上线是分配端口的，所以建议是不传递参数。

2.通过leanCloud初始化的express项目下，其中有app.js和server.js两个文件，默认是启动server.js文件。但是在server.js文件中，通过app.listen(port, callback)来创建服务并监听。
按照socket.io文档中的意思，我们在app.js中添加了如下代码：
```
var express = require('express');
var app = express();
var server = require('http').createServer(app);
var io = require('socket.io')(server);
server.listen(80);
```
由此分析，通过server.js启动app.listen()会启动两个httpserver服务，必然会造成冲突。

查看express的官方文档，app.listen()方法其实是node.js中创建httpserver服务的简单写法。但是在当前情景下，我们不仅需要创建一个server，还需要socket.io模块关联该server，这样才能实现websocket功能。显然用app.listen()的简单写法不太方便满足。

#### 部署要解决的两个问题
1.创建一个服务（即启动app.js文件即可）
2.线上监听哪个端口？

查看leanEngine文档：[网站托管开发指南 · Node.js](https://leancloud.cn/docs/leanengine_webhosting_guide-node.html#项目约束)

得到两个信息：
1.云引擎 Node.js 项目必须有 $PROJECT_DIR/server.js 文件，该文件为整个项目的启动文件。如果 $PROJECT_DIR/package.json 文件有自定义的启动脚本，则会使用 npm start 方式启动。这意味着可以使用 npm scripts 来定制启动过程。（我将server.js中的部分代码移到app.js中来，然后启动app.js）

2.云引擎 Node.js 支持任意 Node.js 的 Web 框架，但是请保证启动你的项目之后程序监听的端口为 process.env.LEANCLOUD_APP_PORT。

所以将代码修改为
```
app.set('port', process.env.LEANCLOUD_APP_PORT || 3000);

server.listen(app.get('port'), function(){
  console.log("Express server listening on port " + app.get('port'));
});
```
即可部署上线成功

访问 [felbry.leanapp.cn](http://felbry.leanapp.cn) ，查看我部署好的聊天室吧。
