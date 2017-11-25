# HTTP的响应数据格式JSON

调用服务器API接口，常见返回内容格式是：`JSON`

http的response返回的内容，原始raw格式，都是字符串string，text的类型

如果想要把raw的text/string转换为json，则可以：

利用很多库自带的功能，把返回内容转换为JSON
