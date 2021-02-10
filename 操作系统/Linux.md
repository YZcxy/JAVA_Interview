# Linux 知识点

## 常用命令

Q：find 命令？  
A：主要用户查找文件。
- `find /usr -type f -size +10240k` 查找 usr 目录下大小超过10MB的文件
- `find /var \! -atime -90` 查找 var 目录下90天之内未被访问过的文件
- `find / -name core` 在整个目录下查找 core 文件

***
Q：ls 命令？  
A：列出当前目录下的文件。
- `ls -lh` 以最易读的方式显示文件的大小
- `ls -ltr` 以最后修改时间升序列出文件

***
Q：pwd 命令？  
A：输出当前的工作目录。

***
Q：cd 命令？  
A：切换工作目录，可以在最近工作的两个目录间切换。

***
Q：mkdir 命令？  
A：创建一个文件或者文件目录。
- `mkdir ~/temp` 在 home 目录下创建一个 temp 的目录
- `mkdir -p dir1/dir2/dir3/dir4/` 创建一个路径上所有不存在的目录

***
Q：rm 命令？  
A：删除文件或者目录。
- `rm -i filename.txt` 删除前会确认
- `rm -r example` 递归删除文件夹下所有文件

***
Q：mv 命令？  
A：移动文件或者目录。
- `mv -i file1 file2` 等于将文件重命名

***
Q：cp 命令?  
A：拷贝文件或者目录。
- `cp -p file1 file2` 拷贝文件，并且保持文件的权限，属组，和时间戳

***
Q：mount 命令？  
A：挂载文件系统，可以挂载磁盘，驱动等。

***
Q：cat 命令？  
A：查看文件里面的内容，输出到控制台。
- `cat file1 file2` 同时打印两个文件，先打印第一个，然后第二个
- `cat -n /etc/logrotate.conf` 打印的同时为每一行加上行号

***
Q：tail 命令？  
A：从文本的最后开始打印，默认打印 10 行。
- `tail -n 100 filename.txt` 指定打印文件的最后 100 行
- `tail -f log-file` 打印后等待，有新行打印到末尾就会继续输出

***
Q：less 命令？  
A：不加载全部文件，可以一页一页的翻页加载。
- `less huge-log-file.log` 特别适合加载大型文件

***
Q：grep 命令？  
A：查找命令，可以从文件中查，也可以从管道中查找。
- `grep -i "the" demo_file` 在文件中查找字符串，不区分大小写
- `grep -r "ramesh" *` 在一个文件夹中递归查找包含指定字符串的文件

***
Q：sed 命令？  
A：sed是一种流编辑器，它是文本处理中非常中的工具，能够完美的配合正则表达式使用。

***
Q：awk 命令？  
A：awk是一种编程语言，用于在linux/unix下对文本和数据进行处理。

***
Q：vim 命令？  
A：查看、编辑及修改文件的内容。
- `vim +/search-term filename.txt` 打开文件并且跳到第一个匹配的行
- `vim -R /etc/passwd` 以只读模式打开

***
Q：diff 命令？  
A：用于比较文件之间的区别。

***
Q：sort 命令？  
A：可以对文件的内容进行排序。

***
Q：xargs 命令？  
A：xargs命令是给其他命令传递参数的一个过滤器。

***
Q：export 命令？  
A：将shell变量输出为环境变量，或者将shell函数输出为环境变量。
- `export ORACLE_HOME=/u01/app/oracle/product/10.2.0` 设置环境变量

***
Q：kill 命令？  
A：用于终止一个进程。一般会先用 `ps -ef` 查找到进程号。
- `kill -9 7243` 强制终止7243进程

***
Q：passwd 命令？  
A：用于修改密码，root 用户可以直接修改其他用户的密码。

***
Q：su 命令？  
A：用于切换用户账号。
- `su - username` 切换到 username 用户

***
Q：yum 命令？  
A：yum命令是在Fedora和RedHat以及SUSE中基于rpm的软件包管理器。

***
Q：rpm 命令？  
A：rpm命令是RPM软件包的管理工具。

***
Q：shutdown 命令？  
A：关机命令。
- `shutdown -h +10` 十分钟后关机
- `shutdown -r now` 重启

***
Q：service 命令？  
A：service 命令用于运行 System V init 脚本，这些脚本一般位于 /etc/init.d 文件下。

***
Q：chmod 命令？  
A：chmod 用于改变文件和目录的权限。

***
Q：chown 命令？  
A：用于改变文件的属主和属组。

***
Q：crontab 命令？  
A：crontab命令被用来提交和管理用户的需要周期性执行的任务

***
Q：uname 命令？  
A：uname 可以显示一些重要的系统信息，例如内核名称、主机名、内核版本号、处理器类型之类的信息。  
- `uname -a` 显示当前主机的系统信息

***
Q：whereis 命令？  
A：可以查找某个命令或者可执行程序的位置。  

***
Q：locate 命令？  
A：locate 命名可以显示某个指定文件（或一组文件）的路径。

***
Q：ifconfig 命令？  
A：ifconfig 用于查看和配置 Linux 系统的网络接口。  

***
Q：ping 命令？  
A：向远程主机发送数据包，测试网络连通性。  

***
Q：curl 命令？  
A：如果我们使用 ping 测试某个地址是否能连接，那么 curl 测试用个 URL 是否可以访问。  

***
Q：ftp 命令？  
A：连接 ftp 服务器，可以进行文件传输。

***
Q：ssh 命令？  
A：可以远程登录到远程主机。

***
Q：ps 命令？  
A：ps 命令用于显示正在运行中的进程的信息。

***
Q：free 命令？  
A：free 命令可以查看系统内存的使用情况。  

***
Q：top 命令？  
A：可以相对全面的查看系统负载的情况和来源。  

***
Q：netstat 命令？  
A：可以查看当前网络的状态。  
- `netstat -ntl` 查看当前监听的端口
***

## Linux 概述

Q：Linux 的体系结构是怎样的？  
A：Linux 体系可以分为两块：  
- 用户空间(User Space) ：用户空间又包括用户的应用程序(User Applications)、C 库(C Library) 。
- 内核空间(Kernel Space) ：内核空间又包括系统调用接口(System Call Interface)、内核(Kernel)、平台架构相关的代码(Architecture-Dependent Kernel Code) 。

![](https://img-blog.csdn.net/20170401170616835?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM2Mzk3MTQx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
***
Q：为什么 Linux 体系结构要分为用户空间和内核空间？  
A：现代 CPU 实现了不同的**工作模式**，不同模式下 CPU 可以执行的指令和访问的寄存器不同。Linux 从 CPU 的角度出发，为了保护内核的安全，把系统分成了两部分。  
用户空间和内核空间是程序执行的**两种不同的状态**，我们可以通过两种方式完成用户空间到内核空间的转移：1）系统调用；2）硬件中断。
***
Q：什么是Linux 内核？  
A：Linux 系统的核心是内核。内核控制着计算机系统上的所有硬件和软件，在必要时分配硬件，并根据需要执行软件。
1. 系统内存管理
2. 应用程序管理
3. 硬件设备管理
4. 文件系统管理

***
Q：Linux 使用的进程间通信方式？  
A：方式如下：  
1. 管道(pipe)、流管道(s_pipe)、有名管道(FIFO)。
2. 信号(signal) 。
3. 消息队列。
4. 共享内存。
5. 信号量。
6. 套接字(socket) 。

***
Q：Linux 文件系统是怎样的？  
A：在 Linux 操作系统中，所有被操作系统管理的资源，例如网络接口卡、磁盘驱动器、打印机、输入输出设备、普通文件或是目录都被看作是一个文件。  
Linux 支持 5 种文件类型：  
![](http://static.iocoder.cn/b6865417dd1245e4f3c4ba877ce9c5aa)






