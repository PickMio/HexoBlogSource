# CMake 基础用法
## 基础格式
基础模板
		
		# 需要CMake最低版本 
		cmake_minimum_required(VERSION 2.4)

		# 项目名称, 一般在根目录的 CMakeLists.txt 中 
		project(hello_world)

		# 头文件目录, 可以直接在项目中使用 #include "name.h"
		include_directories(${PROJECT_SOURCE_DIR}) 
 
		# 设置 CMP0015 即 LINK_DIRECTORIES 为新标准
		cmake_policy(SET CMP0015 NEW) 
		# 设置库目录, 使用相对目录为 CMAKE_CURRENT_SOURCE_DIR
		LINK_DIRECTORIES("./libs")

		# 库的 CMakeLists.txt 的头文件会被依赖它的可执行程序看到
		target_include_directories(${PROJECT_NAME} PUBLIC include)

		# 添加库文件 
		add_library(applib  foo.cpp)
		add_library(applib STATIC  foo.cpp)  // 静态库, 默认
		add_library(applib SHARED  foo.cpp)  // 动态库, 会生成如 libappli.so
		add_library(my_module_lib MODULE lib.cpp) // 使用 MODULE 表示会在运行时使用类似`dlopen`加载的目标
		
		# macro 在全文可见
		macro(set_custom_variable _OUT_VAR)
		  set(${_OUT_VAR} "Foo")
		endmacro(set_custom_variable)
		set_custom_variable(my_foo)
		message(STATUS ${my_foo})
		
		# 设置单个目标文件的标准
		set_target_properties(foo PROPERTIES
			CXX_STANDARD 11
			CXX_STANDARD_REQUIRED ON
		)

		# 添加可执行文件app, 后面是它依赖的源文件名
		add_executable(app main.cpp)
		
		# 设置链接的库
		target_link_libraries(app PUBLIC applib)
				
		# 添加子目录, 子目录中必须有 CMakeLists.txt
		add_subdirectory(highlight)
		
		# 设置安装路径, TODO
		install_targets(app DESTINATION bin)

		# 添加编译链接选项
		SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -pg")
		SET(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -pg")
		SET(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -pg")
		
		
			
			

## 默认规则
- 使用 `add_executable(target main.cc)` 会在makefile 中生成一个可以使用 `make target` 的make目标, 默认会添加到 `make all`中, 可以使用 EXCLUDE_FROM_ALL 从`all` 中排除, 使其不默认生成可执行文件, 如下, 默认不会生成 target, `add_library` 也一样

			add_executable(target EXCLUDE_FROM_ALL main.cc)

## 常见命令
- file(MAKE_DIRECTORY ${DOXYGEN_OUTPUT_DIR}) 创建目录
- if(argc) ...  else() ... endif  if 的使用
	- STREQUAL  字符串相同 if("${_INPUT}" STREQUAL "Foo")

- find_package(Doxygen)	[参考](https://cmake.org/cmake/help/v3.6/command/find_package.html)	, 可以将配置放在`${CMAKE_SOURCE_DIR}/cmake/modules/Find<package>.cmake` [GitHub例子](https://github.com/WebAssembly/wasmint/blob/master/cmake/FindSDL2.cmake)
- message(STATUS msg) 显示信息
- LINK_DIRECTORIES(...)
- [设置 policy 版本](https://gitlab.kitware.com/cmake/community/-/wikis/doc/cmake/Policies), 选择CMP0015 为 NEW, 因为不同版本 CMP0015 实现不一样, 使用 `target_include_directories`时新版目录以CMake目录为准, 老版本以 binary 目录为准
	
		cmake_policy(VERSION 2.8)                                                                                                                                     
		cmake_policy(SET CMP0015 NEW) 
- 设置编译特性, 有 CMAKE_C_COMPILE_FEATURES 和 CMAKE_CXX_COMPILE_FEATURES 具体看 扩展1 和2
		target_compile_features(app 
			PRIVATE     	# 之添加到这个可执行文件中, 不会对依赖它的文件产生影响, PUBLIC 相反    
			cxx_constexpr    )
- 使用 option 设置配置

		# 是否使用自己的 MathFunctions 库                                                                                                                             
		# 这里设置的变量 USE_MYMATH、中间的提示文字、默认值，在 ccmake 命令中会展示
		#option (USE_MYMATH
		#  "Use provided math implementation"
		#  ON
		#)
		# 是否加入 MathFunctions 库
		#if (USE_MYMATH)
		#  # 添加头文件路径
		#  include_directories ("${PROJECT_SOURCE_DIR}/math")
		#  # 添加 math 子目录 (math 目录里必须有 CMakeLists.txt)
		#  add_subdirectory (math)
		#  set (EXTRA_LIBS ${EXTRA_LIBS} MathFunctions)
		#endif (USE_MYMATH)
- add_definitions(-DDEBUG -std=c++11) 添加定义
- 查找 math 目录下的所有源文件，并将名称保存到 MATH_SRC_LIST 变量
		
		aux_source_directory(${PROJECT_SOURCE_DIR}/math MATH_SRC_LIST)
- include(path)
- add_compile_options() 增加源文件编译选项
- target_compile_definitions(为目标增加编译选项)
- target_precompile_headers(target INTERFACE|PUBLIC|PRIVATE header1) [为文件添加pch 预编译](https://cmake.org/cmake/help/latest/command/target_precompile_headers.html), 需要cmake版本3.16以上并且系统禁用ASLR
## 常见定义
- PROJECT_SOURCE_DIR 项目根目录
- set(bin_dir "../build/bin")  使用 set 设置变量
- set(debug_dir ${bin_dir}/debug) set 设置变量时可以直接拼接字符串	
- 在 add_executable 或 add_library 可执行目标后添加  `EXCLUDE_FROM_ALL` 不会默认生成可执行文件或静态, 动态库
- add_library 可添加选项 
	- STATIC 静态链接
	- SHARED 动态链接
	- MODULE 会被动态加载的库
- set(CMAKE_C_STANDARD 99) # 设置c++ 版本为98, 99, 11, 会添加 -std=c++11 给 gcc
- set(CMAKE_CXX_STANDARD  11) # 98, 11, 14, 会添加 -std=c++11 给 gcc
- set(CMAKE_C_STANDARD_REQUIRED  on), set(CMAKE_CXX_STANDARD_REQUIRED on) 设置需要的标准
- set(CMAKE_MODULE_PATH "${CMAKE_MODULE_PATH} ${CMAKE_SOURCE_DIR}/cmake/modules") 设置 MODULE 查找路径
- set(CMAKE_VERBOSE_MAKEFILE 1) make时会打印更多信息, 等价于 make VERBOSE=1
- SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -pg")
- SET(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -pg")
- SET(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -pg")
- CMAKE_CURRENT_SOURCE_DIR 当前目录?
- SET(EXECUTABLE_OUTPUT_PATH ...) 设置可生成目标路径
- SET(LIBRARY_OUTPUT_PATH ...) 设置生成库目录路径
- SET(CMAKE_MODULE_PATH ...) 
- CMAKE_BUILD_TYPE 
- SET(PRECOMPILE_GCHS) ?
- SET(PRECOMPILE_HEADERS)?
- SET(PRECOMPILE_INCLUDE_PATHS)?
- SET(CMAKE_CXX_COMPILER "g++") 设置编译命令
 
## CMake 调用选项
一般在项目根目录下新建一个 `build` 目录用来生成中间代码, 防止污染项目目录.
- cmake ../ 生成编译文件
- cmake --build . 调用各平台的编译命令
- cmake -DCMAKE_BUILD_TYPE=Debug path/to/source  生成Debug 中间文件, 可选选项为
	- Debug debug版本
	- Release release版本
	- RelWithDebInfo release版本带debug信息
	- MinSizeRel relase 版本优化生成文件大小
- cpack path/to/build/directory 将项目打包
- cmake --help-module-list 查看已经安装的库列表
- cmake -D CMAKE_FIND_DEBUG_MODE=ON ..  查看查找库路径的更多debug 信息
- make VERBOSE=1 会打印更多调试信息(打印执行的语句 ), 可以在Cmake 时定义makefile `cmake -DCMAKE_VERBOSE_MAKEFILE=ON <PATH_TO_PROJECT_ROOT>`
- cmake -G "Visual Studio 10" 生成 vs 解决方案
- make edit_cache ccmake 可视化字符界面 

	
## 扩展
1. [CMAKE_C_COMPILE_FEATURES](https://cmake.org/cmake/help/v3.1/prop_gbl/CMAKE_C_KNOWN_FEATURES.html#prop_gbl:CMAKE_C_KNOWN_FEATURES) 选项
			
2. [CMAKE_CXX_COMPILE_FEATURES](https://cmake.org/cmake/help/v3.1/prop_gbl/CMAKE_CXX_KNOWN_FEATURES.html#prop_gbl:CMAKE_CXX_KNOWN_FEATURES) 选项
	
		
3. [configure_file 用法](https://riptutorial.com/cmake/example/26657/examble-based-on-sdl2-control-version), 可以用在 make install 上
4. [编译时复制相关dll, windows  用法](https://riptutorial.com/cmake/example/29468/qt5-dll-copy-example)
5. [vs 生成 Doxygen 文档](https://riptutorial.com/cmake/example/29469/running-a-custom-target)
6. 给项目定义一些预定义信息, 例如版本号

		CMakeLists.txt
		cmake_minimum_required(VERSION 3.8)
		project(project_name VERSION "0.0.0")	
		configure_file(${path to configure file 'config.h.in'}
		include_directories(${PROJECT_BINARY_BIN}) // this allows the 'config.h' file to be used throughout the program
		
		config.h.in
		#pragma once
		#define PROJECT_NAME "@PROJECT_NAME@"
		#define PROJECT_VER  "@PROJECT_VERSION@"
		#define PROJECT_VER_MAJOR "@PROJECT_VERSION_MAJOR@"
		#define PROJECT_VER_MINOR "@PROJECT_VERSION_MINOR@"
		#define PTOJECT_VER_PATCH "@PROJECT_VERSION_PATCH@"
	
		#endif // INCLUDE_GUARD

## 注意问题
### 系统开启的 ASLR 导致 pch 错误
通过设置编译选项 `-Winvalid-pch -H` 可以查看编译头文件的过程和 pch 报错问题, 或者使用 [cotire](https://github.com/sakra/cotire)

	#关闭 ASLR, [sysctl 文档](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt)
	sysctl -w kernel.randomize_va_space=0
	# 参数意思
	#0 - Turn the process address space randomization off.
	#1 - Make the addresses of mmap base, stack and VDSO page randomized.
	#2 - Additionally enable heap randomization.

	#或者临时使用一个 bash来关闭
	setarch $(uname -m) -R /bin/bash