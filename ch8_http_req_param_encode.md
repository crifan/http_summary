# HTTP的请求参数和参数编码

下面介绍HTTP的请求参数`Request Parameters`和请求参数的编码`Request Parameters Encoding`

## GET请求的参数和编码

典型的是：

GET：参数想要放在url中以`?key1=value1&key2=value2`的形式

则有两种做法：

* 自己把参数组合成对应的格式，放到url中
* 把参数（字典，对象等）调用url encode函数去编码，生成对应的格式（再放到url中）

其他一些注意和解释：

其他一些注意和解释：

* GET 请求有长度限制
* * 所以query string一般被限制在1024个字节
  * * 超过限制则后台无法解析参数
* GET 请求可被缓存
* GET 请求保留在浏览器历史记录中
* GET 请求可被收藏为书签
* GET 请求不应在处理敏感数据时使用
* GET 请求只应当用于取回数据

  


## POST请求的参数和编码



