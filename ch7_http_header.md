# HTTP的Header头

HTTP的Header包括两类：

* 请求头=Request Header
* 响应头=Response Header

逻辑总结：  
既然是 Client问 Sever答的过程，那么基本逻辑就是：

- Client告诉服务器端，自己的Request请求，能够接受的各种信息是什么类型的
  - 所以Request中有很多Accept方面的请求头
    - Accept：能接受（返回）哪些类型
      - 格式：type/sub-type
        - */* 表示任何类型
      - 举例：
        - Accept: application/json
        - Accept: text/plain
        - Accept: text/html
        - Accept: image/jpeg
        - Accept: application/msword
        - Accept: image/png
        - Accept: application/pdf
    - Accept-Charset
      - 能接受的字符集
    - Accept-Encoding
      - 能接受的（编码）压缩类型
        - 比如：gzip，deflate
    - Accept-Language
      - 能接受的语言类型
  - 以及其他一些额外的请求和希望
    - User-Agent:告诉你我是哪种浏览器（所处的操作系统是什么类型）等信息
      - 举例：User-Agent：Mozilla/5.0 (Windows; U; Windows NT 5.1; zh-CN;rv:1.8.1.14) Gecko/20080404 Firefox/2.0.0.14
    - Referer：告诉服务器是从（参考）哪个链接去访问的
      - 举例：Referer：
        [https://www.google.co.uk/](https://www.google.co.uk/)
    - Keep-Alive：希望服务器保持此次连接（多长时间）
    - Cache-Control：表示是否使用缓存
    - Connection：完成本次请求的响应后，是否断开连接，是否要等待本次连接的后续请求了
      - Connection: close
      - Connection: keepalive
- Sever返回响应，告诉客户端，自己的响应中数据都是什么类型的
  - 所以Response中有很多Content方面的字段，表示服务器返回的内容的各种类型说明
    - Content-Encoding：此响应中使用了什么压缩方法（gzip，deflate）压缩响应中的对象
      - 表示客户端的你用什么对应的方法去解压缩，才能得到数据的原文
      - 举例：Content-Encoding：gzip
    - Content-Language：用了什么语言
    - Content-Length：数据内容的长度，单位：字节
      - 举例：Content-Length: 2684
    - Content-Type：自己返回的数据是什么格式的
      - 和Request中的Accept对应
      - 举例：Content-Type：application/json
    - Connection：和Request对应
      - Connection: close
      - Connection: keepalive
  - 以及其他一些表示当前信息的情况的
    - Location：一个url地址，表示你想要的资源被转移到别处了，
      - 典型的是发生了302表示自动跳转，告诉你要的东西，换了地址了



