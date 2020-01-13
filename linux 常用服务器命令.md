## 使用 strace

	
	先用ps -mp pid或者top -H查出线程pid。
	然后strace -p pid追踪其中一个线程。

	
	直接用strace -fp pid追踪进程下所有线
	
## md5sum, base64

## curl
- 使用curl 只查看返回的包头, 不要服务器返回 body, 比如在下载数据包前看看其大小

		curl -I -X GET https://download.docker.com/linux/centos/7/x86_64/stable/Packages/containerd.io-1.2.6-3.3.el7.x86_64.rpm
	
- 使用 curl 直接发送包体大于 1024 的数据而不发送 `Expect: 100-continue`

		curl -H Expect: -d "postdata string length mast more than 1024 bytes"
		
- 修改启动选项, 默认进入字符界面, 查看  `/etc/inittab`  说明

	systemctl set-default multi-user.target

- 获取 CentOS 系统版本 

		rpm -q centos-release|cut -d- -f3