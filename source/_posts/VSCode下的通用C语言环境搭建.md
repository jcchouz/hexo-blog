---
title: Visual Studio Code(VSCode)下的通用C语言环境搭建
date: 2020-09-11 14:42:55
tags:
  - Visual Studio Code
  - C
categories:
  - [Visual Studio Code]
  - [C]
---
> 版权声明：本文为博主原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接和本声明。  
> 本文链接：https://www.cnblogs.com/czlhxm/p/11794743.html   
> 参考博客：整理：Visual Studio Code (vscode) 配置C、C++环境/编写运行C、C++（主要Windows、简要Linux）

# 主要流程

1. vscode的下载与简单配置
1. MinGw的下载与安装
1. 配置系统环境变量
1. 修改vscode调试配置文件
1. vscode下开发C项目的使用细节
1. 通用的C语言debug方法
1. 常见问题与解决方案(不定时更新)

<!-- more -->

# 1.vscode的下载与简单配置

https://code.visualstudio.com/Download

# 2.MinGw的下载与安装

因为windows下vscode不直接具备对于C语言的编译调试工具，所以要下载集成gcc等工具的MinGw以提供扩展支持。

https://sourceforge.net/projects/mingw-w64/files/

下载：`x86_64-posix-seh`

![image.png](https://i.loli.net/2020/09/11/LvHFOyNeXtiBSZx.png)

将压缩包内的`mingw64`，解压出来到`C:\Program Files (x86)`，并重命名`MinGW`

# 3.配置系统环境变量Path

打开我的电脑-属性-高级系统设置-高级-环境变量
以下配置均为***系统变量
!!!***
+ 在Path变量中，增加`C:\Program Files (x86)\MinGW\bin`

![image.png](https://i.loli.net/2020/09/11/MkLKrJnyvpgFW3u.png)
+ 新加INCLUDE变量，值为`C:\Program Files (x86)\MinGW\include`

![image.png](https://i.loli.net/2020/09/11/MGQSZ4hI9LEczHb.png)

完成后，全部确定，关闭环境变量设置窗口，按win+r键打开运行，输入cmd回车后，可在控制台下输入gcc -v查看当前计算机mingw是否安装成功

![image.png](https://i.loli.net/2020/09/11/Xl6W8pBMTcqJoay.png)

# 4.修改vscode调试配置文件

重启vscode，安装两个扩展：
+ C/C++
![image.png](https://i.loli.net/2020/09/11/ElgD2YS7a1uqAx5.png)
+ Code Runner
![image.png](https://i.loli.net/2020/09/11/wlRa6ZmkeUx7YHz.png)
配置Code Runner，勾选这两个：
![image.png](https://i.loli.net/2020/09/11/KT4fIOxpQN7J5bz.png)

写一个hello world!程序，并点击右上角箭头，看是否成功运行，vscode下方终端是否有程序运行结果

![image.png](https://i.loli.net/2020/09/11/alOMEf3dtzjSFPY.png)

![image.png](https://i.loli.net/2020/09/11/W1LpMIteQKcJdzE.png)

接下来配置C/C++ debuger

在之前新建的文件夹下再新建一个名为.vscode的文件夹，新建一个launch.json文件，输入：
```
{
    "version": "0.2.0",  
    "configurations": [  
        {  
            "name": "(gdb) Launch", // 配置名称，将会在启动配置的下拉菜单中显示
            "type": "cppdbg",       // 配置类型，这里只能为cppdbg
            "request": "launch",    // 请求配置类型，可以为launch（启动）或attach（附加）  
            "program": "${workspaceFolder}/${fileBasenameNoExtension}.exe",// 将要进行调试的程序的路径  
            "args": [],             // 程序调试时传递给程序的命令行参数，一般设为空即可  
            "stopAtEntry": false,   // 设为true时程序将暂停在程序入口处，一般设置为false  
            "cwd": "${workspaceFolder}", // 调试程序时的工作目录，一般为${workspaceFolder}即代码所在目录  
            "environment": [],  
            "externalConsole": true, // 调试时是否显示控制台窗口，一般设置为true显示控制台  
            "MIMode": "gdb",  
            "miDebuggerPath": "C:\\Program Files (x86)\\MinGW\\bin\\gdb.exe", // miDebugger的路径，注意这里要与MinGw的路径对应  
            "preLaunchTask": "gcc", // 调试会话开始前执行的任务，一般为编译程序，c++为g++, c为gcc  
            "setupCommands": [  
                {   
            "description": "Enable pretty-printing for gdb",  
                    "text": "-enable-pretty-printing",  
                    "ignoreFailures": true  
                }  
            ]  
        }  
    ]  
}
```

注意这里"miDebuggerPath": "D:\\Install\\bin\\gdb.exe"将其值改成自己所解压的mingw\bin\gdb.exe的路径

其他设置可看情况修改

再新建tasks.json文件，输入：
```
{
    "version": "2.0.0",
    "command": "gcc",
    "args": ["-g","${file}","-o","${fileBasenameNoExtension}.exe"],    // 编译命令参数
    "problemMatcher": {
        "owner": "cpp",
        "fileLocation": ["relative", "${workspaceFolder}"],
        "pattern": {
            "regexp": "^(.*):(\\d+):(\\d+):\\s+(warning|error):\\s+(.*)$",
            "file": 1,
            "line": 2,
            "column": 3,
            "severity": 4,
            "message": 5
        }
    }
}
```

注意这里的"command": "gcc"要与launch.json中的"preLaunchTask": "gcc"对应，即都为gcc或都为g++

保存后，回到最初的C代码文件，菜单-调试-启动调试(快捷键F5)，即可成功运行C程序！

![image.png](https://i.loli.net/2020/09/11/8aqw534hpHvzV2c.png)

# 5.vscode下开发C项目的使用细节

对于通常情况下，可将上述完成的.vscode文件夹放在C代码保存的大目录下，该目录下的所有C文件在vscode下调试都将适用该配置文件夹。

注意，在不含上述.vscode文件夹的C程序都无法直接调试！需要重新配置。(或者将配置好的.vscode文件夹复制到该程序所在目录下)

正在编辑过程中，要充分利用vscode的资源管理器，菜单-文件-打开文件夹/打开工作区的方式打开一个含.vscode文件夹的目录，可进行调试。

对于不需要调试的C程序，可利用预装好code runner(装好后窗口右上角会多出一排按钮)直接点击编译运行，在下方终端中可进行数据的输入与输出。

注意在调试方法下运行C程序，需在return前下断点或中断函数等方法使程序到执行出口前停下，否则可能会出现预料之内的闪退。

# 6.通用的C语言debug方法

vscode下的调试工具非常方便，对于需要调试的代码，可在对应行的行数前鼠标左键单击，即可下断点，右键断点，可对断点进行简单管理，如禁用或修改为逻辑断点

![image.png](https://i.loli.net/2020/09/11/TPeYJjgiVcwfWMm.png)

常用快捷键下的调试：
+ F5进入调试
+ F10单步跳过
+ F11单步执行(可进入执行函数体)
+ Shift+F5停止调试
+ Ctrl+Shift+F5重启调试

对于调试异常终止，若不能正常重新启动调试的，重启vscode试试

若上述操作存在无响应的，试试万能的以管理员方式启动大法。

# 7.常见问题与解决方案(不定时更新)

对于程序中含中文调试程序显示乱码的：右下角-UTF8-通过编码保存-输入gb2312回车

对于打开程序发现原本应该是中文的乱码的：右下角-UTF8-通过编码重新打开-输入gb2312回车s