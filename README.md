# linux

## centos7

### 系统服务

- 开启 ssh服务远程登录

	- 查看当前是否安装了ssh-server服务。默认只安装ssh-client服务

		- yum list installed | grep openssh-server

	- 安装ssh-server服务

		- yum install openssh-server

	- 编辑 /etc/ssh/sshd_config

		- 去除关于监听端口、监听地址前的注释(#)

			- Port 22
			- ListenAddress 0.0.0.0
			- ListenAddress ::

		- 然后开启运行远程登录

			- PermitRootLogin yes

		- 开启使用用户密码来作为连接验证

			- PasswordAuthentication yes

		- 保存

			- wq

	- 开启ssh服务

		- sudo service sshd start

	- 检查sshd 服务是否已经开启

		- ps -ef | grep sshd
		- 或

			- 检查 22 号端口是否开启监听

				- netstat -an | grep 22

	- 开启自动启动

		- systemctl enable sshd.service

	- 检查是否已经开启sshd 自启动

		- systemctl list-unit-files | grep sshd

- 查看版本号

	- 查看系统版本

		- rpm -q centos-release

	- 查看内核版本

		- uname -r

- 系统源修改

  参考链接：https://blog.csdn.net/qq_39112646/article/details/86830977

	- 首先备份系统自带yum源配置文件/etc/yum.repos.d/CentOS-Base.repo

		- sudo cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup

	- 进入yum源配置文件所在的文件夹

		- cd /etc/yum.repos.d

	- 下载163的yum源配置文件到上面那个文件夹内

		- sudo wget http://mirrors.163.com/.help/CentOS7-Base-163.repo

	- 运行yum makecache生成缓存

		- yum makecache

	- 这时在更新系统就会看到以下mirrors.163.com 信息

	  [root@localhost yum.repos.d]# yum -y update
	  已加载插件：fastestmirror, refresh-packagekit, security
	  设置更新进程Loading mirror speeds from cached hostfile
	  * base: mirrors.163.com
	  * extras: mirrors.163.com
	  * updates: mirrors.163.com

		- yum -y update

### 常用命令

- 开启服务

	- service 服务名 start

- 关闭防火墙

	- systemctl stop firewalld

- 关闭 swap 分区

	- 第一步 关闭 swap分区

		- swapoff -a

	- 第二步 修改配置文件 - /etc/fstab

		- 删除 swap 相关行 /mnt/swap swap swap default 0 0 这一行或者注释掉这一行

	- 第三步 确认swap 已经关闭

		- free -m

			- 

		- 若swap 行都显示 0 则表示关闭成功

	- 第四步 调整swappiness 参数

		- echo 0 > /proc/sys/vm/swappiness # 临时上校
		- vim /etc/sysctl.conf #永久生效

			- 修改 vm.swappiness的修改为0

				- vm.swappiness=0

					- sysctl -p #使配置生效

### 修改系统网络

- 方法1

	- 编辑 /etc/sysconfig/network-scripts/ifcfg-eth0

		- vim /etc/sysconfig/network-scripts/ifcfg-eth0

			- 自动获取ip

				- TYPE=Ethernet
				- BOOTPROTO=dhcp
				- DEFROUTE=yes
				- IPV4_FAILURE_FATAL=no
				- IPV6INIT=yes
				- IPV6_AUTOCONF=yes
				- IPV6_DEFROUTE=yes
				- IPV6_FAILURE_FATAL=no
				- NAME=eno16777736
				- UUID=34bbe4fa-f0b9-4ced-828a-f7f7e1094e4a
				- DEVICE=eno16777736
				- ONBOOT=yes
				- PEERDNS=yes
				- PEERROUTES=yes
				- IPV6_PEERDNS=yes
				- IPV6_PEERROUTES=yes

			- 修改ip地址

				- TYPE=Ethernet
				- BOOTPROTO=static 静态ip
				- DEFROUTE=yes
				- IPV4_FAILURE_FATAL=no
				- IPV6INIT=yes
				- IPV6_AUTOCONF=yes
				- IPV6_DEFROUTE=yes
				- IPV6_FAILURE_FATAL=no
				- NAME=eno16777736
				- UUID=34bbe4fa-f0b9-4ced-828a-f7f7e1094e4a
				- DEVICE=eno16777736
				- ONBOOT=no
				- PEERDNS=yes
				- PEERROUTES=yes
				- IPV6_PEERDNS=yes
				- IPV6_PEERROUTES=yes
				- IPADDR=192.168.179.3 ip地址
				- NETMASK=255.255.255.0 子网掩码
				- GATEWAY=192.168.179.2 网关
				- #GATEWAY=192.168.179.2 网关   #注释

	- 重启网络服务

		- 运行 service network restart  

	- 修改DNS 地址

		- 编辑 /etc/resolv.conf
		- 修改文件内容

			- nameserver 114.114.114.114

	- 常用DNS 地址

		- 114.114.114.114
		- 114.114.115.115
		- 223.5.5.5 阿里
		- 223.6.6.6阿里
		- 180.76.76.76 百度

- 方法2

## ubuntu

### 系统服务

- 查看版本号

	- 查看系统版本

		- cat /etc/issue

	- 查看内核版本

		- uname -r

- 开启ssh服务远程登录

	- 查看当前的ubuntu是否安装了ssh-server服务。默认只安装ssh-client服务

		- dpkg -l | grep ssh

	- 安装ssh-server服务

		- sudo apt-get install openssh-server

	- 再次查看安装的服务

		- dpkg -l | grep ssh

	- 确认ssh-server 是否启动

	  如果看到sshd那说明ssh-server已经启动了。
	  如果没有则可以这样启动：sudo /etc/init.d/ssh start或sudo service ssh start
	  配置相关：
	  ssh-server配置文件位于/etc/ssh/sshd_config，在这里可以定义SSH的服务端口，默认端口是22，你可以自己定义成其他端口号，如222。（或把配置文件中的”PermitRootLogin without-password”加一个”#”号,把它注释掉，再增加一句”PermitRootLogin yes”）
	  然后重启SSH服务：
	  sudo /etc/init.d/ssh stop
	  sudo /etc/init.d/ssh start
	  

		- ps-e | grep ssh

- 源修改

	- 备份 /etc/apt/sources.list

		- sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

	- 替换国内源

	  deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
	  deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
	  deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
	  deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
	  deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
	  deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
	  deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
	  deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
	  deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
	  deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse

		- sudo gedit sources.list

	- 更新源

		- sudo apt-get update

	- 更新软件

		- sudo apt-get upgrade

### 命令(常用)

- 安装包相关

	- dpkg

		- dpkg [OPTIONS...] <command>

			- OPTIONS(常用)

				- -l

					- 简明列出软件包的状态

- 更新源

	- sudo apt-get update

- 更新软件

	- sudo apt-get upgrade

## 常用命令

### 文件操作

- 复制文件或目录

	- 复制文件

		- cp [options] source dest

	- 复制目录

		- cp [options] source... directory

	- OPTIONS

	  -a：此选项通常在复制目录时使用，它保留链接、文件属性，并复制目录下的所有内容。其作用等于dpR参数组合。
	  -d：复制时保留链接。这里所说的链接相当于Windows系统中的快捷方式。
	  -f：覆盖已经存在的目标文件而不给出提示。
	  -i：与-f选项相反，在覆盖目标文件之前给出提示，要求用户确认是否覆盖，回答"y"时目标文件将被覆盖。
	  -p：除复制文件的内容外，还把修改时间和访问权限也复制到新文件中。
	  -r：若给出的源文件是一个目录文件，此时将复制该目录下所有的子目录和文件。
	  -l：不复制文件，只是生成链接文件。

### ssh

- 开启 ssh 免密登录

	- 1. 客户端生成公私钥

		- 本地生成公私钥

			- ssh-keygen

	- 2. 上传公钥到服务器

		- 实例

			- ssh-copy-id -i ~/.ssh/id_rsa.pub root@192.168.235.22

	- 3. 测试免密登录

		- ssh root@192.168.235.22

