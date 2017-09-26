# 4d-component-google-api

The code is specific to using the [Google Cloud Vision API](https://cloud.google.com/vision/) but the methods for authentication can be applied to  ``OAuth2`` services in general. 

![oauth2](https://user-images.githubusercontent.com/1725068/30836937-64264c88-a29c-11e7-9ea5-8fd4b363f6fc.png)

### Version

<img src="https://cloud.githubusercontent.com/assets/1725068/18940648/2192ddba-8645-11e6-864d-6d5692d55717.png" width="32" height="32" />

**Note**: most critical features are ``HTTP Request`` and ``C_OBJECT``.

[Authenticating to the Cloud Vision API](https://cloud.google.com/vision/docs/auth)
---

### Using an API key

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

### Using a service account

* Authorization

```
$client:=Google client account
$endpoint:=Google endpoint authentication
$scope:=Google scope cloud vision

$url:=$endpoint+\
"?scope="+URL_Escape ($scope)+\
"&client_id="+$client+\
"&response_type=code"+\
"&redirect_uri=urn:ietf:wg:oauth:2.0:oob"

OPEN URL($url;*)

BASIC_MENU_BAR 

$authorizationCode:=Request("paste the code here")

If (OK=1)
	$path:=Get_user_folder +"auth"
	TEXT TO DOCUMENT($path;$authorizationCode;"utf-8")
End if 
```

* Get Access token

```
$path:=Get_user_folder +"auth"

ASSERT(Test path name($path)=Is a document)

$authorizationCode:=Document to text($path;"utf-8")

$client:=Google client account
$clientSecret:=Google client secret
$endpoint:=Google endpoint authorization

$params:="code="+$authorizationCode+\
"&client_id="+$client+\
"&client_secret="+$clientSecret+\
"&grant_type=authorization_code"+\
"&redirect_uri=urn:ietf:wg:oauth:2.0:oob"

C_BLOB($request)
CONVERT FROM TEXT($params;"utf-8";$request)

ARRAY TEXT($headerNames;1)
ARRAY TEXT($headerValues;1)

$headerNames{1}:="Content-Type"
$headerValues{1}:="application/x-www-form-urlencoded"

C_OBJECT($response)
$status:=HTTP Request(HTTP POST method;$endpoint;$request;$response;$headerNames;$headerValues)

If ($status=200)
	
	$path:=Get_user_folder +"token"
	TEXT TO DOCUMENT($path;JSON Stringify($response;*);"utf-8")
	
End if 
```

* Use Refresh token

```
$path:=Get_user_folder +"token"

ASSERT(Test path name($path)=Is a document)

$token:=JSON Parse(Document to text($path;"utf-8"))

C_TEXT($refresh_token)
$refresh_token:=OB Get($token;"refresh_token";Is text)

$client:=Google client account
$clientSecret:=Google client secret
$endpoint:=Google endpoint authorization

$params:="refresh_token="+$refresh_token+\
"&client_id="+$client+\
"&client_secret="+$clientSecret+\
"&grant_type=refresh_token"

C_BLOB($request)
CONVERT FROM TEXT($params;"utf-8";$request)

ARRAY TEXT($headerNames;1)
ARRAY TEXT($headerValues;1)

$headerNames{1}:="Content-Type"
$headerValues{1}:="application/x-www-form-urlencoded"

C_OBJECT($response)
$status:=HTTP Request(HTTP POST method;$endpoint;$request;$response;$headerNames;$headerValues)

If ($status=200)
	
	$path:=Get_user_folder +"token"
	TEXT TO DOCUMENT($path;JSON Stringify($response;*);"utf-8")
	
End if 
```

* Use OAuth2 token

```
$path:=Get_user_folder +"token"

ASSERT(Test path name($path)=Is a document)

$token:=JSON Parse(Document to text($path;"utf-8"))

C_TEXT($access_token;$token_type)
$access_token:=OB Get($token;"access_token";Is text)
$token_type:=OB Get($token;"token_type";Is text)

ARRAY TEXT($headerNames;1)
ARRAY TEXT($headerValues;1)

$headerNames{1}:="Authorization"
$headerValues{1}:=$token_type+" "+$access_token

READ PICTURE FILE(Get 4D folder(Current resources folder)+"sample.png";$image)

$url:=Google cloud vision images annotate

C_OBJECT($request;$response)
$request:=AnnotateImageRequest (->$image;TEXT_DETECTION)

$status:=HTTP Request(HTTP POST method;$url;$request;$response;$headerNames;$headerValues)

If ($status=200)
	
	SET TEXT TO PASTEBOARD(JSON Stringify($response;*))
	
End if 
```
