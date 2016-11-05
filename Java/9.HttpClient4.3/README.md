HttpClient4.3
==================================

### HttpClient 4.3

#### HttpClient的范围/特性
+ 是一个基于`HttpCore`的客户端`Http`传输类库
+ 基于传统的（**阻塞**）IO
+ 内容无关

#### HttpClient不能做的事情
`HttpClient`不是浏览器，它是一个客户端`http`协议传输类库。`HttpClient`被用来发送和接受`Http`消息。
`HttpClient`不会处理`http`消息的内容，不会进行`javascript`解析，不会关心`content type`，如果没有明确设置，
`httpclient`也 **不会** 对请求进行格式化、**重定向url**，或者其他任何和`http`消息传输相关的功能。
