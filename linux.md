### 1、安装centOs7

VMVare傻瓜式安装,选择好镜像文件，网络选择NET,内存20G,双处理器

设置一个root 密码 `123456`

### 2、安装Xterm

下载解压即可

选择SSH链接，在linux中输入 ip a 查看ip地址，输入ip地址连接

### 3、目录结构

查看顶级目录

```sh
[root@localhost ~]# ls /
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

目录介绍

```properties
root：该目录为系统管理员的home目录
bin：该目录放着经常使用的命令
boot：启动Linux时的一些核心文件
etc：存放系统管理所需要的配置文件和子目录
home：普通用户的home目录
usr：默认安装软件的目录，类似Windows中的Program Files目录
opt：主机额外安装软件的目录
```

### 4、基础命令

```sh
# 1. 我是谁
who am i
# 2. 我在哪
pwd
# 3. 查看IP地址
ip a|address
# 4. 清屏
clear
# 5. ping 域名|ip
ping 地址
# 6. 强制停止
Ctrl + c
# 7.当前目录 .
# 8.上一级目录 ..
cd [路径]
```

`.` ->  当前

`..` ->上一级

`~` ->当前用户的HOME目录

`-` ->返回

`/` ->根目录

### 5、操作目录

#### 1、查看目录

```sh
# 1. 列出指定目录的目录列
ls [-ald] [目录名]
# 目录名不填写，默认为当前目录
# -a：列出全部的内容，包括隐藏文件
# -l：列出全部的信息
# -d：仅查看目录本身
ll # 等同于ls -l	
```

#### 2、创建目录

```sh
mkdir name

#递归创建多级目录
mkdir -p a/b/c
```

#### 3、删除目录

```sh
rmdir name # name必须是一个空目录
rm -rf #递归不询问删除目录，直接删除非空目录
# -r：代表递归删除目录
# -f：不询问，直接删除
```

#### 4.复制目录

```sh
cp -r 来源目录 目标目录 
```

#### 5、移动目录

```s6h
mv 来源目录 目标目录
# 若目标目录不存在则为重命名
```

### 6、操作文件

#### 1、创建文件

```sh
touch filename
touch filename1 filename2 ...
#	创建多个文件
```

#### 2、编辑文件

```sh
#	进入查看模式
vi filename
#	进入编辑模式
[i]	# 当前光标位置
[o]	# 当前光标位置后一格
[x] # 当前光标位置后一行
esc # 进入查看模式
:	# 进入命令模式
:x  :wq	# 保存并退出
q!	# 不保存并退出
#	在查看模式下，双击大写的Z（ZZ | Shift + zz），可以快速保存并退出
```

**编辑文件时的其它操作**

```sh
# 在底行命令模式下，可以输入的内容
set nu     # 查看文件的行号
to 行号    # 快速跳转到指定行
set nonu   # 取消行号
# 直接在查看模式下输入
/具体内容   # 类似Ctrl + f搜索文件中具体内容所在的位置，查看下一个可按字母n
```

#### 3、查看文件

```sh
cat 文件名  # 从第一行开始查看文件内容，展示全部
tac 文件名  # 从最后一行开始展示
nl 文件名   # 显示文件内容时，展示行号
more 文件名 # 查看大文件时，可以一页一页往下翻（点击空格）
less 文件名 # 查看大文件时，可以任意地向上或向下翻（键盘上下键 | PageUp/PageDown）
head 文件名 # 只查看前几行
tail 文件名 # 只查看后几行
tail -f 日志 # 监控日志
```

#### 4、移动复制删除

同[操作目录](# 操作目录)

### 7、解压缩

#### .tar.gz .gz .tar

```sh
# 1. 解压
tar [-zxvf] 压缩包名称 [-C 路径]
# -z：代表压缩包后缀是.gz
# -x：代表解压
# -v：代表解压时，打印详细信息
# -f：-f选项必须放在所有选项的最后，指定文件名称
# -C：指定需要解压到的目录
# 2. 打包
tar [-zcvf] 压缩包名称 文件1 文件2 目录1 目录2 ...
# -c：代表打包
```

压缩：`tar -zcvf demo.tar.gz a a.c`

解压`tar -zxvf demo.tar.gz`

#### .zip

安装工具包

```sh
yum -y install zip
yum -y install unzip
```

压缩`zip zipname file1 file2`

解压`unsip zipname`

### 8、mysql

**授权远程主机链接数据库**

```sh
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'P@ssw0rd' WITH GRANT OPTION;
Query OK, 0 rows affected, 1 warning (0.01 sec)

mysql> flush privileges;
Query OK, 0 rows affected (0.01 sec)
```

**防火墙**

```
//临时关闭
systemctl stop firewalld.service
//禁止开机启动
systemctl disable firewalld.service


Removed symlink /etc/systemd/system/multi-user.target.wants/firewalld.service.
Removed symlink /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.
```

### 9、ssh连接

```shell
 ssh root@101.132.164.106 -i ./mawenshu.pem # 连接到root@101.132.164.106
 scp -i .\mawenshu.pem .\reset_jetbrains_eval_windows.vbs root@101.132.164.106:/root/
 sftp -i .\mawenshu.pem  root@101.132.164.106
```

