# Linux操作系统

## &ensp;目录
- [Linux简介](#linux简介)
- [Linux虚拟机环境搭建](#linux虚拟机环境搭建)
- [Linux主流发行版本和特点](#linux主流发行版本和特点)
- [Linux使用](#linux使用)
- [Linux核心理解](#linux核心理解)

## &ensp;Linux简介
1. 1965年，美国麻省理工学院（MIT）. 通用电器公司（GE）及AT&T的贝尔实验室联合开发Multics工程计划，其目标是开发一种交互式的具有多道程序处理能力的分时操作系统，但因Multics追求的目标过于庞大复杂，项目进度远远落后于计划，最后贝尔实验室宣布退出。
2. 1969年，美国贝尔实验室的肯·汤普森在DEC PDP-7机器上开发出了UNIX系统
3. 1971年，肯·汤普森的同事丹尼斯·里奇发明了C语言。1973年，UNIX系统的绝大部分源代码用C语言重写，这为提高UNIX系统的可移植性打下基础
4. Linux系统诞生与1991年，由芬兰大学生利纳斯（Linux Torvalds）和后来陆续加入的众多爱好者共同开发完成。Linux是开源软件，源代码开发的UNIX  
[Linux内核官网](http://www.kernel.org)
## &ensp;Linux虚拟机环境搭建
1. 安装[VMWare](https://www.vmware.com/cn.html)虚拟机
2. 在VM上安装Linux系统镜像,安装镜像可以通过响应的Linux发行版本官网下载。
3. 设置密码，重启系统，安装所需的软件。


## &ensp;Linux主流发行版本和特点
##### &ensp;&ensp;&ensp;UNIX主流发行版本
|  操作系统 | 公司 |  硬件平台 |
|:-----:|:-----:|:-----:|
|AIX |  IBM  |   PowerPC  |
|HP-UX  |  HP  |   PA-RISC  |
|Solaris |  Sun  |   SPARC  |
|Linux |  CentOS、Ubuntu......  |   IA(Inter、AMD、Cyrix......)  |

##### &ensp;&ensp;&ensp;Linux主流发行版本
| 操作系统 | 特点分类 | 特点 |
|:-----:|:-----:|:-----:|
|[Redhat](https://www.redhat.com/en) |  redhat  |  技术支持较，用户界面友好，硬件的兼容性好，采用基于Deb的ATP包管理系统；技术支持和更新服务是需要付费的, 服务器软件生态系统的规模和活力方面稍弱  |
|[Centos](https://www.centos.org/) |  redhat  |  与包括更新在内的服务完全免费，具备良好的社区技术支持，如果需要更专业级别的支持，那么还可以平滑的从CentOS转至RHEL，采用基于yum的RMP包管理系统；不提供专门技术支持，不包含封闭源代码软件；更新服务较为滞后，当然开发团队的可靠性也无法与上面3类商业版本Linux相比。  |
|[Deepin](https://www.deepin.org/) |  debian  |  基于debian，以易用、美观、完善著称；社区还不完善  |
|[Debian](https://www.debian.org/) |  debian  |  精简而稳定，deb包高度集中，依赖性问题少，拥有最大的支持社区；由于它是完全自由的操作系统，因此没有专业的技术支持。另外它的更新周期很长，软件库里很多软件也显得老旧了  |
|[Ubuntu](https://ubuntu.com/) |  debian  |  社区活跃，软件数量众多；但是Unity桌面的性能很低，并且不是很稳定，常常卡死。内置的软件大多数没用，你通常要花上一个星期才能把ubuntu打造成适合自己的系统。  |
|[CoreOS](http://coreos.com/) |  other  |  对容器云的支持，能够方便的部署Kubernetes集群，直接无缝支持docker容器云的部署；但是etcd因为国情限制不能不能很好的部署，需要自己配置DNS才能完整部署  |
[Linux的发行版本及不同版本的联系和区别](https://www.jianshu.com/p/c88a62ac8ca3)

## &ensp;Linux使用
- [Vim](#vim)
- [程序安装](#程序安装)
- [用户和用户组管理](#用户和用户组管理)
- [权限管理](#权限管理)
- [文件系统](#文件系统)
- [Shell](#shell)
- [服务管理](#服务管理)
- [系统管理](#系统管理)
- [日志管理](#日志管理)
- [启动管理](#启动管理)
- [备份与恢复](#备份与恢复)
- [服务器资源命令](#服务器资源命令)
- [编译安装程序](#编译安装程序)
- [the-art-of-command-line](https://github.com/jlevy/the-art-of-command-line/blob/master/README-zh.md)
- [命令大全](https://man.linuxde.net/)

#### &ensp;&ensp;&ensp;Vim
##### &ensp;&ensp;&ensp;&ensp;简介
&ensp;&ensp;&ensp;&ensp;Vim是从 vi 发展出来的一个文本编辑器。代码补全、编译及错误跳转等方便编程的功能特别丰富，在程序员中被广泛使用，和Emacs并列成为类Unix系统用户最喜欢的文本编辑器。  
&ensp;&ensp;&ensp;&ensp;Vim的设计理念是命令的组合。用户学习了各种各样的文本间移动/跳转的命令和其他的普通模式的编辑命令，并且能够灵活组合使用的话，能够比那些没有模式的编辑器更加高效的进行文本编辑。同时VIM与很多快捷键设置和正则表达式类似,可以辅助记忆。并且vim针对程序员做了优化。  
vim键盘图：  
![vim键盘图片](./img/vi-vim-cheat-sheet-sch.png)

##### &ensp;&ensp;&ensp;&ensp;vim模式
&ensp;&ensp;&ensp;&ensp;基本上 vi/vim 共分为三种模式，分别是命令模式（Command mode），输入模式（Insert mode）和底线命令模式（Last line mode）。 这三种模式的作用分别是：
###### &ensp;&ensp;&ensp;&ensp;&ensp;命令模式：用户刚刚启动 vi/vim，便进入了命令模式。
&ensp;&ensp;&ensp;&ensp;此状态下敲击键盘动作会被Vim识别为命令，而非输入字符。比如我们此时按下i，并不会输入一个字符，i被当作了一个命令。
以下是常用的几个命令：
- i 切换到输入模式，以输入字符。
- x 删除当前光标所在处的字符。
- : 切换到底线命令模式，以在最底一行输入命令。

&ensp;&ensp;&ensp;&ensp;若想要编辑文本：启动Vim，进入了命令模式，按下i，切换到输入模式。
命令模式只有一些最基本的命令，因此仍要依靠底线命令模式输入更多命令。

###### &ensp;&ensp;&ensp;&ensp;&ensp;输入模式:在命令模式下按下i就进入了输入模式。
&ensp;&ensp;&ensp;&ensp;在输入模式中，可以使用以下按键：
- 字符按键以及Shift组合，输入字符
- ENTER，回车键，换行
- BACK SPACE，退格键，删除光标前一个字符
- DEL，删除键，删除光标后一个字符
- 方向键，在文本中移动光标
- HOME/END，移动光标到行首/行尾
- Page Up/Page Down，上/下翻页
- Insert，切换光标为输入/替换模式，光标将变成竖线/下划线
- ESC，退出输入模式，切换到命令模式


###### &ensp;&ensp;&ensp;&ensp;&ensp;底线命令模式在命令模式下按下:（英文冒号）就进入了底线命令模式。
&ensp;&ensp;&ensp;&ensp;底线命令模式可以输入单个或多个字符的命令，可用的命令非常多。
在底线命令模式中，基本的命令有（已经省略了冒号）：
- q 退出程序
- w 保存文件

&ensp;&ensp;&ensp;&ensp;按ESC键可随时退出底线命令模式。

&ensp;&ensp;简单的说，我们可以将这三个模式想成底下的图标来表示：
![vim模式](./img/vim-vi-workmodel.png)

##### &ensp;&ensp;&ensp;&ensp;优点
- 命令丰富，几乎能满足文本编辑的所有操作
- 对硬件要求不高
- 开源，插件丰富
- 速度快（运行，使用）
##### &ensp;&ensp;&ensp;&ensp;缺点
- 学习曲线陡峭，对初学者不友好
- vim内置脚本语言有局限性，开发效率比不上专业的程序开发软件

##### &ensp;&ensp;&ensp;&ensp;常用命令：
- h 或 向左箭头键(←)：光标向左移动一个字符
- j 或 向下箭头键(↓)：光标向下移动一个字符
- k 或 向上箭头键(↑)：光标向上移动一个字符
- l 或 向右箭头键(→)：光标向右移动一个字符
- [Ctrl] + [f]：屏幕『向下』移动一页，相当于 [Page Down]按键 (常用)
- [Ctrl] + [b]：屏幕『向上』移动一页，相当于 [Page Up] 按键 (常用)
- [Ctrl] + [d]：屏幕『向下』移动半页
- [Ctrl] + [u]：屏幕『向上』移动半页
- G：移动到这个档案的最后一行(常用)
- nG：n 为数字。移动到这个档案的第 n 行。例如 20G 则会移动到这个档案的第 20 行(可配合 :set nu)
- gg：移动到这个档案的第一行，相当于 1G 啊！ (常用)
- n<Enter>：n 为数字。光标向下移动 n 行(常用)
- /word：向光标之下寻找一个名称为 word 的字符串。例如要在档案内搜寻 vbird 这个字符串，就输入 /vbird 即可！ (常用)
- ?word：向光标之上寻找一个字符串名称为 word 的字符串。
- n：这个 n 是英文按键。代表重复前一个搜寻的动作。举例来说， 如果刚刚我们执行 /vbird 去向下搜寻 vbird 这个字符串，则按下 n 后，会向下继续搜寻下一个名称为 vbird 的字符串。如果是执行 ?vbird 的话，那么按下 n 则会向上继续搜寻名称为 vbird 的字符串！
- N：这个 N 是英文按键。与 n 刚好相反，为『反向』进行前一个搜寻动作。 例如 /vbird 后，按下 N 则表示『向上』搜寻 vbird 。
- dd：删除游标所在的那一整行(常用)
- ndd：n 为数字。删除光标所在的向下 n 行，例如 20dd 则是删除 20 行 (常用)
- d1G：删除光标所在到第一行的所有数据
- dG：删除光标所在到最后一行的所有数据
- yy：复制游标所在的那一行(常用)
- nyy：n 为数字。复制光标所在的向下 n 行，例如 20yy 则是复制 20 行(常用)
- [Esc]：退出编辑模式，回到一般模式中(常用)
- :w：将编辑的数据写入硬盘档案中(常用)
- :w!：若文件属性为『只读』时，强制写入该档案。不过，到底能不能写入， 还是跟你对该档案的档案权限有关啊！
- :q：离开 vi (常用)
- :q!：若曾修改过档案，又不想储存，使用 ! 为强制离开不储存档案。  
注意一下啊，那个惊叹号 (!) 在 vi 当中，常常具有『强制』的意思～
- :wq：储存后离开，若为 :wq! 则为强制储存后离开 (常用)

#### &ensp;&ensp;&ensp;程序安装
- rpm包安装方式步骤
  1. 找到相应的软件包，比如soft.version.rpm，下载到本机某个目录；
  2. 打开一个终端，su -成root用户；
  3. cd soft.version.rpm所在的目录；
  4. 输入rpm -ivh soft.version.rpm
- deb包安装方式步骤:
  1. 找到相应的软件包，比如soft.version.deb，下载到本机某个目录；
  2. 打开一个终端，su -成root用户；
  3. cd soft.version.deb所在的目录；
  4. 输入dpkg -i soft.version.deb
- tar.gz源代码包安装方式：
  1. 找到相应的软件包，比如soft.tar.gz，下载到本机某个目录；
  2. 打开一个终端，su -成root用户；
  3. cd soft.tar.gz所在的目录；
  4. tar -xzvf soft.tar.gz //一般会生成一个soft目录
  5. cd soft
  6. ./configure
  7. make
  8. make install
- tar.bz2源代码包安装方式：
  1. 找到相应的软件包，比如soft.tar.bz2，下载到本机某个目录；
  2. 打开一个终端，su -成root用户；
  3. cd soft.tar.bz2所在的目录；
  4. tar -xjvf soft.tar.bz2 //一般会生成一个soft目录
  5. cd soft
  6. ./configure
  7. make
  8. make install
- apt方式安装：（安装deb包）
  1. 打开一个终端，su -成root用户；
  2. apt-cache search soft 注：soft是你要找的软件的名称或相关信息
  3. 如果2中找到了软件soft.version，则用apt-get install soft.version命令安装软件
- yum方式安装(安装rpm包)：rpm 是linux的一种软件包名称，以.rmp结尾，安装的时候语法为：rpm -ivh。
rpm包的安装有个很大的缺点就是文件的关联性太大，有时装一个软件要安装很多其他的软件包，很麻烦。
所以为此RedHat小红帽开发了yum安装方法，他可以彻底解决这个关联性的问题，很方便，只要配置两个文件即可安装，安装方法是：yum -y install 。
yum并不是一中包，而是安装包的软件
简单的说： rpm 只能安装已经下载到本地机器上的rpm 包. yum能在线下载并安装rpm包,能更新系统,
且还能自动处理包与包之间的依赖问题,这个是rpm 工具所不具备的。
- bin文件安装：
  1. 打开一个终端，su -成root用户；
  2. chmod +x soft.bin
  3. ./soft.bin //运行这个命令就可以安装软件了
- 不需要安装的软件：有了些软件，比如lumaqq，是不需要安装的，自带jre解压缩后可直接运行。假设下载的是lumaqq.tar.gz，使用方法如下：
  1. 打开一个终端，su -成root用户；
  2. tar -xzvf lumaqq.tar.gz //这一步会生成一个叫LumaQQ的目录
  3. cd LumaQQ
  4. chmod +x lumaqq //设置lumaqq这个程序文件为可运行
  5. 此时就可以运行lumaqq了，用命令./lumaqq即可，但每次运行要输入全路径或切换到刚才生成的LumaQQ目录里
  6. 为了保证不设置路径就可以用，你可以在/bin目录下建立一个lumaqq的链接，用命令ln -s lumaqq /bin/ 即可，以后任何时候打开一个终端输入lumaqq就可以启动QQ聊天软件了
  7.  如果你要想lumaqq有个菜单项，使用菜单编辑工具，比如Alacarte Menu
Editor，找到上面生成的LumaQQ目录里的lumaqq设置一个菜单项就可以了，当然你
也可以直接到 /usr/share/applications目录，按照里面其它*.desktop文件的格
式生成一个自己的desktop文件即可。

#### &ensp;&ensp;&ensp;用户和用户组管理
##### &ensp;&ensp;&ensp;&ensp;用户管理简介：
- 所有越是对服务器安全性要求高的服务器，越需要建立合理的用户权限等级制度和服务器操作规范
- 在Linux中主要是通过用户配置文件来查看和修改用户信息
##### &ensp;&ensp;&ensp;&ensp;/etc/passwd
- 第1字段：用户名称
- 第2字段：密码标志/标识
- 第3字段：UID（用户ID）
  - 0：超级用户
  - 1-499：系统用户（伪用户：不能登入，不能删除）
  - 500-2^32：普通用户
- 第4字段：GID（用户[初始组](#初始组和附加组)ID）
- 第5字段：用户说明
- 第6字段：家目录
  - 超级用户：/root/
  - 普通用户：/home/用户名/
- 第7字段：登陆之后的[Shell](#什么是shell)

##### &ensp;&ensp;&ensp;&ensp;/etc/shadow
- 第1字段：用户名
- 第2字段：加密密码
  - 加密算法为SHA512散列加密算法
  - 如果密码位是"!"或"*"代表没有密码，不能登入
- 第3字段：密码最后一次修改日期
- 第4字段：两次密码修改的间隔时间（和第3字段相比）
- 第5字段：密码有效期（和第3字段相比）
- 第6字段：密码修改到期钱的警告天数（和第5字段相比）
- 第7字段：密码过期后的宽限天数（和第5字段比较）
  - 0：代表密码过期后立即消失
  - -1：代表密码永远不会失效
- 第8字段：账户失效时间
- 第9字段：保留

##### &ensp;&ensp;&ensp;&ensp;常用命令
- 添加用户信息：[useradd](#useradd)
- 修改用户密码：[passwd](#passwd)
- 修改用户信息：[usermod](#usermod)
- 修改用户密码状态：[chage](#chage)
- 删除用户：userdel [-r] 用户名
- 切换用户：su [-/-c] 用户名字(只使用"-"代表连带用户的环境变量一起切换)
- 添加用户组：groupadd [-g GID] 组名
- 修改组：groupmod [-g GID/-n 新组名] 组名
- 删除组: groupdel 组名



###### &ensp;&ensp;&ensp;&ensp;&ensp;初始组和附加组
- 初始组：用户一创建就立刻拥有这个用户组的相关权限，每个用户的初始组只能有一个，一般就是和这个用户的用户名相同的组名作为这个用户的初始组
- 附加组：指用户可以加入多个其他的用户组，并拥有这个组的权限，附加组可以有多个

###### &ensp;&ensp;&ensp;&ensp;&ensp;什么是Shell
&ensp;&ensp;&ensp;&ensp;Shell就是Linux的命令解释器。在/etc/password当中，除了标准的Shell是/bin/bash之外，还可以写如/sbin/nologin来禁止普通用户登入

###### &ensp;&ensp;&ensp;&ensp;&ensp;useradd
```
useradd [选项] 用户名
-g：设置默认组
-d：设置默认家目录
-e：设置账户有效日期（yyyy-MM-dd）
例子：
# useradd kk //添加用户kk
# useradd -g root kk //添加用户kk，并指定用户所在的组为root用户组
# useradd -r kk //创建一个系统用户kk
# useradd-d /home/myf kk //新添加用户kk，其home目录为/home/myf
//当用户名kk登录主机时，系统进入的默认目录为/home/myf
```

###### &ensp;&ensp;&ensp;&ensp;&ensp;passwd
```
passwd [选项] 用户名
-d：删除密码
-f：强迫用户下次登录时必须修改口令
-w：口令要到期提前警告的天数
-k：更新只能发送在过期之后
-l：停止账号使用
-S：显示密码信息
-u：启用已被停止的账户
-x：指定口令最长存活期
-g：修改群组密码
指定口令最短存活期
-i：口令过期后多少天停用账户
例子：
# passwd kk  //设置runoob用户的密码
Enter new UNIX password:  //输入新密码，输入的密码无回显
Retype new UNIX password:  //确认密码
passwd: password updated successfully

# passwd -S kk  // 显示账号密码信息
runoob P 05/13/2010 0 99999 7 -1

# passwd -d ll // 删除用户密码
passwd: password expiry information changed.
```

###### &ensp;&ensp;&ensp;&ensp;&ensp;usermod
```
usermod [选项] 用户名
-u UID：修改用户的UID号
-c 用户说明：修改用户的说明信息
-G 组名：修改用户的附加组
-L：临时锁定用户（Lock）
-U：解锁用户锁定（Unlock）
例子：
# usermod -d /home/hnlinux kk  // 更改登录目录
# usermod -u 777 kk  // 改变用户的uid
```

###### &ensp;&ensp;&ensp;&ensp;&ensp;chage
```
chage [选项] 用户名
-l：列出用户的详细密码状态
-d 日期：修改密码最后一次更改日期（shadow 3字段）
-m 天数：两次密码修改间隔（4字段）
-M 天数：密码有效期（5字段）
-W 天数：密码过期前警告天数（6字段）
-I 天数： 密码过后宽限天数（7字段）
-E 日期：账户失效时间（8字段）
例子：
# chage -l kk   // 查看kk用户以及密码有效期
# chage -M 60 -m 7 -W 7 kk  // 设置kk用户60天后密码过期，至少7天后才能修改密码，密码过期前7天开始收到告警信息。
# chage -d 0 kk // 强制用户第一次登陆时修改密码
```

#### &ensp;&ensp;&ensp;权限管理

#### &ensp;&ensp;&ensp;文件系统

#### &ensp;&ensp;&ensp;Shell

#### &ensp;&ensp;&ensp;服务管理

#### &ensp;&ensp;&ensp;系统管理

#### &ensp;&ensp;&ensp;日志管理

#### &ensp;&ensp;&ensp;启动管理

#### &ensp;&ensp;&ensp;备份与恢复

#### &ensp;&ensp;&ensp;服务器资源命令

#### &ensp;&ensp;&ensp;编译安装程序


## &ensp;&ensp;Linux核心理解
- [同步机制]
- 1. [自旋锁]
  2. [原子操作]
  3. [信号量]
- [文件系统]
- [内存管理]
- [网络系统]
- [线程/进程/进程间通信]
`