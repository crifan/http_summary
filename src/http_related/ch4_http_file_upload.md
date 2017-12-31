# HTTP的文件上传

在HTTP相关开发期间，往往会涉及到上传文件，其中会涉及`multipart/form-data`。

而官方的HTTP和`multipart/form-data`相关的协议有：
* [Returning Values from Forms:  multipart/form-data](https://www.ietf.org/rfc/rfc2388.txt)
* [Format of Internet Message Bodies](https://www.ietf.org/rfc/rfc2045.txt)
* [Form submission](https://www.w3.org/TR/html401/interact/forms.html#h-17.13)

对于用HTTP的`multipart/form-data`实现文件上传：
* 既有合适的如Postman等工具方便去调试
* 也有iOS中Alamofire等库很好的支持
* （如果清楚协议详情）也可以自己手动写代码拼接出`multipart/form-data`所需要的字段和内容去实现文件上传

下面就来详细介绍这方面的内容。

### iOS中Alamofire支持文件上传的`MultipartFormData`
可以基本实现文件上传的iOS的Alamofire的代码：
```swift
func uploadImageData(imageData:NSData){
    dispatchBackground_async({
        gLog.verbose("imageData.length=\(imageData.length)")

        self.pleaseWait()

        var curHeader = Dictionary<String, String>()
        curHeader["authenticate"] = "token " + gCurUserItem.accessToken

        gLog.verbose("curHeader=\(curHeader)")

        let curDate = NSDate()

        let name = "file"
        let filename = curDate.toString("yyyyMMdd_hhmmss") + ".jpg"
        gLog.verbose("name=\(name), filename=\(filename)")
        //uploadImageData > name=file, filename=20160625_085958.jpg

        Alamofire.upload(
            .POST,
            ServerApi.uploadFileUrl(gCurUserItem.id),
            headers: curHeader,
            multipartFormData: {multipartFormData in
                multipartFormData.appendBodyPart(
                    data: imageData,
                    name: name,
                    fileName: filename,
                    mimeType: "image/jpeg"
                )
            },

            encodingCompletion: { encodingResult in
                gLog.verbose("encodingResult=\(encodingResult)")
                /*
encodingResult=Success($ curl -i \
    -X POST \
    -H "User-Agent: Sales App/com.qoro.QorosSalesApp (2016.6.24; OS ban ben 9.3(ban hao 13E230))" \
    -H "authenticate: token fmc2q2m25vre0htehsijmlur53" \
    -H "Content-Type: multipart/form-data; boundary=alamofire.boundary.5fd5efc56016a4df" \
    -H "Accept-Encoding: gzip;q=1.0, compress;q=0.5" \
    -H "Accept-Language: en-US;q=1.0, zh-Hans-US;q=0.9" \
    "http://192.168.1.102:8080/app/user/10000010/upload", false, nil)

encodingResult=Success($ curl -i \
    -X POST \
    -H "User-Agent: Sales App/com.qoro.QorosSalesApp (2016.6.24; OS ban ben 9.3(ban hao 13E230))" \
    -H "authenticate: token 4up3enr7nfd6c3rfei1ukcg1vv" \
    -H "Content-Type: multipart/form-data; boundary=alamofire.boundary.01fc8db6b551cd51" \
    -H "Accept-Encoding: gzip;q=1.0, compress;q=0.5" \
    -H "Accept-Language: en-US;q=1.0, zh-Hans-US;q=0.9" \
    "http://qapp.chinacloudapp.cn/app/user/10000010/upload", false, nil)
                 */

                switch encodingResult {
                //case .Success(let uploadRequest, streamingFromDisk, streamFileURL):
                    case .Success(let uploadRequest, _, _):
//                        gLog.verbose("uploadRequest=\(uploadRequest), streamingFromDisk=\(streamingFromDisk), streamFileURL=\(streamFileURL)")
                        gLog.verbose("uploadRequest=\(uploadRequest)")
                    //upload=POST http://192.168.1.102:8080/app/user/10000010/upload
                    //uploadRequest=POST http://qapp.chinacloudapp.cn/app/user/10000010/upload

                    uploadRequest.responseJSON(completionHandler:
                        { response in
                            gLog.verbose("response=\(response)")
                            /*
                             response=SUCCESS: {
                                code = 200;
                                data =     {
                                    created = 1466850689624;
                                    id = 1000001020160625103129624;
                                    location = "<null>";
                                    name = "image.jpg";
                                    owner = "<null>";
                                    uploader = 10000010;
                                };
                                message = ok;
                            }

                            response=SUCCESS: {
                                errors =     (
                                    "The request body may not be null",
                                    "The request body may not be null"
                                );
                            }

                            response=SUCCESS: {
                                code = 200;
                                data =     {
                                    created = 1466859610413;
                                    id = 1000001020160625130010413;
                                    location = "<null>";
                                    name = "20160625_085958.jpg";
                                    owner = "<null>";
                                    uploader = 10000010;
                                };
                                message = ok;
                            }
                             */

                            switch response.result {
                            case .Success(let value):
                                let valueJson = JSON(value)
                                gLog.verbose("\(valueJson)")
                                /*
                                {
                                  "message" : "ok",
                                  "code" : 200,
                                  "data" : {
                                    "name" : "image.jpg",
                                    "id" : "1000001020160625103129624",
                                    "uploader" : 10000010,
                                    "location" : null,
                                    "created" : 1466850689624,
                                    "owner" : null
                                  }
                                }
                                 */

                                let statusCode = valueJson["code"].int ?? 0
                                if statusCode == 200 {
                                    let dataObj = valueJson["data"].object
                                    let dataJson:JSON = JSON(dataObj)
                                    self.uploadAttachmentsHandler(Alamofire.Result.Success(dataJson))
                                    gLog.verbose("dataJson=\(dataJson)")

                                } else {
                                    self.noticeError("上传附件出错", autoClear: true, autoClearTime: 1)

                                    let message = valueJson["message"].string ?? ""
                                    let messageStrArr = message.splitToStrArr("|")

                                    var messageStr:String = message
                                    var subCode:Int = 0

                                    if messageStrArr.count > 1 {
                                        messageStr = messageStrArr[0]
                                        subCode = Int(messageStrArr[1]) ?? 0
                                    }

                                    let error:NSError = NSError(domain: HttpErrorDomain, code: statusCode, userInfo: [
                                        "message"   : messageStr,
                                        "code"      : statusCode,
                                        "subCode"   : subCode,
                                        ])

                                    self.uploadAttachmentsHandler(Alamofire.Result.Failure(error))
                                }
                            case .Failure(let error):
                                gLog.verbose("error=\(error)")
                                self.uploadAttachmentsHandler(Alamofire.Result.Failure(error))
                            }
                    })

                case .Failure(let encodingError):
                    gLog.error("上传附件失败\(encodingError)")
                }
            }
        )

    })
}
```

详见：

[［已解决］Alamofire中multipartFormData中的name和filename到底应该是什么值](http://www.crifan.com/ios_swift_alamofire_upload_multipartformdata_name_filename_meaning)

### Postman中可以方便的调试文件上传
当然，如果后台设计的不是很规范，则可能导致，即使传入了标准的参数设置：
* POST xxx/xxx/upload
* Headers
    * `Authorization: Bearer 6c9f198878cb42e6aad83fecbb598d10_8a9bfdab5a21ca27015a21d72fb20000`
    * `Content-Type: multipart/form-data;boundary=any_random_value`
    * `Accept: application/json`

却仍可能会返回错误`HTTP 415 Unsupported Media Type`

详见：

[【已解决】Mac中Postman去POST实现文件上传时出错：HTTP 415 Unsupported Media Type – 在路上](https://www.crifan.com/mac_postman_post_upload_file_error_http_415_unsupported_media_type/)

但是本身[Postman](https://github.com/crifan/api_tool_postman)对于文件上传的支持，还是很方便很好用的。

### C\#中如何手动裸写`multipart/form-data`的文件上传
之前也曾折腾过，自己用C#代码拼凑出`multipart/form-data`所需要的内容的格式。

如此折腾后，算是对于`multipart/form-data`有了更深入的认识。

详见：

[【教程】模拟登陆之如何分析并用代码模拟上传文件 – 在路上](https://www.crifan.com/emulate_login_example_for_analysis_and_write_code_for_emulate_upload_file/)


