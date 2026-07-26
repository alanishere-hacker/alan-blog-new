---
title: "验证码绕过与Token防爆破技术实战"
published: 2025-01-01
description: "使用Burp Suite工具实现客户端验证码绕过、服务端验证码绕过以及Token防爆破机制的绕过技术。"
tags: ["Burp Suite", "验证码绕过", "Token", "爆破", "Web安全"]
category: "大三下实训"
---

客户端验证码绕过：

按f12

![图片](./images/jr4_623_img1.png)

找到设置

禁用javascript

![图片](./images/jr4_623_img2.png)

常规密码爆破

![图片](./images/jr4_623_img3.png)

服务器验证码绕过：

输入正确验证码，开启拦截抓包

![图片](./images/jr4_623_img4.png)

抓包后进行爆破

![图片](./images/jr4_623_img5.png)

爆破成功

![图片](./images/jr4_623_img6.png)

Token防爆破绕过：

![图片](./images/jr4_623_img7.png)

![图片](./images/jr4_623_img8.png)

如果使用的是其他版本，设置token的位置在options模块（代理模块旁边）

设置token

![图片](./images/jr4_623_img9.png)

![图片](./images/jr4_623_img10.png)

![图片](./images/jr4_623_img11.png)

不同版本的攻击模式名字可能不同，找结尾是grep的攻击模式

![图片](./images/jr4_623_img12.png)

修改线程池

![图片](./images/jr4_623_img13.png)

爆破成功

![图片](./images/jr4_623_img14.png)
