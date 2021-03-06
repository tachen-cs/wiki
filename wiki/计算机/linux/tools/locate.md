---



locate(locate) 命令用来查找文件或目录。 locate命令要比find -name快得多，原因在于它不搜索具体目录，而是搜索一个数据库/var/lib/mlocate/mlocate.db 。这个数据库中含有本地所有文件信息。Linux系统自动创建这个数据库，并且每天自动更新一次，



> 转载自：<https://www.cnblogs.com/xqzt/p/5426666.html>

## 1、命令简介

​        **locate**(locate) 命令用来查找文件或目录。 locate命令要比find -name快得多，原因在于它不搜索具体目录，而是搜索一个数据库/var/lib/mlocate/mlocate.db 。这个数据库中含有本地所有文件信息。Linux系统自动创建这个数据库，并且每天自动更新一次，因此，我们在用whereis和locate 查找文件时，有时会找到已经被删除的数据，或者刚刚建立文件，却无法查找到，原因就是因为数据库文件没有被更新。为了避免这种情况，可以在使用locate之前，先使用updatedb命令，手动更新数据库。整个locate工作其实是由四部分组成的:

1. /usr/bin/updatedb   主要用来更新数据库，通过crontab自动完成的
2. /usr/bin/locate         查询文件位置
3. /etc/updatedb.conf   updatedb的配置文件
4. /var/lib/mlocate/mlocate.db  存放文件信息的文件

## 2、用法

```
locate [OPTION]... [PATTERN]...
```

## 3、选项

```
  -b, --basename         match only the base name of path names
  -c, --count            只输出找到的数量
  -d, --database DBPATH  使用DBPATH指定的数据库，而不是默认数据库 /var/lib/mlocate/mlocate.db
  -e, --existing         only print entries for currently existing files
  -L, --follow           follow trailing symbolic links when checking file existence (default)
  -h, --help             显示帮助
  -i, --ignore-case      忽略大小写
  -l, --limit, -n LIMIT  limit output (or counting) to LIMIT entries
  -m, --mmap             ignored, for backward compatibility
  -P, --nofollow, -H     don't follow trailing symbolic links when checking file existence
  -0, --null             separate entries with NUL on output
  -S, --statistics       don't search for entries, print statistics about eachused database
  -q, --quiet            安静模式，不会显示任何错误讯息
  -r, --regexp REGEXP    使用基本正则表达式
      --regex            使用扩展正则表达式
  -s, --stdio            ignored, for backward compatibility
  -V, --version          显示版本信息
  -w, --wholename        match whole path name (default)
```

# 4、示例

##### 示例1： 搜索etc目录下所有以my开头的文件

```
[root@cent6 lib]# locate /etc/my
/etc/my.cnf
```

##### 示例2：新增的文件无法locate，使用updatedb

```
[root@cent6 ~]# touch new.txt
[root@cent6 ~]# locate new.txt
[root@cent6 ~]# updatedb
[root@cent6 ~]# locate new.txt
/root/new.txt
```

##### 示例3：updatedb的配置文件/etc/updatedb.conf

```
[root@cent6 ~]# cat /etc/updatedb.conf 
PRUNE_BIND_MOUNTS = "yes"
PRUNEFS = "9p afs anon_inodefs auto autofs bdev binfmt_misc cgroup cifs coda configfs cpuset debugfs devpts ecryptfs exofs fuse fusectl gfs gfs2 hugetlbfs inotifyfs iso9660 jffs2 lustre mqueue ncpfs nfs nfs4 nfsd pipefs proc ramfs rootfs rpc_pipefs securityfs selinuxfs sfs sockfs sysfs tmpfs ubifs udf usbfs"
PRUNENAMES = ".git .hg .svn"
PRUNEPATHS = "/afs /media /net /sfs /tmp /udev /var/cache/ccache /var/spool/cups /var/spool/squid /var/tmp"
```

第一行PRUNE_BIND_MOUNTS="yes"的意思是：是否进行限制搜索。

第二行是排除检索的文件系统类型，即列出的文件系统类型不进行检索。

第二行表示对哪些后缀的文件排除检索，也就是列在这里面的后缀的文件跳过不进行检索。不同后缀之间用空格隔开。

第四行是排除检索的路径，即列出的路径下的文件和子文件夹均跳过不进行检索。*updatedb*之后使用*locate*仍然找不到想要文件

可以检查挂载的目录是否被忽略了