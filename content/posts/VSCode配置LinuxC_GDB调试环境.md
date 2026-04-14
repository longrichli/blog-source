+++
date = '2026-04-13T22:53:49+08:00'
draft = false
title = 'VS Code 配置 LinuxC/C++ GDB调试环境'
summary = '本文介绍了VS Code 如何集成GDB，可视化的调试 Linux C/C++ 程序。'
tags = ['GDB', 'VS Code', 'Debug']
categories = [ '使用教程' ]
+++

## 1. 安装c/c++插件

![iamge-00](https://lcf-resources.oss-cn-beijing.aliyuncs.com/resources/longrichli-blog/2026/04/VSCode配置LinuxC调试环境/iamge-00.png)

## 2. 编写debug配置文件

1. 点击debug按钮，然后点击 create a launch.json file 链接 ，如下图所示

![image-01](https://lcf-resources.oss-cn-beijing.aliyuncs.com/resources/longrichli-blog/2026/04/VSCode配置LinuxC调试环境/image-01.png)

2， vs code 会创建 launch.json 文件

![image-02](https://lcf-resources.oss-cn-beijing.aliyuncs.com/resources/longrichli-blog/2026/04/VSCode配置LinuxC调试环境/image-02.png)

3. 点击 add configuration 按钮

![image-03](https://lcf-resources.oss-cn-beijing.aliyuncs.com/resources/longrichli-blog/2026/04/VSCode配置LinuxC调试环境/image-03.png)

4. 点击 gdb launch 后， vs code会自动补全配置

![image-04](https://lcf-resources.oss-cn-beijing.aliyuncs.com/resources/longrichli-blog/2026/04/VSCode配置LinuxC调试环境/image-04.png)

补全后如下所示

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) Launch",
            "type": "cppdbg",
            "request": "launch",
            "program": "enter program name, for example ${workspaceFolder}/a.out",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${fileDirname}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                },
                {
                    "description": "Set Disassembly Flavor to Intel",
                    "text": "-gdb-set disassembly-flavor intel",
                    "ignoreFailures": true
                }
            ]
        }

    ]
}
```

5. 在json中设置好程序的路径和参数和程序工作路径

```json
{
            "program": "程序路径",
            "args": ["参数1", "参数2", "..."],
            "cwd":"程序工作路径"
}
```

6. 点击菜单栏的Run菜单下面的Start Debugging 调试即可

![image-05](https://lcf-resources.oss-cn-beijing.aliyuncs.com/resources/longrichli-blog/2026/04/VSCode配置LinuxC调试环境/image-05.png)

7. 使用技巧
- 打断点后，鼠标右键点击断点可以编辑条件断点
- debug console 中 输入 -exec &lt;gdb command&gt; 可以执行gdb调试命令