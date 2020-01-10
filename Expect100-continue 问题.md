在使用 curl 发送 POST 数据大于 `1024` 字节时,  curl 并不会直接发起 POST 请求, 而是会分两部执行:

	1. 发送一个请求, 包含一个Expect:100-continue, 询问Server使用愿意接受数据
	2. 接收到Server返回的100-continue应答以后, 才把数据POST给Server
	
这是 libcurl 的行为, 具体RFC 参考 [Use of the 100 (Continue) Status](https://www.w3.org/Protocols/rfc2616/rfc2616-sec8.html#sec8.2.3)
有些http 服务器可能不会应答`100-continue`, 比如 lighttpd 会返回 417 “Expectation Failed”
在处理这个问题时居然搜到了大名鼎鼎的鸟哥的博客, [参考](http://www.laruence.com/2011/01/20/1840.html)
解决方法

	// Disable Expect: header (lighttpd does not support it)
	curl_setopt($ch, CURLOPT_HTTPHEADER, array('Expect:'));
           
服务器收到这样的包头

	REQ SIZE ok:172,str:
	POST /cgi-bin/game HTTP/1.1
	Host: 119.23.214.227:9001
	Accept: */*
	Content-Length: 2849
	Content-Type: application/x-www-form-urlencoded
	Expect: 100-continue

使用 curl 的命令行直接发送消息时, 可以添加 `Expect:` 头, 来取消`libcurl` 的这个特性, 代码如下

	curl -H Expect: -d "postdata string length mast more than 1024 bytes"

查看 libcurl 源码中的文档如下:

		However, many servers don't implement the Expect: stuff properly and if the
		server doesn't respond (positively) within 1 second libcurl will continue
		and send off the data anyway. 
		
发现 libcurl 在服务器超时1秒没返回时, 会继续发送 POST 数据, libcurl 使用 ` --expect100-timeout 10` 选项, 可以设置为10秒才超时
