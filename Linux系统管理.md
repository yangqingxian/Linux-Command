# 思维导图

![这里写图片描述](http://img.blog.csdn.net/20170219214123651?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWVFYTExXWQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

# Linux系统管理

## 进程管理

进行进程管理是为了：

- 判断服务器健康状况
- 查看系统中所有进程
- 杀死进程

### 进程简介

> 进程是正在执行的一个程序或命令，每一个进程都是一个运行的实体，都有自己的地址空间，并占用一定的系统资源。

*这里额外附带一个关于线程的介绍，线程就是进程中的一部分，多个线程组成进程。*

### 进程查看:ps 选项 和 pstree [选项]

ps 选项：

- -a:显示一个终端的所有进程
- -u:显示进程的归属用户以及内存的使用情况
- -x:显示没有控制终端的进程
- -l:长格式显示。就是显示更加详细的信息
- -e:显示所有进程

	ps aux # 一般使用 ps aux 和 ps -le 的组合，注意，ps aux 是没有一般的 - 的
	\#下面就拿一组来做例子
	USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
	root         1  0.0  0.1 119972  6108 ?        Ss   07:39   0:02 /sbin/init splash	
	
- USER:该进程是由哪个用户产生的
- PID:进程的ID号
- %CPU:该进程占用CPU资源的百分比
- %MEN:该进程占用物理内存的百分比
- VSZ:该进程占用虚拟内存的大小，单位KB
- RSS:该进程占用实际内存的大小，单位KB
- TTY:该进程是在哪个终端中运行的。其中tty1-tty7代表本地控制台终端，tty1-tty6是本地的字符界面终端，tty7是图形终端。pts0-255代表虚拟终端
- STAT:该进程状态，常见的有 R running，S sleeping，T 停止，s 包含子进程，+ 位于后台
- START:该进程启动时间
- TIME:该进程占用CPU的运算时间
- COMMAND:产生该进程的命令的绝对路径

-------------------------------------

pstree 选项：

- -p:显示进程的PID
- -u:显示进程的所属用户

运行

	pstree -pu

结果如下图所示：

![这里写图片描述](http://img.blog.csdn.net/20170219133120847?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWVFYTExXWQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 系统健康状况查看：top [选项]

选项：

- -d:秒数，指定top命令每隔几秒更新。*默认情况是3秒*
- -b:使用批处理模式输出。一般和“-n”选项合用
- -n 次数:指定top命令执行的次数

仅运行

	top 
	
后的结果：

![这里写图片描述](http://img.blog.csdn.net/20170219134012241?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWVFYTExXWQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

这个时候top命令处于交互模式中，输入相应的命令可以进行下一步操作：

- ? 或 h:显示交互模式的帮助信息
- P:以*CPU*占有率排序
- M:以*内存*占有率排序
- N:以*PID*排序
- q:quite，退出

其中在最上面显示的信息如下(纯手打，TM的输出重定向就乱码了)：

top - 13:38:13 up 5:58, 1 user, load average: 0.11,0.18,0.25

|显示内容|说明|
|--------
|13:38:13|系统时间|
|up 5:58|系统已运行时间，这里代表系统已运行小时，58分钟|
|1 user|当前登录用户的数量|
|load average: 0.11,0.18,0.25|系统之前1分钟，5分钟，15分钟的平均负载|

Tasks:227 total, 1 running, 226 sleeping, 0 stopped, 0 zombie

|显示内容|说明|
|--------
|227 total|系统中总进程数|
|1 running|正在运行进程数|
|226 sleeping|睡眠的进程|
|0 stopped|正在停止的进程|
|0 zombie|僵尸进程，我更喜欢叫丧尸进程，半死不活的，需要人为清理|

%Cpu(s): 0.2 us, 0.2 sy, 0.0 ni, 99.5 id, 0.0 wa, 0.0 hi, 0.0 si, 0.0 st

> %Cpu(s):用户模式占用的CPU百分比(0.2us)，系统模式占用的CPU百分比(0.2sy)，改变过优先级的用户进程占用的CPU百分比(0.0 ni)，空闲CPU的CPU百分比(99.5 id)，等待输入输出

|显示内容|说明|
|--------
|0.2 us|用户模式占用的CPU百分比|
|0.2 sy|系统模式占用的CPU百分比|
|0.0 ni|改变过优先级的用户进程占用的CPU百分比|
|99.5 id|空闲CPU的CPU百分比|
|0.0 wa|等待输入\输出的进程的占用百分比|
|0.0 hi|硬件中断请求占用的CPU百分比|
|0.0 si|软件中断请求占用的CPU百分比|
|0.0 st|st(Steal time)虚拟时间百分比|

KiB Mem: 3166904 total, 175692 free, 1435396 used, 1555816 buff/cache

|显示内容|说明|
|------------
|3166904 total|物理内存的总量，单位KB|
|175692 free|空闲的物理内存数量|
|1435396 used|已使用的物理内存数量|
|1555816 buff/cache|作为缓存和缓冲的内存数量|

<font color="red">这里插一句缓冲：buff 和 缓存：cache 的区别，缓冲是当数据无法立刻写入硬盘时，就将数据存储在缓冲中，待系统闲暇时再将数据写入硬盘。而缓存，则是保存经常修改的数据，而不必每次都要从硬盘中读取。</font>

所以：

- cache:缓存，加快了数据的读写速度。
- buff:缓冲，加快了数据的写入速度。

KiB Swap:3292180 total, 3293180 free, 0 used, 1344476 avail Mem

|显示内容|说明|
|------------
|3292180 total|交换分区(虚拟内存)的总大小|
|3293180 free|空闲的交换分区大小|
|0 used|已使用的交换分区大小|
|1344476 avail Mem||

### 杀死进程：kill PID 和 killall [选项][信号] 进程名 和 pkill [选项][信号] 进程名

	kill -l 
	\#查看可用的进程信号
	
进程信号：进程间通信的信号。

执行结果如下：

	 1) SIGHUP	 2) SIGINT	 3) SIGQUIT	 4) SIGILL	 5) SIGTRAP
	 6) SIGABRT	 7) SIGBUS	 8) SIGFPE	 9) SIGKILL	10) SIGUSR1
	11) SIGSEGV	12) SIGUSR2	13) SIGPIPE	14) SIGALRM	15) SIGTERM
	16) SIGSTKFLT	17) SIGCHLD	18) SIGCONT	19) SIGSTOP	20) SIGTSTP
	21) SIGTTIN	22) SIGTTOU	23) SIGURG	24) SIGXCPU	25) SIGXFSZ
	26) SIGVTALRM	27) SIGPROF	28) SIGWINCH	29) SIGIO	30) SIGPWR
	31) SIGSYS	34) SIGRTMIN	35) SIGRTMIN+1	36) SIGRTMIN+2	37) SIGRTMIN+3
	38) SIGRTMIN+4	39) SIGRTMIN+5	40) SIGRTMIN+6	41) SIGRTMIN+7	42) SIGRTMIN+8
	43) SIGRTMIN+9	44) SIGRTMIN+10	45) SIGRTMIN+11	46) SIGRTMIN+12	47) SIGRTMIN+13
	48) SIGRTMIN+14	49) SIGRTMIN+15	50) SIGRTMAX-14	51) SIGRTMAX-13	52) SIGRTMAX-12
	53) SIGRTMAX-11	54) SIGRTMAX-10	55) SIGRTMAX-9	56) SIGRTMAX-8	57) SIGRTMAX-7
	58) SIGRTMAX-6	59) SIGRTMAX-5	60) SIGRTMAX-4	61) SIGRTMAX-3	62) SIGRTMAX-2
	63) SIGRTMAX-1	64) SIGRTMAX	

	kill PID
	\#PID可以使用ps命令查看到

-----------------------------------------------------

	killall [选项][信号] 进程名
	\#进程名同样可以使用ps查看到
	
选项：

- -i:交互式，询问是否要杀死某个进程
- -I:忽略进程名的大小写

<font color="red">killall和kill的区别是，kill只能删除一个进程，而killall则是可以根据进程名，关掉同样进程名的进程。</font>

如apache2，默认状态下，即使当前没有人连接apache2，也会默认运行多个线程，如以下所示：

	ps aux | grep apache2
	\#以下是输出结果
	root      1033  0.0  0.8 258264 26672 ?        Ss   07:39   0:01 /usr/sbin/apache2 -k start
	www-data  2273  0.0  0.2 258304  8160 ?        S    07:44   0:00 /usr/sbin/apache2 -k start
	www-data  2274  0.0  0.2 258304  8160 ?        S    07:44   0:00 /usr/sbin/apache2 -k start
	www-data  2275  0.0  0.2 258304  8160 ?        S    07:44   0:00 /usr/sbin/apache2 -k start
	www-data  2276  0.0  0.2 258304  8160 ?        S    07:44   0:00 /usr/sbin/apache2 -k start
	www-data  2277  0.0  0.2 258304  8160 ?        S    07:44   0:00 /usr/sbin/apache2 -k start

在这里添加一个知识，是关于killall中能添加[信号]的选项，同样以apache2为例，假设我使用killall杀死apache2的全部进程，则当前链接着apache2服务的所有用户都会断开与apache2的连接，而之后即使我再重启apache2，本来已经登录的用户也会变成未登录状态。所以为了能使当前用户不会被apache2踢出当前用户，应该这么写

	killall -1 apache2
	
这里的 -1 就是 kill -l 中显示的SIGHUP信号，也叫平滑重启信号，该信号能让进程立刻关闭，然后重新读取配置文件之后重启，这样当前用户的信息就会被重新载入。

------------------------------------

	pkill [- t 终端号][信号] 进程名
	
这个命令不仅可以杀死进程，更可以按照终端号来结束进程，当有用户使用某一远程连接进程登录服务器时，就可以使用该命令结束该用户的进程。

这里附带一个命令，查看当前登录系统用户：w

------------------------------------

	xkill 
	
这个是用在图形界面的，是用来关闭在图形界面无法关闭的软件的，运行后鼠标就会变成一个骷髅，点击哪个窗口，哪个窗口就会关闭。

### 进程优先级：PRI+NI

> Linux操作系统是一个多用户，多任务的操作系统，Linux系统中同时运行着非常多的进程。但是CPU同一个时钟周期内只能运算一个指令。进程优先级决定了每隔进程处理的先后顺序。

这里先查看一下各个进程的优先级：

	\#运行 ps 命令中的 -le 两个参数
	ps -le
	\#下面摘录了显示结果中的几组数据
	F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
	4 S     0     1     0  0  80   0 - 29989 -      ?        00:00:01 systemd
	1 S     0     2     0  0  80   0 -     0 -      ?        00:00:00 kthreadd
	1 S     0     3     2  0  80   0 -     0 -      ?        00:00:00 ksoftirqd/0

其中的 PRI 和 NI，两个共同表示该进程的优先级。PRI，Priority，NI，Nice，两者的**和**就是该进程的优先级，**数字越小代表优先级越高。**

对于普通用户，只能将NI的值设定在0-19之间，而且不能提升现在以自己身份运行的线程的优先级，只能降低，也就是只能将NI的值改大，却不能改小，而且普通用户无法修改PRI的值。当然对于超级用户——root，想怎么改就怎么改。

**设定命令的NI：nice [-n NI值] 命令**

注意，nice命令不能修改正在运行的进程的NI值，而是只能设定接下来命令的NI值。

**修改已经存在进程的NI值：renice [新NI值] PID**

对于上面的nice的不足之处，就使用这条命令来弥补。

## 工作管理

> 工作管理指的是在单个登录终端中同时管理多个工作的行为。

<font color="red">注意事项</font>

- 当前的登录终端，只能管理当前终端的工作，而不能管理其他登录终端的工作。
- 放入后台的命令必须可以持续执行一段时间，这样我们才能捕捉和操作这个工作。
- 放入后台执行的命令不能和前台用户有交互或需要前台输入，否则放到后台后会自动停止服务。

### 把进程放入后台：命令 &

在命令后加上 & 表示把命令放入后台执行，比如复制文家——cp，移动文件等——mv，期间并不需要人为干预的，都可以放入后台去执行。

还有一种方式，就是把当前命令放入后台，但是不执行，只是暂停在后台。

### 查看后台的工作：jobs [-l]

加上-l，表示显示工作的PID。这里有一个知识点，

	\#这里我故意扔了三个很耗费资源的命令到后台去
	find / -name abc &
	find / -name add &
	find / -name he &
	\#因为从根目录下开始搜索，所以很耗费资源，这时候使用 jobs 查看当前在后台运行的程序
	jobs
	\#输出的结果
	[1]   退出 1                find / -name abc
	[2]-  退出 1                find / -name add
	[3]+  退出 1                find / -name he

这里要讲解的知识点就是在[2]，[3]后面的 + 和 -。<font color="blue">这里的 + 表示最近一个放入后台的工作，- 代表倒数第二个放入后台的工作。</font>

### 将后台暂停的命令从后台移回到前台执行：fg %工作号

这里的工作好就是 [] 中的数字，% 可以省略。注意，是工作好，不是进程号。

	\#这里为了说明，把 top 放到后台去执行，由于 top 是有交互模式的，所以放在后台后自动暂停
	top &
	\#查看后台进程
	jobs
	[1]+  已停止               top
	\#接着把程序回复到前台
	fg 1
	

### 开启后台暂停的工作：bg %工作号
	
	\#前面不是把top放入后台后会停止服务吗？我强行使用 bg 进行了环境，结果我的命令行出现了令我震惊的显示结果，劝你们别作
	\#人都是被自己作死的，没有例外
	top &
	jobs
	[1]+  已停止               top
	bg 1
	\#命令行就疯了，"拿小拳拳锤你胸口哦"——命令行内心的声音

### 后台命令脱离登录终端执行

> 把命令放入后台，只能在当前登录终端执行，一旦退出或者关闭终端，后台程序就会停止。

为了应对这种状况，视频中老师介绍了三种方法，分别如下：

- 修改/etc/rc.local 文件，设置成开机自启动
- 使用crond服务的crontable来设置成定时任务
- 使用nohup命令

第一个在Ubuntu中没有，第二个[crond服务的相关知识前面介绍过了](http://blog.csdn.net/yqxllwy/article/details/55258978)。

	nohup 命令 &
	
就是这么一个格式。

## 系统资源查看

顾名思义，就是查看系统参数的一些命令介绍。

### 监控系统资源：vmstat [刷新延时 刷新次数]

这是我在自己电脑上运行的结果：

	procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
	 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
	 2  0      0 831992  87720 1020852    0    0    79    15  224  385  4  1 93  2  0

一部分一部分来看吧：

**procs**:进程信息字段

- r:等待运行的进程数
- b:不可被唤醒的进程数量

**memory**:内存信息字段

- swpd:虚拟内存使用情况
- free:空闲的内存容量
- buff:缓冲的内存容量
- cache:缓存的内存容量

**swap**:交换分区的信息字段

- si:从磁盘中交换到内存中数据的数量，单位KB
- so:从内存中交换数据到磁盘中数据的数量，单位KB

**io**:磁盘读写信息字段

- bi:从块设备读物数据的总量，单位是block，块
- bo:写到块设备的数据总量，单位也是块

**system**:系统信息字段

- in:美妙被中断的进程次数
- cd:每秒钟进行的事件切换次数

**CPU**:CPU信息字段

- us:非内核进程消耗CPU运算时间的百分比
- sy:内核进程消耗CPU运算时间的百分比
- id:空闲CPU的百分比
- wa:等待I/O所消耗的CPU百分比
- st:被虚拟机所盗用的CPU占比

### 开机时内核检测信息：dmesg

### 查看内存使用状况：free -单位

单位有：

- -b:字节
- -k:KB
- -m:MB
- -g:GB

	free -g
	\#显示的结果
			      total        used        free      shared  buff/cache   available
	Mem:              3           1           0           0           1           1
	Swap:             3           0           3

### 查看CPU信息：cat/proc/cpuinfo

查看这个配置文件就可以了。

### 显示内容跟top的第一行数据：uptime

### 查看系统与内核相关命令：uname [选项]

选项：

- -a:查看系统所有有关信息
- -r:查看内核版本
- -s:查看内核命令

### 查看系统位数：file /bin/ls

后面的 /bin/ls 不一定，可以是别的，这里就是为了查看下面的输出内容的一部分：

	file /bin/ls

这就是执行的结果，我们要的信息我已经使用红色标出来了：

/bin/ls: ELF <font color="red">64-bit</font> LSB executable, x86-64, version 1 (SYSV), .........

### 查看当前Linux系统的发行版本：lsb_release -a

### 列出进程打开或者使用的文件信息：lsof [选项]

选项：

- -c 字符串:只列出以字符串开头的进程打开的文件
- -u 用户名:只列出某个用户的进程打开的文件
- -p PID:列出某个PID进程打开的文件

## 系统定时任务

### at服务

这部分我没看，有兴趣的可以自己去搜。

### crond服务

基础介绍在这里[CronTab简介](http://blog.csdn.net/yqxllwy/article/details/55258978)。这里再添加一部分内容：anacron。

> anacron是用来保证在系统关机的时候错过的任务，可以在系统开机之后再来执行。

Crond的运行读取配置文件的顺序是下面这样的：

第一个是 /etc/crontab 的配置文件，里面有以下内容：

	\# m h dom mon dow user  command
	  17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
	  25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
	  47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
	  52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
	\#

首先会开启 /usr/sbin/anacron，该命令执行后会读取 /etc/anacrontab ，该文件是 anacron 的配置文件，里面的重要内容是：

	# These replace cron's entries
	1       5       cron.daily      run-parts --report /etc/cron.daily
	7       10      cron.weekly     run-parts --report /etc/cron.weekly
	@monthly        15      cron.monthly    run-parts --report /etc/cron.monthly

如果上面的内容失败了的化，则会执行 || 后面的命令，借由这个命令来开启 cron.weekly 和 cron.monthly 里面的可执行文件，这里就拿 cron.weekly 做例子，该目录下有一个0anacron的文件，该文件内容是：

	test -x /usr/sbin/anacron || exit 0
	anacron -u cron.monthly

在这个文件中，开启 anacron 命令。

借用上面这种方法，就是为了保证 anacron 命令能按时执行。然后，如果你想要搭顺风车的话，就把相应要执行的命令写入到相应的配置文件中吧。
	







