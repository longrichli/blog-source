+++
date = '2026-04-18T18:44:23+08:00'
draft = false
title = 'core文件调试教程'
summary = '当进程被某些信号终止后，会产生一个包含进程终止时的内存映像的文件，这个文件叫做 core 文件，中文名叫核心转储文件。这个文件可以被调试器（如GDB）使用，用来查看进程终止时的状态。本文介绍了如何用 GDB 来调试 core 文件。'
tags = ['core', 'GDB', 'Linux C', 'Debug']
categories = [ '使用教程' ]
+++

## 什么是 core 文件
当进程被某些信号终止后，会产生一个包含进程终止时的内存映像的文件，这个文件叫做 core 文件，中文名叫核心转储文件。这个文件可以被调试器（如GDB）使用，用来查看进程终止时的状态。

以下是常见的会产生 core 文件的信号
| 信号名       | 编号 | 含义         | 是否常见  | 典型触发场景                            |
| --------- | -- | ---------- | ----- | --------------------------------- |
| `SIGSEGV` | 11 | 段错误        | ⭐⭐⭐⭐⭐ | 空指针、野指针、越界访问、use-after-free       |
| `SIGABRT` | 6  | 程序主动中止     | ⭐⭐⭐⭐  | `assert` 失败、`abort()`、double free |
| `SIGFPE`  | 8  | 算术异常       | ⭐⭐⭐   | 除 0、浮点异常                          |
| `SIGILL`  | 4  | 非法指令       | ⭐⭐⭐   | 函数指针错误、执行非法内存                     |
| `SIGBUS`  | 7  | 总线错误       | ⭐⭐⭐   | mmap 越界、未对齐访问                     |
## ulimit 设置

```jsx
ulimit -a // 查看是否限制core文件生成
```

执行结果如下

```jsx
real-time non-blocking time  (microseconds, -R) unlimited
core file size              (blocks, -c) 0
data seg size               (kbytes, -d) unlimited
scheduling priority                 (-e) 0
file size                   (blocks, -f) unlimited
pending signals                     (-i) 14341
max locked memory           (kbytes, -l) 64
max memory size             (kbytes, -m) unlimited
open files                          (-n) 1024
pipe size                (512 bytes, -p) 8
POSIX message queues         (bytes, -q) 819200
real-time priority                  (-r) 0
stack size                  (kbytes, -s) 8192
cpu time                   (seconds, -t) unlimited
max user processes                  (-u) 14341
virtual memory              (kbytes, -v) unlimited
file locks                          (-x) unlimited

```

当 core file size  为0 时， 不会生成 core 文件，使用如下命令改变 core file size。

```jsx
ulimit -c unlimited // unlimited 意思为不限制，也可以用数字指定大小，单位是 block （512字节）
```

## kernel.core_pattern  设置

当进程因为 **致命信号**（如 `SIGSEGV`、`SIGABRT`）崩溃时，内核会：

1. 检查 `RLIMIT_CORE`（即 `ulimit -c`）
2. 若允许生成 core
3. 按 `kernel.core_pattern` 指定的规则
    
    → **生成 core 文件** 或 **将 core 通过管道交给用户态程序处理**
    

因此它决定了两件事：

- core 文件**叫什么名字 / 放在哪里**
- core 是**直接写文件**，还是**交给处理器（如 systemd-coredump）**

### 查看当前设置

```jsx
cat /proc/sys/kernel/core_pattern 
```

或者

```jsx
sysctl kernel.core_pattern
```

### 常见输出示例：

```
core
```

或：(后面说明这种方式，这种方式是交由了systemd-coredump 进程处理)

```
|/usr/lib/systemd/systemd-coredump%P%u%g%s%t%c%h%e
```

### 临时更改设置

1. 使用 `sysctl`

```bash
sudo sysctl -w kernel.core_pattern=core
```

或：

```bash
sudo sysctl -w kernel.core_pattern=/tmp/core.%e.%p
```

  2. 直接写 `/proc`

```bash
echo"/tmp/core.%e.%p" >> /proc/sys/kernel/core_pattern
```

### 永久更改设置

编辑：

```bash
sudo vim /etc/sysctl.conf
```

或创建单独配置文件（更规范）：

```bash
sudo vim /etc/sysctl.d/99-core.conf
```

写入：

```
kernel.core_pattern=/tmp/core.%e.%p
```

使其生效：

```bash
sudo sysctl -p # 或
sudo sysctl --system
```

### core_pattern 占位符（语义化命名）

| 占位符 | 含义 |
| --- | --- |
| `%e` | 可执行文件名 |
| `%p` | 进程 PID |
| `%u` | 用户 UID |
| `%g` | 用户 GID |
| `%s` | 导致 core 的信号编号 |
| `%t` | 崩溃时间（epoch 秒） |
| `%h` | 主机名 |
| `%c` | core 文件大小限制 |

### “管道模式”

如果 `core_pattern` **以 `|` 开头**，表示：

> **不生成 core 文件，而是把 core dump 通过管道传给一个用户态程序**
> 

典型例子（现代发行版默认）：

```
|/usr/lib/systemd/systemd-coredump%P%u%g%s%t%c%h%e
```

这意味着：

- core **不在当前目录**
- 被 systemd 收集、压缩、存储
- 需要用专门工具查看

### systemd-coredump 相关操作

查看 core 列表

```bash
coredumpctl list
```

调试某个 core

```bash
coredumpctl gdb <PID>
```

导出 core 文件

```bash
coredumpctl dump <PID> > core.dump
```

## 调试 core 文件

### systemd-coredump 管理的 core 文件

见上述systemd-coredump 相关操作

### 普通 core 文件

```jsx
gdb <可执行文件> <core文件>
```

运行后到达崩溃处，然后bt，frame调试即可。
