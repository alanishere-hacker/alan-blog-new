---
title: "VI编辑器与Linux文件权限管理实践"
published: 2025-11-03
description: "学习VI文本编辑器的基本操作与三种模式切换，掌握Linux文件权限管理、用户和用户组的创建与管理方法。"
tags: ["Linux", "VI编辑器", "文件权限", "用户管理", "chmod"]
category: "Linux实验"
---
## 实验目的及要求

1. 掌握vi编辑器的进入与退出方法。
2. 了解文本编辑器的三种模式。
3. 熟练掌握使用vi编辑器进行编辑、选择及操作文本文件的命令。
4. 理解Linux中文件所有者和权限的概念。
5. 掌握有关权限操作的常用命令。
6. 熟悉命令行操作方式进行用户和用户组管理。

## 二、实验原理与内容

1. 进入和退出vi。
2. 利用文件拷贝方式建立一个文件。
3. 在该文件上移动光标位置。
4. 对该文件内容执行删除、修改、替换等操作。
5. 普通文件与目录权限的修改。
6. 改变文件属主。
7. 建立新用户账号权限测试。
8. 为新用户建立账号和工作组，并进行相应配置。
9. 以该用户身份登录，修改密码。
10. 删除该用户。

## 三、实验软硬件环境

台式电脑、VMware虚拟机、CENTOS 7以上版本。

## 四、实验过程（实验步骤、记录、数据、分析）

### 1. VI操作

#### 新建目录与复制文件

在当前用户默认工作目录下新建一个名为"test"的目录，将文件 `/etc/rsyslog.conf` 复制到"test"目录下。

![新建目录](./images/linux_exp4_img1.jpeg)

![复制文件](./images/linux_exp4_img2.jpeg)

#### 使用vi打开文件

使用vi打开"test"目录下的rsyslog.conf。

![vi打开文件](./images/linux_exp4_img3.jpeg)

#### 查找RULES所在行

指出设定RULES的所在行号，将光标移到该行，并复制该行内容。

![查找RULES](./images/linux_exp4_img4.jpeg)

#### 粘贴与撤销

将该行内容粘贴至最后一行，然后撤销第6步的动作。

![粘贴内容](./images/linux_exp4_img5.jpeg)

![撤销操作](./images/linux_exp4_img6.jpeg)

#### 删除行

光标移到 `$FileOwner syslog`，删除该行。（未找到该行）

![删除操作](./images/linux_exp4_img7.jpeg)

#### 存盘但不退出

![存盘](./images/linux_exp4_img8.jpeg)

#### 插入模式编辑

将光标移到首行，插入模式下输入 `Hello,this is vi world!`。

![插入模式](./images/linux_exp4_img9.jpeg)

#### 查找字符串

向下查找字符串"syslog"，再向上查找字符串"TCP"。

![向下查找](./images/linux_exp4_img10.jpeg)

![向上查找](./images/linux_exp4_img11.png)

#### 强制退出

强制退出vi，不存盘。

![强制退出](./images/linux_exp4_img13.jpeg)

### 2. 文件权限操作

#### 2.1 创建目录和文件

在用户默认工作目录下创建目录test03，进入test03目录，使用touch命令创建空文件file1。

![创建test03和file1](./images/linux_exp4_img14.jpeg)

#### 2.2 显示文件详细信息

显示file1详细信息，记录文件的权限信息、所属用户和组。

![ls -l查看权限](./images/linux_exp4_img15.jpeg)

#### 2.3 为其他用户添加写权限

对file1设置权限，使其他用户可以对此文件进行写操作，并查看设置结果。

![chmod o+w](./images/linux_exp4_img16.jpeg)

#### 2.4 取消同组用户读取权限

取消同组用户对file1的读取权限，并查看设置结果。

![chmod g-r](./images/linux_exp4_img17.jpeg)

#### 2.5 数字形式设置权限（755）

用数字形式来为file1设置权限，使所有者可读、可写、可执行；其他用户和所属组用户只有读和执行的权限。

![chmod 755](./images/linux_exp4_img18.jpeg)

#### 2.6 数字形式设置权限（400）

用数字形式更改file1权限，使文件所有者只能读取此文件，其他任何用户都没有权限。

![chmod 400](./images/linux_exp4_img19.jpeg)

#### 2.7 为其他用户添加写权限

![chmod o+w](./images/linux_exp4_img20.jpeg)

#### 2.8 查看目录权限

回到上层目录，查看test03的权限。

![查看目录权限](./images/linux_exp4_img21.jpeg)

#### 2.9 为其他用户添加目录写权限

![chmod o+w目录](./images/linux_exp4_img25.jpeg)

#### 2.10 查看所属用户和组

查看并记录目录test03及其中文件的所属用户和组。

#### 2.11 删除目录

删除目录test03及其下的文件。

#### 2.12 umask测试

设定umask为000（`umask 000`），创建testdir目录，并查看其权限。

![umask 000](./images/linux_exp4_img26.jpeg)

### 3. 文件特殊权限操作

#### 3.1 查看passwd命令权限

使用whereis命令查看passwd命令的可执行文件，并查看passwd可执行文件的文件权限。

![passwd权限](./images/linux_exp4_img27.jpeg)

#### 3.2 普通用户修改密码

在普通用户模式下使用passwd命令修改当前用户的密码。

> **注意**：新口令要字符与数字结合，而且要满足长度要求。

![修改密码](./images/linux_exp4_img28.jpeg)

#### 3.3 查看/etc/shadow文件

查看 `/etc/shadow` 文件，特别注意查看该文件的修改时间。

![查看shadow](./images/linux_exp4_img29.jpeg)

#### 3.4 SUID权限测试

修改passwd可执行文件属主权限为rwx，然后再在普通用户模式下使用passwd命令尝试修改密码，看是否能成功，分析原因。

![SUID测试](./images/linux_exp4_img30.jpeg)

### 4. 创建用户（以aaa为例）

#### 4.1 创建新用户aaa

![创建用户aaa](./images/linux_exp4_img31.jpeg)

#### 4.2 查看/etc/passwd

查看 `/etc/passwd` 文件的最后一行内容，并记录。

![查看passwd](./images/linux_exp4_img32.jpeg)

#### 4.3 查看/etc/shadow

查看 `/etc/shadow` 文件的最后一行内容，并记录。

![查看shadow](./images/linux_exp4_img33.jpeg)

#### 4.4 给用户aaa设置密码

![设置密码](./images/linux_exp4_img34.jpeg)

#### 4.5 查看shadow变化

查看 `/etc/shadow` 文件的最后一行内容，记录并说明变化。

![shadow变化](./images/linux_exp4_img35.jpeg)

#### 4.6 使用aaa用户登录测试

使用aaa用户登录系统，测试能否登录成功。

### 5. 创建用户（以user为例）

#### 5.1 创建新用户user

按照步骤4创建新用户user。

![创建user](./images/linux_exp4_img37.jpeg)

#### 5.2 更改user所属附加群组为root

```bash
useradd -g/-G
```

![更改附加群组](./images/linux_exp4_img38.jpeg)

#### 5.3 查看属组情况

查看 `/etc/passwd` 文件，记录aaa用户和user用户的属组情况。

![查看属组](./images/linux_exp4_img39.jpeg)

#### 5.4 更改用户aaa的帐户名为user1

![更改用户名](./images/linux_exp4_img40.jpeg)

#### 5.5 查看passwd变化

查看 `/etc/passwd` 文件的最后一行内容，记录并说明变化。

![查看变化](./images/linux_exp4_img41.jpeg)

#### 5.6 删除用户user1

![删除用户](./images/linux_exp4_img42.jpeg)

### 6. 组的管理（以stuff为例）

#### 6.1 创建新组stuff

![创建组](./images/linux_exp4_img44.jpeg)

#### 6.2 查看/etc/group

查看 `/etc/group` 文件的最后一行内容，并记录。

![查看group](./images/linux_exp4_img45.jpeg)

#### 6.3 创建用户test并设置为stuff组

创建一个新帐户test，并将其起始组和附属组都设为stuff。

![创建test用户](./images/linux_exp4_img46.jpeg)

#### 6.4 查看group变化

查看 `/etc/group` 文件中的最后一行内容，记录并说明变化。

#### 6.5 设置stuff组密码

![设置组密码](./images/linux_exp4_img50.jpeg)

#### 6.6 从stuff组中删除用户test

![删除组中用户](./images/linux_exp4_img51.jpeg)

#### 6.7 查看group变化

查看 `/etc/group` 文件中的最后一行，记录并说明变化。

![查看group变化](./images/linux_exp4_img52.jpeg)

#### 6.8 删除组stuff

![删除组](./images/linux_exp4_img53.jpeg)

## 五、实验结论与体会

### 实验结论

**1. VI编辑器三模式掌握**：熟练掌握了vi编辑器的命令模式、插入模式和底行模式之间的切换，能够熟练使用各种编辑命令进行文本操作。

**2. 文件权限管理理解**：
- 理解了Linux中文件权限的三种类型：读(r)、写(w)、执行(x)
- 掌握了权限的三种身份：所有者(u)、所属组(g)、其他用户(o)
- 学会了使用符号法（`chmod u+w`）和数字法（`chmod 755`）修改文件权限

**3. 用户和组管理技能**：
- 能够创建、修改、删除用户账户
- 掌握了用户组的创建、管理和删除
- 理解了用户与组之间的关系配置

**4. 特殊权限认识**：通过passwd命令的SUID权限设置，理解了特殊权限在系统安全中的重要作用。

### 体会与收获

1. **命令行操作效率**：相比图形界面，命令行操作虽然学习曲线较陡峭，但一旦掌握后效率更高，特别是在远程服务器管理方面。

2. **权限安全重要性**：实验中通过不同权限设置的测试，深刻体会到合理的权限配置对系统安全至关重要，过度授权会带来安全风险。

3. **问题解决能力**：在实验过程中遇到的各种错误（如用户进程占用、组删除限制等），锻炼了分析问题和查阅资料解决问题的能力。

4. **实践加深理解**：通过亲手操作，对Linux用户管理、文件系统权限等抽象概念有了更直观的认识，理论知识与实践操作相结合效果显著。

### 遇到的困难及解决方法

1. **用户重命名限制**：在修改用户名时遇到用户进程占用问题，通过退出相关会话或终止进程解决。
2. **组删除条件**：删除组时发现组中还有用户，通过修改用户主组或删除用户后成功删除组。