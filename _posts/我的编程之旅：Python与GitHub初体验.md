---
layout:     post
title:      我的编程之旅：Python与GitHub初体验
subtitle:   编程之旅
date:       2024-10-20
author:     BY
header-img: img/post-bg-cook.jpg
catalog: true
tags:
    - iOS
---

- 前言：
作为一名编程新手，我踏上了探索Python和GitHub的旅程。Python以其简洁明了的语法和强大的功能吸引了我，而GitHub则是代码管理与协作的圣地。以下是我在学习过程中的笔记和心得体会，希望对同样处于起点的你有所帮助。

- Python：我的编程启蒙
1、为什么选择Python？
易学：Python的语法清晰，接近英语，非常适合编程初学者。
应用广泛：无论是Web开发、数据分析还是机器学习，Python都能胜任。

2、学习资源
官方教程：Python官网提供了详尽的入门教程。
在线课程：Coursera、edX等平台提供了丰富的Python课程。
书籍：《Python Crash Course》和《Automate the Boring Stuff with Python》是两本很好的入门书籍。

3、我的学习心得
实践是关键：通过编写小程序和解决实际问题来学习Python。
代码复用：学会利用Python的丰富库来简化编程任务。
社区支持：Stack Overflow和Reddit等社区是解决编程问题的宝贵资源。
	
- GitHub：代码管理的艺术
	
1、为什么使用GitHub？
版本控制：通过Git进行代码版本控制，方便追踪更改和协作。
代码分享：GitHub是分享代码、参与开源项目的最佳平台。

2、我的使用心得
分支管理：使用分支进行功能开发和错误修复，避免干扰主分支。
合并请求：通过合并请求（Pull Request）参与代码审查和协作。
GitHub Pages：利用GitHub Pages托管个人网站或博客。
	
- 结语
Python和GitHub是我编程之旅的两大支柱。Python让我体验到了编程的乐趣，而GitHub则教会了我如何高效地管理代码和与他人协作。虽然刚开始时可能会遇到挑战，但坚持不懈和不断学习将带来巨大的回报。

### 2. OC内存管理的范围


## 内存管理的原理及分类


- **引用计数**

- `ARC` or `MRC`

## MRC快速入门

- +1 

	alloc new copy 
- -1 

	release autorelease
	
## 内存管理原则


- 原则

	对象有人使用，不回收
	
	使用对象，+1
	
	不使用对象 -1
	
- 谁创建， 谁release

- 谁retain， 谁release 
- 总结

	+1 就该-1
	
	
内存管理的研究内容：

1. 野指针
	
	1. 定义的指针没有初始化（没有指向）
	2. 指向的空间已经被释放 （僵尸对象）

2. 内存泄漏

	栈区指针变量已经被释放，而堆区的空间还没有被释放
	
## 单个对象的内存管理（野指针）

###僵尸对象
已经被释放的对象，在内存中可能还能继续访问，但容易出

开启僵尸对象检测：

<img src = "https://ww3.sinaimg.cn/large/006tKfTcgy1fbul3381n8j30pu0eyq4w.jpg" width = "800">

### 避免使用僵尸对象

对象释放后，将 `指针` -> `nil`

### 带个对象的内存泄漏

⚠️ 注意 相互持有 、自己调用自己 的情况
		
	[self mothd:self]；
 

