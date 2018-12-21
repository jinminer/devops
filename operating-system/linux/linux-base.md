# 基础文件

> 参考资料：
>
> [**《鳥哥的 Linux 私房菜》**](http://linux.vbird.org/linux_basic/) 
>
> **Linux man function** 



## 文件属性与目录配置



> - 多人多任务环境
> - 用户和文件的安全管理机制



### 使用者与组群

* 用户权限：`owner/group/other`
  * 为毛`Linux`要搞个`owner`拥有者、`group`用户组、`other其他人？
  * `Linux`支持多人同时作业，既然是多人，那么这些人直接必有其差异性。最直接地，同一个文件对于不同的人有不同的操作权限，包括`read`可读、`write`可写、`execute`可执行，这些访问限制的约定极大的保证了系统的安全性，在用户协同作业方面更是独领风骚。



* 用例：互联网公司技术部
  * 人员构成
    * 首席技术官CTO
    * 老王负责的支付项目组：老王、赵四、张三
    * 旺财负责的电商项目组：旺财、二哈、狗蛋
  * 资源：CTO办公室、休息室、厕所、支付项目组工作区、电商项目工作区



![user-group](https://github.com/jinminer/docs/blob/master/devops/operating-system/linux/user-group.png)



  * 不同的人`user`有不同的工作区域，不同的资源有不同的访问权限。
    * 文件的`owner`所有者，默认是刚开始创建它的那个用户，也可以修改。比如赵四写了一份技术文档，文档的所有者是赵四，某一天赵四离职了，把技术文档交接给了张三，这个时候该文档的`owner`就变成了张三。
    * 相同组`group`的`user`对组内的公共技术文档都有访问权。
    * 每个`user`也有自己的私密文件(比如工资条)，只有CTO和自己知道。当然在自己允许的情况下，别人也可以知道。
    * 不同组的用户关系都是`other`，即老王对于旺财是other，反过来旺财也是老王的`other`。
    * 非组内成员`other`，在未被允许时不能访问组内的资源。
    * 公共资源大家都可以访问，比如厕所和休息室。
    * 系统级别的配置只有`root`超级用户才能访问，比如CTO办公室只有CTO有权出入。
    * root超级用户具有系统内所有资源操作权限，在`Linux`系统中权力大到无边，可以为所欲为。所以`root`用户的私有信息必须严格保密，就像CTO办公室一样不会对外开放。



### Linux 文件/目录权限

以`root`用户登录`Linux`，执行`ls -a`指令，列出当前目录`/root`所有文件和目录以及各自的详细属性。

![file-list](https://github.com/jinminer/docs/blob/master/devops/operating-system/linux/file-list.png)

* 如图所示，每一行代表一个文件/目录的所有信息，不同的列表示文件/目录的不同属性，共有7项属性。以`.oh-my-zsh`文件为例：



  ```powershell
    drwxr-xr-x  11 root root  4096 Sep  4 23:16 .oh-my-zsh
  ```



    ![file-detail](https://github.com/jinminer/docs/blob/master/devops/operating-system/linux/file-detail.png)



  * I. 文件/目录的名称。
  * II. 文件/目录的最近修改日期，格式为`月份 日期 时间`。新建新文件/目录，日期值是它的创建时间。
  * III. 文件/目录的容量大小。
  * IV. 文件/目录的`group`，所属的用户组。
  * V.  文件/目录`own`，所有者。
  * VI. 连结节点数
    * 文件：被占用的节点数`i-node`，即该文件有几个链接，和`link file`有关。
    * 目录：该目录下有几个目录，要注意只是指直接目录的数目，不包含子目录，也不包含文件数目。
  * VII. 文件/目录的操作属性：`read/write/execute`

#### 文件/目录的操作属性

每个文件/目录的操作属性共有10个以`.oh-my-zsh`文件的操作属性为例



```spreadsheet
 drwxr-xr-x 
```



![](https://github.com/jinminer/docs/blob/master/devops/operating-system/linux/file-method-detail.png)



* I. 类型
  * `[ d ]` 目录
  * `[ - ]` 文件
  * `[ l ]` 链接文件(link file) ，可以理解为windows中的快捷方式
  * `[ b ]` 装置文件中可供存储的接口设备
  * `[ c ]` 装置文件中的串行接口设备，如：键盘、鼠标等。

* II. `own`的操作权限属性
* III. 当前用户组(与`own`处于相同的`group`)操作权限属性
* IV. other(非本组`user`)的操作权限属性
* 操作权限属性`wxr`
  * `[r]` -> `read `可读
  * `[w]` -> `write `可写
  * `[x]` -> `execute`  可执行
  * 注意：一个`user`想要具有进入目录的权限，该目录的操作属性设置必须为`wrx` 或 `-rx` ,即必须同时满足`[r]`和`[x]`权限。

#### 修改group用户组



```shell
chgrp [-R] groupname dirname/filename
```



* `chgrp` -> `change group`
* `[-R]` 当对某一目录执行指令时，进行递归`recursive`形式的持续变更，该指令的执行结果对目录下的所有文件和目录都会生效。



#### 修改own拥有者



```shell
chown [-R] username	dirname/filename

chown [-R] username:groupname dirname/filename
```



* `chown` -> `change owner`
* 切换到`root`权限执行`chown`



#### 修改操作属性

* I. 数字型

  * 文件/目录的操作权限包含3个组别`owner/group/others`和3个基本操作属性`read/write/execute`。3个组别中的不同角色对file的操作属性分别可进行如下设置

    * `r` -> `read` 权限值：4
    * `w` -> `write `权限值：2
    * `x` -> `execute` 权限值：1
    * `-` -> `denied` 拒绝访问 权限值:   0

  * 以`rwxr-x---`为例，不同`user`对应的`method-permission-value`如下：

    * `owner = 4 + 2 + 1 = 7 `
    * `group = 4 + 0 + 1 = 5`
    * `other = 0 + 0 + 0 = 0` 



    ```shell
    # v1=owner-value; v2=group-value; v3=other-value;
    chmod [-R] v1v2v3 filename/dirname
    chmod -R 750 test
    ```

  * 这些权限值可以是`0,1,2,4`这四个数字中任意三个之和。

* II. 符号型

  * 角色 `user/group/others`
    * `[ u ]` -> `own` 拥有者
    * `[ g ]` -> `group` 所属用户组
    * `[ o ]` -> `others` 非组内用户
    * `[ a ]` -> `all` 所有用户

  * 运算法则
    * `[ + ]` 加入
    * `[ - ]` 去除
    * `[ = ]` 赋值

  * `[rwx]`读写属性


```shell
# 对 指定用户角色 赋予指定的操作权限
# user(u) 具有可读、可写、可执行权限；group/others(g/o)具有可读可执行权限 
chmod u=rwx,go=rx test.txt

# 对 所有角色 统一添加某个操作权限
# all(a) 
chmod a+x test.txt

# 对 所有角色 统一去除某个操作权限
# all(a) 
chmod a-r test.txt
```



#### 目录操作属性详解

* `[ r ]` -> `read contents in directory`

  * `beyond`和`jinm`属于同一个`group`，登录到`jinm`用户修改目录访问权限，再切换到`beyond`用户验证权限。

  * 当user对某个文件具有`[ r ]`权限时，才可以查看该目录中内容



    ```shell
    drwx------ 4 jinm   jinm 4096 Dec 14 14:31 test
    [beyond@localhost jinm]$ ls test
    ls: cannot open directory test: Permission denied
    ```

  * 但是需要注意的是user必须同时具有`[ r ]`和`[ x ]`权限时才能查看详细信息



    ```shell
    [beyond@localhost jinm]$ ll
    drwxr----- 4 jinm   jinm 4096 Dec 14 14:31 test
    [beyond@localhost jinm]$ ls test
    ls: cannot access test/c.txt: Permission denied
    ls: cannot access test/a: Permission denied
    ls: cannot access test/b: Permission denied
    a  b  c.txt
    ```

  * user必须同时具有`[ r ]`和`[ x ]`权限时才能进入该目录



    ```shell
    drwx-w---- 4 jinm   jinm 4096 Dec 14 14:31 test
    [beyond@localhost jinm]$ cd test
    bash: cd: test: Permission denied
    ```

* `[ w ]` -> `modify contents of directory` 当给一个用户设定了目录`[ w ]`权限时，用户**进入该目录后**可进行以下操作：

  * 创建新的文件/目录
  * 删除已经存在的文件/目录(不管是属于谁)
  * 修改已经存在文件/目录的名称
  * 移动目录/文件的位置
  * 注意
    * I. 用户对某一目录持有`[ w ]`权限时，可以在该目录下为所欲为，所以分配此权限时需谨慎。
    * II. 以上操作内容必须在用进入该目录的前提下，即权限必须是`[ rwx ]`，未设置`[ r ]` 和`[ x ]` ，无法进入目录，一切操作都是免谈。

* `[ x ]` -> `access directory` 只有为用户设置了这个权限，才能进入。

#### 综合练习



```shell
#root用户登录
#新建用户组 组名：test：
groupadd test
#检查创建是否成功
cat /etc/group 
#文件最后一列应该是刚才创建的用户组名称
#创建用户 用户名:test1
useradd -d /home/test1 -m -g test test1
#设置权限：
chmod -R 770 /home/test1
#设置密码：
passwd test1
#userA登录：
su - test1
#查看用户路径是否正确：
pwd
#创建文件夹：
mkdir test1
#设置目录访问权限：
chmod -R 750 test1
#查看权限是否正确：
ls -l
```



### Linux 目录配置

* 文件名与文件的绝对路径(由`/`开始写起的文件名)有字符大小限制
  * 单一文件/目录允许定义名称的最大值为`255`个字符
  * 包含绝对路径的完整名称大小限制为`4096`个字符







## 磁盘与目录管理

* `df` 



  ```shell
  df [OPTION]... [FILE]...
  ```



  * `options`

    * `-a` 列出所有的文件系统，包括系统的虚拟文件。比如： `/proc` 等；

    * `-k` 以 `KBytes` 的容量显示各文件系统；

    * `-m ` 以 `MBytes` 的容量显示各文件系统；

    * `-h ` 以人们较易阅读的 `GBytes`, `MBytes`,` KBytes` 等格式自行显示；

    * `-H ` 以 `M=1000K` 取代 `M=1024K` 的进位方式；

    * `-T` 打印文件系统类型；

    * `-i ` 以 `inode` 的数量代替`block`来展示用量

  * `e.g.` 不指定参数，查看当前已挂载系统的所有可用空间



    ```shell
    # root @ localhost in ~ [22:10:53] 
    df 
    Filesystem     1K-blocks    Used Available Use% Mounted on
    /dev/vda1       41151808 1922400  37115976   5% /
    devtmpfs         1931336       0   1931336   0% /dev
    tmpfs            1940844       0   1940844   0% /dev/shm
    tmpfs            1940844     312   1940532   1% /run
    tmpfs            1940844       0   1940844   0% /sys/fs/cgroup
    tmpfs             388172       0    388172   0% /run/user/0
    ```

  * `e.g.` 查看当前已挂载系统的所有可用空间，并以易读的格式显示



    ```shell
    # root @ localhost in ~ [22:10:55] 
    df -h
    Filesystem      Size  Used Avail Use% Mounted on
    /dev/vda1        40G  1.9G   36G   5% /
    devtmpfs        1.9G     0  1.9G   0% /dev
    tmpfs           1.9G     0  1.9G   0% /dev/shm
    tmpfs           1.9G  312K  1.9G   1% /run
    tmpfs           1.9G     0  1.9G   0% /sys/fs/cgroup
    tmpfs           380M     0  380M   0% /run/user/0
    ```

  * `e.g.` 查看当前已挂载系统的所有可用空间，并展示文件系统的类型



    ```shell
    df -T
    Filesystem     Type     1K-blocks    Used Available Use% Mounted on
    /dev/vda1      ext4      41151808 1922408  37115968   5% /
    devtmpfs       devtmpfs   1931336       0   1931336   0% /dev
    tmpfs          tmpfs      1940844       0   1940844   0% /dev/shm
    tmpfs          tmpfs      1940844     312   1940532   1% /run
    tmpfs          tmpfs      1940844       0   1940844   0% /sys/fs/cgroup
    tmpfs          tmpfs       388172       0    388172   0% /run/user/0
    
    ```

  * `e.g.` 列出包括虚拟文件在内的所有文件系统的信息。



    ```shell
    df -a
    Filesystem     1K-blocks    Used Available Use% Mounted on
    rootfs                 -       -         -    - /
    sysfs                  0       0         0    - /sys
    proc                   0       0         0    - /proc
    devtmpfs         1931336       0   1931336   0% /dev
    securityfs             0       0         0    - /sys/kernel/security
    tmpfs            1940844       0   1940844   0% /dev/shm
    devpts                 0       0         0    - /dev/pts
    tmpfs            1940844     368   1940476   1% /run
    tmpfs            1940844       0   1940844   0% /sys/fs/cgroup
    cgroup                 0       0         0    - /sys/fs/cgroup/systemd
    pstore                 0       0         0    - /sys/fs/pstore
    cgroup                 0       0         0    - /sys/fs/cgroup/devices
    cgroup                 0       0         0    - /sys/fs/cgroup/hugetlb
    cgroup                 0       0         0    - /sys/fs/cgroup/cpu,cpuacct
    cgroup                 0       0         0    - /sys/fs/cgroup/net_cls,net_prio
    cgroup                 0       0         0    - /sys/fs/cgroup/memory
    cgroup                 0       0         0    - /sys/fs/cgroup/cpuset
    cgroup                 0       0         0    - /sys/fs/cgroup/perf_event
    cgroup                 0       0         0    - /sys/fs/cgroup/pids
    cgroup                 0       0         0    - /sys/fs/cgroup/blkio
    cgroup                 0       0         0    - /sys/fs/cgroup/freezer
    configfs               0       0         0    - /sys/kernel/config
    /dev/vda1       41151808 1922452  37115924   5% /
    systemd-1              -       -         -    - /proc/sys/fs/binfmt_misc
    mqueue                 0       0         0    - /dev/mqueue
    debugfs                0       0         0    - /sys/kernel/debug
    hugetlbfs              0       0         0    - /dev/hugepages
    tmpfs             388172       0    388172   0% /run/user/0
    binfmt_misc            0       0         0    - /proc/sys/fs/binfmt_misc
    tmpfs             388172       0    388172   0% /run/user/1000
    
    ```

* `du`



  ```shell
  du [OPTION]... [FILE]...
  du [OPTION]... --files0-from=F
  ```



    * `options`

        * `-a` 统计当前目录下，所有文件和子目录的占用量，多级子目录递归展示；
        * `-h`以人们较易读的容量格式 `G/M`显示；
        * `-s` 仅仅展示当前目录的总占用量，不会显示目录中的文件和子目录的占用量；
        * `-k` 以 `KBytes` 列出容量显示；
        * `-h` 以 `MBytes `列出容量显示；

    * `e.g.` 递归显示当前目录下，所有子目录的占用量。



      ```shell
      du
      4	./test/a
      4	./test/b
      12	./test
      4	./dir-jinm/test
      8	./dir-jinm
      52	.
      ```

    * `e.g.` 递归显示当前目录下，所有子目录以及文件的占用量。



      ```shell
      du -a
      0	./test1.txt
      4	./.zshrc
      4	./.bash_profile
      0	./test/c.txt
      4	./test/a
      4	./test/b
      12	./test
      4	./dir-jinm/test
      8	./dir-jinm
      4	./.bashrc
      4	./.bash_logout
      4	./.viminfo
      4	./file-jinm.txt
      4	./.bash_history
      52	.
      ```

    * `e.g.` 统计当前目录的总占用量。



      ```shell
      du -s
      52	.
      ```

    * `e.g.` 查看当前目录下，每个目录和文件各自的占用量，不包括隐藏文件，亦不会递归展示子目录。



      ```shell
      #使用通配符 * 匹配当前目录下的所有一级子目录和一级子文件。
      du -sm /home/jinm/*
      1	/home/jinm/dir-jinm
      1	/home/jinm/file-jinm.txt
      1	/home/jinm/test
      0	/home/jinm/test1.txt
      ```



* `ln`

  * `hard link` 硬式链接/实际链接；一个或多个文件指向某个源文件(一个或多个指针指向了某个源文件的`inode`)，该实体文件的`inode` 和 `block` 不会 随着‘指针’的改变而改变，也不会占用实体文件的`inode` 和 `block` 。

    * 不能跨`filesystem`域建立链接
    * 不能链接目录，`link` 只能指向硬盘中某个实体文件的`inode` 

  * `symbolic link` 创建一个独立的链接文件，会占用源文件的 `inode` 和 `block` ，当指向的源文件被删除时该链接文件就不能打开。

  * `hard link`指向源文件的`inode`，`symbolic link`链接指向源文件的`inode`的指针。所以只要硬盘中的数据还在，`hard link`就不会失效；对于`symbolic link`而言，只要他指向的文件指针失效，它既失效。

  * `options` 



    ```shell
    ln [OPTION] TARGET DIRECTORY                     
    ```



    * `ln` 不加任何参数，就是`hard link` 
    * `-s` 就是`symbolic link` 
    * `-f` 删除已存在的目标文件，即如果目标文件已存在，会主动删除后再建立

  * `e.g.` 将`/etc/passwd` 复制到`/tmp`底下，查看`inode` 和 `block` 



    ```shell
    # root @ localhost in /tmp [10:11:54] 
    cp -a /etc/passwd .
    
    # root @ localhost in /tmp [10:12:25] 
    du -sb passwd
    1123	passwd
    
    # root @ localhost in /tmp [10:12:34] 
    du -sb /etc/passwd
    1123	/etc/passwd
    
    # root @ localhost in /tmp [10:12:50] 
    df -i passwd
    Filesystem      Inodes IUsed   IFree IUse% Mounted on
    /dev/vda1      2621440 43634 2577806    2% /
    # root @ localhost in /tmp [10:12:59] 
    $ df -i /etc/passwd
    Filesystem      Inodes IUsed   IFree IUse% Mounted on
    /dev/vda1      2621440 43634 2577806    2% /
    ```

  * `e.g.` 将 `/tmp/passwd` 制作 `hard link` 成为 `passwd-hd` 文件。查看 `passwd` 和 `passwd-hd`文件的查看`inode` 和 `block` 。



    ```shell
    # root @ localhost in /tmp [10:14:53] 
    ln passwd passwd-hd
    # root @ localhost in /tmp [10:15:11] 
    ll 
    -rw-r--r-- 2 root root 1.1K Dec 18 05:07 passwd
    -rw-r--r-- 2 root root 1.1K Dec 18 05:07 passwd-hd
    
    # root @ localhost in /tmp [10:15:16] 
    du -sb passwd-hd
    1123	passwd-hd
    
    # root @ localhost in /tmp [10:15:58]  
    ls -il passwd*
    393226 -rw-r--r-- 2 root root 1123 Dec 18 05:07 passwd
    393226 -rw-r--r-- 2 root root 1123 Dec 18 05:07 passwd-hd
    
    ```

  * `e.g.` 将将 `/tmp/passwd` 制作 `symbolic link` 成为 `passwd-so` 文件，查看容量。



    ```shell
    # root @ localhost in /tmp [10:17:25] 
    ln -s passwd passwd-so
    
    # root @ localhost in /tmp [10:30:32] 
    ll 
    -rw-r--r-- 2 root root 1.1K Dec 18 05:07 passwd
    -rw-r--r-- 2 root root 1.1K Dec 18 05:07 passwd-hd
    lrwxrwxrwx 1 root root    6 Dec 18 10:30 passwd-so -> passwd
    
    
    # root @ localhost in /tmp [10:30:34] 
    ls -li passwd*
    393226 -rw-r--r-- 2 root root 1123 Dec 18 05:07 passwd
    393226 -rw-r--r-- 2 root root 1123 Dec 18 05:07 passwd-hd
    393355 lrwxrwxrwx 1 root root    6 Dec 18 10:30 passwd-so -> passwd
    
    ```



    `passwd-so`指向的`inode number`变了，这是一个新的文件。这个文件的内容时指向`passwd`的，大小为`6bytes`，这个大小式因为链接文件的内容是连结对象的文件名，也就是`passwd`，所以你需要连结的源文件名(可以是目录)有多少个字符，这个链接文件就有多大。而且这时候我们发现硬盘容量和`inode`的使用数都变了。

  * `e.g.` 删除源文件`passwd`查看`passwd-hd`和`passwd-so`是否正常，能够开启？



    ```shell
    # root @ localhost in /tmp [10:44:38] 
    cat passwd-hd
    root:x:0:0:root:/root:/usr/bin/zsh
    bin:x:1:1:bin:/bin:/sbin/nologin
    daemon:x:2:2:daemon:/sbin:/sbin/nologin
    
    # root @ localhost in /tmp [10:44:46] 
    cat passwd-so
    cat: passwd-so: No such file or directory
    ```



## 文件与目录管理

---

### 目录与路径

* 相对路径绝对路径
  * 绝对路径：路径由根目录`/`开始，例如`/usr/share/doc`
  * 相对路径：路径由当前目录开始，例如从`/usr/share/doc`进入`/usr/share/man` 使用 `cd ../man` 即可。

* 特殊目录
  * `.` 表示当前目录，即当前工作目录
  * `..` 表示当前目录的上一层目录。注意: 根目录`/` 的`..` 是它自己本身`.` 
  * `-` 进入当前目录之前，所处的工作目录
  * `~` 当前登录的用户的家目录
  * `~username`  代表 `username` 这个用户的家目录

* 目录操作指令
  * `cd`  -> `change directory` 切换目录

  * `pwd` -> `print working directory` 查看当前工作目录的绝对路径

    * `-P` -> `physical` 查看真实路径。即对于 `link` 路径，会显示完整路径。

  * `mkdir`  -> `make directory` 创建一个新的目录

  * 

    ```shell
    mkdir [options] file
    ```



    * `-m` -> `mode` 创建目录并设置该目录的操作权限。如果不加该参数，系统会预设权限值。



      ```shell
      mkdir -m 711 test
      ```

    * `-p` -> `parents` 递归创建多级目录，如果路径默认已经存在则沿用旧目录

      ```shell
      mkdir -p dir1/dir2/dir3
      ```

  * `rmdir` -> `remove directory` 删除目录，只能删除空目录

    * `-p` 递归删除多级空目录；e.g. `'rmdir -p a/b/c' is similar to 'rmdir a/b/c a/b a'` 
    * `rm -rf directory` 强制删除目标目录中的所有内容，删库跑路喽~~~

* `$PATH` 配置全局环境变量

  * 将某个路径配置到该变量后，在任意目录下都可执行该路径中的可执行文件



    ```shell
    PATH="$PATH":/directory
    ```

  * 不配置`PATH` 使用绝对路径来访问，前提知道要执行文件的准确绝对路径



    ```shell
    /directory/filename
    
    ./directory/filename
    ```

  * 当`PATH` 中配置了同一个可执行文件的多个路径，则按照配置的顺序，系统先查到谁就会执行谁



### 文件/目录管理

* `ls` -> `list directory contents` 列出文件/目录的信息

  * `synopsis` 



    ```shell
    ls [OPTION]... [FILE]...
    ```

  * `options` 

    * `-a` -> `--all` 列出目标目录下所有的子目录/文件，包括以`.`开头的文件/目录

    * `-A` -> `--almost-all` 列出目标目录下所有的子目录/文件，不包括以`.`和`..`开头的文件/目录

    * `-d` -> `--directory` 列出目标目录自己本身，不包括目录下的其他内容

    * `-f` 列出目标目录下所有的子目录/文件，但不会对列出的内容进行排序，也不会用颜色区分文件/目录的类型

    * `-F` -> `--classify` 列出目标目录下所有的子目录/文件，并在每个条目后面追加文件/目录的类型`(one of */=>@|)`，不包括以`.`和`..`开头的文件/目录

    * `-h` -> `--human-readable` 列出目标目录下所有的子目录/文件，并以人易读的方式展示容量大小，不包括以`.`和`..`开头的文件/目录。`e.g., 1K 234M 2G`

    * `-i` -> `--inode` 列出目标目录下所有的目录/文件，并展示文件的`inode`索引，不包括以`.`和`..`开头的文件/目录。

    * `-l` -> `--all` 以长列表格式列出目标目录下的所有文件，不包括以`.`和`..`开头的文件/目录。

    * `-n` -> `--numeric-uid-gid` 以长列表格式列出目标目录下的所有文件，并展示`userid`和`groupid`的数字值，不包括以`.`和`..`开头的文件/目录。

    * `-r` -> `--reverse`  倒序列出目标目录下所有的目录/文件，不包括以`.`和`..`开头的文件/目录。

    * `-R` -> `--recursive` 递归显示目标目录下所有的子目录/文件，不包括以`.`和`..`开头的文件/目录。

    * `-S` 列出目标目录下所有目录/文件，并以文件容量大小的倒序显示，不包括以`.`和`..`开头的文件/目录。

    * `-t` 列出目标目录下所有的目录/文件，并以最近修改时间倒序显示，不包括以`.`和`..`开头的文件/目录。

    * `--color=[OPTION]` 列出目标目录下所有目录/文件，不包括以`.`和`..`开头的文件/目录。

      * `never` 不根据文件类型显示相应的颜色

      * `auto ` 系统自行判断是否根据文件类型显示颜色

      * `always ` 根据文件类型显示相应的颜色


      ```shell
      ls --color=auto jinm/
      dir-jinm  file-jinm.txt  test  test1  test1.txt  test2  test3
      
      # root @ localhost in /home [0:35:07] 
      ls --color=never jinm/
      dir-jinm  file-jinm.txt  test  test1  test1.txt  test2  test3
      
      # root @ localhost in /home [0:35:18] 
      ls --color=always jinm/
      dir-jinm  file-jinm.txt  test  test1  test1.txt  test2  test3
      ```

    * `--full-time` 以长列表的方式列出目标目录下所有文件/目录，并展示详细的最近修改时间`year-month-day hour:minutes:seconds.millisecondstimestamp` ，不包括以`.`和`..`开头的文件/目录。

      ```shell
      # root @ localhost in /home [0:35:27] 
      ls --full-time jinm/
      drwxr-xr-x 3 beyond jinm 4096 2018-12-13 17:25:35.211053250 +0800 dir-jinm
      -rw-rw-r-- 1 jinm   jinm   17 2018-12-13 16:37:44.331346441 +0800 file-jinm.txt
      drwx-w---- 2 jinm   jinm 4096 2018-12-18 16:05:32.386878919 +0800 test
      drwxrwxr-x 3 root   root 4096 2018-12-18 16:06:17.452924751 +0800 test1
      -rw-r--r-- 1 beyond jinm    0 2018-12-13 17:56:32.577884166 +0800 test1.txt
      drwxrwxr-x 2 root   root 4096 2018-12-18 15:52:59.109674461 +0800 test2
      drwxrwxr-x 2 root   root 4096 2018-12-18 15:54:09.907889943 +0800 test3
      ```

    * `--time=[OPTION]` 根据权限修改时间列出目标目录中的文件/目录

      * `atime` 以最近修改文件操作权限时间的倒序展示

      * `ctime` 以最近修改用户组/拥有者权限时间的倒序展示


      ```shell
      # 示例目录初始状态
      # root @ localhost in ~jinm [1:01:49] 
      ls -l
      drwxr-xr-x 3 beyond jinm 4096 Dec 13 17:25 dir-jinm
      -rw-rw-r-- 1 jinm   jinm   17 Dec 13 16:37 file-jinm.txt
      drwx-w---- 2 jinm   jinm 4096 Dec 18 16:05 test
      drwxrwxrwx 3 root   root 4096 Dec 18 16:06 test1
      -rw-r--r-- 1 beyond jinm    0 Dec 13 17:56 test1.txt
      drwxrwxr-x 2 root   root 4096 Dec 18 15:52 test2
      drwxrwxr-x 2 root   root 4096 Dec 18 15:54 test3
      ----------------------------------------------------------------------------------------------
      
      # 修改test的拥有者
      # root @ localhost in ~jinm [1:02:19] 
      chown beyond test
      
      # root @ localhost in ~jinm [1:05:20] 
      ls --time=ctime 
      test  test1  test3  test2  test1.txt  dir-jinm  file-jinm.txt
      ----------------------------------------------------------------------------------------------
      
      # 修改test1的文件操作权限
      # root @ localhost in ~jinm [1:19:51] 
      $ chmod 700 test1
      
      # root @ localhost in ~jinm [1:21:02] 
      ls --time=atime 
      test1  test  dir-jinm  test2  test3  test1.txt  file-jinm.txt
      ```




* `cp` -> `copy files and directories` 

  * `synopsis` 



    ```shell
    cp -[options] source target
    
    # 将多个目录一次性复制到同一个目标目录中去
    cp -[options] source1 source2 ... sourcen target
    ```

  * `options` 

    * `-a` -> `--archive` 等同于 `-pdr` 
    * `-d` -> `--no-dereference --preserve=links`  若来源为`link file` 时，复制该链接文件的属性而非被`link` 的真实文件的`inode`。
    * `-f` -> `--force`  强制复制。如果目标目录无法访问(比如文件重复等)，则删除该目录，并且重试。
    * `-i` -> `--interactive` 若复制过程中出现覆盖情况，则会进行提示。
    * `-l` -> `--link` 复制到到目标目录内容的`hard link` 而不是文件本身
    * `-p` -> `--preserve`  将复制内容的文件属性也进行复制，文件属性保持原状，不会由系统预设。
    * `-r` -> `--recursive` 递归复制源目录，包括其子目录和子文件。
    * `-s` -> `--symbolic-link` 以符号链接`symbolic link`的方式复制内容。
    * `-u` -> `--update` 当`source` 是新建的，`destination` 被删除了，才会执行复制过程。当`destination`与`source`有差异时才进行复制，可以用于备份等工作。

  * 当前用户必须要有`source` 的`read`权限才能进行复制

  * 复制过程中会改变源文件的`owner`和`group` 属性，谁复制就会变成谁的。当某些文件权限要求较高(比如密码文件`/etc/shadow`)，在复制是加上`-a` 或 `-p` 等属性保证访问安全性。



* `rm` ->  `remove files or directories` 

  * `synopsis` 



    ```shell
    rm [option] file
    ```

  * `options` 

    * `-f` -> `--force` 强制删除目标文件/目录，忽略不存在的参数和文件，并且没有提示。
    * `-i` 删除目标文件/目录时会进行提示，询问操作者是否确定执行删除动作。删除多级目录时，也会逐个提示。
    * `-r` -> `--recursive` 递归删除目标目录，包括其子目录和文件。

  * `e.g.` 某些文件或目录名称中带有关键字符 比如 `-` 等，执行相关命令操作时，系统无法识别 `-` 后面的文件名称(当中指令的参数)，可以用`./filename`等方式执行命令。



    ```shell
    # root @ localhost in /home/jinm [11:45:19] 
    ll 
    -rw-r--r-- 1 root   root    0 Dec 19 11:45 -hello-.txt
    
    # root @ localhost in /home/jinm [11:45:20] 
    rm -hello-.txt
    rm: invalid option -- 'h'
    Try 'rm ./-hello-.txt' to remove the file ‘-hello-.txt’.
    Try 'rm --help' for more information.
    --------------------------------------------------------------------------------------------------
    # root @ localhost in /home/jinm [11:49:52] C:1
    rm ./-hello-.txt
    ```



* `mv` -> `move (rename) files` 

  * `synopsis` 



    ```shell
    mv [OPTION] SOURCE DIRECTORY
    mv [OPTION] SOURCE1,SOURCE2...SOURCEN DIRECTORY
    ```

  * `option`

    * `-f` -> `--force` 强制移动目标文件/目录，文件覆盖没有提示。
    * `-i` -> `--interactive` 覆盖之前进行提示。
    * `-u` -> `--update` 移动`destination` 中没有或者最近修改时间比 `source`老的文件。



* 获取文件/目录名称和所处的路径



  ```shell
  # root @ localhost in /home/jinm [15:36:13] 
  ll 
  drwx-w---- 2 beyond jinm 4.0K Dec 18 16:05 test
  
  # root @ localhost in /home/jinm [15:36:43] 
  basename /home/jinm/test
  test
  
  # root @ localhost in /home/jinm [15:37:04] 
  dirname /home/jinm/test
  /home/jinm
  ```



### 查看文件内容

* `cat` -> `concatenate` 

  * `synopsis` 



    ```shell
    cat [OPTION]... [FILE]...
    ```

  * `options`

    * `-A` -> `--show-all` 查看目标文件的内容，等同于`-vET`。
    * `-E` -> `--show-ends` 查看目标文件的内容，并展示每行的断尾符`$`。
    * `-n` -> `--number` 查看目标文件的内容，并打印行号。
    * `-T` -> `--show-tabs` 查看目标文件的内容，并将内容中的制表符`TAB` 以`^I`显示出来。
    * `-v` -> `--show-nonprinting` 查看目标文件的内容，并显示内容中包含的特殊符号，不包括`LFD and TAB`。

  * 在` Linux` 是以 `$` 为断行字符，而在 `Windows` 则是以` ^M$ `为断行字符。



* `tac` -> `concatenate in reverse`

  * `synopsis` 



    ```shell
    tac [OPTION]... [FILE]...
    ```

  * `cat` 是按行号顺序显示内容，`tac` 恰好相反是按行号的倒序显示



* `nl` -> `number lines of files` 

  * `synopsis`



    ```shell
    nl [OPTION]... [FILE]...
    ```

  * `options`

    * `-b` -> `--body-numbering=STYPE` 查看目标文件的内容，并打印行号。
      * `a` 不论是否为空行都打印行号 ，等同于 `cat -n` 

      * `t` 如果是空行不打印行号


    ```shell
    # root @ localhost in /home/jinm [16:50:12] 
    ll 
    -rw-r--r-- 1 jinm   jinm   67 Dec 19 16:49 hello.txt
    
    # root @ localhost in /home/jinm [16:50:14] 
    nl -b t hello.txt
         1	jjkfdsl
           
         2	jkdsljf
         3	324234
         4	435342$%$#%
         5	%&^*%^#$%@#
         6	32423432
         7	dgdfgerre
    
    # root @ localhost in /home/jinm [16:50:29] 
    nl -b a hello.txt
         1	jjkfdsl
         2	
         3	jkdsljf
         4	324234
         5	435342$%$#%
         6	%&^*%^#$%@#
         7	32423432
         8	dgdfgerre
    ```



    * `-n` -> `--number-format=FORMAT` 查看目标文件的内容，并以给定的位置打印行号。

      * `ln` 行号在屏幕的最左方显示

      * `rn` 行号在自己字段`-n`的最右方显示，且不加`0`

      * `rz` 行号在自己字段`-n`的最左方显示，且加`0` 


    ```shell
    # root @ localhost in /home/jinm [16:59:54] 
    ll 
    -rw-r--r-- 1 jinm   jinm   67 Dec 19 16:49 hello.txt
    
    # root @ localhost in /home/jinm [16:51:29] 
    $ nl -n ln hello.txt
    1     	jjkfdsl
           
    2     	jkdsljf
    3     	324234
    4     	435342$%$#%
    5     	%&^*%^#$%@#
    6     	32423432
    7     	dgdfgerre
    
    # root @ localhost in /home/jinm [17:00:25] C:1
    $ nl -n rn hello.txt
         1	jjkfdsl
           
         2	jkdsljf
         3	324234
         4	435342$%$#%
         5	%&^*%^#$%@#
         6	32423432
         7	dgdfgerre
    
    # root @ localhost in /home/jinm [17:00:51] 
    $ nl -n rz hello.txt
    000001	jjkfdsl
           
    000002	jkdsljf
    000003	324234
    000004	435342$%$#%
    000005	%&^*%^#$%@#
    000006	32423432
    000007	dgdfgerre
    ```



    * `-w` -> `--number-width=NUMBER` 查看目标文件的内容，并以指定的字符长度打印行号。


    ```shell
    # root @ localhost in /home/jinm [17:06:24] 
    $  nl -n rz hello.txt
    000001	jjkfdsl
           
    000002	jkdsljf
    000003	324234
    000004	435342$%$#%
    000005	%&^*%^#$%@#
    000006	32423432
    000007	dgdfgerre
    
    # root @ localhost in /home/jinm [17:06:39] 
    $  nl -n rz -w 3 hello.txt
    001	jjkfdsl
        
    002	jkdsljf
    003	324234
    004	435342$%$#%
    005	%&^*%^#$%@#
    006	32423432
    007	dgdfgerre
    
    # root @ localhost in /home/jinm [17:06:51] 
    $  nl -n rz -w 2 hello.txt
    01	jjkfdsl
       
    02	jkdsljf
    03	324234
    04	435342$%$#%
    05	%&^*%^#$%@#
    06	32423432
    07	dgdfgerre
    ```




* `more` -> `file perusal filter for crt viewing` 

  * 查看文件内容，当要查看的内容长度大于当前屏幕长度时，会在屏幕下方显示当前显示内容的百分百，不能向前翻页

  * `synopsis` 



    ```shell
    more -[options] file [...]
    ```

  * `operation` 

    * `space` 在`more` 运行时按下空格键，表示向下翻一页
    * `enter` 在`more` 运行时按下`enter` 键，表示向下翻一行
    * `/`  在`more` 运行时按下`/` 键，光标会停留在屏幕最后一行等待输入，输入字符串并按下`enter` 键，会向下查找输入的内容。
    * `:f` 在`more` 运行时依次按下`:` 键和`f`键，会在屏幕最后一行显示当前查看文件的文件名，以及当前显示内容所在的行数
    * `q` 在`more` 运行时按下`q` 键，就会退出`more` 程序。



* `less` -> `opposite of more` 
  * 可以向前向后翻阅内容，屏幕下方显示`:` 在这里可以输入相应的指令。
    * `space` 向下翻动一页
    * `pagedown` 向下翻动一页
    * `pageup` 向上翻动一页
    * `↑` 向下翻动一行
    * `↓` 向下翻动一行
    * `/`  等待输入，当输入字符串`target`后按回车`enter`，会向下搜索`target`
    * `?` 等待输入，当输入字符串`target`后按回车`enter`，会向上搜索`target`
    * `n` 重复上一个搜索动作，继续搜索目标字符串。在`/`和`?` 查找时用。
    * `n` 反向重复上一个搜索动作，继续搜索目标字符串。在`/`和`?` 查找时用。
    * `q` 退出`less` 搜索程序
    * `ctrl` +  `g` 直接到打文件内容的最后一页。老夫查看日志内容，一般用`ctrl` +  `g` 到尾页，然后`?` + `n`自底向上查找问题。



* `head` -> `output the first part of files` 

  * `synopsis` 



    ```shell
    head -[OPTION]... [FILE]...
    head -[OPTION] [number]... [FILE]...
    ```

  * `options`

    * `n` 具体的数字值，代表几行，即查看文件的前`n` 行。


    ```shell
    # root @ localhost in /home/jinm [18:01:30] 
    head -5 hello.txt
    jjkfdsl
    
    jkdsljf
    324234
    435342$%$#%
    
    # root @ localhost in /home/jinm [18:01:40] 
    head -n 5 hello.txt
    jjkfdsl
    
    jkdsljf
    324234
    435342$%$#%
    ```




* `tail` -> `output the last part of files` 

  * `synopsis` 



    ```shell
    tail [OPTION]... [FILE]...
    ```

  * `options` 

    * `n` 具体的数字值，代表几行，即查看文件的后 `n` 行。
    * `f` 前面接具体的`num`数字值，代表几行。要注意的是该参数表示实时查看，如果别查看的文件内容一直更改增加，增加的内容会按照`num`个行数不断`append`到屏幕末尾。 



* `od` -> `dump files in octal and other formats` 

  * `synopsis` 



    ```shell
    od [OPTION]... [FILE]...
    ```

  * 




## 文件的压缩与打包

---









## VI编辑器

---











## BASH Shell 初识

---

















