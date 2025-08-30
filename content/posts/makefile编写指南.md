+++
date = '2025-08-30T23:16:41+08:00'
draft = false
title = 'Makefile编写指南'
summary = '本文介绍了makefile文件是什么，以及编写方法。给出单个Makefile文件编译示例和多个Makefile文件编译示例'
tags = ['make', 'Makefile', '使用教程']
+++


## 1. **Makefile 基础概念**

1. Makefile 用于自动化编译、构建、测试、打包等任务。
2. `make` 工具会根据 **Makefile** 定义的规则执行任务。
3. 常见格式：
    
    ```makefile
    target: prerequisites
        command
    ```
    
    - **target**：目标，例如生成的文件或任务名。
    - **prerequisites**：依赖文件或其他目标。
    - **command**：执行的命令，必须以 **Tab 键** 开头。

## 2. **简单示例**

假设有以下源文件：`main.c` 和 `functions.c`。

### 示例 Makefile

```makefile
# 变量定义
CC = gcc
CFLAGS = -Wall -g
TARGET = program
OBJECTS = main.o functions.o

# 默认目标
all: $(TARGET)

# 编译目标文件
$(TARGET): $(OBJECTS)
	$(CC) $(CFLAGS) -o $(TARGET) $(OBJECTS)

# 编译源文件为中间目标 (object 文件)
main.o: main.c
	$(CC) $(CFLAGS) -c main.c

functions.o: functions.c
	$(CC) $(CFLAGS) -c functions.c

# 清理目标
clean:
	rm -f $(TARGET) $(OBJECTS)

# 伪目标：不生成文件
.PHONY: all clean
```

### 执行流程

1. **执行 `make` 命令**：
    - `make` 会查找默认目标 `all` 并执行相关依赖规则。
2. **编译生成目标文件**：
    - `main.c` 和 `functions.c` 会被编译为 `main.o` 和 `functions.o`。
3. **链接生成最终可执行文件**：
    - 链接中间文件，生成 `program`。
4. **执行 `make clean`**：
    - 清理中间文件和目标文件。

---

## 3. **Makefile 语法详解**

### 3.1 **变量**

- 使用 `=` 定义变量。
- 使用 `$(VAR)` 引用变量。

```makefile
CC = gcc
CFLAGS = -Wall -g
```

### 3.2 **自动变量**

- `$@`：表示目标文件名。
- `$<`：第一个依赖文件。
- `$^`：所有依赖文件。

```makefile
$(TARGET): $(OBJECTS)
	$(CC) $(CFLAGS) -o $@ $^
```

### 3.3 **伪目标 (.PHONY)**

- 用于指定不会生成实际文件的目标，如 `clean`、`all`。

```makefile
.PHONY: clean
clean:
	rm -f $(TARGET) $(OBJECTS)
```

### 3.4 **模式规则**

- 通过通配符简化规则，`%` 表示任意字符。

```makefile
%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@
```

表示将所有 `.c` 文件编译成对应的 `.o` 文件。

---

## 4. **Makefile 进阶技巧**

### 4.1 **自动搜索依赖**

使用 `-MMD` 选项自动生成依赖文件。

```makefile
CFLAGS = -Wall -g -MMD
-include $(OBJECTS:.o=.d)
```

### 4.2 **多目标编译**

```makefile
all: program1 program2

program1: program1.c
	$(CC) -o program1 program1.c

program2: program2.c
	$(CC) -o program2 program2.c
```

### 4.3 **定义函数**

使用 `define` 定义复杂操作。

```makefile
define compile
	$(CC) $(CFLAGS) -c $1.c -o $1.o
endef

main.o:
	$(call compile, main)
```

---

## 5. **常用命令总结**

| 命令 | 说明 |
| --- | --- |
| `make` | 执行默认目标（第一个规则） |
| `make target` | 执行指定目标 |
| `make clean` | 执行`clean`规则 |
| `make -jN` | 并行执行任务，N 表示线程数 |
| `make -n` | 只打印命令，不执行 |

---

## 6. **示例执行过程**

```bash
# 编译项目
make

# 执行清理
make clean

# 使用多线程编译
make -j4
```

---

## 7. 基本示例

### 7.1 编译Makefile文件同级目录下的 .c 文件

```makefile
# 变量定义
CC = gcc
CFLAGS = -Wall -g
TARGET = program

# 自动查找当前目录下的所有 .c 文件
SRCS := $(wildcard *.c)
OBJS := $(SRCS:.c=.o)

# 默认目标
all: $(TARGET)

# 链接目标文件
$(TARGET): $(OBJS)
	$(CC) $(CFLAGS) -o $@ $(OBJS)

# 编译 .c 文件为 .o 文件（模式规则）
%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@

# 清理目标
clean:
	rm -f $(TARGET) $(OBJS)

.PHONY: all clean
```

### 7.2 编译Makefile文件所在目录及子目录的c文件

```makefile
# 变量定义
CC = gcc
CFLAGS = -Wall -g
TARGET = program

# 查找当前目录及所有子目录中的 .c 文件
SRCS := $(shell find . -name "*.c")
OBJS := $(SRCS:.c=.o)

# 默认目标
all: $(TARGET)

# 链接目标文件
$(TARGET): $(OBJS)
	$(CC) $(CFLAGS) -o $@ $(OBJS)

# 编译 .c 文件为 .o 文件（模式规则）
%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@

# 清理目标
clean:
	rm -f $(TARGET) $(OBJS)

.PHONY: all clean

```

### 7.3 多个Makefile编译

1. 假设项目目录结构如下：

```makefile
project/
├── Makefile           # 顶层 Makefile
├── main.c
├── src/
│   ├── Makefile       # src 目录下的 Makefile
│   ├── func1.c
│   └── func2.c
└── lib/
    ├── Makefile       # lib 目录下的 Makefile
    ├── utils.c
    └── helper.c
```

1. 顶层 `Makefile`

顶层 `Makefile` 负责调用子目录中的 `Makefile` 并编译整个项目。

```makefile
# 变量定义
SUBDIRS = src lib
CC = gcc
CFLAGS = -Wall -g
TARGET = program

# 默认目标
all: subdirs $(TARGET)

# 编译主程序
main.o: main.c
	$(CC) $(CFLAGS) -c main.c -o main.o

# 链接所有目标文件
$(TARGET): main.o
	@echo "Linking target: $(TARGET)"
	$(CC) $(CFLAGS) -o $(TARGET) main.o src/*.o lib/*.o

# 递归编译子目录
subdirs:
	@for dir in $(SUBDIRS); do \
		echo "Building in $$dir..."; \
		$(MAKE) -C $$dir; \
	done

# 清理目标
clean:
	@for dir in $(SUBDIRS); do \
		echo "Cleaning in $$dir..."; \
		$(MAKE) -C $$dir clean; \
	done
	rm -f $(TARGET) main.o

.PHONY: all clean subdirs

```

1. 子目录的 `Makefile`

`s**rc/Makefile**`

```makefile
CC = gcc
CFLAGS = -Wall -g
OBJS = func1.o func2.o

all: $(OBJS)%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@

clean:
	rm -f $(OBJS)

.PHONY: all clean
```

**`lib/Makefile`**

```makefile
CC = gcc
CFLAGS = -Wall -g
OBJS = utils.o helper.o

all: $(OBJS)%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@

clean:
	rm -f $(OBJS)

.PHONY: all clean
```

1. 工作原理
1. 顶层 `Makefile`：
    - 定义了子目录 `SUBDIRS = src lib`。
    - 使用 `$(MAKE) -C` 命令进入子目录并执行子目录的 `Makefile`。
2. **递归编译**：
    - `$(MAKE) -C dir` 会在 `dir` 目录中执行 `make`。
    - 子目录的 `Makefile` 负责编译自己的 `.c` 文件并生成 `.o` 文件。
3. **主程序链接**：
    - 在顶层 `Makefile` 中，链接主程序的 `main.o` 和子目录生成的所有 `.o` 文件，生成最终的可执行文件。
4. **清理目标**：
    - `make clean` 会递归调用子目录的 `Makefile` 中的 `clean` 目标，删除所有中间文件。

end.