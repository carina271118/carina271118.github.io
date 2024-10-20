---
layout:     post
title:      编程基础：Python Learning Journey
date:       2024-09-28
author:     BY
header-img: img/post-bg-cook.jpg
catalog: true
tags:
    - iOS
---


## Python Learning Journey
作为一名香港岭南大学融合科技理学硕士的学生，我最近完成了计算机编程基础课程。
这门课程不仅让我掌握了变量、循环、条件语句等基本概念，还让我对编程有了更深入的理解。
今天，我成功编写了一个数据分析脚本，它能够从数千条数据中提取关键信息，这让我对编程的热情更加高涨。
这门课程不仅提升了我的技术能力，也让我对未来的科技应用充满期待。



作为一名编程新手，我踏上了探索Python和GitHub的旅程。Python以其简洁明了的语法和强大的功能吸引了我，而GitHub则是代码管理与协作的圣地。以下是我在学习过程中的笔记和心得体会，希望对同样处于起点的你有所帮助。

## 正文

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


- 移除监听

```objc
- (void)removeObserver:(NSObject *)observer forKeyPath:(NSString *)keyPath;
```





@interface NSObject (kvo)
/// 添加一个KVO方法
- (void)by_addObserver:(NSObject *)observer forKeyPath:(NSString *)keyPath options:(NSKeyValueObservingOptions)options context:(nullable void *)context;
@end
```
> 下面代码运行会报错 在 `Build Settings` 中设置`ENABLE_STRICT_OBJC_MSGSEND = NO`即可


```objc
#import "NSObject+kvo.h"
#import <objc/runtime.h>
#import <objc/message.h>

@implementation NSObject (kvo)

- (void)by_addObserver:(NSObject *)observer forKeyPath:(NSString *)keyPath options:(NSKeyValueObservingOptions)options context:(void *)context{
    //动态添加一个类
    NSString *originClassName = NSStringFromClass([self class]);
    
    NSString *newClassName = [@"BY_NSKVONotifying_" stringByAppendingString:originClassName];
    
    // 继承自当前类，创建一个子类，类名模仿KVO底层命名 BY_NSKVONotifying_+类名的形式
    Class kvoClass = objc_allocateClassPair([self class], [newClassName UTF8String], 0);
    
    // 添加setter方法 这里我们只监听 name，手动添加setName方法。
    // v@:@：v 对应setName方法的返回值void，@: 表示方法本身，@ 表示参数是个对象
    class_addMethod(kvoClass, @selector(setName:), (IMP)setName, "v@:@");
    
    //注册新添加的这个类
    objc_registerClassPair(kvoClass);
    
    // 修改isa指针，由 personModel 指向我们创建的 BY_NSKVONotifying_BYPrsonModel 对象实现替换
    object_setClass(self, kvoClass);
    
    // 保存观察者属性到当前类中
    objc_setAssociatedObject(self, (__bridge const void *)@"observer", observer, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
}

#pragma mark - 重写父类方法

void setName(id self, SEL _cmd, NSString *name) {
    
    // 保存当前KVO的类
    Class kvoClass = [self class];
    
    // 将self的isa指针指向父类BYPersonModel，调用父类setter方法
    object_setClass(self, class_getSuperclass([self class]));
    objc_msgSend(self, @selector(setName:), name);
    
    // 获取BY_NSKVONotifying_BYPrsonModel观察者
    id objc = objc_getAssociatedObject(self, (__bridge const void *)@"observer");
    // 通知观察者，执行通知方法
    NSDictionary<NSKeyValueChangeKey,id> *change = @{@"kind": @1, @"new": name};
    objc_msgSend(objc, @selector(observeValueForKeyPath:ofObject:change:context:), @"name", self, change, nil);
    
    // 将指针重新指向 BY_NSKVONotifying_BYPrsonModel
    object_setClass(self, kvoClass);
}


@end
```

使用我们的kvo方法

```objc
self.personModel = [[BYPersonModel alloc] init];
[self.personModel setName:@"Tony Qiu"];
    
NSLog(@"%@", object_getClass(self.personModel));
[self.personModel by_addObserver:self forKeyPath:@"name" options:NSKeyValueObservingOptionNew | NSKeyValueObservingOptionOld context:nil];
NSLog(@"%@", object_getClass(self.personModel));
```

输出

```
KVO_demo[51608:1429122] BYPersonModel
KVO_demo[51608:1429122] BY_NSKVONotifying_BYPersonModel
KVO_demo[51608:1429122] {
    kind = 1;
    new = "Peng YuYan";
}
```

可以看到，`BYPersonModel`类被替换成了`BY_NSKVONotifying_BYPersonModel`类，也能监听到`name`的变化，手写KVO成功。
当然实际的KVO实现的细节远比我们手写的复杂，这个只是一探究竟而已。



### 参考
- [《Introduction to Key-Value Observing Programming Guide》
](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html#//apple_ref/doc/uid/10000177-BCICJDHA)
- https://blog.csdn.net/science_lee/article/details/82843080
- https://www.cenzhijun.top/2018/05/kvo/
