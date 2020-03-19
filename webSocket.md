# webSocket

## 特点 
##### 服务器主动向客户端推送信息
##### 客户端也可以主动向服务器发送信息
##### TCP协议
##### 数据格式轻量
##### 可以发送二进制数据
##### 没有同源，不存在跨域

## 对比轮询

![markdown](https://img2018.cnblogs.com/blog/1784731/201909/1784731-20190907185445747-1582509880.png "markdown")

## 头部信息
以http协议发送请求，但多了几个参数

    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Extensions: permessage-deflate; client_max_window_bits
    Sec-WebSocket-Key: +YToyoe+pGm3QguEX7wZQw==
    Sec-WebSocket-Version: 13
    Origin: http://example.com

为了告诉服务器我发起的是websocket协议

    Upgrade: websocket
    Connection: Upgrade


#### 客户端
Sec-WebSocket-Extensions 确认客户端的扩展，某类协议可能支持多个扩展，通过它可以实现协议增强
Sec-WebSocket-Key 浏览器随机生成密钥，用来验证websocket，避免跨协议攻击。对应`Sec-WebSocket-Accept`
Sec-WebSocket-Version 协议版本
Sec_WebSocket-Protocol 用来区分同URL下，不同服务所需要的协议
> 注意：浏览器差异，部分浏览器可能会穿undefind，适配方法  var Socket = new WebSocket(url, [protocol] );

    Sec-WebSocket-Extensions: permessage-deflate; client_max_window_bits
    Sec-WebSocket-Key: wR369dOAKXnBUdWKOTqA4Q==
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13

#### 服务器
Sec-WebSocket-Accept 服务器确认WebSocket协议，SHA-1加密，再进行BASE-64编码

    Sec-WebSocket-Accept: xyiAwSgNwu8sfVG7V0anbaSe/8w=

## 构造函数
    var ws = new WebSocket('ws://www.google.com')

#### webSocket.url
链接

#### webSocket.readyState
0: 正在连接
1: 连接成功
2: 连接正在关闭
3: 连接已关闭

#### 回调
连接成功

    ws.onopen = function () {
        ws.send('hi');
    }
连接关闭

    ws.onclose = function () {
        ...
    }
接受数据,也可能是二进制数据（blob对象或Arraybuffer对象）

    ws.onmessage = function () {
        ...
    }
报错处理

    ws.onerror = function () {
        ...
    }
也可以通过addEventListener绑定

    ws.addEventListener("error", function(event) {
        // handle error event
    });
#### 发送数据
发送文本

    ws.send('hi');
发送blob对象

    ws.send(document.querySelector('input[type="file"]').files[0])
发送ArrayBuffer

    var img = canvas_context.getImageData(0, 0, 400, 320);
    var binary = new Uint8Array(img.data.length);
    for (var i = 0; i < img.data.length; i++) {
    binary[i] = img.data[i];
    }
    ws.send(binary.buffer);

#### webSocket.bufferedAmount
表示还有多少字节二进制数据没有发送出去，用它来判断发送是否结束

    var data = new ArrayBuffer(10000000);
    socket.send(data);

    if (socket.bufferedAmount === 0) {
    // 发送完毕
    } else {
    // 发送还没结束
    }

#### ping pong 用来检测链接是否断开
    {"type":"ping","value":1584622155785}
    {"pong":"1584622155785"}

## 攻防
### websocket CSRF攻击
默认情况下WebSocket服务器不会检查Origin，只使用cookies检查用户身份
攻击者可读取响应，代表受害者双向通信

解决方案：服务端检查Origin，若不被信任，拒绝该请求

### websocket 信息泄露
websocket发送纯文本

解决方案： 使用TLS加密 wss

### websocket DOS攻击

默认情况下，websocket允许无限制链接，导致服务器崩溃

解决方案：IP防护，同IP多个链接，展示验证码

