## CentOS 关闭 IPV6
编辑文件`/etc/sysctl.conf`

	vi /etc/sysctl.conf

添加下面的行：

	net.ipv6.conf.all.disable_ipv6 = 1
	
	net.ipv6.conf.default.disable_ipv6 = 1

如果你想要为特定的网卡禁止IPv6，比如，对于enp0s3，添加下面的行。

	net.ipv6.conf.enp0s3.disable_ipv6 = 1


执行下面的命令来使设置生效。

	sysctl -p
	
## iptables 命令
显示规则列表
	iptables -nL
清除规则
	iptables -F
	
## chkconfig 查看系统自动启动的进程服务

## 查看防火墙
查看防火墙状态
	systemctl status firewalld.service
	
启动防火墙
	systemctl start firewalld.service
禁止防火墙自启动：
	systemctl disable firewalld.service