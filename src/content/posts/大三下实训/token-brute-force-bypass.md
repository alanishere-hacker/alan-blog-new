---
title: "Burp Suite Token防爆破机制绕过实战"
published: 2025-01-01
description: "使用Burp Suite的Pitchfork攻击模式配合Grep-Extract功能，实现Token防爆破机制的绕过。"
tags: ["Burp Suite", "Token", "爆破", "Web安全"]
category: "大三下实训"
---

Token防爆破：

随便输入用户名和密码，burp点击启动拦截，然后再提交登录。

![图片](./images/jr4_token_img1.png)

![图片](./images/jr4_token_img2.png)

添加字典符，切换攻击模式

![图片](./images/jr4_token_img3.png)

![图片](./images/jr4_token_img4.png)

![图片](./images/jr4_token_img5.png)

选中token值之后记得复制一下，填入第6步的初始payload里面。

![图片](./images/jr4_token_img6.png)

这里填写的token值是第5步里选中的token值！！

![图片](./images/jr4_token_img7.png)

![图片](./images/jr4_token_img8.png)

开始攻击

![图片](./images/jr4_token_img9.png)

登录成功

![图片](./images/jr4_token_img10.png)
