---
title: Terminal命令小结
date: 2017-11-22 13:56:34
tags: 
	- Terminal Command Line
---



作为迁移博客的第一篇文章，不知道要写些什么内容，最近几个月忙项目忙装修，几乎从未想起博客这件事，近来有种卸磨杀驴的失落感，自知能力决定待遇的道理，所以还是要不断加强自身能力，其最好的方式莫过于分享，博客就是其中优选。

上次迁移博客是从博客园到简书，这次是从简书到github，其实决定因素还是美观度，hexo给我更极客的感觉，虽然自己并不极客。整个搭建、配置主题的过程大概用了两三天时间，这个过程中接触到了很多新知识，近期的博客计划也就从其中总结出来。

<!-- more -->

## 关于Command Line

从入行至今，使用Terminal的次数并不多，现用现查是常态，平时开发中`Git`也是使用图形界面工具，所以对Terminal非常陌生，直到最近自己搭了一个VPN才真正意识到命令行的便捷，而搭建博客的过程中命令行更是不可缺少的工具。

>  你可以整天驾驶汽车而不用知道如何修理它们，但是如果你希望当一个维护员，你就需要知道事情是如何运作的。同样的事情也发生在了 Mac OS X 上；你可以一直使用 Mac 而不用知道如何修理它，但是如果你想对系统做一些维护或解决一些问题，那么你需要知道如何使用 command-line。 
>
>  —— 网摘自**Renfei Song**'s Blog

Mac上很多功能不提供图形界面，只能通过命令行来实现，例如说`ssh`，Windows下需要使用`XShell`之类的软件来操作，而*nix系统直接使用终端命令就可以直接链接：

```
$ ssh username@hostname
```

通过Terminal可以访问所有目录，包括`Finder`隐藏的目录，而`Finder`的隐藏文件功能也只能通过`Terminal`开关，当然在系统`10.12`中可以使用`Shift`+`Command`+`.`来快速开启与关闭：

```
$ defaults write com.apple.finder AppleShowAllFiles -boolean true ; killall Finder 
$ defaults write com.apple.finder AppleShowAllFiles -boolean false ; killall Finder 
```

Terminal可以做界面图形App能做的绝大多数操作，因为界面图形App背后使用的就是Command Line，例如在你安装`Xcode`之后，你就可以通过命令行进行编译和打包操作：

```
$ xcodebuild -workspace YourWorkspaceName -scheme YourSchemeName -configuration Debug archive -archivePath YourArchivePath
$ xcodebuild -exportArchive -archivePath YourArchivePath -exportPath YourExportPath -exportOptionsPlist ./ConfigPlistName.plist
```

虽然学习Command Line需要一个记忆和熟练的过程，但其强大且广泛的功能带给我们的便利快捷要远大于其学习成本，所以说使用Terminal，学会Command Line是一个程序员必备的素质之一。

## 关于man命令

Terminal里面成百上千条命令，每一条命令还有其具体的参数方法，谁也不能全都记住，那么怎么办？推荐学习Command Line的人们最先掌握的命令`man`。通过`man CommandName`来获取你想知道的命令的使用指南：

```
$ man xcodebuild

NAME
     xcodebuild -- build Xcode projects and workspaces

SYNOPSIS
     xcodebuild [-project name.xcodeproj]
                [[-target targetname] ... | -alltargets]
                [-configuration configurationname]
                [-sdk [sdkfullpath | sdkname]] [action ...]
                [buildsetting=value ...] [-userdefault=value ...]
...
```

包括命令的名称、参数意义、使用描述等等，足够你理解使用这个命令了，在浏览期间可以通过`Space`翻页、`↑`和`↓`来滚动、输入`/ SearchKeyword`来搜索，使用`Q`退出页面。如果你不清楚这个命令的名字，可以使用`man -K Keyword`来搜索，Terminal会询问你搜索的结果是否是你想要的。

## 关于路径与文件命令

虽然平时使用Finder管理文件已经很方便了，但是某些时候使用Terminal来进行路径和文件的操作也会非常便捷。

### 路径命令

路径分为绝对路径和相对路径。

绝对路径总是以`/`开头，例如`/Users/xuhaoran/blog`。

相对路径是根据你的Terminal当前的路径位置有关，Terminal默认路径为`Home Folder`，就是`⌘`+`⇧`+`H`的Folder，相对路径可以使用`../`来表示上一级路径，使用`./`表示本级路径，通常可以省略，使用`~/`则表示你的`Home Folder`。

绝对路径的第一个`/`代表根目录，而`/Users/UserName`即是`HomeFolder`。

如何获取绝对路径？有两个方法：1、打开Terminal后，直接拖拽Folder或File进来；2、使用`pwd`命令：

```
$ pwd
/Users/xuhaoran/blog
```

跳转到指定路径使用`cd`命令：

```
$ cd DirectoryName
```

`cd -`命令可以后退目录：

```
$ cd ~/Desktop/
$ cd ~/Downloads/
$ cd -
/Users/xuhaoran/Desktop
```

查看当前路径下的所有Folder和File使用`ls`命令，值得一提的是使用`Tab`可以进行匹配输入，注意Terminal大小写敏感。通常情况`ls`命令是不显示隐藏文件的，可以使用`ls -A`来显示所有隐藏文件，即`.Name`形式的文件：

```
$ ls -A
.CFUserTextEncoding	.gitignore_global	.vim
.DS_Store		.hgignore_global	.viminfo
```

`pwd`、`cd`、`ls`三条命令搭配使用可以完成绝大多数的路径查询跳转工作，至于这三个命令的其他参数可以使用`man`来自行查询。

而创建与删除路径使用`mkdir`与`rmdir`两个命令：

```
$ mkdir DirectoryName
$ rmdir DirectoryName
```

使用`mkdir`命令时，如果想创建多层目录的话可以添加参数`p`：

```
$ mkdir -p dir1/dir2/dir3
```

### 文件命令

通过Command Line检视文件，可以使用命令`cat`将文件内容输出到Terminal中：

```
$ cat Desktop/
cat: Desktop/: Is a directory
$ cat Hello.text
Hello World!
```

面对较长的文本直接输出到Terminal中显然并不合理，那么可以使用`less`命令，在`less`命令进入文件后按`V`进入vim编辑器。

如果想查看文件类型信息，特别是在文件扩展名丢失的情况下，可以使用`file`命令：

```
$ file unnamed
unnamed: ASCII text
```

可以使用`touch`命令创建文件和指定文件扩展名：

```
$ touch FileName.ExpandedName
```

创建之后可以直接进入编辑器进行编辑，可以使用`vim`命令进去vim编辑器或者`nano`命令进入nano编辑器：

```
$ vim FileName.ExpandedName
```

可以使用`open`命令打开一个文件或者使用Finder打开一个Folder，使用`open .`命令来打开当前的Folder：

```
$ open .
```

对于基本的复制剪切操作可以分别使用`cp` 、`mv`命令：

```
// 将Test/ 复制/移动到 Workspace/
$ cp Test/ Workspace/
$ mv Test/ Workspace/
```

## 关于权限命令

### 权限

查看当前目录下的文件权限可以使用`ls -alh`命令：

```
$ ls -alh
total 8152
/*
    权限        拥有者	 群组
*/
-rw-r--r--@  1 xuhaoran  staff   104K 11 22 14:07 430DDC5092226B6C4F062BAEF2242B78.jpg
drwxr-xr-x  10 xuhaoran  staff   320B 11  2 14:05 AlamofireDemo
-rw-r--r--@  1 xuhaoran  staff   956B 10 13  2016 CertificateSigningRequest.certSigningRequest
...
```

其中权限部分详细的分为`d rwx rwx rwx`四部分。

第一部分中`d`表示为Directory，而`-`则表示File。

后面三部分分别表示“对所有者”、“对同组人”、“对其他人”的权限，`r`读取权限、`w`写入权限、`x`执行权限，`-`表示无权限。

以其中`AlamofireDemo`为例，表示为目录、所有者可读可写可执行、同组人可读可执行、其他人可读可执行。

而三部分的权限可使用数字表示，其中`r`权限为2²，`w`权限为2¹，`x`权限为2º，例如：`AlamofireDemo`目录的全校可以表示为`755`，而`cerSigningRequest`文件的权限为`644`。

权限是可以变更的，使用`chmod`命令：

```
$ chmod -R 755 AlamofireDemo
```

这是通常情况下的标准命令，为文件添加`rwxr-xr-x`权限，`-R`参数表示改变目录结构下所有子目录文件的权限而并非目录本身。`chmod 777 FileName`则是非常危险的命令，赋予了其他人与root相同的权限。

`chmod`还有一种改变权限的形式，使用运算符`+`(增加)、`-`(去除)、`=`(覆盖设定)配合用户类型`u`(所有者)、`g`(同组人)、`o`(其他人)、`a`(全部)：

```
$ chmod -R g+x AlamofireDemo/
```

上面这条命令是为`AlamofireDemo`这个目录的同组人添加执行权限。

同样，拥有者和群组也是可以改变的，使用`chown`命令：

```
$ chown UserName:GroupName FileName
```

### root权限

`sudo`命令的作用在于会用root账户权限来执行后面的命令，通常在执行一条命令提示`Permission denied`时使用。使用`sudo !!`再次执行：

```
$ cat secret.doc
Permission denied
$ sudo !!
Password: *
```

如果执行多条需要root权限的命令，可以使用`sudo -s`获取root权限，注意`#`代替了`$`表示使用root权限操作，操作结束可以使用`Control`+`D`或者输入`exit`命令来退出环境：

```
xuhaoran$ sudo -s
Password:
bash-3.2# exit
```

## 关于网络通信的命令

### ping命令

在网络通信之前，最常用的操作就是`ping`命令，它会给网络主机发送一个回应请求，进而验证可否进行通信：

```
$ ping panghu.cf
PING panghu.cf (128.14.32.165): 56 data bytes
64 bytes from 128.14.32.165: icmp_seq=0 ttl=50 time=168.334 ms
64 bytes from 128.14.32.165: icmp_seq=1 ttl=50 time=158.529 ms
```

可使用`Control`+`C`停止发送请求。

### ssh命令

`ssh`是一种远程登录和其他安全服务的协议，远程登录相比较于`telnet`和`rlogin`两种协议，有其特有的优势，例如加密传输、数据压缩等。

Mac的Terminal自带SSH，使用`ssh`就可以登录远程服务器：

```
$ ssh root@panghu.cf
root@panghu.cf's password: *
```

之后就可以在远程服务器进行一些常规操作或者一些逗逼的命令：

```
$ wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubi/doubi/master/ssr.sh && chmod +x ssr.sh && bash ssr.sh
```

网络通信命令还有很多，这里限于能力就不献丑了。

## 关于其他系统命令

`bash`命令是用来执行一段脚本命令的命令，通常可以用来执行本地写好的脚本，例如：

```
$ bash ssr.sh
```

`who`命令可以列出当前登录的所有用户，`who am i`显而易见是回答“我是谁？”的问题：

```
$ who
xuhaoran console  Nov 20 17:06 
xuhaoran ttys001  Nov 22 13:52 
xuhaoran ttys002  Nov 23 14:52 
$ who am i
xuhaoran ttys002  Nov 23 14:52 
```

`console`是电脑的底层控制台，伴随着电脑开机而启动；`ttys`（Teletypewriter的缩写）则是Terminal的类型名称，随着Terminal数量增加而增加计数。使用`Command`+`N`来新建Terminal。

至于`Console`、`Terminal`、`tty`和`Shell`的关系这里就不叙述了。

使用`ps`命令来查看进程状态，可以确定哪些进程僵死或过多占用资源等。通常使用`ps aux`来查看具体进程：

```
$ ps aux
USER               PID  %CPU %MEM      VSZ    RSS   TT  STAT STARTED      TIME COMMAND
xuhaoran           355   3.0  3.0  7056284 249392   ??  S    一05下午  57:40.33 /Applications/WeChat.app/Contents/MacOS/WeChat -psn_0_57358
root                96   0.3  0.1  4346796   8364   ??  Ss   一05下午   3:00.86 /usr/libexec/opendirectoryd
...
```

`PID`表示进程的id，`%CPU`、`%MEM`分别表示进程占用CPU和内存的比重，`VSZ`表示使用掉的虚拟内存(KB)，`RSS`表示占用的固定内存(KB)，`TT`表示在哪个Terminal上执行，`STAT`表示该进程目前的状态。

关于`STAT`的状态码：

| 状态码  |  意义   |
| :--- | :---: |
| `R`  | 正在运行  |
| `S`  |  休眠   |
| `T`  |  停止   |
| `Z`  |  僵死   |
| `<`  | 高优先级  |
| `N`  | 低优先级  |
| `L`  |  锁定   |
| `s`  | 存在子进程 |
| `+`  | 后台进程组 |

查询到进程之后可以使用`kill`命令用来终止进程：

```
$ kill -SignalNumber PID
```

至于`SignalNumber`可以通过命令`kill l`获取：

```
$ kill -l
 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL
 5) SIGTRAP      6) SIGABRT      7) SIGEMT       8) SIGFPE
 9) SIGKILL     10) SIGBUS      11) SIGSEGV     12) SIGSYS
13) SIGPIPE     14) SIGALRM     15) SIGTERM     16) SIGURG
17) SIGSTOP     18) SIGTSTP     19) SIGCONT     20) SIGCHLD
21) SIGTTIN     22) SIGTTOU     23) SIGIO       24) SIGXCPU
25) SIGXFSZ     26) SIGVTALRM   27) SIGPROF     28) SIGWINCH
29) SIGINFO     30) SIGUSR1     31) SIGUSR2 
```

其中常用的为9(SIGKILL)，对付大多数程序可以`kill -9 PID`直接干掉。

其他常用的系统命令有查看时间的命令`date`：

```
$ date
2017年11月23日 星期四 16时47分40秒 CST
```

查看日历的命令`cal`：

```
$ cal
      十一月 2017        
日 一 二 三 四 五 六  
          1  2  3  4  
 5  6  7  8  9 10 11  
12 13 14 15 16 17 18  
19 20 21 22 23 24 25  
26 27 28 29 30   
```

`cal`命令后面可接年份，例如`cal 2017`，也可以接月份+年份，例如`cal 8 2018`表示展现2018年8月的日历。



## 一些小技巧

### alias与unalias命令

对于常用的命令可以使用`alias`为其添加别名，例如：

```
$ alias del="rm -rf"
```

删除别名可以使用`unalias`:

```
$ unalias del
```

### 退出及清屏命令

使用`control`+`C` 可以终止持续进行的命令，例如`ping`命令。

使用`control`+`D`或者`exit`命令可以结束当前状态，例如结束`ssh`的远程链接。

使用`control`+`L`或者`clear`命令可以清屏，与`reset`命令不同的是，`clear`可以滚动查看之前的内容，而`reset`则是完全清除内容。

### 光标操作

|       操作符        |        作用 |
| :--------------: | --------: |
|  `control`+`A`   |   移动光标至行首 |
|  `control`+`E`   |   移动光标至行尾 |
|  `control`+`U`   |    删除整行命令 |
|  `control`+`K`   | 删除光标后面的内容 |
| `option`+`←`、`→` |   按词组移动光标 |

### 快速操作

| 操作符  |                                       作用 |
| :--: | ---------------------------------------: |
| `!!` |                                  执行上一条命令 |
| `!`  | 重复命令，例如`$ ! -3`执行前三条命令、`$ !pod`重复最近一次的`pod`命令等 |
| `|`  | 将左侧命令结果扔给右侧命令，例如`$ ps aux | pbcopy`将进程信息保存的剪贴板 |
| `>`  | 将左侧命令结果保存进右侧文件，例如`$ pbpaste > out.txt`将剪贴板内容保存到文件 |
| `&&` | 等待前一条命令执行成功之后执行后面命令，例如`$ chmod +x ssr.sh && bash ssr.sh` |
| `&`  | 多条命令同时执行，例如`$ chmod +x ssr.sh & chmod +x other.sh` |
| `;`  | 不管前面命令成功与否都继续执行后面命令，例如`$ chmod +x ssr.sh ; cp ssr.sh ~/Desktop/` |

使用`history`命令可以查询执行命令的历史记录`history PageCnt`：

```
$ history 10
  543  kill l
  544  kill -l
  545  date
  546  cal
  547  ssh root@panghu.cf
  548  ls
  549  ping panghu.cf
  550  ftp panghu.cf
  551  yum
  552  ls
```



## 小结

连续写了两天时间，期间不断的去查询和完善内容，感觉这个过程又收获了很多知识。

Terminal的强大只有真正融入进去才能感受得到，以上这些都只是本人在工作中遇到的最基本的内容，说成冰山一角毫不为过，所以日后遇到新的内容还会继续进行补充更新。

最后，很多命令下的参数都有不同的用法，当遇到问题了请熟练地使用`man`命令，希望对您有所帮助。