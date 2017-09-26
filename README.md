# 4d-component-google-api
Example of calling Google APIs from 4D

### Version

<img src="https://cloud.githubusercontent.com/assets/1725068/18940648/2192ddba-8645-11e6-864d-6d5692d55717.png" width="32" height="32" />

**Note**: most critical features are ``HTTP Request`` and ``C_OBJECT``.

## About

Example calls Google Cloud Vision (OCR) to read text in image.

![oauth2](https://user-images.githubusercontent.com/1725068/30836937-64264c88-a29c-11e7-9ea5-8fd4b363f6fc.png)

### [Authenticating to the Cloud Vision API](https://cloud.google.com/vision/docs/auth)

* Using an API key

```
READ PICTURE FILE(Get 4D folder(Current resources folder)+"sample.png";$image)

$url:=Google cloud vision images annotate+"?key="+Google api key

C_OBJECT($request;$response)
$request:=AnnotateImageRequest (->$image;TEXT_DETECTION)

$status:=HTTP Request(HTTP POST method;$url;$request;$response)

If ($status=200)
	
	SET TEXT TO PASTEBOARD(JSON Stringify($response;*))
	
End if 
```
