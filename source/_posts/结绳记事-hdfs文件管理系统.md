---
title: '结绳记事: hdfs文件管理系统'
date: 2019-04-08 14:39:45
tags:
---

## hdfs文件管理系统
HDFS拾遗
知识拾遗
Hdfs权限
分析：是由于代码所设定的用户没有该权限的问题。
Hadoop分布式文件系统实现了一个和POSIX系统类似的文件和目录的权限模型。每个文件和目录有一个所有者（owner）和一个组（group）。文件或目录对其所有者、同组的其他用户以及所有其他用户分别有着不同的权限。对文件而言，当读取这个文件时需要有r权限，当写入或者追加到文件时需要有w权限。对目录而言，当列出目录内容时需要具有r权限，当新建或删除子文件或子目录时需要有w权限，当访问目录的子节点时需要有x权限。不同于POSIX模型，HDFS权限模型中的文件没有sticky，setuid或setgid位，因为这里没有可执行文件的概念。为了简单起见，这里也没有目录的sticky，setuid或setgid位。总的来说，文件或目录的权限就是它的模式（mode）。HDFS采用了Unix表示和显示模式的习惯，包括使用八进制数来表示权限。当新建一个文件或目录，它的所有者即客户进程的用户，它的所属组是父目录的组（BSD的规定）。
通过FS命令更改文件权限
chgrp
使用方法：hadoop fs -chgrp [-R] GROUP URI [URI …] Change group association of files. With -R, make the change recursively through the directory structure. The user must be the owner of files, or else a super-user. Additional information is in the Permissions User Guide. -->
改变文件所属的组。使用-R将使改变在目录结构下递归进行。命令的使用者必须是文件的所有者或者超级用户。更多的信息请参见HDFS权限用户指南。
chmod
使用方法：hadoop fs -chmod [-R] <MODE[,MODE]... | OCTALMODE> URI [URI …]
改变文件的权限。使用-R将使改变在目录结构下递归进行。命令的使用者必须是文件的所有者或者超级用户。更多的信息请参见HDFS权限用户指南。
chown
使用方法：hadoop fs -chown [-R] [OWNER][:[GROUP]] URI [URI ]
改变文件的拥有者。使用-R将使改变在目录结构下递归进行。命令的使用者必须是超级用户。更多的信息请参见HDFS权限用户指南。
通过API更改文件权限
import org.apache.hadoop.conf.Configuration
import org.apache.hadoop.fs.permission.{FsAction, FsPermission}
import org.apache.hadoop.fs.{FileSystem, Path}
    
val conf = new Configuration()
val fs = FileSystem.get(conf)
val permission = new FsPermission(FsAction.ALL, FsAction.ALL, FsAction.ALL)
    
val uri = "/data/testFileAuthority/"
val path = new Path(uri)
fs.setPermission(path, permission)
fs.close()


API删除文件或路径
在实际应用中出现：通过代码的api，可以读，可以写，但不能覆盖和删除的现象。
原因，不用deleteOnExit而用delete （因为这是在进程结束的时候删除）

参考材料
Hdfs官网说明文档
关于权限管理的部分：http://hadoop.apache.org/docs/r1.0.4/cn/hdfs_shell.html#chgrp

### 还要做的
hdfs命令的使用
hdfs开发者的代码调用

