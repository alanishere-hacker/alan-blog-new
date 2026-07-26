---
title: "内网信息收集常用命令与技巧汇总"
published: 2025-07-21
description: "整理内网渗透中主机信息收集与域信息收集的常用命令，涵盖网络配置、系统信息、进程服务、用户列表、端口补丁及域控制器查询等关键操作。"
tags: ["内网渗透", "信息收集", "域渗透", "Windows命令", "Mimikatz", "域控制器"]
category: "大三下实训"
---

内网信息收集

主机信息收集：

查询网络配置信息：ipconfig /all

![图片](./images/jr4_intranet_img1.png)

查询操作系统及安装软件的版本信息：

systeminfo | findstr /I "OS Name OS Version"

![图片](./images/jr4_intranet_img2.png)

软件查询：wmic product get name,version

Powershell：powershell "Get-WmiObject -class Win32_Product |Select-Object -Property name,version"

![图片](./images/jr4_intranet_img3.png)

查询本机服务信息：wmic service list brief

![图片](./images/jr4_intranet_img4.png)

查询进程列表：Tasklist /v

![图片](./images/jr4_intranet_img5.png)

关闭360杀毒软件：taskkill /im 360Tray.exe /f

查询启动程序信息：wmic startup get command,caption

![图片](./images/jr4_intranet_img6.png)

查询计划任务及开机时间

计划任务：schtasks /query /fo LIST /v

![图片](./images/jr4_intranet_img7.png)

开机时间： net statistics workstation

![图片](./images/jr4_intranet_img8.png)

查询用户列表

用户：Net user 可以找命名规则 推测整个域的用户命名方式

![图片](./images/jr4_intranet_img9.png)

获取本地管理员（含域）：net localgroup administrators

![图片](./images/jr4_intranet_img10.png)

查询当前在线用户：query user || qwinsta

![图片](./images/jr4_intranet_img11.png)

读取密码：mimikatz

![图片](./images/jr4_intranet_img12.png)

查询端口列表及补丁

端口查询：Netstat –ano

![图片](./images/jr4_intranet_img13.png)

查询补丁列表：Systeminfo

![图片](./images/jr4_intranet_img14.png)

例：wmic qfe get Caption,Description,HotFixID,InstalledOn

![图片](./images/jr4_intranet_img15.png)

查询路由表：arp -a

![图片](./images/jr4_intranet_img16.png)

查询本机共享列表：net share

![图片](./images/jr4_intranet_img17.png)

查询IE浏览器的代理信息：reg query "HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"

![图片](./images/jr4_intranet_img18.png)

查询RDP端口号：reg query "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-TCP" /v portNumber

![图片](./images/jr4_intranet_img19.png)

查询用户列表：net user

#通过分析本机用户列表，分析命名规则，推测整个域的命名方式

![图片](./images/jr4_intranet_img20.png)

域信息收集：

域内用户基本信息

net view 查看域内机器列表

net view /domain: ZZZ 查看ZZZ域中所有的机器列表。

net group /domain 查询域里面所有的用户组列表

![图片](./images/jr4_intranet_img21.png)

net group "domain computers" /domain 查看所有域成员计算机列表

![图片](./images/jr4_intranet_img22.png)

net accounts /domain 查询域用户密码过期等信息

![图片](./images/jr4_intranet_img23.png)

net user /domain 获取域用户列表

![图片](./images/jr4_intranet_img24.png)

net group "domain admins" /domain 获取域管理员列表

![图片](./images/jr4_intranet_img25.png)

net group "domain controllers" /domain 查看域控制器(如果有多台)

![图片](./images/jr4_intranet_img26.png)

net localgroup administrators 查看本地管理员组用户[通常含有域用户]

![图片](./images/jr4_intranet_img27.png)

net localgroup administrators /domain 登录域管理员用户

![图片](./images/jr4_intranet_img28.png)

net view /domain 查看内网存在多少个域

查询所有域用户列表

向域控制器进行查询

net user /domain

![图片](./images/jr4_intranet_img29.png)

获取域内用户详细信息

wmic useraccount get /all

![图片](./images/jr4_intranet_img30.png)

查看存在的用户

dsquery user

![图片](./images/jr4_intranet_img34.png)

查询域内置本地管理员组用户

net localgroup administrators /domain

![图片](./images/jr4_intranet_img31.png)

查询域管理员用户组

查询域管理员用户

net group “domain admins” /domain

![图片](./images/jr4_intranet_img32.png)

查询管理员用户组

net group “Enterprise Admins” /domain

![图片](./images/jr4_intranet_img33.png)
