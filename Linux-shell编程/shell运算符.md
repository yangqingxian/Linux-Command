这一节超级简单，就三个主要内容。

# declare [+/-][选项] 变量名

- - 表示给变量设定类型属性
- + 表示取消掉设定的类型属性

选项：

- a 数组类型
- i 整数类型
- x 设为环境变量
- r 将该变量设定为只读属性，设定后无法被unset取消掉，无法修改其中的内容
- p 显示指定变量的被声明类型

## 整数类型例子

	x=1
	y=2
	declare -i result=$x+$y
	echo $result
	#结果是3,不是之前的 1+2
	#还有一点，不需要把x和y也使用declare设定，当然设定了也没有错

## 环境变量例子
	
	#先使用前面的介绍的方法设定环境变量
	export x=1
	#上面的 export 就是调用了 declare 来设定环境变量的
	declare -x x=1
	
## 数组例子

	#一般设定数组方法
	movie[0]=hello
	movie[1]=world
	#或者也可以使用 declare -a 来设定
	declare -a movie[2]=again
	#注意接下来的输出
	#第一种输出方式：$((数组名[下标]))
	echo $((movie[0]))
	#输出结果是 hello
	#如果直接写
	echo $movie[0]
	#那么shell先解析movie，这里默认输出movie[0]，也就是hell，接着的[0]，被当作字符，拼接上前面的内容输出，所以结果是
	hello[0]
	#第二种输出方式：$[数组名[下标]]
	echo $[movie[1]]
	#输出结果是 world

# 数值运算的方法

## expr/let $变量1 运算符 $变量2 运算符 ·········

直接看例子就懂了：

	x=1
	y=2
	c=$(expr $x + $y)
	echo $c
	#结果是 3

<font color="red">注意，$变量1 运算符 $变量2 运算符·····这样的格式之间一定要有空格隔开，否则 expr 无效。</font>

## $[运算式] 和 $((运算式))
	
	x=1
	y=1
	echo $(($x+$y))
	echo $[$x+$y]

就是在计算式外面套上一层。

## 运算符优先级

![这里写图片描述](http://img.blog.csdn.net/20170220222142143?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWVFYTExXWQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

# 变量测试

![这里写图片描述](http://img.blog.csdn.net/20170220223005241?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWVFYTExXWQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

这里解释一下怎么看这张表格，以第一个x=${y-新值}，为例

- 当y没有设置的时候，或者刚被 unset y 之后，这条式子的结果就是x=新值。
- 当y有值，可是为空的时候，那么x也会等于空值。
- 当y有值，且不为空时，则x=$y，就是x会等于y的值

同样的表达式，会根据y的情况决定x的情况，所以就可以拿x来做y的变量测试了，下面的就是表达式不同，结果也不同，就不一一解释了。


	





