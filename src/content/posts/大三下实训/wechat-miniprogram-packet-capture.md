---
title: "微信小程序抓包与越权漏洞测试实践"
published: 2025-07-20
description: "使用Yakit和Proxifier对微信小程序进行MITM抓包，发现积分兑换中的参数篡改漏洞，并完成Android模拟器证书配置以实现移动端抓包。"
tags: ["微信小程序", "抓包", "Yakit", "Proxifier", "MITM", "越权漏洞", "参数篡改", "Android模拟器"]
category: "大三下实训"
---

小程序抓包&简单漏洞测试

环境准备

Yakit：https://yaklang.com/

Proxifier中文版：https://github.com/1564307973/Proxifier-CN

微信PC端：https://weixin.qq.com/

夜神模拟器：https://www.yeshen.com/

（1）配置Yakit MITM

1. 打开Yakit → 点击顶部 “MITM” 标签

2. 点击 “启动MITM”

3. 记录代理端口（默认 8083 ）

4. 安装证书（首次使用需要）：

访问 http://mitm.it

下载Windows证书并安装到"受信任的根证书颁发机构"
![图片](./images/jr4_mini_img1.png)

![图片](./images/jr4_mini_img2.png)

文件后缀改成.der

![图片](./images/jr4_mini_img3.png)

双击开始安装

![图片](./images/jr4_mini_img4.png)

![图片](./images/jr4_mini_img5.png)

![图片](./images/jr4_mini_img6.png)

证书安装完成

![图片](./images/jr4_mini_img7.png)

（2）配置Proxifier代理服务器

（使用汉化版时，可将 Chs 文件夹里的 exe 覆盖程序文件夹里的原文件。）

打开Proxifier → Profile → Proxy Servers

点击 Add，添加：

Address: 127.0.0.1

Port: 8083（与Yakit端口一致）

Protocol: HTTPS

![图片](./images/jr4_mini_img8.png)

配置微信小程序抓包

![图片](./images/jr4_mini_img9.png)

小程序运行在独立的沙箱进程中，网络请求全部由 WeChatAppEx.exe 发出

配置Proxifier规则：

1. 打开Proxifier → Profile → Proxy Rules

2. 点击 Add 添加2条新规则：

名称：WeChat_MiniProgram

应用程序：WeChatAppEx.exe

目标主机：Any

端口：Any

动作：Yakit_Proxy（你的代理）

名称：WeChat_Main

应用程序：WeChat.exe

目标主机：Any

端口：Any

动作：Direct

![图片](./images/jr4_mini_img10.png)

![图片](./images/jr4_mini_img11.png)

确保这条规则在 Default规则之前（优先级更高）

![图片](./images/jr4_mini_img12.png)

3.  验证抓包成功

（1）查看Proxifier日志

正确配置后，应该能看到：

![图片](./images/jr4_mini_img13.png)

（2）查看Yakit流量

1. 打开Yakit → MITM → 历史数据包

2. 启动小程序并操作

3. 应该能看到大量请求涌入

![图片](./images/jr4_mini_img14.png)

真实漏洞测试：

打开小程序（具体名字不透露）

测试开始之前的优惠券数量和积分数量

![图片](./images/jr4_mini_img15.png)

进入积分商城，随便选择一个商品进行兑换

![图片](./images/jr4_mini_img16.png)

随便选择一个商品

![图片](./images/jr4_mini_img17.png)

点击立即兑换

![图片](./images/jr4_mini_img18.png)

点击提交订单

![图片](./images/jr4_mini_img19.png)

抓到提交订单的包

![图片](./images/jr4_mini_img20.png)

将amount=1改成=-2（或者任何你想要的整数量但必须是负整数），然后发包

![图片](./images/jr4_mini_img21.png)

这时我们返回小程序，查看积分记录，可以看到，我们的积分先是正常兑换一个90额度的商品扣了1*90=90积分，然后我们抓包改数据amount=-2再发包，相当于再次进行兑换的操作，但兑换的数量变成了-2，于是扣积分的计算就变成了-2*90=-180积分，扣-180积分=--180积分=+180积分（负负得正）。于是，就有了积分+180的结果。

![图片](./images/jr4_mini_img22.png)

这时，我们返回主页面查看优惠劵数量和积分数量

![图片](./images/jr4_mini_img23.png)

很明显看到，优惠券数量加了1，这是我们兑换一个90积分的优惠券成功的效果。然而，积分从一开始的220变成现在的310，这中间发生的变化即是：220-90+180=310。

那怎么做到在不扣积分的前提下，增加积分？

答案：不要关闭抓包页面，在确保 amount=负整数 这个条件下，继续点击发包，发一次即增加相应数量的积分，理论上可以发包无数次获得无数积分。

测试截图：

![图片](./images/jr4_mini_img24.png)

查看积分，发现成功加了相应积分

![图片](./images/jr4_mini_img25.png)

拿到积分后，可以去积分商城进行正常的优惠券兑换。

模拟器配置代理

导出yakit证书，修改为der格式

![图片](./images/jr4_mini_img26.png)

证书不能直接导入模拟器中，需要再对它进行一定的加工让它成为pem后缀的证书才可以。

![图片](./images/jr4_mini_img27.png)

将.pem证书上传到kali上，使用openssl，执行以下命令：

openssl x509 -inform PEM -subject_hash_old -in yakit.pem | head -1

mv yakit.pem 86ae248d.0

![图片](./images/jr4_mini_img28.png)

然后打开手机模拟器（建议安装安卓7及以下，否则抓包会有问题）

进入设置搜索关于平板电脑，在版本号那一块连续点击5下

![图片](./images/jr4_mini_img29.png)

点击完后找到开发者选项，打开USB调试

![图片](./images/jr4_mini_img30.png)

开启手机Root权限

![图片](./images/jr4_mini_img31.png)

将证书从kali下载到模拟器的Nox\bin上

![图片](./images/jr4_mini_img32.png)

然后我们要用abd将86ae248d.0导入到模拟器中的/system/etc/security/cacerts/路径下。

adb.exe文件在安装模拟器的Nox\bin文件夹下

在当前目录下执行以下命令：

adb.exe connect 127.0.0.1:62001  //夜神模拟器的默认端口号（62001）

adb.exe devices //查看连接设备

adb.exe root // 提升到root权限

adb remount // 重新挂载system分区

adb.exe push 86ae248d.0 /system/etc/security/cacerts/ //将证书放到系统证书目录

![图片](./images/jr4_mini_img33.png)

可以看到证书已被成功转移到模拟器/system/etc/security/cacerts/下。我们还需要一个工具，让证书能够生效。

MT管理器：是一款在 Android 平台上非常知名的文件管理与 APK 逆向修改神器，常被极客和玩机用户称为“安卓上的瑞士军刀”。它由南京州游网络科技有限公司开发，核心特点是兼顾日常文件操作与深度的应用修改能力。

下载完毕后，将工具拖到手机模拟器中，会自动安装成功

进入mt后，到system/etc/security/cacerts/下，看到86ae248d.0已存在，但没有执行权限，我们还需给他加响应的权限，才能正常运作

![图片](./images/jr4_mini_img39.png)

打开刚刚的cmd终端

adb root

adb remount

![图片](./images/jr4_mini_img34.png)

退回到/，找到system,点属性，改写权限，改777然后，子文件也勾上

![图片](./images/jr4_mini_img35.png)

最后只需再设置下yakit的代理监听，指定地址就是本机的IP地址

![图片](./images/jr4_mini_img36.png)

模拟器打开设置-->网络和互联网-->互联网-->网络详情，配置代理

![图片](./images/jr4_mini_img37.png)

检验抓包结果

![图片](./images/jr4_mini_img38.png)
