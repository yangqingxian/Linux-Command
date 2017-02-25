# 计划任务——Crontab

Crontab是一个用于设置周期性被执行的任务的工具。

- Cron Job：被周期性执行的任务我们称为Cron Job
- Cron Table：被周期性执行的任务组成的列表，我们称为Cron Table

## Ubuntu安装cron

	apt-get install cron

不论之前是否安装过，这里都可以使用service来查看：

	service --status-all | grep cron

或者查看详细信息以及日志信息：

	service cron status

## 设置Cron Table：crontab -e

然后就会显示下面的图片内容：

![](http://img.blog.csdn.net/20170216103047396?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWVFYTExXWQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

这个时候就是输入模式，可以直接输入。在编辑完成后要退出的话，可以先点击 Esc 键，接着按照最下面的提示符来执行命令，提示符中的 ^ 表示 Alt，例如离开就是Alt X，按下后如果有修改，那么会询问你是否愿意保存修改，如下面所示：

![](http://img.blog.csdn.net/20170216125353652?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWVFYTExXWQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

接着会询问你要保存的文件：

![](http://img.blog.csdn.net/20170216125451107?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWVFYTExXWQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

这里直接敲 Enter 就可以保存退出了。

## 查看上面修改的那个文件内容：crontab -l

## Cron Table设置格式：* * * * * 命令

这里前面的五个 * 表示时间，从左到右分别代表

- 分钟：0-59
- 小时：2-23
- 日期：1-31
- 月份：1-12
- 星期：1-7(0) #0也表示星期天

也就是下面的顺序

分钟 小时 日期 月份 星期数 命令

### 例1：30 21 * * * service apache2 restart

这里就表示每晚的21：30使用service命令重启apache2。

### 例2：45 4 1,10,22 * * service apache2 restart

这里表示每月的1号，10号，22号的4：45,重启apache2。与例子的区别在于，<font color="red">当要设置多个值时，只需要使用 ， 隔开即可。</font>

### 例3：45 4 1-10 * * service apache2 restart

这里表示每月的1号**到**10号，每天都在4：45,重启apache2。与例2不同的是，<font color="red">当要设置多个连续值的时候，可以使用-。</font>

### 例4：*/2 * * * * service apache2 restart

这里表示**每2分钟**，重启一次apache2服务。与上面都不同的是，如果直接写

	2 * * * * service apache2 restart

则表示在**每小时的第2分钟时**重启apache2。

这里把两者结合一下：

	1-59/2 * * * * service apache2 restart

表示在**每小时中，每2分钟**执行一遍apache2重启命令。

### 例5：0 23-7/1 * * * service apache2 restart

这里的含义是在**每天的晚上23：00到第二天的早上7：00之间，每1个小时重启一边apache2。**这里我们可以看出，如果有一个设定中前一个设定小于后一个设定，如23-7中的23>7，那么也就意味着，下一次计时的时间，如这里是第二天。

### 例6：0,30 18-21 * * * service apache2 restart

表示**每天的18:00,18:30,19:00,19:30,20:00,20:30,21:00**,重启apache2，换一种说法就是在18:00-21:00之间，每隔半个小时重启一遍apache2。

换一种写法也是可以的：

	0-59/30 18-21 * * * service apache2 restart

作用是一样的。

### 总结

- * 表示任何时间都匹配
- A,B,C 表示A或者B或者C时匹配
- A-C表示A到C之间匹配
- */A 表示每A的时间都匹配

## cron日志文件：/var/log/cron.log

如果在本路径下没有找到该日志文件，那么就要修改一个设定值：

	vim /etc/rsyslog.d/50-default.conf

这个目录文件我猜他全名应该是：record-system-log，是rsyslog服务的配置文件。表示存储者系统的日志文件，在这里目录下的那个文件中修改一个设定：

	#cron.*                          /var/log/cron.log

把前面的#去掉，就能开启cron的日志了。在修改配置后要想执行就要重启rsyslog服务。

	service rsyslog restart
	service cron restart

顺道把cron服务也重启一遍吧。这之后我们来实际尝试一下crontab。

## crontab实例

	<!--配置crontab-->	
	crontab -e

在最后部分按照上面的教程添加一个易于查看结果的命令：

	*/1 * * * * echo"one minute“

之后就保存退出，去查看日志文件：

	tail -f /var/log/cron.log
	<!-- tail -f 表示一直刷新该文件，并且显示文件最后内容-->
	<!--可以按 ctrl C 进行退出-->

下面的就是输出结果：

	Feb 16 13:59:01 yang-ThinkPad-Edge-E445 CRON[10025]: (root) CMD (echo "one minute" )
	Feb 16 13:59:01 yang-ThinkPad-Edge-E445 CRON[10024]: (CRON) info (No MTA installed, discarding output)
	Feb 16 14:00:01 yang-ThinkPad-Edge-E445 CRON[10040]: (root) CMD (echo "one minute" )
	Feb 16 14:00:01 yang-ThinkPad-Edge-E445 CRON[10039]: (CRON) info (No MTA installed, discarding output)
	Feb 16 14:01:01 yang-ThinkPad-Edge-E445 CRON[10049]: (root) CMD (echo "one minute" )
	Feb 16 14:01:01 yang-ThinkPad-Edge-E445 CRON[10048]: (CRON) info (No MTA installed, discarding output)
	Feb 16 14:02:01 yang-ThinkPad-Edge-E445 CRON[10066]: (root) CMD (echo "one minute" )
	Feb 16 14:02:01 yang-ThinkPad-Edge-E445 CRON[10065]: (CRON) info (No MTA installed, discarding output)
	Feb 16 14:03:01 yang-ThinkPad-Edge-E445 CRON[10084]: (root) CMD (echo "one minute" )
	Feb 16 14:03:01 yang-ThinkPad-Edge-E445 CRON[10083]: (CRON) info (No MTA installed, discarding output)

注意时间，每一分钟执行一次，运行命令 echo "one minute"，后面的命令是因为我没有把输出正确输出而已。





	












