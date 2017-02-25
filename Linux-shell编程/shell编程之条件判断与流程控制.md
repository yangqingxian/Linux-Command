# shell编程之条件判断语句与流程控制

简单点来说就是if，for，case，where之类的判断语句，然后结合一些shell命令来达到简化一些操作。

下面的内容是重点，是条件判断的重点！！！刚开始不理解也没关系，下面有实例演示。

# 按照文件类型判断

![这里写图片描述](http://img.blog.csdn.net/20170222144626991?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWVFYTExXWQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 判断格式 [ 需要进行判断的语句 ]

# 按照文件权限进行判断

![这里写图片描述](http://img.blog.csdn.net/20170222144917081?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWVFYTExXWQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

# 两个文件之间进行比较

![这里写图片描述](http://img.blog.csdn.net/20170222144945745?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWVFYTExXWQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

# 两个整数之间比较

![这里写图片描述](http://img.blog.csdn.net/20170222145006042?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWVFYTExXWQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

# 字符串的判断

![这里写图片描述](http://img.blog.csdn.net/20170222145035918?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWVFYTExXWQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

# 多重条件判断(逻辑判断)

![这里写图片描述](http://img.blog.csdn.net/20170222145053628?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWVFYTExXWQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

# if

## 单分支if条件语句

	if [ 条件判断式 ];then
	    程序
	fi
	
或者也可以使用下面的形式：

    if [ 条件判断式 ]
        then
        程序
    fi

<font color="purple">Fuck啊！！！注意，在if(空格)[(空格)条件判断式(空格)]中的空格！！！原先以为有没有都每关系的，差点被卡在这里卡到怀疑人生。</font>
    
### 实例1：判断当前用户是否是root

	#!/bin/bash

	#上面这句话可能不加程序也能正常运行，但是加了有备无患

	#这里是读取环境变量中的USER字段，在其中保存着现在登录用户的信息
	echo $(env | grep "USER")
	#再使用cut命令截取该字段中的第2列，并且使用-d指定分割符为=
	user=$(env | grep "USER"|cut -d "=" -f 2)
	#最后使用if语句判断，当前用户是否是root
	if [ $user==root ]
	then
	echo $user
	fi
	
注意，要在创建文件后给文件赋予执行权限(x)，默认是不赋予的。

## 双分支if条件语句

    if [ 条件判断 ]
        then
        条件成立时，执行的程序
        else
        条件不成立时，执行的另一个程序
    fi
    
可以看出就是比上面多了一个else。

### 实例2：计算跟分区使用情况

    #!/bin/bash
    
    amount=$(df -h | grep /dev/sda6 | awk '{print $5}' | cut -f 1 -d "%")

    if [ $amount -gt 10 ]
	    then
	    echo "跟分区使用量："$amount
    else
	    echo "跟分区已使用："$amount
    fi

### 实例3：判断apache2服务是否开启

    #!/bin/bash
    #可以试着把最后的筛选条件 grep -v apache2删掉看看会显示什么内容
    service=$(ps aux|grep apache2|grep -v apache2)

    #注意，这里一定要加上"$变量名"，否则即使$service没有显示内容，可是也不算是空
    if [ -n "$service" ]
	    then
	    echo 'everything is ok'
	    else 
	    echo 'apache2 start'
	    #重启apache2服务
	    service apache2 start
    fi

## 多分支语句

    if [ 条件判断1 ]
        then
        程序
        elseif [ 条件判断2 ]
            then
            程序
        else
            程序，当所有条件都不成立时，最后运行此程序
    fi
        
### 实例4：简单计算机

就是交互，按照要求和顺序输入式子，结束输入后判断输入是否正确，如果正确则显示计算结果，如果错误则将显示错误信息。

这是原本简单版的，可是却存在很多问题，但是其中一些内容很值得参考！！！所以我也把它记录下来了：

	#!/bin/bash
	read -p "请输入第一个参数:" num1
	read -p "请输入第二个参数:" num2
	read -p "请输入需要的计算:" opt
	#在这里为了显示接下来可能会出现的问题，我把输入的参数打印一下
	printf '%s \n %s \n' $num1 $num2
	printf '%s \t' $opt
	printf '\n'
	if [ $opt==+ ]
		then 
		echo $[$num1+$num2]
	elif [ $opt==- ]
		then 
		echo $[$num1-$num2]
	elif [ $opt==* ]
		then
		echo $[$num1*$num2]
	elif [ $opt==/ ]
		then
		echo $[$num1/$num2]
	else
		echo "别做死"
	fi

乍一看可能觉得思路没有问题，就是借助read实现交互，再来判断需要执行的操作，最后打印结果，可是实际上的执行效果如下图所示：

![这里写图片描述](http://img.blog.csdn.net/20170223121218934?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWVFYTExXWQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

从中可以看出上面的脚本存在的问题。

- 问题1：==字符串判断是否相等，没有起作用
- 问题2：当 $opt 为 * 时，表示当前了路径下所有的文件，所以才会显示除那么多文件。我尝试过使用转义符，就是在输入 opt 时输入 \*，可是无效

所以我把程序修改成下面的样子：

	#!/bin/bash
	read -p "请输入第一个参数:" num1
	read -p "请输入第二个参数:" num2
	read -p "请输入需要的计算 [1:+,2:-,3:*,4:/]" opt
	#在这里为了显示接下来可能会出现的问题，我把输入的参数打印一下
	printf '%s \n %s \n' $num1 $num2
	printf '%s \t' $opt
	printf '\n'
	if [ $opt -eq 1 ]
		then 
		echo $[$num1+$num2]
	elif [ $opt -eq 2 ]
		then 
		echo $[$num1-$num2]
	elif [ $opt -eq 3 ]
		then
		echo $[$num1*$num2]
	elif [ $opt -eq 4 ]
		then
		echo $[$num1/$num2]
	else
		echo "别做死"
	fi

执行结果如下所示：

![这里写图片描述](http://img.blog.csdn.net/20170223122204532?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWVFYTExXWQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

本来这样就实现了基本的计算功能，可是当需要人来输入时，总是需要考虑错误的情况，如除数为0的情况，输入为数字的情况，所以对上面的内容改进如下：

	#!/bin/bash

	read -p "Input first number" num1
	read -p "Input second number" num2
	read -p "1:+ 2:- 3:* 4:/" opt
	
	#printf '%s \t %s \t %s \n' $num1 $num2 $opt
	#判断所有输入是否为空，都不为空才能执行下一步，所以使用 -a 来做逻辑与
	if [ -n "$num1" -a -n "$num2 -a -n "$opt ]
		then
	#这里是为了验证输入的都是数字，使用sed命令做替换，如果num1和num2中仅存在数字的话，则sed命令会执行，将数字全部替换为空，
	#则test1和test2为空，如果test1和test2非空，则表示在num1和num2中存在别的字符，不能被sed命令替换掉
		test1=$(echo $num1 | sed -n 's/[0-9]//g')
		test2=$(echo $num2 | sed -n 's/[0-9]//g')
		if [ -z $test1 -a -z $test2 ] 
			then
			if [ "$opt" -eq 1 ]
				then
				echo $[$num1+$num2]
	#这里是只打算做最简单的计算器，不涉及小数和负数
			elif [ "$opt" -eq 2 -a $num1 -gt $num2 ]
				then 
				echo $[$num1-$num2]
			elif [ "$opt" -eq 3 ]
				then 
				echo $[$num1*$num2]
	#这里是为了验证除数不为0
			elif [ "$opt" -eq 4 -a $num2 -ne 0 ]
				then
				echo $[$num1/$num2]
			else
			echo "Ueless Input."
			fi
		else
		echo "Input must be numbers!!!"
		fi
		else
		echo "Input can't be empty!!!"
	fi

# case

> case语句和if···elif···else语句一样都是多分支条件语句，不过和if多分支条件语句不同的是，case语句只能判断一种条件关系，而if语句可以判断多种条件关系。

## 结构

![这里写图片描述](http://img.blog.csdn.net/20170223130152175?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWVFYTExXWQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 实例5：判断用户输入yes或者no

	#!/bin/bash

	read -p "请输入[yes/no]:" input

	case $input in
		"yes")
			echo "yes"
			;;
		"no")
			echo "no"
			;;
		*)
			echo "other"
			;;
	esac

就是语法上有点不习惯，总体上来说这个程序还是很简单的。

# for

## 结构1

	for 变量 in 值1 值2 值3
		do
			程序
		done

## 结构2

![这里写图片描述](http://img.blog.csdn.net/20170223131032304?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWVFYTExXWQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

##实例6：批量修改文件内容

	#!/bin/bash

	for((i=1;i<=10;i=i+1))
		do
			echo hello world >> /home/yang/temp.log
		done	
		
运行完该命令后再去查看 /home/yang/temp.log文件，结果发现里面多了10条记录。

# while

> while循环是不定循环，也称做条件循环，只要条件判断式成立，循环就会一直继续，直到条件判断式不成立，循环才会停止。

## 结构

	while [ 条件判断式 ]
		do
			程序
		done
		
# until

> until循环，和while循环相反，until循环时只要条件判断式不成立则进行循环，并执行循环程度。一旦循环条件成立，则终止循环。

	



































