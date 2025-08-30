+++
date = '2025-08-30T01:27:24+08:00'
draft = false
title = 'Hugo 使用教程'
summary = 'Hugo 是一个静态web网站生成器，记录一下它的使用方法。'
tags = ['Hugo','使用教程']
+++
## 简单介绍
Hugo是一个静态web网站生成器。同类软件有：hexo，Jekyll。相比后两个，我更喜欢Hugo，它由go语言编写，速度快，直接一个二进制程序，没有任何依赖，就很简洁。

## 使用方法
创建一个website 项目

```shell
hugo new site <projectName>
```
进入项目目录

```shell
cd <projectName>
```
添加主题， 主题网站： https://themes.gohugo.io

```shell
git init
git submodule add <主题git地址> themes/<主题名称>
```

打开hugo.toml文件，添加下面内容设置主题

```shell
theme = '<主题名称>'
```
创建文章

```shell
hugo new posts/<文章名称>.md
```
在 content/posts/ 目录下可以找到刚刚创建的<文章名称>.md文件，内容类似这样：

```md
+++
date = '2025-08-30T02:02:00+08:00'
draft = true
title = 'Test'
+++
```
上面的被 “+++” 括起来的叫 ”Front Matter“，就是记录了文章的一些信息。

- date 是文章创建的日期
- draft 表示是不是草稿状态，当要正式发布时，改为false
- title 表示文章名称，可以改

常见Front Matter 参数

- title: 页面或文章的标题。
- date: 文章的创建日期。
- draft: 布尔值（true 或 false），如果设置为 true，则该文章被视为草稿，默认不会被构建（除非使用 hugo -D 命令）。
- summary: 简短的文章摘要或描述，通常用于 SEO 和网站列表页。
- author: 文章的作者。
- tags: 文章的标签，通常是一个字符串数组。例如：tags: ["Hugo", "建站", "技巧"]。
- categories: 文章的分类，与标签类似，但通常用于更宽泛的主题归类。例如：categories: ["技术", "教程"]。
- featured_image: 文章的特色图片或封面图片的路径。
- weight: 用于控制内容的排序，通常数字越小，文章在列表中的位置越靠前。
- lastmod: 文章最后修改的日期。
- toc: 布尔值（true 或 false），用于控制是否在页面上生成目录（Table of Contents）。``
- aliases: 一个字符串数组，用于设置该页面的别名或旧 URL，当访问这些别名时会自动重定向到新 URL，这对于更改文章的 URL 很重要

运行本地web server，查看网站效果

```shell
hugo server
```
运行结果部分内容如下

```shell
Built in 1 ms
Environment: "development"
Serving pages from disk
Running in Fast Render Mode. For full rebuilds on change: hugo server --disableFastRender
Web Server is available at http://localhost:1313/ (bind address 127.0.0.1) 
Press Ctrl+C to stop
```
找到  http://localhost:1313/ 到浏览器访问即可

构建静态站点

```shell
hugo
```
生成的静态站点文件在public目录中，找个静态网站托管平台托管即可。

清理缓存

```shell
hugo --gc
```
或者

```shell
rm -rf public
```
## 总结
	简单记录下hugo的使用方法。

