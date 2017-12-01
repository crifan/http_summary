# HTTP内部流程
其实内部都是通用的HTTP的逻辑：
- 发送HTTP的请求
- 接受到响应后
    - 解析并显示出对应的内容
    - 解析得到所需要的数据

那如何才能看到内部的HTTP的请求和响应到底是什么样的呢？
有很多方式可以实现查看内部到底发送了什么样的HTTP请求，和接收到了什么样的响应：
- 对于浏览器访问网页
    - 可以用`Chrome`/`Safari`/`Firefox`等浏览器自带的调试工具：`开发者工具`
    - 可以用其他工具去模拟
        - 比如：命令行工具`curl`
- 对于写代码调用后台接口
    - 可以用postman去模拟和查看

下面详细介绍如何查看内部的具体流程：

## 用Chrome的开发者工具去查看
先去打开：
右上角 三个点-》更多工具-》开发者工具
![](../assets/img/9EED85A6-83B3-42DB-85D5-2B3B3E5F06A5.png)

点击到Network列，然后再去访问地址www.baidu.com

就可以看到会有一堆的内容列出来，点击第一个www.baidu.com的Header

即可看到，对应的Request和Response的信息：

![](../assets/img/681BB8D6-73BC-4EAD-804E-4014E5C12299.png)

通过点击Request Headers和Response Headers的view source可以看到未被解析之前的原始内容：
![](../assets/img/B7221FAE-3275-4973-88BC-D4E79EB59875.png)
![](../assets/img/58E15E6F-F862-4DFA-B248-EA981AB89FE6.png)

对应的原始内容为：

Request请求：
```bash
GET / HTTP/1.1
Host: www.baidu.com
Connection: keep-alive
Cache-Control: max-age=0
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/61.0.3163.100 Safari/537.36
Upgrade-Insecure-Requests: 1
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.8,en;q=0.6
Cookie: BAIDUID=6720BF4EAED8C8448EB6540DAF83BE56:FG=1; BIDUPSID=6720BF4EAED8C8448EB6540DAF83BE56; PSTM=1449798993; MCITY=-224%3A; BD_CK_SAM=1; PSINO=3; BD_HOME=0; H_PS_PSSID=1430_24568_21116_24880_20929; BD_UPN=123253
```

Response响应：
```bash
HTTP/1.1 200 OK
Bdpagetype: 1
Bdqid: 0xd4bdc47c00000909
Bduserid: 0
Cache-Control: private
Connection: Keep-Alive
Content-Encoding: gzip
Content-Type: text/html; charset=utf-8
Cxy_all: baidu+1ea6be4d211f802306f9e10940507eba
Date: Mon, 13 Nov 2017 01:32:41 GMT
Expires: Mon, 13 Nov 2017 01:32:06 GMT
Server: BWS/1.1
Set-Cookie: BDSVRTM=0; path=/
Set-Cookie: BD_HOME=0; path=/
Set-Cookie: H_PS_PSSID=1430_24568_21116_24880_20929; path=/; domain=.baidu.com
Strict-Transport-Security: max-age=172800
Vary: Accept-Encoding
X-Powered-By: HPHP
X-Ua-Compatible: IE=Edge,chrome=1
Transfer-Encoding: chunked
```
## 用curl工具去模拟浏览器访问百度的过程

在命令行中输入：
```bash
curl -v www.baidu.com
```
返回的结果是：
```bash
➜  ~ curl -v www.baidu.com
* Rebuilt URL to: www.baidu.com/
*   Trying 180.97.33.107...
* TCP_NODELAY set
* Connected to www.baidu.com (180.97.33.107) port 80 (#0)
> GET / HTTP/1.1
> Host: www.baidu.com
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< Server: bfe/1.0.8.18
< Date: Mon, 13 Nov 2017 01:35:55 GMT
< Content-Type: text/html
< Content-Length: 2381
< Last-Modified: Mon, 23 Jan 2017 13:28:24 GMT
< Connection: Keep-Alive
< ETag: "588604f8-94d"
< Cache-Control: private, no-cache, no-store, proxy-revalidate, no-transform
< Pragma: no-cache
< Set-Cookie: BDORZ=27315; max-age=86400; domain=.baidu.com; path=/
< Accept-Ranges: bytes
<
<!DOCTYPE html>
<!--STATUS OK--><html> <head><meta http-equiv=content-type content=text/html;charset=utf-8><meta http-equiv=X-UA-Compatible content=IE=Edge><meta content=always name=referrer><link rel=stylesheet type=text/css href=http://s1.bdstatic.com/r/www/cache/bdorz/baidu.min.css><title>百度一下，你就知道</title></head>; <body link=#0000cc> <div id=wrapper> <div id=head> .............. <a href=http://jianyi.baidu.com/ class=cp-feedback>意见反馈</a>&nbsp;京ICP证030173号&nbsp; <img src=//www.baidu.com/img/gs.gif>; </p> </div> </div> </div> </body> </html>
* Connection #0 to host www.baidu.com left intact
```

![](../assets/img/1D928B86-A1C2-404B-A682-48332C1F71DF.png)

## 用`postman`去模拟前面的调用后台接口的登录过程
![](../assets/img/4ACF224B-4768-406B-B28D-50F8131C38C4.png)

其中内部发送的HTTP请求，可以通过Code查看：
![](../assets/img/523C7AA5-AF8F-477A-A7CF-BA415AC37F8F.png)

```bash
POST /v1/skr/login HTTP/1.1
Host: 123.206.101.36:9090
Content-Type: application/json; charset=UTF-8
authorization: Bearer mum5emp17q28st6v36ajltfvf3
Cache-Control: no-cache
Postman-Token: deb6784d-3689-32ce-ac01-687c22ad1b6e

{
    "user" : "13429",
    "password" : "666666"
}
```


