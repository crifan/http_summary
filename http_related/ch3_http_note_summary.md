# HTTP相关心得

## iOS（swift）用Alamofire时，注意返回response时所处的线程

iOS（swift）对于http库，[Alamofire的响应返回默认处于主的UI线程](https://github.com/Alamofire/Alamofire#response-handler-queue)

-》而很多耗时的操作，是不建议，不允许，在主线程中操作的

-〉所以我后来自己在Alamofire基础上封装的库，是用dispatchBackground\_async让responseJSON返回后运行在后台线程

-》避免大量的操作堵塞了UI的响应

-〉又由于iOS中只能在主线程UI线程中操作UI元素

-》所以此时如果直接在Alamofire返回的地方去操作UI元素则会报错：`Terminating app due to uncaught exception NSInternalInconsistencyException reason Only run on the main thread`或`UI API called from background thread xx must be used from main thread only`

解决办法是：

对于每个Alamofire的返回的response时，自己根据需要，加上异步主线程，在其中处理UI操作的部分。

示例代码：

```swift
    func getUnreadCount(){
        let url = ServerApi.getUnreadCountUrl()

        getUrlRespJson_async(
            httpMethod: .get,
            url: url,
            parameters: nil,
            respJsonHandle: { (response) in
                if response.isSuccess {
                    if let count = response.successValue["count"].int {
                        gLog.debug("count:\(count)")
                        dispatchMain_async ({
                            if count <= 0{
                                self.tabBarItem.badgeValue = nil
                            } else if count >= 100 {
                                self.tabBarItem.badgeValue = "99+"
                            } else {
                                self.tabBarItem.badgeValue = "\(count)"
                            }
                        })
                    }
                } else if response.isFailure {
                    dispatchMain_async ({
                        self.noticeInfo(response.failedMessage)
                    })
                }
        })
    }
```

在respJsonHandle中当isSuccess时，用dispatchMain\_async确保处于主线程，然后才能去操作UI中的元素：self.tabBarItem.badgeValue

而对于上述函数详见：

* http函数getUrlRespJson\_async详见 [CrifanLibHttp.swift](https://github.com/crifan/crifanLib/blob/master/swift/Http/CrifanLibHttp.swift)
* 线程函数dispatchBackground\_async详见：[CrifanThread.swift](https://github.com/crifan/crifanLib/blob/master/swift/Thread/CrifanThread.swift)

## 断点续传就是利用Http的Range实现的

![](/assets/img/http_range_download.png)

详见：[［已解决］swift 下载时支持断点续传](http://www.crifan.com/ios_swift_download_support_breakpoint_resume)

