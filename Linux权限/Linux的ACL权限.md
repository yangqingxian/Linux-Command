# Linux特殊权限

## ACL权限简介

该权限是在所有者，所属组，其他人之外，再为另一个用户或者用户组单独设置访问该文件的权限。这里的文件也指目录。注意，一个文件可以设置多个ACL权限。

### 查看是否开启ACL权限

    <!-- 查看磁盘分区情况，查找根分区的系统文件 -->
    df
    <!-- 这里拿 /dev/sda6 为例 -->
    dumpe2fs -h /dev/sda6
    <!-- -h这个参数我也不知道是什么意思，接着在下面显示的文件中查看这一行内容： -->
    Default mount options:    user_xattr acl
    <!-- 默认的挂载设置支持 acl -->

如果没有开启ACL权限，那么可以**临时手动开启**：

    mount -o remount,acl /
    <!-- -o 表示特殊选项，remount 表示重新挂载，acl表示开启ACL，/表示重新挂载根目录下 -->

如果想**永久生效**，那么就要修改配置文件，是

    /etc/fstab

但是怎么改跟教程不太一样，我也不知道了。

### 设定ACL权限：setfacl 选项 文件名

选项：

- -m 设定ACL权限,给用户设定ACL权限格式: u:用户名:权限,给用户组设定: g:用户组:权限
- -x 删除指定的ACL权限 
- -b 删除所有的ACL权限
- -d 设定默认ACL权限
- -k 删除默认ACL权限
- -R 递归设定ACL权限

### 实例

先讲解一下需求便于理解，你创建了一个movie目录，在这个目录下放了一些资源，你本来打算这么分配权限：

- 你自己：所有者：rwd
- 你的朋友：所属组：rwd
- 其他人：其他人：---

这个时候你的邻居，老王也想查看这个目录下的内容，可是你想分配给老王的权限是r-x，这个时候原本Linux的设置就不够用了，因为你无法把老王归属到任何一个权限中去，他既不属于文件的所有者，也不能属于所属组，更不能是其他人，所以要为老王单独赋予一个ACL权限，具体操作看下面：

    <!-- 先创建一个movie目录 -->
    mkdir movie
    <!-- 接着为你的朋友创建一个用户组 -->
    groupadd friends
    <!-- 这里省略把用户拉入组的操作 -->
    <!-- 为老王创建一个用户：lw -->
    useradd lw
    <!-- 由于文件是你自己创建的,所以默认的用户组是你的主要组,这里修改成 friends -->
    chgrp friends movie
    <!-- 设置好文件所属组后就要修改文件的权限,根据我们上面的设定,设置的权限是770 -->
    chmod 770 movie
    <!-- 接下来开始为lw添加acl权限 -->
    setfacl -m u:lw:rx movie
    <!-- 查看最后的结果 -->
    ll
    <!-- 
    drwxrwx------+ root friends movie/ 
    这里的+就表示该文件有acl权限 -->
    <!-- 查看ACL权限 -->
    getfacl movie
    <!-- 
    \# file: movie/
    \# owner: root
    \# group: friends
    user::rwx
    user:lw:r-x #单独为lw添加的acl权限就在这里显示了
    group::rwx
    mask::rwx #这里下面会重点讲
    other::---
     -->

###  查看ACL权限：getfacl 文件名

### 最大有效权限:mask

<font color="red">设置最大权限命令：setfacl -m m:r/w/x 文件名</font>

在上面的实例中我们为lw设置了r-x权限,实际上这个权限是要与这个mask的设置**相与**才是lw对于movie的实际有效权限,如:

    ACL      : r - x
    mask     : r w x
    effective: r - x

<font color="red">计算方法是: r/w/x 逻辑"与"运算 - 等于 -,r/w/x 逻辑"与"运算 r/w/x 等于 r/w/x。</font>

这里我们尝试修改mask的值，再来看lw的**实际有效权限**。

    setfacl -m m:rx movie
    <!-- m:rx 表示修改文件的mask的值，这里修改成rw-，那么再按照上面的方法计算一下最后的结果 -->
    <!-- 
    ACL      : r - x
    mask     : r w -
    effective: r - -
     -->

下面我们再查看一下

    getfacl movie/
    <!--
    \# file: movie/
    \# owner: root
    \# group: friends
    user::rwx
    user:lw:r-x         #effective:r--
    group::rwx          #effective:rw-
    mask::rw-
    other::---
    \#这里在lw和group那里都出现了一行注释，提示说实际拥有的有效权限就变成了与mask逻辑与之后的值了
    -->

### 删除ACL权限：setfacl -x u/g:用户名/组名 文件名

### 删除一个文件所有ACL权限：setfacl -b 文件名

### 递归ACL权限：setfacl -m u/g:用户名/组名：权限 -R 目录名

> 递归ACL权限是指父目录在设定ACL权限时，所有子文件和子目录也会拥有相同的ACL权限。

结合上面的老王的例子来说，我们为老王创建了关于movie目录的权限，可是针对movie目录内部的文件，老王还是没有权限的，我们只是允许老王查看里面的内容，可是却没有赋予老王查看目录内具体的文件内容的权限，这样老王是不会满意的，可是如果里面有成百上千的文件，那么我们需要把每一个文件都添加上一个老王的ACL权限，那么也是不可能的，所以这里就使用递归创建ACL权限。

    setfacl -m u:lw:rx -R movie
    <!-- 接着在这个目录内创建一个新文件 -->
    cd movie/
    touch for_lw.avi
    <!-- 接着你查看一下for_lw.avi的ACL权限信息 -->
    getfacl for_lw.avi
    <!-- 
    \# file: for_lw.avi
    \# owner: root
    \# group: root
    user::rw-
    user:lw:r-x     #这里默认为lw对该文件添加了一个ACL权限，该权限与lw对movie的权限一致
    group::r--
    mask::r-x
    other::r--
     -->

<font color="blue">
注意，这里有一个危险的地方，前面我们讲过，文件在创建时是没有x权限的，可是刚才我们为了lw能进入该目录，所以我们给了lw对目录movie的x权限，在递归创建ACL权限后，目录下文件的权限就被修改成rx。
</font>

如：

    <!-- 当前在movie目录下 -->
    touch for_everyone.avi
    <!-- 查看文件的权限为：rw-r--r-- -->
    <!-- 接着退到上一级目录后递归创建ACL权限 -->
    cd ..
    setfacl -m u:lw:rx -R movie/
    <!-- 再来查看for_every.avi的权限 -->
    ll
    <!-- 权限就变成了：rw-r-xr--+ -->

在未执行递归创建ACL权限之前：

- rw- r-- r--

执行递归创建之后：

- rw- r-x r-- +

这时就为文件创建了可执行权限，这就叫*权限溢出*。

-----------------------

接着再思考一种情况，在运行完 setfacl -m u:lw:rx -R movie/ 后，我再在里面新建文件，比如 for_myself.avi 文件，那么这个文件是否也会有针对lw的ACL权限呢？答案是没有的，那么我为了lw，每一次再其中新添加一个文件都要执行一遍setfacl，那不是很麻烦吗？所以为了新建的文件也有ACL权限，就要用到下面的知识。

### 默认ACL权限：setfacl -m d:u/g:用户名/组名：权限 目录名

>   默认ACL权限的作用是如果给父目录设定了默认ACL权限，那么父目录中所有新建的子文件都会继承父目录的ACL权限。

    <!-- 这里先设定一下默认ACL权限 -->
    setfacl -m d:u:lw:rx movie/
    <!-- 再在movie目录下新建一个文件 -->
    touch movie/lw.avi
    <!-- 这个时候新建的lw.avi就有了一个默认ACL权限 -->
    getfacl movie/lw.avi
    <!-- 
    \# file: movie/lw.avi
    \# owner: root
    \# group: root
    user::rw-
    user:lw:r-x         #effective:r--
    group::rw-
    mask::rw-           #这里默认最大权限是不会给x权限的，额外话
    other::---
     -->


