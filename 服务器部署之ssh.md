---
title: 服务器部署之ssh
date: 2019-12-30 19:19:19
categories: 运维
tags: ssh, 运维
---

## 关于ssh
可以通过 ssh 连接到远程服务器. 在服务器端启动 `sshd` 服务后, 客户端可以使用 `ssh` 登录到服务器, 还能配置免密登录服务器, `vscode` 的 `remote Development` 就能通过 `ssh`在远程服务器编译调试. 这里主要介绍 `ssh` 服务的部署和问题排查.

## 安装方法
### windows
OpenSSH 客户端和服务器是 Windows 10 1809 的可安装功能。  
若要安装 OpenSSH，请启动`设置`，然后转到`应用`>`应用和功能`>`管理可选功能`。  
查看此列表，看 OpenSSH 客户端是否已安装。 如果没有，则在页面顶部选择“添加功能”，然后：  


- 若要安装 OpenSSH 客户端，请找到“OpenSSH 客户端”，然后单击“安装”。  
- 若要安装 OpenSSH 服务器，请找到“OpenSSH 服务器”，然后单击“安装”。  

安装完成后，请返回“应用”>“应用和功能”>“管理可选功能”，你应当会看到列出的 OpenSSH 组件。
本文主要介绍 linux 下 `ssh` 的部署, windows 具体安装方案请参考 [[使用OpenSSH管理Windows]](https://docs.microsoft.com/zh-cn/windows-server/administration/openssh/openssh_install_firstuse)

### linux
linux 安装方法如下表格, 也可直接运行 `sudo yum install -y openssh` 或 `sudo apt-get install -y openssh` 直接安装客户端和服务器.

|System|client| server|
|------|------|-------:|
| Debian/Ubuntu	| Run sudo apt-get install openssh-client|sudo apt-get install openssh-server|
| RHEL / Fedora / CentOS	| Run sudo yum install openssh-clients|sudo yum install openssh-server && sudo systemctl start sshd.service && sudo systemctl enable sshd.service|

## 服务器部署
1. 在自己家目录 `~/` 创建 `.ssh` 目录, 并设置权限 700

		mkdir -p ~/.ssh
		chmod 700 .ssh

2. 生成私钥和公钥, 执行下面命令会在`.ssh` 目录下创建`id_rsa` 私钥文件和 `id_rsa.pub` 公钥文件.   
   可以这样理解, 公钥是很多一样的锁, 私钥是这些锁的钥匙, 将公钥`id_rsa.pub` 放到远端服务器, 本地存放私钥, 就可以自动使用私钥开启远方的"门"了

		ssh-keygen -t rsa -C "yourmail@mail.com" 

3. 如果有远端服务器 A, 本地服务器 B, 要使用 B ssh 到 服务器A 上, 执行如下流程 

	- 设置服务器 A `.ssh` 目录的权限
			mkdir -p ~/.ssh
			chmod 700 .ssh

	- 将公钥存放到远端服务器 A 的 `.ssh` 目录下. 然后执行如下语句将ssh 文件写入 `authorized_keys` 并设置其权限

			cat id_rsa.pub >> authorized_keys
			chmod 600 authorized_keys
			chmod 644 id_rsa.pub

	- 将私钥`ssh_rsa`存放到本地服务器 B 的 `.ssh` 目录, 修改其权限

			chmod 600 id_rsa

	-  将如下语句添加到 `~/.bash_profile`, 是用来自动启动 `ssh-agent` 服务管理私钥来认证公钥.

			if [ -z "$SSH_AUTH_SOCK" ]
			then
			   # Check for a currently running instance of the agent
			   RUNNING_AGENT="`ps -ax | grep 'ssh-agent -s' | grep -v grep | wc -l | tr -d '[:space:]'`"
			   if [ "$RUNNING_AGENT" = "0" ]
			   then
			        # Launch a new instance of the agent
			        ssh-agent -s &> .ssh/ssh-agent
			   fi
			   eval `cat .ssh/ssh-agent`
			fi
	- 临时运行`ssh-agent` 服务, 并添加私钥

			eval `ssh-agent -s`
			ssh-add ~/.ssh/id_rsa

4. 经过上面的设置, 就可以通过 `ssh` 链接到远端服务器了, 使用命令免密码登录服务器了

		ssh -p port username@hostname  
		-p 指定端口 port, 一般是22, 没有修改可以不要 -p 参数
		username 是你的用户名, 比如 mio@10.0.4.110, 就是在 10.0.4.110 上的  mio 用户
		如下
		ssh mio@10.0.4.110

5. windows 可以使用 `%%/.ssh/config` 来配置客户端免密码登录, 在 config 文件下键入如下内容:

		Host 10.0.4.110
		  HostName 10.0.4.110
		  User mio
		  IdentityFile ~/.ssh/id_rsa
		  
## 注意
要注意密钥和目录的权限, 不然会登录不上, 报错. 各个目录权限如下  

| 名称    | 类型      | 权限     | 权限数值      | 简介 |
|  --    |  --       |  --      |   ---       |  -- |
| ~/.ssh | 服务器目录  | rwx------|  chmod 700 | 服务器配置目录|
| ~/.ssh/authorized_keys | 服务器认证文件  | rw-------|  chmod 600 | 服务器公钥写入文件 |
| ~/.ssh/id_rsa.pub | 服务器公钥文件  | rw-r--r--|  chmod 644 | 服务器公钥|
| ~/.ssh | 客户端目录  | rwx------|  chmod 700 | 客户端配置目录|
| ~/.ssh/id_rsa| 客户端私钥文件  | rw-------|  chmod 600 | 客户端私钥文件|
|.ssh/config| 客户端配置文件   | rw-r--r--| chmod 644 | 客户端配置文件|

## 扩展
1. scp 的使用

		scp 本地用户名 @IP 地址 : 文件名 1 远程用户名 @IP 地址 : 文件名 2

		-v 和大多数 linux 命令中的 -v 意思一样 , 用来显示进度 
		-P 选择端口 . 注意 -p 已经被 rcp 使用 . 
		-r 复制目录
		-4 强行使用 IPV4 地址 . 
		-6 强行使用 IPV6 地址 .
		例子:#scp -p 4588 remote@www.abc.com:/usr/local/sin.sh /home/administrator

## 安装时碰到问题
当服务器和客户端都按照上面流程配置后, 一般时不会有什么问题的, 如果此时链接服务器还需要密码, 做为服务器开发人员, 要耐心分析问题, 产生这种情况肯定时有原因的, 可以从一下几个方向来查看问题
1. 查看客户端日志, 使用`ssh -v ` 选项可以查看链接时的详细日志, 如果觉得不够详细, 还可以再加一个 `v`, `ssh -vv mio@10.0.4.110`, 如果出现以下日志, 则可能表示链接的服务器端口不对, 可以执行 ss -anopt | grep 22 看服务器端口是否开开着, 或者进入`/etc/ssh/sshd_config` 查看服务器配置的端口字段 `Port` 为什么, 或者 `ps aux | grep sshd` 查看服务是否启动

		debug1: connect to address 119.27.172.153 port 22: Connection refused

2. sshd 服务可以使用 `service sshd status` 查看状态, 如果没在运行中, 可以执行`service sshd start` 启动服务
3. sshd 的配置在 `/etc/ssh/sshd_config` 中, 可以将日志等级设置为 INFO, `LogLevel INFO`, 然后在 /var/log/secure 中查看登录日志
4. 如果有别的不明白的, linux的 `man page` 对命令的描述是第一首资料, 可以仔细阅读, 如 `man sshd`.
5. 再有具体问题可以 `google` 之, 搜索问题提示的关键字


## PS:
1. 可以修改 `/etc/hosts` 将远程主机设置别名, 比如添加如下行:
	
		10.0.4.110 mio

	就可以直接使用 ssh cmio@mio 登录到 `10.0.4.110` 了

2. 服务端可以禁止密码登录, 修改 `/etc/ssh/sshd_config` 将 `#PasswordAuthentication no` 前面的# 取消来设置只能 使用 sshkey 登录, 注意这时候就不能使用密码登录了. 

		vim /etc/ssh/sshd_config
		# 删除下面配置前面的  `#`
		PasswordAuthentication no