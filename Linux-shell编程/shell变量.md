# 思维导图

** shell编程基础：变量 **

# shell编程简介

shell编程与java，php等以主要实现功能为目的的编程语言不同，他的目的主要是为了简化管理人员的管理操作。

这一节的重点主要是为了介绍编程中的变量内容，超级简单的。

# 变量定义

> 
- 变量是计算机内存的单元，其中存放的值可以改变。
- 变量让你能够把程序中准备使用的每一段数据都赋值给一个简短、易于记忆的名字，因此他们十分有用。

*我略微强迫症，所以一定要把上面那段话打出来，不理解也没关系。*

# 变量的命名规则

1. 变量名必须以**字母或下划线打头**，名字中间只能由**字母、数字和下划线组成**。
2. 变量名的长度不得超过255个字符。
3. 变量名在有效范围内必须是唯一的。
4. 在Bash中，**变量的默认类型都是字符型**。

<font color="red">注意，第4条是重点！！！从下面的内容中就能看出来。</font>

# 变量的分类

有用户自定义变量，环境变量，位置参数变量，预定义变量。详细的内容下面再介绍。

# 用户自定义变量

## 定义方法：变量名=变量值

注意，变量名=变量值，之间不能用空格隔开，否则shell会把变量名解析成命令，而不是变量。

## 调用变量：$变量名

## 变量的查看：set

直接在命令行中敲如set，就可以查看系统全部变量。

## 删除变量：unset 变量名

## 变量叠加

这里就可以展现——在Bash中，**变量的默认类型都是字符型**这句话的含义了，看下面的例子：

	x=123
	y=456
	echo $x+$y
	\#输出结果是
	123+456
	\#可以看到，bash是把$x和$y解析成字符串，同时把+也当作字符串，只是把这几个字符串拼接在一起而已
	\#下面的内容可能更加直观一点
	echo $x$y
	\#结果是
	123456

# 环境变量

> 用户自定义变量只在当前的shell中生效，而环境变量在当前shell和这个shell的所有子shell中生效。

注意，对系统生效的环境变量名和变量作用是固定的。如果经常在windows上安装软件的人都知道，经常需要修改一个叫做path的值，而这个值在linux中也有，叫做PATH，他就是常用的环境变量之一。

前面说过可以使用 set 命令查看所有系统变量，其中就有环境变量，这里列举几个重要的。
	
- HOSTNAME:主机名
- SHELL:当前shell类型
- TERM:终端环境
- HISTSIZE:最大保存历史命令条数
- SSH_CLIENT:远程连接的客户端的ip
- SSH_TTY:
- USER:当前登录用户

## 最重要的环境变量之一：PATH

由于牵扯到别的知识点，所以单独把这个环境变量拿来讲。看下面这个例子：

	whereis ls
	ls: /bin/ls
	\#我们前面学习过，要运行一个命令应该输入这个命令的绝对路径
	\#可是为什么一般情况下我们都只要需要输入 ls 就能运行 ls 命令
	\#而不是输入 \bin\ls，当然如果你一定要这么输的话，谁也拦不住你

这就是PATH这个变量的作用了，直接查看PATH变量的作用：

	echo $PATH
	\#
	/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
	\#里面就有：/bin/，所以运行 ls 的时候，系统就会去这些PATH目录下查找，有没有叫ls的命令，有的话就运行

<font color=purple">注意，PATH中各个值之间使用 ;——分号隔开。</font>

这里再提一个环境变量，PS1。

这个变量保存的就是命令行前面的那一段显示，如我的是root@yang-ThinkPad-Edge-E445:/#。

其中各个位代表的含义，以及替代符如下所示：
	
- \d:显示日期，格式是“星期 月 日”
- \H:显示完整的主机名
- \t:显示24小时制的时间，如“HH:MM:SS”
- \A:显示24小时制的时间，如“HH:MM”
- \u:显示当前用户名
- \w:显示当前所在目录的完整名称
- \W:显示当前所在目录的最后一个目录
- \$:提示符，如果是root显示提示符为 #，如果是普通用户是 $

所以我使用的PS1的值是：

	\u@\H :\w\$

如果你喜欢的话，那么你也可以修改成你自己喜欢的模式。

## 设置环境变量：export 变量名=变量值

或者还有另外一种方式：export 变量名

## 只查看环境变量：env

## 查看语言支持情况：locale

专业一点的叫法叫语系。

	locale
	\#
	LANG=zh_CN.UTF-8	#定义系统主语系的变量
	LANGUAGE=zh_CN:zh
	LC_CTYPE="zh_CN.UTF-8"
	LC_NUMERIC="zh_CN.UTF-8"
	LC_TIME="zh_CN.UTF-8"
	LC_COLLATE="zh_CN.UTF-8"
	LC_MONETARY="zh_CN.UTF-8"
	LC_MESSAGES="zh_CN.UTF-8"
	LC_PAPER="zh_CN.UTF-8"
	LC_NAME="zh_CN.UTF-8"
	LC_ADDRESS="zh_CN.UTF-8"
	LC_TELEPHONE="zh_CN.UTF-8"
	LC_MEASUREMENT="zh_CN.UTF-8"
	LC_IDENTIFICATION="zh_CN.UTF-8"
	LC_ALL=		       #定义整体语系的变量，一般为空

这里 zh_CN 表示中文，UTF-8表示国际编码。

	locale -a #查看Linux支持的所有语系

### 为Linux安装中文支持

**前提条件：**正确安装的中文字体和中文语系

- 如果有图形界面，可以正确支持中文显示
- 如果使用第三方远程工具，只要语系设置正确，可以支持中文显示
- 如果是纯字符界面，必须使用第三方插件，否则即使修改LANG参数也没有用，会乱码

# 位置参数变量

首先，接下来的内容都是针对输入参数的，我们写一个脚本可能需要人为的输入一些参数，而这些参数则是使用下面的方式在shell脚本中被处理的。

|位置参数变量|作用|
|----------
|$n|n为数字，$0表示命令本身，也就是没有输入参数，$1-$9代表第一个到第九个参数，十个以上的参数需要用大括号包含，如${10}|
|$*|这个变量代表命令行中所有的参数，$*把所有参数看成一个整体|
|$@|这个变量代表命令行中所有的参数，与$*不同的是，$@把每个参数区分对待|
|$#|这个变量代表命令行中所有参数的个数|

在开始讲解之前，先把for函数简单介绍一下：

	for 变量名 in 待处理数据
		do
			/code
		done

结合for就能解释上面那几个位置参数变量的区别了。

## $n

先创建一个example.sh，里面的内容如下：
	
	#!/bin/bash	
	firstInput=$1	#$1 表示第一个输入的参数
	secondInput=$2  #$2 表示第二个输入的参数
	result=$firstInput+$secondInput	#这里只是简单的把第一个参数和第二个参数使用+拼接起来，默认类型是字符串嘛
	echo "\$result="$result #打印输出结果，\ 表示转义

然后在命令行中的赋予执行权限以及执行：
	
	chmod 777 example.sh
	./example.sh 1 2

执行结果是：

![这里写图片描述](http://img.blog.csdn.net/20170220192858474?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWVFYTExXWQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

我这里把程序简化了。

## $* 和 $@

这两个的作用类似，所以放在一起讲。先创建一个下面内容的test.sh文件：

    #!/bin/bash

    #展示 $* 的作用
    for i in "$*"
	    do
            echo $i
            # 换行
            echo -e "\n"
        done

echo "-------------------------------------------"

    #展示 $@ 的作用
    for y in "$@"
	    do
            echo $y
            # 换行
            echo -e "\n"
        done

接着赋予该sheel文件执行权限，这里直接给777，并运行：

    chmod 777 test.sh
    ./test.sh 123 456 789

输出结果如下图所示：

![这里写图片描述](http://img.blog.csdn.net/20170220144140053?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWVFYTExXWQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

从结果中可以，比起\$n，\$\* 和 \$@并不需要提前确定输入参数的个数，因为，\$\*是把所有输入参数当成一个整理在处理，即使我输如的是 ./test.sh 123 456 789，对于\$\*而言，就是输入了一个整数“123 456 789”。而同样的输入对应于 \$@ 来说，则是“123”，“345”，“678”。

## $#

就是简单的统计输入的参数的个数。

## 常用读取用户输入参数方式：read [选项] [变量名]

选项：

- -p "提示信息":设置此选项之后，就会先显示"提示信息"，之后等待用户输入，用户输入完按回车(Enter)之后，就会把用户的输入值赋予给后面的变量
- -t 秒数:确定等待的时间，如果在规定秒数内用户没有输入的话，则终止该进程
- -n 字符数:限制用户输入的参数的字符数量
- -s:将用户输入隐藏起来，就像输passwd时，明明键盘有输入，可是显示器上面是没有显示的，就只需要添加这个选项就可以了

例：

	#!/bin/bash
	read -p "please input your name:" name
	read -n 1 -p "please input your sex[m/f]:" sex
	#这里主要是为了美观才加了换行符的，否则在你输入完成，没有按回车键就会自动执行下一个命令
	echo -e "\n"
	read -s -p "please input your passwd:" passwd 
	#这里也是为了美观，否则等你输完密码，按完回车，下面的显示会接着 "please input your passwd:"后面接着输出，超级丑
	echo -e "\n"
	echo $name
	echo -e "\n"
	echo $sex
	echo -e "\n"
	echo $passwd
	echo -e "\n"

执行结果：

![这里写图片描述](http://img.blog.csdn.net/20170220201549304?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWVFYTExXWQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

# 预订义变量

|预订义变量|作用|
|--------
|$?|判断上一次命令是否正确执行，如果正确执行的话，则返回0,否则则返回程序指定的返回值。|
|$$|返回当前进程的进程号(PID|
|$!|后台运行的最后进程的进程号|










