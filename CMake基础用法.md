# CMake 基础用法
## 基础格式
基础模板
		
		# 需要CMake最低版本 
		cmake_minimum_required(VERSION 2.4)
		# 项目名称, 一般在根目录的 CMakeLists.txt 中 
		project(hello_world)
		# 头文件目录, 可以直接在项目中使用 #include "name.h"
		include_directories(${PROJECT_SOURCE_DIR})  
		# 库的 CMakeLists.txt 的头文件会被依赖它的可执行程序看到
		target_include_directories(${PROJECT_NAME} PUBLIC include)
		# 激活test
		enable_testing()
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
		# test 的可执行文件
		add_executable(app_test test_main.cpp)
		# 设置链接的库
		target_link_libraries(app PUBLIC applib)
		# 设置编译特性, 有 CMAKE_C_COMPILE_FEATURES 和 CMAKE_CXX_COMPILE_FEATURES 具体看 扩展1 和2
		target_compile_features(app 
			PRIVATE     # 之添加到这个可执行文件中, 不会对依赖它的文件产生影响, PUBLIC 相反    
			cxx_constexpr    )
		
		# 添加 make test target
		add_test(NAME my_test COMMAND my_test)
		
		# 添加子目录, 子目录中必须有 CMakeLists.txt
		add_subdirectory(highlight)
		
		# 设置安装路径, TODO
		install_targets(app DESTINATION bin)
		# 添加编译链接选项
		SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -pg")
		SET(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -pg")
		SET(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -pg")
		
		
			
			

## 默认规则
### 使用 `add_executable(target main.cc)` 会在makefile 中生成一个可以使用 `make target` 的make目标, 默认会添加到 `make all`中, 可以使用 EXCLUDE_FROM_ALL 从`all` 中排除, 使其不默认生成可执行文件, 如下, 默认不会生成 target, `add_library` 也一样

			add_executable(target EXCLUDE_FROM_ALL main.cc)

## 常见命令
- file(MAKE_DIRECTORY ${DOXYGEN_OUTPUT_DIR}) 创建目录
- if(argc) ...  else() ... endif  if 的使用
	- STREQUAL  字符串相同 if("${_INPUT}" STREQUAL "Foo")

- find_package(Doxygen)	[参考](https://cmake.org/cmake/help/v3.6/command/find_package.html)	, 可以将配置放在`${CMAKE_SOURCE_DIR}/cmake/modules/Find<package>.cmake` [GitHub例子](https://github.com/WebAssembly/wasmint/blob/master/cmake/FindSDL2.cmake)
- message(STATUS msg) 显示信息
- 

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
	
## 扩展
1. [CMAKE_C_COMPILE_FEATURES](https://cmake.org/cmake/help/v3.1/prop_gbl/CMAKE_C_KNOWN_FEATURES.html#prop_gbl:CMAKE_C_KNOWN_FEATURES) 选项
	c_function_prototypes
		Function prototypes, as defined in ISO/IEC 9899:1990.
	c_restrict
		restrict keyword, as defined in ISO/IEC 9899:1999.
	c_static_assert
		Static assert, as defined in ISO/IEC 9899:2011.
	c_variadic_macros
		Variadic macros, as defined in ISO/IEC 9899:1999.
		
2. [CMAKE_CXX_COMPILE_FEATURES](https://cmake.org/cmake/help/v3.1/prop_gbl/CMAKE_CXX_KNOWN_FEATURES.html#prop_gbl:CMAKE_CXX_KNOWN_FEATURES) 选项

	cxx_aggregate_default_initializers
		Aggregate default initializers, as defined in N3605.
	cxx_alias_templates
		Template aliases, as defined in N2258.
	cxx_alignas
		Alignment control alignas, as defined in N2341.
	cxx_alignof
		Alignment control alignof, as defined in N2341.
	cxx_attributes
		Generic attributes, as defined in N2761.
	cxx_attribute_deprecated
		[[deprecated]] attribute, as defined in N3760.
	cxx_auto_type
		Automatic type deduction, as defined in N1984.
	cxx_binary_literals
		Binary literals, as defined in N3472.
	cxx_constexpr
		Constant expressions, as defined in N2235.
	cxx_contextual_conversions
		Contextual conversions, as defined in N3323.
	cxx_decltype_incomplete_return_types
		Decltype on incomplete return types, as defined in N3276.
	cxx_decltype
		Decltype, as defined in N2343.
	cxx_decltype_auto
		decltype(auto) semantics, as defined in N3638.
	cxx_default_function_template_args
		Default template arguments for function templates, as defined in DR226
	cxx_defaulted_functions
		Defaulted functions, as defined in N2346.
	cxx_defaulted_move_initializers
		Defaulted move initializers, as defined in N3053.
	cxx_delegating_constructors
		Delegating constructors, as defined in N1986.
	cxx_deleted_functions
		Deleted functions, as defined in N2346.
	cxx_digit_separators
		Digit separators, as defined in N3781.
	cxx_enum_forward_declarations
		Enum forward declarations, as defined in N2764.
	cxx_explicit_conversions
		Explicit conversion operators, as defined in N2437.
	cxx_extended_friend_declarations
		Extended friend declarations, as defined in N1791.
	cxx_extern_templates
		Extern templates, as defined in N1987.
	cxx_final
		Override control final keyword, as defined in N2928, N3206 and N3272.
	cxx_func_identifier
		Predefined __func__ identifier, as defined in N2340.
	cxx_generalized_initializers
		Initializer lists, as defined in N2672.
	cxx_generic_lambdas
		Generic lambdas, as defined in N3649.
	cxx_inheriting_constructors
		Inheriting constructors, as defined in N2540.
	cxx_inline_namespaces
		Inline namespaces, as defined in N2535.
	cxx_lambdas
		Lambda functions, as defined in N2927.
	cxx_lambda_init_captures
		Initialized lambda captures, as defined in N3648.
	cxx_local_type_template_args
		Local and unnamed types as template arguments, as defined in N2657.
	cxx_long_long_type
		long long type, as defined in N1811.
	cxx_noexcept
		Exception specifications, as defined in N3050.
	cxx_nonstatic_member_init
		Non-static data member initialization, as defined in N2756.
	cxx_nullptr
		Null pointer, as defined in N2431.
	cxx_override
		Override control override keyword, as defined in N2928, N3206 and N3272.
	cxx_range_for
		Range-based for, as defined in N2930.
	cxx_raw_string_literals
		Raw string literals, as defined in N2442.
	cxx_reference_qualified_functions
		Reference qualified functions, as defined in N2439.
	cxx_relaxed_constexpr
		Relaxed constexpr, as defined in N3652.
	cxx_return_type_deduction
		Return type deduction on normal functions, as defined in N3386.
	cxx_right_angle_brackets
		Right angle bracket parsing, as defined in N1757.
	cxx_rvalue_references
		R-value references, as defined in N2118.
	cxx_sizeof_member
		Size of non-static data members, as defined in N2253.
	cxx_static_assert
		Static assert, as defined in N1720.
	cxx_strong_enums
		Strongly typed enums, as defined in N2347.
	cxx_thread_local
		Thread-local variables, as defined in N2659.
	cxx_trailing_return_types
		Automatic function return type, as defined in N2541.
	cxx_unicode_literals
		Unicode string literals, as defined in N2442.
	cxx_uniform_initialization
		Uniform intialization, as defined in N2640.
	cxx_unrestricted_unions
		Unrestricted unions, as defined in N2544.
	cxx_user_literals
		User-defined literals, as defined in N2765.
	cxx_variable_templates
		Variable templates, as defined in N3651.
	cxx_variadic_macros
		Variadic macros, as defined in N1653.
	cxx_variadic_templates
		Variadic templates, as defined in N2242.
	cxx_template_template_parameters
		Template template parameters, as defined in ISO/IEC 14882:1998.
		
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