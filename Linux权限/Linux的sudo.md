# Linux特殊权限：sudo

## sudo权限

使普通用户能暂时使用root权限执行命令。

## 设定sudo权限：visudo

这个命令实际上是修改了

    /etc/sudoers

文件。如果你执行了visudo，那么你会在命令行底端看见这么两行提示：

                                                                
    ^G 求助        ^O Write Out   ^W 搜索        ^K 剪切文字    ^J 对齐        ^C 游标位置    ^Y 上页        M-\ 首行       M-W 何处是下一个
    ^X 离开        ^R 读档        ^\ 替换        ^U Uncut Text  ^T 拼写检查    ^_ 跳行        ^V 下页        M-/ 尾行       M-] To Bracket

这里讲一下如何执行这些命令：Esc，然后命令中的 ^ 代表的是 Alt，如离开：^X，就需要依次按 Esc,Alt x，这样才会退出，有点像vim。

所以还不如直接修改 /etc/sudoers 文件。

在这个文件中最重要的就是这句设定：

    root    ALL=(ALL) ALL

这句话的含义是：

    用户名 被管理主机的地址=(可使用的身份) 授权命令

- 用户名：表示被赋予权限的用户的用户名，如lw
- 被管理主机的地址：这个地址既可以是本机，也可以是本机管理下的别的计算机，这里的地址代表ip地址
- 可使用的身份：表示该用户可以使用su切换成别的用户
- 授权命令：就是一般的命令，而且你对命令写的越模糊，赋予给这个用户的权限越大，如 rm，和 cd，那么你就允许该用户直接运行cd 命令到任何路径下并且 rm任何文件。


<font color="red">授权命令一定要写命令的绝对路径：使用 whereis 命令，的格式就可以查看该命令的保存路径，当然有的命令是Linux内核命令，那么就查不到了，如 cd。</font>

这里拿ls来举例子：

    whereis shutdown 
    <!-- 下面显示结果 -->
    shutdown: /sbin/shutdown /usr/share/man/man2/shutdown.2.gz /usr/share/man/man8/shutdown.8.gz
    <!-- 接着为lw赋予shutdown的命令 -->
    vim sudoers
    <!-- 在其中添加这一行内容：
    lw    ALL=(ALL) /sbin/shutdown
    但是如果只允许lw执行重启命令，则这么写
    lw    ALL=(ALL) /sbin/shutdown -r 
     -->
     <!-- 下面把身份切换成lw，来尝试运行一下shutdown命令 -->
     

*可能sudoers原先的针对所有人的权限是只读权限，所以要使用chmod 740 sudoers，命令来修改文件访问权限再做修改，个人意见，试试后记得改回去，包括文件内容，否则系统可能会报错，我TM就被吓得以为要重装系统了。*






