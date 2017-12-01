# HTTP的响应数据格式JSON

调用服务器API接口，常见返回内容格式是：`JSON`

http的response返回的内容，原始raw格式，都是字符串string，text的类型

如果想要把raw的text/string转换为json，则可以：

利用很多库自带的功能，把返回内容转换为JSON
## iOS的Alamofire
```swift
Alamofire.request("https://httpbin.org/get").responseJSON { response in
  if let json = response.result.value {
        print("JSON: \(json)") // serialized json response
    }
}
```
## Python的Requests
```python
>>> r = requests.get('https://api.github.com/user', auth=('user', 'pass'))
>>> r.status_code
200
>>> r.headers['content-type']
'application/json; charset=utf8'
>>> r.encoding
'utf-8'
>>> r.text
u'{"type":"User"...'
>>> r.json()
{u'disk_usage': 368627, u'private_gists': 484, ...}
```
