---
title: "Apache Shiro-550反序列化漏洞（CVE-2016-4437）复现"
published: 2025-01-01
description: "Apache Shiro RememberMe反序列化漏洞（CVE-2016-4437）的完整复现过程，包括漏洞检测、密钥爆破、利用链选择及命令执行。"
tags: ["Shiro", "反序列化", "CVE-2016-4437", "RememberMe", "Web安全"]
category: "大三下实训"
---

Shiro-550反序列化漏洞复现（CVE-2016-4437）

靶机环境：win11

配置tomcat

![图片](./images/jr4_shiro_img1.png)

配置工件

![图片](./images/jr4_shiro_img2.png)

运行pom.xml
![图片](./images/jr4_shiro_img3.png)

打开bp，用bp内置浏览器打开http://localhost:8080/samples_web_war_exploded/

![图片](./images/jr4_shiro_img4.png)

输入账号密码，勾选remember me，登录

![图片](./images/jr4_shiro_img5.png)

登录成功

![图片](./images/jr4_shiro_img6.png)

bp抓包分析

![图片](./images/jr4_shiro_img7.png)

抓包后，在返回包当中发现存在rememberMe=deleteMe字样，可以大概确定有配置shiro，可以进行下一步。因为shiro本身功能就是一个身份验证管理，所以一般都在登录口可以看到。

客户端在点击remember me的时候，服务器会生成一个cookie，cookie采取base64以及aes的加密手段，在存在漏洞的shiro版本，aes的加密存在默认的密钥

利用shiro_attack工具爆破密钥并进一步利用

启动工具

java -jar shiro_attack-4.5.6-SNAPSHOT-all.jar

![图片](./images/jr4_shiro_img8.png)

爆破密钥

![图片](./images/jr4_shiro_img9.png)

![图片](./images/jr4_shiro_img10.png)

接下来爆破利用链以及回显方式

![图片](./images/jr4_shiro_img11.png)

尝试命令执行

![图片](./images/jr4_shiro_img12.png)
