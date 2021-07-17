# HTTP的响应的状态码

`Http状态码`=`HTTP Code`

关于状态码的类别的含义的总体解释是：

| 状态码大类 | 表示的含义 | 客户端client要做的事 | 服务器端server要做的事 |
| :--- | :--- | :--- | :--- |
| 1xx | Informational 信息 | 啥都不用做，知道就好 | 告诉client，信息收到了，我后续会处理 |
| 2xx | Successful 成功 | 啥都不用做，知道就好 | 告诉client，请求已正确处理 |
| 3xx | Redirection 重定向 | 重新请求返回的新地址 -> 才能获取真正需要的数据 | 告诉client，你需要的内容，由于一些原因，比如地址已发生变化了，然后返回该内容的新地址 |
| 4xx | Client Error 客户端的错误 | 确保用正确的参数和信息正确，重新请求 | 告诉client，请求已正确处理 |
| 5xx | Server Error 服务器端的错误 | （一般来说）都无需啥操作 -> 往往需要服务器端改了bug后，重新发送请求 | 需要服务器Server端自己找到具体出了啥错 -> 往往是服务器端的代码的bug导致了出错 |

## 最常用的状态码及含义

* Successful - 2xx：成功类，行为被成功地接受、理解和采纳
  * `200`=`OK`
    * 服务器成功返回用户请求的数据
      * 注意：（部分人）为了简化处理，`POST`创建成功后应该返回`201`的但也返回`200`
* `400`=`BAD REQUEST`
  * 错误的请求
    * 注意：（部分人）往往为了简化处理，把属于`401`的`没有权限`和属于`403`的`有权限但是权限不够`，往往都返回`404`
* `404`=`NOT FOUND`
  * 找不到资源
    * 注意：有些做法是，把属于`401`或`403`的原因，假装找不到而返回`404`
* `500`=`INTERNAL SERVER ERROR`
  * 服务器内部错误
    * 最常见的原因是：服务器内部挂了
    * 比如：你传递参数中有些参数是空，而导致后台代码无法解析，出现异常而崩溃

## 次常用的响应码及含义

* Informational - 1xx：信息类，请求收到，继续处理
* Successful - 2xx：成功类，行为被成功地接受、理解和采纳
  * 201 CREATED
    * 通过POST或PUT创建资源成功
      * 通过response header中会返回新创建的资源的url连接
      * response body可能有信息，也可能为空
  * 202 Accepted
    * 服务器接受了此请求，但是待会才会执行
  * 204 NO CONTENT
    * 资源创建成功，但是没有返回内容
      * 常用于DELETE或PUT操作的返回
* Redirection - 3xx：重定向类，为了完成请求，必须进一步执行的动作
  * 301 Moved
    * 该资源的URI接口已经变了，变成别的接口了
      * response header中应该告诉新接口地址，比如：
      * URI: url地址
* Client Error - 4xx：客户端错误类，请求包含语法错误或者请求无法实现
  * 401 UNAUTHORIZED
    * 没有权限访问该资源
      * 典型情况：用户没有登录，没有获得对应的access token而直接访问某资源
  * 403 FORBIDDEN
    * 禁止访问
      * 典型情况：虽然用户已登录，但是去更新/删除需要更高权限才能操作的资源
  * 405 METHOD NOT ALLOWED
    * 方法不允许
      * 举例：比如某个资源不允许PATCH操作，但是你调用PATCH操作
  * 406 - Not Acceptable
    * 无效的请求
      * 比如：请求中的部分参数是无效的
  * 409 CONFLICT
    * 有冲突
      * 举例：新建一个用户，但是主键手机号和之前已有用户冲突
  * 415 Unsupported Media Type
    * 典型情况：请求的header有问题
  * 422 Unprocessable Entity
    * Specify values for all required parameter fields
* Server Error - 5xx：服务器错误类，服务器不能实现一种明显无效的请求
  * 502 BAD GATEWAY
    * 网管错误
  * 503 SERVICE UNAVAILABLE
    * 无服务
      * 举例：比如服务器内部正在维护，暂时不提供服务。可以换个时间重试
