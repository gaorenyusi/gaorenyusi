
#### DedeCMS V5.7.115 xss vulnerability in background ads management.

**Vulnerability Validation:**

Click on Add an Ad in the Module Ads Manager

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240831015303395.png)

Inserting malicious malicious commands at the ad code

```html
<script>alert(111)</script>
```

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240831015329087.png)

Confirm and click the code button

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240831015352932.png)

pop-up window

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/image-20240831015409340.png)

