---
layout: post
title: C CPP VSCode configuration
date: 2024-08-12 11:34::00
description: record common vscode configuration
tags: c/cpp code
categories: 
featured: true
toc:
   beginning: true
   sidebar: left
---


--- 
# Before 
分享下自己的环境配置过程， 也是为了能快速的完成环境搭建。 首先要知道从代码到编译产物的路径。  
> ***Compilation Progress***: [https://www.javatpoint.com/compilation-process-in-c](https://www.javatpoint.com/compilation-process-in-c)    

大概四个过程： 预编译 --> 编译 --> 汇编 --> 链接 。  我们不去追究底层的过程。只是配置我们的环境时围绕上面的编译过程展开。  

# Basic

* Installation
如果有Clion，也可以使用Clion。 VSCode只是一个文本编辑器，所以需要依赖我们其他的配置来让它变成IDE，首先下载VSCode：  
> [https://code.visualstudio.com/docs/setup/linux](https://code.visualstudio.com/docs/setup/linux )  

以Fedora为例，添加仓库，更新本地缓存，下载。  
```bash
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
echo -e "[code]\nname=Visual Studio Code\nbaseurl=https://packages.microsoft.com/yumrepos/vscode\nenabled=1\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" | sudo tee /etc/yum.repos.d/vscode.repo > /dev/null
```
上面的命令会引入gpg认证，在`/etc/yum.repos.d/`创建仓库。  
```bash
➜  yum.repos.d pwd
/etc/yum.repos.d
➜  yum.repos.d cat ./vscode.repo 
[code]
name=Visual Studio Code
baseurl=https://packages.microsoft.com/yumrepos/vscode
enabled=1
gpgcheck=1
gpgkey=https://packages.microsoft.com/keys/microsoft.asc
```

* Clangd
然后我们需要安装插件来完成代码补全，跳转等词法功能，完成这个功能的是`Language Serve Protocol` ,VSCode推荐的`C/C++ Extension`词法分析的性能不太好， 所以我们选用`clangd`，VSCode的clangd插件只是提供了一个接口，主机还需要安装clangd程序，不过插件检测也会自动安装。   
要安装的软件：  
> ***Fedora***: sudo dnf  install make cmake ninja-build clang llvm  clang-tools-extra 
> ***Debian/Ubuntu***: sudo apt install make cmake ninja-build clang clangd llvm   clang-format

然后配置clangd，重启语言服务器  
![选择自己的clangd路径](https://i-blog.csdnimg.cn/direct/eb0d9dc7a98945d99d8ca6f9fb4e8a61.png#pic_center)  
当然这还不能够完成代码补全，clangd会去读取`compile_commands.json`文件，可以在`clangd`配置项里确定文件位置，一般是`${workspaceFolder}/build/`。 如果你的项目使用CMake构建。那么一条命令就可以生成：  
```cmake
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
```
对于使用`Unix Makefile`相关工具构建的项目，可以使用`bear`来拦截构建请求
```bash
bear -- make all
```

`Ctrl+Shift+P`可以唤出VSCode 的命令栏，输入clangd可以查看有关clangd 的配置，我们选择restart  
![restat Language Server](https://i-blog.csdnimg.cn/direct/4c0c7e60645c45cf83adeff0def0e16a.png#pic_center)  
我常用的项目都是CMake构建的，所以在安装cmake相关插件。  
![CMake插件](https://i-blog.csdnimg.cn/direct/fdf890b3030448aa9507258716ce959b.png#pic_center)  
* CMakeTools
`CMake Tools`这个工具非常有用， 提供了一个底部的工作面板，而且可以很方便的通过`json kits file`完成工具链的配置。  
![工作面板](https://i-blog.csdnimg.cn/direct/2692488de9bd4e65bb73dba0cffaeb02.png#pic_center)  

`Ctrl+Shift+P`输入kits，可以编辑CMakeTools的工具链配置json。  
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/9afb5eac960d48fb90136a96391c2923.png#pic_center)  
![Json](https://i-blog.csdnimg.cn/direct/eff7c2cf50b543ccb41501a53ac1bdc0.png#pic_center)

  
  * CMake  
`CMake`插件提供的调试面板。     
![CMakeToolsDebug](https://i-blog.csdnimg.cn/direct/b78e4c9192a348a39dafd82f6ee24ca7.png#pic_center)    
* CMake Format
 Cmake format主要提供CMakeLists文件的格式化， 我们`clnag-format`没有对CMake语言的格式化服务。   

# Debug
调试器  
一般的调试相关的配置VSCode都会去读取打开的工作目录下`.vscode`文件夹内的`launch.json`文件。  
调试器的选择一般就`gdb`或者`lldb`，可以选择安装插件`Native Debug`，它提供了对多种调试器的支持。 如果你的工作目录下没有`.vscoe/launch.json`文件，在Debug时会自动根据现有插件的支持创建一个launch.json。  
![Launch](https://i-blog.csdnimg.cn/direct/11502f3945f8440987ca8ea9189f6d08.png#pic_center)  
选择`Add Configurations`可以在添加其他调试项：  
![Other](https://i-blog.csdnimg.cn/direct/7d206435dafe4bbba1e3ad2fbb02b64b.png#pic_center)  
当然我们一般选择`Launch Program`类型的就可以了。    

* 编写Launch.json
然后就是如何编写`launch.json`这需要了解VSCode提供的json 变量和插件提供的***变量***。  
>***VSCode***: [https://code.visualstudio.com/docs/editor/variables-reference](https://code.visualstudio.com/docs/editor/variables-reference)
***VSCode CMake extension***:  [https://github.com/microsoft/vscode-cmake-tools/blob/main/docs/cmake-settings.md](https://github.com/microsoft/vscode-cmake-tools/blob/main/docs/cmake-settings.md)  

官方的例子：  
> ***VSCode Launch.json***: [https://code.visualstudio.com/docs/cpp/launch-json-reference](https://code.visualstudio.com/docs/cpp/launch-json-reference)    

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        


        {
            "type": "lldb",
            "request": "launch",
            "name": "Debug",
            "program": "${workspaceFolder}/<executable file>",
            "args": [],
            "cwd": "${workspaceFolder}"
        },
        {
            "type":"gdb",
            "request": "launch",
            "name": "Gdb",
            "program": "${workspaceFolder}/build/$",
            "args": [],
            "cwd":"${workspaceFolder}"
        }

    ]
}
```

基本选项： 
* type: 指定调试器类型，需要插件提供支持
* request: 该项的请求类型，仅可选：launch/attach  
* name: 出现在调试器面板的名字
* program: 用于调试的程序名字，可以结合VSCode和CMake的变量指定，不需要手动指定。 
* args: 调试器的参数，使用gdb --help / lldb --help 查看可传递的参数 
* cwd: 用于查找依赖和其他文件的工作目录，一般就是${workspaceFolder}



![Debug Pannel](https://i-blog.csdnimg.cn/direct/08c4146cabcb4b2c8f58a35186cd6d33.png#pic_center)

不过方便的是: `CMake Tools`插件提供了根据`CMakeLists.txt`的`target`为基本单位的调试面板，因为在我们的项目比较复杂，编译产物比较多时。编写一个合适的`launch.json`会比较困难。  
>***CMake tools Debug***: [https://github.com/microsoft/vscode-cmake-tools/blob/34ca947fc153d8061c1f53f5a6b26a041d2c1f17/docs/debug-launch.md](https://github.com/microsoft/vscode-cmake-tools/blob/34ca947fc153d8061c1f53f5a6b26a041d2c1f17/docs/debug-launch.md)  

* tasks.json

都说了`launch.json`肯定还有`tasks.json`，不过一般有`CMake tools`用不到。 `tasks.json`也能指定多个编译进程。  
> ***task.json***： [https://code.visualstudio.com/docs/editor/tasks#_variable-substitution](https://code.visualstudio.com/docs/editor/tasks#_variable-substitution)  

随之一起的，可以出现在`.vscode`里的还有一个`settings.json`，这个和`user settings json`基本一样，只是仅作用于当前的工作目录。 
> ***settings.json*** : [https://code.visualstudio.com/docs/getstarted/settings](https://code.visualstudio.com/docs/getstarted/settings)  


  
* MemoryView
这个插件可以让你方便的查看目标地址附近的值：  
![MemoryView](https://i-blog.csdnimg.cn/direct/f0970447e61f430daf315cc1adeec3d8.png#pic_center)
 
  # Format 
  这里来分享下文档，格式化相关内容。  
   编写`markdown`直接安装插件`Markdown All in One` 就好了。  
   拼写检查可以安装一个`Code Spell Checker`。  
   然后是代码的格式化插件`Clang-format`。这个插件会读取工作目录下的`.clang-format`文件,这个文件描述了格式化的所有参数，非常详尽。`clang-format`插件默认携带了几个风格的格式化， 不过不太喜欢的可以自己写一封通用的`.clang-format`文件。   
>***Clang-format***:    [https://clang.llvm.org/docs/ClangFormat.html](https://clang.llvm.org/docs/ClangFormat.html)  
***编写.clang-format***： [https://clang.llvm.org/docs/ClangFormatStyleOptions.html](https://clang.llvm.org/docs/ClangFormatStyleOptions.html)    

可以使用命令来自动生成配置文件：  
```bash
➜  .vscode clang-format --help 
OVERVIEW: A tool to format C/C++/Java/JavaScript/JSON/Objective-C/Protobuf/C# code.

If no arguments are specified, it formats the code from standard input
and writes the result to the standard output.
If <file>s are given, it reformats the files. If -i is specified
together with <file>s, the files are edited in-place. Otherwise, the
result is written to the standard output.

USAGE: clang-format [options] [@<file>] [<file> ...]

OPTIONS:

Clang-format options:

  --Werror                       - If set, changes formatting warnings to errors
  --Wno-error=<value>            - If set don't error out on the specified warning type.
    =unknown                     -   If set, unknown format options are only warned about.
                                     This can be used to enable formatting, even if the
                                     configuration contains unknown (newer) options.
                                     Use with caution, as this might lead to dramatically
                                     differing format depending on an option being
                                     supported or not.
  --assume-filename=<string>     - Set filename used to determine the language and to find
                                   .clang-format file.
                                   Only used when reading from stdin.
                                   If this is not passed, the .clang-format file is searched
                                   relative to the current working directory when reading stdin.
                                   Unrecognized filenames are treated as C++.
                                   supported:
                                     CSharp: .cs
                                     Java: .java
                                     JavaScript: .mjs .js .ts
                                     Json: .json
                                     Objective-C: .m .mm
                                     Proto: .proto .protodevel
                                     TableGen: .td
                                     TextProto: .textpb .pb.txt .textproto .asciipb
                                     Verilog: .sv .svh .v .vh
  --cursor=<uint>                - The position of the cursor when invoking
                                   clang-format from an editor integration
  --dry-run                      - If set, do not actually make the formatting changes
  --dump-config                  - Dump configuration options to stdout and exit.
                                   Can be used with -style option.
  --fallback-style=<string>      - The name of the predefined style used as a
                                   fallback in case clang-format is invoked with
                                   -style=file, but can not find the .clang-format
                                   file to use. Defaults to 'LLVM'.
                                   Use -fallback-style=none to skip formatting.
  --ferror-limit=<uint>          - Set the maximum number of clang-format errors to emit
                                   before stopping (0 = no limit).
                                   Used only with --dry-run or -n
  --files=<filename>             - A file containing a list of files to process, one per line.
  -i                             - Inplace edit <file>s, if specified.
  --length=<uint>                - Format a range of this length (in bytes).
                                   Multiple ranges can be formatted by specifying
                                   several -offset and -length pairs.
                                   When only a single -offset is specified without
                                   -length, clang-format will format up to the end
                                   of the file.
                                   Can only be used with one input file.
  --lines=<string>               - <start line>:<end line> - format a range of
                                   lines (both 1-based).
                                   Multiple ranges can be formatted by specifying
                                   several -lines arguments.
                                   Can't be used with -offset and -length.
                                   Can only be used with one input file.
  -n                             - Alias for --dry-run
  --offset=<uint>                - Format a range starting at this byte offset.
                                   Multiple ranges can be formatted by specifying
                                   several -offset and -length pairs.
                                   Can only be used with one input file.
  --output-replacements-xml      - Output replacements as XML.
  --qualifier-alignment=<string> - If set, overrides the qualifier alignment style
                                   determined by the QualifierAlignment style flag
  --sort-includes                - If set, overrides the include sorting behavior
                                   determined by the SortIncludes style flag
  --style=<string>               - Set coding style. <string> can be:
                                   1. A preset: LLVM, GNU, Google, Chromium, Microsoft,
                                      Mozilla, WebKit.
                                   2. 'file' to load style configuration from a
                                      .clang-format file in one of the parent directories
                                      of the source file (for stdin, see --assume-filename).
                                      If no .clang-format file is found, falls back to
                                      --fallback-style.
                                      --style=file is the default.
                                   3. 'file:<format_file_path>' to explicitly specify
                                      the configuration file.
                                   4. "{key: value, ...}" to set specific parameters, e.g.:
                                      --style="{BasedOnStyle: llvm, IndentWidth: 8}"
  --verbose                      - If set, shows the list of processed files

```
选择不同的风格
```bash
  clang-format --style=<LLVM/GNU/Google/Chromium/Microsoft/Mozilla/WebKit>  -dump-config > .clang-format 
  ```
 然后配置格式化相关的快捷键： 
 ![Keyboard Shortcuts](https://i-blog.csdnimg.cn/direct/2662691519b14d40a0a80e50b94144e4.png#pic_center)  
![Format Shortcuts](https://i-blog.csdnimg.cn/direct/bafa1757c500420e9ffeca37b0912ce6.png#pic_center)  
这样就非常方便了。

# Settings
  这里来写一下VSCOde编辑器和插件的设置`json`文件，`Ctrl+Shift+P`搜索`users  `:    
  
![Users Json](https://i-blog.csdnimg.cn/direct/6d69cd6765064fa582c1ec43d2f0e6ee.png#pic_center)    这样相比使用`Ctrl + , `一个一个的去找，方便很多，而且也有利于不同机器之间的同步。     
![UsersJson](https://i-blog.csdnimg.cn/direct/ca64af46834548cf85e5ee32f50f0cd8.png#pic_center)  
可以配置相关的键位，行为等，具体细节需要查看每个扩展的设置页面。 然后可以在VSCode登陆自己的账户，开启同步。  
Linux的VSCode配置相关的文件`json`一般会在`.config/Code/User/,  .vscode/  $XDG_CONFIG_HOME/Code/  `下。  

# Remote 
`VSCode remote `连接需要  
* 安装remote系列插件  
![Remote Extension](https://i-blog.csdnimg.cn/direct/1f0901cd6f16411eb87b8afe3ecfdb97.png#pic_center)  

* 本级与目标机器能ssh连接，有无密码都行 
ssh无密码连接，一般机器需要使用本地ssh代理加入连接的密钥，不然光是把公钥复制到目标机器上还是没用。 
相关操作推荐github的ssh reference：  
> ***github ssh***: [https://docs.github.com/en/authentication/connecting-to-github-with-ssh/using-ssh-agent-forwarding](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/using-ssh-agent-forwarding)   
* 目标机器需要有网络下载`vscode server`   , 下载完成后一般会有`$HOME/.vscode-server`文件夹生成，里面存放了数据、插件信息。  
* CLIon
CLion的远程连接需要配置网桥，有点麻烦。  

# CMake 
后面项目的构建，库相关的操作就是CMake的内容了，推荐文章：
[https://blog.csdn.net/qq_35721063/article/details/141469557?spm=1001.2014.3001.5502](https://blog.csdn.net/qq_35721063/article/details/141469557?spm=1001.2014.3001.5502)

# Windows VSCode 
可以先自己安装`mingw-64`  
> mingw-64 download: [https://www.mingw-w64.org/downloads/#w64devkit](https://www.mingw-w64.org/downloads/#w64devkit) 


Windows下一切差不多，主要使用的工具区别。  VSCode默认的PowerShell环境可以换成`MSys2`的更加统一,也可以使用Gitbash。这需要在`user settings (json)`里面修改相关的terminal设置，直接`Ctrl  + Shift + p `搜索users。   
```bash 
C:\Users\Administrator\AppData\Roaming\Code\User\settings.json
...... 
......
......

"terminal.integrated.profiles.windows": {
        "Msys2": {
            "path": "cmd",
            "args":[
            			"/c",
            			"C:\\Your Msys2 Install Path \\msys2_shell.cmd -defterm -no-start -here -use-full-path -shell <Your love shell >  -<msys2/mingw64/mingw32/Yout Msys2 version> "
            			],
            "env":{
            "MSYSTEM":"MINGW64/Your MSYS2 version",
             "PATH":"/mingw64/bin:/usr/bin:/usrlocal/bin:/bin:/sbin:${env:PATH}"
             },
            "icon": "terminal-linux"
        }
        ...........................
   },
   "terminal.integrated.defaultProfile.windows":"Msys2" 
   ............................
```
这样就可以使用Msys了。  
Msys2使用`pacman`作为包管理器。  
> ***pacman***:  [https://www.msys2.org/docs/package-management](https://www.msys2.org/docs/package-management/ )  

* Msys2 
`Msys2`可以在Windows下提供一个类似于Linux的环境，使用体验还不错，配合软件`Windows Terminal`和`zsh`可以获得一个很好用的终端。 `Msys2`的包管理器`packman`也提供了一般开发使用的库。   


# WSL 
也可以使用`wsl2`来完成平常的开发，项目文件存放在`wsl`内，VSCode需要安装插件。  
然后在项目目录下`code  . ` 









