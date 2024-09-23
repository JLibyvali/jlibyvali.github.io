---
layout: post
title: C CPP Cmake generic configuration
date: 2024-07-21 12:10:00-0400
description: some cmake configurationscs
tags: c/cpp code
toc:
  beginning: true
  sidebar: left
---

# I.
分享下很常用的项目构建工具`cmake`,`cmake`的脚本编写还是很庞杂的，不过一般用的多的就是固定的一些语句。官方也有Tutorial。  
建议掌握基本构建语句后，随写随查。  
> ***CMake variable***: <https://cmake.org/cmake/help/latest/manual/cmake-variables.7.html>  

# II. Baisc

一个最简单的`cmake`项目只需要三个语句。  项目组织如下:  
```
tmp/
	|-build/
	|-CMakeLists.txt
	|-main.cpp
```
* 首先在项目根目录下新建一个`CMakeLists.txt`，并编写:  
```
./CMakeLists.txt

cmake_minimum_required(VERSION 3.20)
project(tmp)
add_executable(${PROJECT_NAME} main.cpp)
```
`cmake_minimum_required(VERSION 3.20)`默认规定了构建本项目最小的cmake版本，低于这个版本的cmake构建时回报错。  
当然了也可以指定最大版本，版本范围等。  
> ***cmake_minimum_required***: <https://cmake.org/cmake/help/latest/command/cmake_minimum_required.html>  

`project()`显示指定当前目录项目名字，如果是在顶层`CMakeLists.txt`文件中，则制定了了整个项目的名字。同时也会初始化一些cmake变量的值，比如`${PROJECT_NAME}`和`${CMAKE_PROJECT_NAME}` 

`add_executable()`是告诉cmake我们要构建的编译产物是可执行文件及源文件。

```cpp
./main.cpp

#include <iostream>
int main(int argc, char* argv[]){

    std::cout<<"Hello World!"<<std::endl;
    return 0;
}
```
``` 
tmp/ 
mkdir build 
cmake .. 
cmake --build . --parallel <jobs num, like  ' make -j N'>
# cmake --install . 
```


来看下`cmake`的参数：  
```
➜  ~ cmake --help 
Usage

  cmake [options] <path-to-source>
  cmake [options] <path-to-existing-build>
  cmake [options] -S <path-to-source> -B <path-to-build>

Specify a source directory to (re-)generate a build system for it in the
current working directory.  Specify an existing build directory to
re-generate its build system.

Options
  -S <path-to-source>          = Explicitly specify a source directory.
  -B <path-to-build>           = Explicitly specify a build directory.
  -C <initial-cache>           = Pre-load a script to populate the cache.
  -D <var>[:<type>]=<value>    = Create or update a cmake cache entry.
  -U <globbing_expr>           = Remove matching entries from CMake cache.
  -G <generator-name>          = Specify a build system generator.
  -T <toolset-name>            = Specify toolset name if supported by
                                 generator.
  -A <platform-name>           = Specify platform name if supported by
                                 generator.
  --toolchain <file>           = Specify toolchain file
                                 [CMAKE_TOOLCHAIN_FILE].
  --install-prefix <directory> = Specify install directory
                                 [CMAKE_INSTALL_PREFIX].
  --fresh                      = Configure a fresh build tree, removing any
                                 existing cache file.
  --build <dir>                = Build a CMake-generated project binary tree.
  --install <dir>              = Install a CMake-generated project binary
                                 tree.
  -N                           = View mode only.
  -P <file>                    = Process script mode.
  --find-package               = Legacy pkg-config like mode.  Do not use.
  --log-level=<ERROR|WARNING|NOTICE|STATUS|VERBOSE|DEBUG|TRACE>
                               = Set the verbosity of messages from CMake
                                 files.  --loglevel is also accepted for
                                 backward compatibility reasons.
  --log-context                = Prepend log messages with context, if given
Generators

The following generators are available on this platform (* marks default):
  Green Hills MULTI            = Generates Green Hills MULTI files
                                 (experimental, work-in-progress).
* Unix Makefiles               = Generates standard UNIX makefiles.
  Ninja                        = Generates build.ninja files.
  Ninja Multi-Config           = Generates build-<Config>.ninja files.

```
如果手动在命令行使用cmake的话：  
* -S 指定项目的源代码目录
* -B 指定项目的编译输出目录  
比如我们`cd build`目录里，此时的`build`就是我们的编译输出目录。   
* -D 定义一个cmake缓存变量值  
主要是用于定义cmake变量，比如指定cmake编译的类型为Debug模式`cmake -DCMAKE_BUILD_TYPE=Debug .. `，这样我们实际上制定了`CMAKE_BUILD_TYPE`这个变量值。 这和在`CMakeLists.txt`文件中写`set(CMAKE_BUILD_TYPE  Debug)`是一样的。  
* -G 指定编译系统生成器
一般通用的构建系统就是`make`和`ninja`，较新的`cmake`构建时默认会选择`ninja`。  
* -T 指定提供给编译构建系统使用的工具集，这个一般比较少用。    

Genrators制定了项目采用的构建工具，使用"Unix MakeFile"则最后会生成`MakeFile`文件，如果使用`Ninja`，则最后会生成`build.ninja`文件。 

--install-prefix指定了`cmake --install . `时的安装路径，

如果VSCode安装了`CMake Tools`插件，可以直接点击底部的状态栏进行编译。  
* 具体的cmake语法描述: 
***cmake_language***: <https://cmake.org/cmake/help/latest/manual/cmake-language.7.html>  


* cmake源文件名
```
Directories (CMakeLists.txt)

Scripts (<script>.cmake)

Modules (<module>.cmake)
```
* cmake 分割， 一般采用空格分割参数，命令
* cmake注释， "#"
* cmake 变量、命令是大小写敏感的 
* cmake参数写法  
> Command Arguments
There are three types of arguments within Command Invocations:

argument ::=  bracket_argument | quoted_argument | unquoted_argument

* 可以用“***[ ]***"分割，中间使用lua语法。
* 可以使用双引号“”分割，就是完全保留字面量
* 可以什么都不加，空格分割 ， 这样变量就无法保留一些符号比如#,(),[],<>等  
# III. More
下面是常用配置 
##  Variable
`cmake`主要的变量就三种  
* 普通变量
* cache变量条目
* env环境变量

普通变量： 直接`set()`和`unset()`就可以了 
cache变量条目： `set( variable_name  CACHE <type> [FORCE])` 是存储在`build/CMakeCache.txt`中的，项目在初次构建后，后面的重新编译都会从这个文件里去读cache变量。所以cache变量相当于全局静态cmake变量，仅在项目构建时创建。  
`<type>`指定变量类型，一般就`BOOL/PATH/STRING`。要注意cmake变量是大小写敏感的。  
env环境变量: 用于shell`env`输出的环境变量列表，可以通过`set(ENV{variable_name} value)`创建。  
变量使用时则是`${VAR}`或者`$ENV{VAR}`。  


## Language
启用项目的语言支持，可以使用  
* `enable_language()` ，可以填写`CXX/C/OBJC/OBJCXX/Fortran/etc`。  要在第一次对`project()`的调用后使用。默认会启用C/C++支持。
* `project( your_name VERSION your_version LANGUAGES C CXX CSharp)`指定语言  

对于C/CXX,指定版本：  
```
set(CMAKE_C_EXTENSIONS OFF)
set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
```
> ***CMAKE_<LANG>_EXTENSIONS***： This property specifies whether compiler specific extensions should be used. For some compilers, this results in adding a flag such as -std=gnu++11 instead of -std=c++11 to the compile line.   

***CMAKE_EXPORT_COMPILE_COMMANDS***  
这个变量可以导出`compile_commands.json`文件，clangd会去读取这个文件来进行语法补全，跳转。  

## Directory / Library
通过`add_subdirectory`可以添加一个 含有`CMakeLists.txt`文件的子目录 ， 把它作为源文件目录添加到项目的构建。这样就意味着我们可以引入别人的代码或者库。 

通过`add_library(<name> [<type>] [EXCLUDE_FROM_ALL] <sources>...)`可以把指定源文件按照库名添加到项目中来。比如如下结构  
```
tree -L 1/2
.
├── CMakeLists.txt
├── UI
│   ├── CMakeLists.txt
│   ├── UI_base.h
│   ├── UI_mainwindow.cpp
│   └── UI_mainwindow.h
```

在`UI`目录内，可以通过`add_library()`把UI目录添加为一个库，而不是在top级目录手动添加头文件
```
./UI/CMakeLists.txt
cmake_minimum_required(VERSION 3.20)

project(UI)

add_library(${PROJECT_NAME} SHARED
UI_base.h 
UI_mainwindow.h UI_mainwindow.cpp
)
```
默认一般是静态库，使用后会在编译目录下生成对应的`libUI.so`。  
然后在`top`级的`CMakeLists.ttx`文件内：  
```
./CMakeLists.txt
......
add_executable(${PROJECT_NAME} ${INC_SRCS}  main.cpp)

####### Sub directory
add_subdirectory(UI)
target_include_directories(${PROJECT_NAME} PUBLIC UI)
....
```
 add_subdirectory()添加指定源文件为库，  
 然后使用`target_include_directories()`把由	`add_library()`		或者		`add_executable()`  生成的目标加入对应target 的include目录里。 
 详见：  <https://cmake.org/cmake/help/latest/command/target_include_directories.html#command:target_include_directories>
 
## Build System  
编译库和可执行文件：  
```cmake
add_library(Lib_name src1.cpp src2.cpp src3.cpp ...) 默认静态库
add_executable(executable_name main.cpp other.cpp ... )
```
这也就意味着，在添加这个编译的target时就要指定源文件列表，可以手动指定，也可以：  
`file ( [GLOB/GLOB_RECURSE]  ${VAR}  "./dir1/*.cpp")`通过file通配符生成源文件列表在`${VAR}`中。   

上面两个在编译链接库时都是：  
```
target_link_libraries(executable_name  Lib_name)
```

上述命令会生成对应类型的`target`，在`CMake`编译构建系统里，`target`是一个很重要的输出单位，那自然就会有围绕target 的一系列cmake 命令。 `target`可以是可执行二进制程序，可以是输出的库，可以是执行一段语句后的输出结果。 
在使用`target`有关的命令时要填写`scope`关键词：  
* PUBLIC  `target`在编译和被使用时都有权利使用该命令参数
* PRIVATE `target`仅在编译时在有权使用
* INTERFACE `target`仅在被使用时才有权利使用 

`target`有关的命令都是以`target_xxxxx`开头，相比全局性的一些`add_xxx`命令，主要能把命令的作用范围精确到`target`，减少变量空间的污染与交叉定义。  
* 添加项目的宏  
```
target_compile_definitions(archive
  PRIVATE   BUILDING_WITH_LZMA
  INTERFACE USING_ARCHIVE_LIB
)

或者
add_compile_definitions(<definition> ...)
添加给当前文件内每个在CMakeLists.txt内的target
```

* 添加include搜索路径
```cmake 
target_include_directories(mylib PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include/mylib>
  $<INSTALL_INTERFACE:include/mylib>  # <prefix>/include/mylib
)

或者
include_directories([AFTER|BEFORE] [SYSTEM] dir1 [dir2 ...])
往编译器头文件搜索路径直接添加 

也可以引入cmake 模块，即.cmake文件  
include(<file|module> [OPTIONAL] [RESULT_VARIABLE <var>]
                      [NO_POLICY_SCOPE])
 
 ```

* 添加源文件 
``` 
target_sources(<target>
  <INTERFACE|PUBLIC|PRIVATE> [items1...]
  [<INTERFACE|PUBLIC|PRIVATE> [items2...] ...])
```
* 添加库相关的操作  

```
连接时的库，也就是连接器linker的参数  
target_link_libraries(<target> ... <item>... ...)
<item>应该是以下内容： 

A library target name: The generated link line will have the full path to the linkable library file associated with the target. The buildsystem will have a dependency to re-link <target> if the library file changes.
库的名字，如果前面有 add_library()生成的就可以直接使用库名字。  

A full path to a library file: The generated link line will normally preserve the full path to the file. The buildsystem will have a dependency to re-link <target> if the library file changes.
库的绝对路径

A plain library name: The generated link line will ask the linker to search for the library (e.g. foo becomes -lfoo or foo.lib).
预计的库名字

A link flag: Item names starting with -, but not -l or -framework, are treated as linker flags. Note that such flags will be treated like any other library link item for purposes of transitive dependencies, so they are generally safe to specify only as private link items that will not propagate to dependents.
添加的链接选项，以符号“-"开头。 比如”-Wl","-verbose" 

```
库的搜索路径
```
target_link_directories(<target> [BEFORE]
  <INTERFACE|PUBLIC|PRIVATE> [items1...]
  [<INTERFACE|PUBLIC|PRIVATE> [items2...] ...])
```
                     
添加链接时传给链接器的选项：
```
target_link_options(<target> [BEFORE]
  <INTERFACE|PUBLIC|PRIVATE> [items1...]
  [<INTERFACE|PUBLIC|PRIVATE> [items2...] ...])
```
 
 ## Flags
这里说下传给compiler 和 linker的,其实这些选项主要和使用的编译器和链接器有关。  
可以通过用户环境变量指定，然后这些变量的值会给具体的cmake变量。  
 
```
env
CFLAGS	# Add default compilation flags to be used when compiling C files.
CSFLAGS #Add default compilation flags to be used when compiling CSharp files
LDFLAGS #Will only be used by CMake on the first configuration to determine the default linker flags, after which the value for LDFLAGS is stored in the cache as CMAKE_EXE_LINKER_FLAGS_INIT, CMAKE_SHARED_LINKER_FLAGS_INIT, and CMAKE_MODULE_LINKER_FLAGS_INIT
CXXFLAGS #Add default compilation flags to be used when compiling CXX (C++) files.  
LINK_FLAGS #DAdditional flags to use when linking this target if it is a shared library, module library, or an executable

```

然后是相关的cmake变量。 
```
CMAKE_<LANG>_FLAGS #对于特定语言的参数，构建整体项目时，会传递给编译器

CMAKE_C_FLAGS: Initialized by the CFLAGS environment variable.

CMAKE_CXX_FLAGS: Initialized by the CXXFLAGS environment variable.

CMAKE_CUDA_FLAGS: Initialized by the CUDAFLAGS environment variable.

CMAKE_Fortran_FLAGS: Initialized by the FFLAGS environment variable.

CMAKE_CSharp_FLAGS: Initialized by the CSFLAGS environment variable.

CMAKE_HIP_FLAGS: Initialized by the HIPFLAGS environment variable.

CMAKE_ISPC_FLAGS: Initialized by the ISPCFLAGS environment variable.

CMAKE_OBJC_FLAGS: Initialized by the OBJCFLAGS environment variable.

CMAKE_OBJCXX_FLAGS: Initialized by the OBJCXXFLAGS environment variable.
```
对于不同的项目构建类型可以选则
```
CMAKE_<LANG>_FLAGS_DEBUG
CMAKE_<LANG>_FLAGS_RELEASE
```
则cmake在对应编译类型时，会选择特定的flags传递给编译器。可以在这时指定优化等级啥的。

对应的还有链接参数：  
```
CMAKE_EXE_LINKER_FLAGS
CMAKE_NODULE_LINKER_FLAGS
CMAKE_SHARED_LINKER_FLAGS
```

# IV. Advanced
--- 

然后是自定义的`target`和`command`，在我们编译一个大型项目的过程中，可能需要有许多小的构建步骤，检查步骤。在完成最终的项目构建前需要得到**先前的构建步骤的结果**；也可能是我们需要在**整个项目**构建途中，**检查**某些命令的**输出**。为了完成这个操作我们引入：
```
add_custom_commnad # Add a custom build rule to the generated build system.
add_custom_target  #Add a target with no output so it will always be built.
```
比如生成指定文件
```cmake
add_custom_command(
  OUTPUT out.c
  COMMAND someTool -i ${CMAKE_CURRENT_SOURCE_DIR}/in.txt
                   -o out.c
  DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/in.txt
  VERBATIM)
add_library(myLib out.c)
```
`add_custom()`会执行`COMMAND`后面的内容`someTool -i ${CMAKE_CURRENT_SOURCE_DIR}/in.txt   -o out.c`,在这之前检查依赖`DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/in.txt` 。 然后输出文件`OUTPUT out.c`。 

比如简化命令的使用
```cmake 
add_custom_command(
    USES_TERMINAL
    OUTPUT flash-cmd
    COMMAND ${ST_INFO} --probe
    COMMAND ${ST_FLASH} --format binary write
            ${CMAKE_CURRENT_BINARY_DIR}/${CMAKE_PROJECT_NAME}.bin ${FLASH_ADDR}
    DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/${CMAKE_PROJECT_NAME}.bin
    COMMENT "Running the flash......")

  add_custom_target(flash DEPENDS flash-cmd)
  ```
`add_custom_command`会
检查依赖`DEPENDS .....`后
执行`COMMAND $(ST_INFO} --probe`
后执行`COMMAND ${ST_FLASH} ...`
然后输出信息`COMMENT "Running the flash ......"`到控制台  
最后输出文件`flash-cmd`
我们添加一个target`flash`，依赖上述输出

也可以生成不同种类的文件：
```
add_custom_command(
  OUTPUT table.csv
  COMMAND makeTable -i ${CMAKE_CURRENT_SOURCE_DIR}/input.dat
                    -o table.csv
  DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/input.dat
  VERBATIM)
add_custom_target(generate_table_csv DEPENDS table.csv)
```

这样编译后，执行`generate_table_csv`就会输出CSV文件了。  