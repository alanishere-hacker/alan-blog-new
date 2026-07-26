---
title: "Pikachu靶场XSS漏洞第1-6关通关实战"
published: 2025-01-01
description: "Pikachu靶场XSS漏洞练习，涵盖反射型XSS（GET/POST）、存储型XSS、DOM型XSS及XSS盲打共6关的通关记录。"
tags: ["XSS", "Pikachu", "反射型XSS", "存储型XSS", "DOM型XSS", "Web安全"]
category: "大三下实训"
---

Pikachu -xss第1-6关

反射型xss（get）

搜索框输入<script>alert(1)</script>

![图片](./images/jr4_xss_img1.png)

打开控制台查看源代码，发现maxlength=”20”

![图片](./images/jr4_xss_img2.png)

把20修改成100试试，此时发现能完整输入<script>alert(1)</script>

![图片](./images/jr4_xss_img3.png)

成功弹窗

![图片](./images/jr4_xss_img4.png)

反射性xss(post)

admin/123456登录

![图片](./images/jr4_xss_img5.png)

搜索框输入<script>alert(document.cookie)</script>

![图片](./images/jr4_xss_img6.png)

存储型xss

留言板中输入<script>alert(1)</script>

![图片](./images/jr4_xss_img7.png)

刷新页面依旧还有弹窗

![图片](./images/jr4_xss_img8.png)

DOM型xss

输入123提交，发现“123”被发送到<a href=””>里

![图片](./images/jr4_xss_img9.png)

javascript:alert(1)

![图片](./images/jr4_xss_img10.png)

![图片](./images/jr4_xss_img11.png)

DOM型xss

1. 输入123
![图片](./images/jr4_xss_img12.png)

2.输入javascript:alert(1)

![图片](./images/jr4_xss_img13.png)

xss盲打

输入<script>alert(document.cookie)</script>提交

![图片](./images/jr4_xss_img14.png)

进入后台/xssblind/admin_login.php登录admin/123456，登录进去后立刻有弹窗

![图片](./images/jr4_xss_img15.png)

![图片](./images/jr4_xss_img16.png)
