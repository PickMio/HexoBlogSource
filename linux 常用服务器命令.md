## 使用 strace

	
	先用ps -mp pid或者top -H查出线程pid。
	然后strace -p pid追踪其中一个线程。

	
	直接用strace -fp pid追踪进程下所有线
	
## md5sum, base64