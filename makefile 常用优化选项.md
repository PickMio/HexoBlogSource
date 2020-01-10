# MakeFile 常见优化选项

1. 常见优化选项 [GCC 选项文档](https://gcc.gnu.org/onlinedocs/gcc/Invoking-GCC.html)

	| Flag   |    Purpose   | Red Hat  | Fedora |
	| -------| --------     | -------- | -----  |
    |-D_FORTIFY_SOURCE=2      |     Run-time buffer overflow detection       |      ALL  |    ALL  |
	| -D_GLIBCXX_ASSERTIONS   | Run-time bounds checking for C++ strings and containers | ALL | ALL|
	| -fasynchronous-unwind-tables | 	 required for many debugging and performance tools Increased reliability of backtraces| All  | All |
	|-fexceptions | Enable table-based thread cancellation | ALL | ALL|
	| -fpie -Wl,-pie | Full ASLR for executables| 7 later | ALL|
	| -fpic -shared | 	No text relocations for shared libraries| All (for shared libraries) | All (for shared libraries)|
	|-fplugin=annobin |  Generate data for hardening quality control | Future | Fedora 28 and later|
	|-fstack-clash-protection| reliability of stack overflow detection,prevents attacks based on an overlapping heap and stack| Future (after 7.5)| 27 and later (except armhfp)|
	|-fstack-protector or -fstack-protector-all| Stack smashing protector| 6 only | n/a|
	| -fstack-protector-strong| Likewise| 7 and later| all|
	| -g| Generate debugging information| ALL|ALL|
	| -grecord-gcc-switches| Store compiler flags in debugging information| ALL|ALL|
	|-mcet -fcf-protection | Control flow integrity protection| Future| 28 later x86 only|
	| -O2 | Recommended optimizations, O3 is not entirely ABI-compatiable | ALL |ALL|
	| -pipe|Avoid temporary files, speeding up builds | ALL | ALL|
	| -Wall | Recommended compiler warnings| ALL| ALL|
	| -Werror=format-security| Reject potentially unsafe format string arguents| ALL|ALL|
	| -Werror=implicit-function-declaration| RejectGCC allows code to call undeclared functions, treating them as returning int| ALL(C only, c++ default) | ALL(C only)|
	| -Wl,-z,defs | Detect and reject underlinking| ALL|ALL|
	| -Wl,-z,now| 	Disable lazy binding| 7 and later | ALL|
	| -Wl,-z,relro| segments after relocation | 6 and later | ALL|

2. 使用 `-Wl` 的参数被传递给 `ld`, [参考文档](https://sourceware.org/binutils/docs/ld/Options.html)
3. `-D` 的定义参数[参考文档](https://gcc.gnu.org/onlinedocs/libstdc++/manual/using_macros.html)
4. `-fstack-protector-strong` [参考](https://www.cnblogs.com/gm-201705/p/9863958.html)