# 文件特殊权限

这是很好玩的东西。

## SetUID

被赋予SUID权限的的**可执行二进制程序文件**，在被其他用户执行时，会按照文件**所有者**的权限来执行。

所以要赋予一个文件SUID权限需要这些前提条件：

- 该文件是可执行的二进制文件
- 当前用户需要有该文件有执行权限

那么这个权限有什么用呢？下面来举一个例子。

### SUID权限实例

	<!--首先查看这个文件的权限-->	
	ll /etc/shadow

关于这个文件的介绍在这里：[shadow保存用户经过加密后的密码](http://blog.csdn.net/yqxllwy/article/details/54984360#t1)，这个文件的权限是：

	-rw-r----- 1 root shadow 1490 2月  15 19:30 shadow

表示**其他人**都无法对这个文件进行任何查看和修改，可是当我们运行passwd命令的时候就会修改里面的内容，那么不是矛盾了吗？我们以一个其他人的身份对这个文件执行了w权限的操作，这里运用的就是SUID，我们查看一下passwd命令所在的文件就可以看出来了。

	whereis passwd #结果是：passwd /usr/bin/passwd
	\#这里我们查看一下该文件的权限：
	ll /usr/bin/passwd
	\#结果是：
	-rwsr-xr-x 1 root root 54256 3月  29  2016 passwd*

这里就表明passwd命令是有SUI权限的，而且对于其他人执行时是以root(*该文件的所有者*)的身份来修改 /etc/shadow 文件的，所以可以修改。

--------------------------

这里穿插一点关于passwd的额外介绍。像上面讲的，我们在运行passwd命令时是以root身份来执行的，那么我们是否可以修改别人的密码呢？如

	passwd lw

如果你现在(没运行passwd命令以前)的身份是root，那么运行这条命令是没错的，可是如果你现在不是root，而是别人，比如另一个身份，则运行这条命令是会报错的：

	passwd lw	#会报下面的错
	passwd：您不能查看或更改 lw 的密码信息。

所以，即使在运行passwd命令时你的身份是root，也可以保证你不能修改别人的密码。

### 设置SUID权限：chmod u+s 文件名

	chmod 4剩余权限 文件名 #如 chmod 4755 文件名，这里的4指的就是SUID权限
	chmod u+s 文件名 # s 指的就是SUID

这里的4就是这一部分内容中的特殊权限之一：[umask命令](http://blog.csdn.net/yqxllwy/article/details/55209911#t14)。

### 删除SUID权限：chmod u-s 文件名

或者也可以运行下面的这条命令：

	chmod 0剩余命令 文件名	# 0表示原始默认的权限

### 超级作死实例：chmod u+s /usr/bin/vim

为什么说这条命令在超级作死？因为我们先查看一下 /usr/bin/vim 这个命令的权限：

	lrwxrwxrwx 1 root root 21 9月  16 22:27 /usr/bin/vim -> /etc/alternatives/vim*

注意哦！！！这个文件的**所有者**是root，而且这个命令对其他人是rwx，如果vim添加了SUID权限后，每次运行vim命令都可以以root用户的身份来查看，修改任何你可以访问到的目录内的文件！！！简直是勇敢！！！

## SetGID

<font color="red">SGID的命令作用和SUID类似，只是SUID是会以文件的所有者身份来运行，而SGID则是以文件的所属组的身份来运行。还有一点不同的是SUID仅针对可执行二进制程序文件，而SGID不仅可以针对二进制程序文件，也可以针对目录来设置SGID程序。</font>

这里拿locate命令来举例子，locate命令实际上是搜索

	/var/lib/mlocate/mlocate.db

这个文件，而查看这个文件的权限，我们可以看到：

	-rw-r----- 1 root mlocate 7190360 2月  15 08:12 /var/lib/mlocate/mlocate.db

**其他人**对与该文件又TM没有任何权限，感觉Linux超级种族歧视啊。可是看locate命令的权限：

	lrwxrwxrwx 1 root root 24 8月  12  2016 /usr/bin/locate -> /etc/alternatives/locate*
	<!--这里没有看到SGID权限，是因为这里只是软链接，我们接着去找 /etc/alternatives/locate* 这个文件-->
	ll /etc/alternatives/locate*
	<!--很可惜，这个也是软链接-->
	lrwxrwxrwx 1 root root 16 8月  12  2016 /etc/alternatives/locate -> /usr/bin/mlocate*
	<!--要是下一个还是软链接我就无语了....-->
	ll /usr/bin/mlocate
	<!--还好不是了，是原始文件了-->
	-rwxr-sr-x 1 root mlocate 39520 11月 18  2014 /usr/bin/mlocate*

这里我们看见了SGID的表示，在所属组的权限栏里标有S，表示这个文件有SGID权限。所以用户在执行locate命令的时候，是以locate的所属组的身份，也就是mlocate组的身份来运行locate命令的，而mlocate组对mlocate.db文件又有r权限，所以可以查看内容。

### 添加SGID权限：chmod g+s 文件名/目录

也可以运行：

	chmod 2剩余权限 文件/目录

### SGID权限和目录的关系简介

我们上面讲过SGID权限也可以赋予给目录，那么赋予给目录有什么作用吗？那就是被赋予SGID权限的目录，在该目录下创建的文件，**如果你是该目录的所属组的成员**，则你在该目录下创建的文件的所属组都会变成该目录的所属组。看下面这个例子：

	<!--先为非root用户添加一个附属组，这里就为lw添加一个附属组friends-->
	gpasswd -a lw friends
	<!--接着再创建一个新目录，来做实验-->
	mkdir SGID_test
	<!--修改该目录的所属组-->
	chgrp frinds SGID_test
	<!--再为该目录添加SGID权限-->
	chmod g+s SGID_test
	<!--做了这么多前期准备，现在终于可以开始演示了-->
	<!--先把身份切换成lw-->
	su lw
	touch SGID_test/同船爱歌无删减版.avi
	<!--这部电影无删减版简直，你看过就懂，下面来看这个文件的权限-->
	-rw-r--r-- 1 lw friends    0 2月  15 22:16 同船爱歌无删减版.avi

这个文件的所有者是lw，而所属组则是继承了拥有SGID权限的目录的所属组，下面把SGID_test目录的SGID权限删掉，再来创建文件，就会发现不同了。
	
	<!--先切换回root-->
	sudo chmod g-s SGID_test
	su lw
	touch SGID_test/漂亮男孩.avi
	ll SGID_test/漂亮男孩.avi
	-rw-rw-r-- 1 lw lw       0 2月  15 22:13 漂亮男孩.avi

这个时候创建的新文件，漂亮男孩.avi的所属组就是lw自己的主要组了。

### 删除SGID权限：chmod g-s 文件名/目录名






