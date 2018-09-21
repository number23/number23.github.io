{:title "Tips for curl command"
 :layout :post
 :tags  ["Linux", "curl"]}

### 1.下载一个文件
有两个选项：`-O` 和 `-o`，(大小写的字母`O`, 不是数字`0`), 前者获取URL中的文件名用作结果的文件名， 后者可以自定义文件名
```
$ curl -O http://yourdomain.com/yourfile.tar.gz # 保存为 yourfile.tar.gz
$ curl -o newfile.tar.gz http://yourdomain.com/yourfile.tar.gz # 保存为 newfile.tar.gz
```
我们可以通过指定URL来一次下载多个文件:
```
$ curl -O URL1 -O URL2
```
如果我们在同一服务器下载多个文件，`CRUL`将尝试重新使用该连接。

### 2. 使用-L选项跟随 HTTP 位置标头
有时候一个`URL`会被重定向到另外一个，`CURL` 默认是不会跟随的，只会返回一个`3XX`的重定向
```
$ curl http://yourdomain.com


<html>
<head><title>301 Moved Permanently</title></head>
<body bgcolor="white">
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx</center>
</body>
</html>
```

### 3. 恢复一个已经中断的下载
如果下载因某种原因中断了(例如，使用`Ctrl + c`), 可以使用`-C -`从停止的地方开始继续下载。
```
$ curl -O http://www.gnu.org/software/gettext/manual/gettext.html 
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  2 1375k    2 40901    0     0  41738      0  0:00:33 --:--:--  0:00:33 41735^C

$ curl -C - -O http://www.gnu.org/software/gettext/manual/gettext.html
** Resuming transfer from byte position 290816
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 1091k  100 1091k    0     0   414k      0  0:00:02  0:00:02 --:--:--  414k
```

### 4. HTTP身份验证
有时，网站需要用户名和密码才能查看内容(例如在`.htaccess`文件设定的)，在`-u`选项的帮助下，我们可以将这些凭据传递到Web服务器，如下所示
```
$ curl -u username:password URL
```
默认情况下，使用基本HTTP身份验证。 我们可以使用`-ntlm | –digest`指定其他身份验证方法

### 5. 从FTP服务器下载文件
```
$ curl -u username:password -O ftp://yourftpserver/yourfile.tar.gz
```
以上命令将在当前工作目录从`ftp://yourftpserver`下载`yourfile.tar.gz`，
如果FTP服务器允许匿名登录，您可以跳过`-u username:password`。

### 6. 上传文件到FTP服务器
```
$ curl -u username:password -T mylocalfile.tar.gz ftp://yourftpserver
```
还可以使用范围操作同时上载多个文件：
```
$ curl -u ftpuser:ftppass -T "{file1,file2}" ftp:/yourftpserver
```

### 7. 查询 HTTP 消息头
HTTP消息头允许远程Web服务器发送有关自身的其他信息以及实际请求，提供了有关如何处理请求的详细信息   
```
$ curl -I www.google.com

HTTP/1.1 200 OK
Date: Fri, 21 Sep 2018 05:47:05 GMT
Expires: -1
Cache-Control: private, max-age=0
Content-Type: text/html; charset=ISO-8859-1
P3P: CP="This is not a P3P policy! See g.co/p3phelp for more info."
Server: gws
X-XSS-Protection: 1; mode=block
X-Frame-Options: SAMEORIGIN
Set-Cookie: 1P_JAR=2018-09-21-05; path=/; domain=.google.com
Set-Cookie: NID=139=XwjdbwcijthXcvsa4bTuw_mJJj8O5gY8Bc-HvulmzBFTesG-IJ4lIQXxgtGf9cVD1O_A0ePVWWun5x4tQiTtXLnzdFIG9EEFCw0QocNvc_6q7ddAgYJbmTKJ-xwbN3ql; path=/; domain=.google.com; HttpOnly
Transfer-Encoding: chunked
Accept-Ranges: none
Vary: Accept-Encoding
Proxy-Connection: keep-alive
```

### 8. 使用代理
```
$ curl -x proxy.yourdomain.com:8080 -U user:password -O http://yourdomain.com/yourfile.tar.gz
```
如果代理服务器允许匿名，您可以跳过`-U username:password`。

### 9. 提交表单
```
curl --data "firstName=John&lastName=Doe" https://yourdomain.com/info.php
```
以上命令将`firstName`和`lastName`参数及其对应值发送到`https://yourdomain.com/info.php`。

### 10. 使用 Cookies
写入 `Cookies` 文件：
```
$ curl --cookie-jar c.txt https://www.google.com
```
使用 `Cookies` 文件：
```
$ curl --cookie c.txt https://www.google.com
```

更多参考[这里](https://curl.haxx.se/docs/httpscripting.html)

