## Linux常用命令

参考:[Linux常用命令(超详细) - HZX↑ - 博客园 (cnblogs.com)](https://www.cnblogs.com/hzxll/p/16006567.html)

### 基本命令

#### 关机和重启

- 关机

`shutdown -h now `立刻关机

` shutdown -h 5 ` 5分钟后关机

`poweroff`  立刻关机

- 重启

`shutdown -r now` 立刻重启

`shutdown -r 5 ` 5分钟后重启

`reboot` 立刻重启

#### 帮助命令

`--help`  

`man`命令说明书，如`man shutdown`，打开man命令后，用q退出。



### 目录操作命令

#### 目录切换 cd

- `cd /`切换到根目录
- `cd /usr` 切换到根目录下的usr目录
- `cd ../ `切换到上一级目录 或者`cd ..`
- `cd ~ ` 切换到home目录
- `cd -` 切换到上次访问的目录

#### 目录查看 ls [-al]

- `ls`查看当前目录下的所有目录和文件
- `ls -a`查看当前目录下的所有目录和文件（包括隐藏的文件）
- `ls -l 或 ll `列表查看当前目录下的所有目录和文件（列表查看，显示更多信息）
- `ls /dir `查看指定目录下的所有目录和文件 如：ls /usr

#### 目录操作【增，删，改，查】

##### 创建目录（增）

- `mkdir aaa`在当前目录下创建一个名为aaa的目录
- `mkdir /usr/aaa`在指定目录下创建一个名为aaa的目录

##### 删除目录或者文件（删）

###### 删除文件

- `rm file`删除当前目录下的文件，后面加-f表示不再询问。

###### 删除目录

- `rm -r test`递归删除当前目录下的test目录，可以加-f不再询问。

所有文件或者目录 都可以使用`rm -rf file/directory/zip`



##### 目录修改

###### 重命名目录

- `mv aaa bbb `将目录aaa改为bbb

###### 剪切目录

- `mv /usr/temp/aaa /usr`将/usr/temp/aaa目录剪切到/usr。其不仅能对目录，还能对文件和压缩包。

###### 拷贝目录

`cp -r /usr/temp/aaa /usr`将/usr/temp/aaa拷贝到/usr，-r代表递归，如果是文件或者压缩包就不需要使用。



##### 目录查找

其命令格式为`find 目录 参数 文件名称`

如`find /usr/temp -name 'a*'`查找/usr/temp下所有以a开头的目录或文件。



### 文件操作命令--增、删、改、查

#### 新建文件

`touch aa.txt`新增一个aa.txt文件

#### 删除文件

`rm -rf file`

#### 修改文件

`vim aa.txt`打开该文件，使用i进入编辑模式，然后使用:wq保存并退出。

#### 查看文件

- `cat file`只能打开最后一屏内容。

- `more file`百分比显示。
- `less file`翻页查看，使用键盘上的PgUp和PgDn进行翻页。
- `tail file`指定行数，如`tail file -10`查看最后十行。



### 其他命令

#### grep命令

```bash
$ ps -ef | grep sshd  #查找指定ssh服务进程
$ ps -ef | grep sshd -c #查找指定进程个数 
```



 #### pwd

查看当前所在位置



#### 查看进程ps

`ps -ef`查看现在的所有进程

#### 结束进程kill

`kill -9 pid`强制杀死指定进程

#### 修改文件权限

`chmod 777`

#### 清屏

`ctrl+l`