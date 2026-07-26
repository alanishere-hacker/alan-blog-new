---
title: "MySQL数据库基础操作：school数据库建表与数据管理"
published: 2025-06-01
description: "完成school数据库的创建与数据表设计，包括学生表、教师表、课程表、成绩表的建表、约束设置、数据插入，以及字段的修改与删除等DDL/DML操作练习。"
tags: ["MySQL", "数据库", "DDL", "DML", "建表", "约束", "外键"]
category: "大二下实训"
---
第一题：创建字符集为utf-8，库名为“school”的数据库

截图：

![IMG_256](./images/jr2_hw1_img14.png)

第二题：在该数据库中创建如下数据表，并插入数据；

学生表：student

Sno：主键

Sname：不为空

Sage：数据类型为int

Ssex：默认值为‘男’

![图片](./images/jr2_hw1_img15.png)

截图：

![IMG_256](./images/jr2_hw1_img16.png)

教师表：teacher

Tno：主键

Tname：不为空

![图片](./images/jr2_hw1_img17.png)

截图：

![IMG_256](./images/jr2_hw1_img18.png)

课程表：course

Cno：主键

Cname：不为空

Tno：外码，引用teacher表的tno字段

![图片](./images/jr2_hw1_img19.png)

截图：

![图片](./images/jr2_hw1_img2.png)

成绩表：score

Sno，Cno：主键

Score：int型，可空

![图片](./images/jr2_hw1_img3.png)

截图：

![图片](./images/jr2_hw1_img4.png)

第三题：将score表中的score字段改名为“chengji”；

截图：

![图片](./images/jr2_hw1_img5.png)

第四题：删除score表中chengji字段；

截图：

![图片](./images/jr2_hw1_img6.png)

第五题：在score表中增加名为score字段，数据类型为int型，约束为非空的字段；

截图：

![图片](./images/jr2_hw1_img7.png)

第六题：按照socre表图片内容修改score表中score字段的数据；

截图：

![图片](./images/jr2_hw1_img8.png)

![图片](./images/jr2_hw1_img9.png)

第七题：删除course表中的C010的数据；

截图：

![图片](./images/jr2_hw1_img10.png)

![图片](./images/jr2_hw1_img11.png)
