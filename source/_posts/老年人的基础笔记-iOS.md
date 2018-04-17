---
title: 老年人的基础笔记-iOS
date: 2018-4-11 16:21:34
top: flase
tags: 
	- note
---



*太久没有动笔了，最近面临着换工作，需要复习一下早已忘记的基础知识了，考虑到老年人只有1周的记忆，所以2018年第一篇博客，献给我自己，方便以后随时查看补充。*



<!-- more -->

## 架构模式

### 什么才是好的架构

- 各部分职责清晰、单一、具体
- 具有可测性，让单元测试尽可能覆盖
- 使用成本低，维护成本也低

### MVC

两张经典的图，第一张为苹果期望中的MVC，第二张为实际项目中的MVC：

![苹果期望的MVC](/assets/ArticleImg/mvc1.png)

![现实中的MVC](/assets/ArticleImg/mvc2.png)

Cocoa中的MVC可以称作重控制器模式，期望中View与Model通过Controller进行间接联系，从而达到解耦，但是实际中View与Controller耦合非常严重，Controller异常庞大，可测试性与Debug效率很低。但是相比其他模式，代码总量偏少，门槛低，便于简单的项目进行快速开发。

### MVP

这是一张关于MVP的经典图：

![mvp](/assets/ArticleImg/mvp.png)

与MVC看似相似，实际上有很大不同，View层实际上是View也是Controller，Presenter层用来负责将Model与View/Controller进行组合，并且不会存在具体的布局，只有数据组装。一般来讲，逻辑部分的处理都在Model与Presenter层，这就造就了一个轻量级的View层，便于进行单元测试，虽然代码量多了很多很多，但是设计逻辑并不会比MVC复杂多少。

### MVVM

还是先放经典图：

![mvvm](/assets/ArticleImg/mvvm.png)

与MVP的结构看起来很像，Presenter换成了ViewModel而已。其实，ViewModel并不是直接把二者的数据组合到一起，View层负责绑定ViewModel并传递所有的事件消息，ViewModel响应这些消息进而更新数据并进行页面展示。简单来讲，ViewModel连接View与Model，其中一者发送消息，另一方及时改变。

MVVM的绑定与消息机制可以通过KVO或者消息通知等方式实现，或者可以用到更强大的ReactiveCocoa这种响应式框架，但同时也将面临着复杂的调试环境。

### 小结

一种架构并不是一个项目中唯一存在，而是根据项目规模、业务场景以及各种因素决定的，MV(X)架构模式都是彼此兼容，可以任意组合和转换的。



## 类与实例

### 类对象

在`objc.h`中可以看到类是由`Class`表示的，实质上是一个指向`objc_class`结构体的指针：

``````
/// An opaque type that represents an Objective-C class.
typedef struct objc_class *Class;
``````

而在`runtime.h`中的 `objc_class`结构体的定义：

```
struct objc_class {
    Class _Nonnull isa  OBJC_ISA_AVAILABILITY;

#if !__OBJC2__
    Class _Nullable super_class                              OBJC2_UNAVAILABLE;
    const char * _Nonnull name                               OBJC2_UNAVAILABLE;
    long version                                             OBJC2_UNAVAILABLE;
    long info                                                OBJC2_UNAVAILABLE;
    long instance_size                                       OBJC2_UNAVAILABLE;
    struct objc_ivar_list * _Nullable ivars                  OBJC2_UNAVAILABLE;
    struct objc_method_list * _Nullable * _Nullable methodLists                    OBJC2_UNAVAILABLE;
    struct objc_cache * _Nonnull cache                       OBJC2_UNAVAILABLE;
    struct objc_protocol_list * _Nullable protocols          OBJC2_UNAVAILABLE;
#endif

} OBJC2_UNAVAILABLE;
```

- isa指针指向的是也是一个类，类对象的isa指向的是这个类的所属类，也就是元类(metaclass)。日常使用的类方法就是保存在元类的类方法列表中，使用时通过isa去元类中的类方法列表寻找对应的函数指针。
- ivars成员变量列表，保存着这个类下面的所有实例变量的地址指针。
- info标识符，其中`CLS_CLASS(0x1)`表示该类为普通类，而`CLS_META(0x2)`表示该类为元类，还有其他将近20个标识，具体可以查看`runtime.h`源码。
- methodLists方法列表，根据info标识符，普通类保存的就是实例方法，元类保存的就是类方法。
- cache缓存方法列表，在调用方法的时候，系统会先遍历缓存中是否使用过该方法，没有的话再去方法列表调用。

### 实例对象

对类对象使用`new/alloc`操作后会创建该类的实例对象，本质使用`class_createInstance`方法创建一个指向`objc_object`结构体的指针，在`objc.h`中定义为：

```
/// Represents an instance of a class.
struct objc_object {
    Class _Nonnull isa  OBJC_ISA_AVAILABILITY;
};
```

每个实例的类型都是它的类对象，而`id`也是一个实例对象的类型，这个类型可以转换成任何一种实例对象，在`objc.h`中的定义：

```
/// A pointer to an instance of a class.
typedef struct objc_object *id;
```

### 元类对象

元类是类对象的类，类对象的isa指针指向的就是元类，它的isa指针指向的是它父类的地址，其中存储着类对象的类方法列表。`runtime.h`中提供了一些元类的方法：

```
OBJC_EXPORT Class _Nullable
objc_getMetaClass(const char * _Nonnull name)
    OBJC_AVAILABLE(10.0, 2.0, 9.0, 1.0, 2.0);
OBJC_EXPORT BOOL
class_isMetaClass(Class _Nullable cls) 
    OBJC_AVAILABLE(10.5, 2.0, 9.0, 1.0, 2.0);
```

### 实例、类、元类的关系

从网上找的很经典的两张图：

![instance、class、metaclass1](/assets/ArticleImg/instance、class、metaclass1.png)

![instance、class、metaclass2](/assets/ArticleImg/instance、class、metaclass2.jpg)

通过这两张图能很明显地看出三者之间的横向、纵向以及内部的关系，不再赘述。

## KVC与KVO 

### KVC

键值编码，使用`valueForKey:`来获取值，使用`setValue:forKey:`来赋值，利用字符串作为Key去查找和修改相应的Value，流程大致如下：

- 在对象中查找`set`方法，如果有，就给这个对象赋值。
- 如果没有`set`方法，则寻找`_key`成员属性，如果有就赋值。
- 如果没有`_key`属性，则去寻找`key`属性，如果有就赋值。
- 以上都不成立，报错。

### KVO

键值观察，日常使用三个个方法：

```
// 添加观察者
- (void)addObserver:(NSObject *)observer forKeyPath:(NSString *)keyPath options:(NSKeyValueObservingOptions)options context:(nullable void *)context;
// 移除观察者
- (void)removeObserver:(NSObject *)observer forKeyPath:(NSString *)keyPath;
// 代理回调
- (void)observeValueForKeyPath:(nullable NSString *)keyPath ofObject:(nullable id)object change:(nullable NSDictionary<NSKeyValueChangeKey, id> *)change context:(nullable void *)context;
```

KVO是基于`runtime`机制实现的，内部原理如下：

- 当某个类`ClassA`的属性被观察时，系统会动态地创建一个派生类`NSKVONotifying_ClassA`继承于它。
- 在派生类中重写被观察属性的`set`方法，`NSObject(NSKeyValueObserverNotification)`的两个方法帮助完成了这整个的过程：

```
- (void)setSome:(id)aSome {
  [self willChangeValueForKey:@"some"];
  [super setValue:aSome forKey:@"some"];
  [self didChangeValueForKey:@"some"];
}
```

- 当被观察时，系统将`ClassA`的isa指针指向`NSKVONotifying_ClassA`，从而被观察属性变化时，调用的是重写后的`set`方法。
- 在被观察者值变化之前和之后，通过代理会掉通知观察者。

## 自动释放池

苹果文档上说明，不论在MRC还是ARC模式下，都应该用`@autoreleasepool {}`这个block来创建使用自动释放池。强调一下，`autoreleasepool`在ARC下依然有其重要作用！

在工程`main.m`中，系统为我们在程序的入口处就使用了一个`autoreleasepool`来包裹整个程序，每个程序中至少都要有一个`autoreleasepool`。

在MRC的环境下，自动释放池的整个过程大致为：

- 系统每个线程中存在一个`NSAutoreleasePool`的栈，当新的`autoreleasepool`创建时入栈，被销毁时出栈。


- `[Object autorelease]`表示将对象放入一个池中，会有很多对象进入`autoreleasepool`。
- 当`autoreleasepool`销毁时，池子出栈，每个池中的对象执行`[Object release]`。

### 何时使用`autoreleasepool`

苹果文档上是这么说的：

```
If you are writing a program that is not based on a UI framework, such as a command-line tool.
如果你写的项目不是基于UI framework，例如一个命令行工具。

If you write a loop that creates many temporary objects.
You may use an autorelease pool block inside the loop to dispose of those objects before the next iteration. Using an autorelease pool block in the loop helps to reduce the maximum memory footprint of the application.
如果你写一个包含很多临时对象的循环，你可以在循环内部使用一个自动释放池，在下一次循环前来管理这些对象。这样使用可以帮助减少app的峰值内存。

If you spawn a secondary thread.
You must create your own autorelease pool block as soon as the thread begins executing; otherwise, your application will leak objects.
如果你创建一个辅助线程，那么当线程一开始执行时，必须创建一个自动释放池，否则你的app将会出现内存泄漏。
```

对于iOS开发来说，不论在MRC还是ARC下，都可以用到的场景：

- 大型的循环可以在内部使用`autoreleasepool`优化循环的资源消耗。
- 使用常驻的辅助线程（类似于AFNetworking线程）时必须使用`autoreleasepool`。

`SDWebImage`中对图片的缓存用到了`autoreleasepool`来优化资源消耗：

```
dispatch_async(self.ioQueue, ^{
	if (operation.isCancelled) {
		return;
	}
	@autoreleasepool {
		UIImage *diskImage = [self diskImageForKey:key];
		if (diskImage && self.shouldCacheImagesInMemory) {
			NSUInteger cost = SDCacheCostForImage(diskImage);
			[self.memCache setObject:diskImage forKey:key cost:cost];
		}
		dispatch_async(dispatch_get_main_queue(), ^{
			doneBlock(diskImage, SDImageCacheTypeDisk);
		});
	}
});
```

## RunLoop

`RunLoop`是一个管理待处理事件和消息的对象，提供一个入口函数来执行事件循环的逻辑，是iOS消息机制的处理模式。每个线程都有自己的`RunLoop`来保证随时处理事件而不退出。

iOS系统中提供了两个`RunLoop`对象，一个是`NSRunLoop`，另一个是`CFRunLoopRef`。前者是基于后者的封装，提供了面向对象的API，但是这些API并未线程安全。而后者提供的是基于`CoreFoundation`框架的纯C函数API，这些API都是线程安全的。

### RunLoop与线程

`CFRunLoop`是基于`pthread`来管理的，由于苹果不允许直接创建`RunLoop`，所以只能通过两个函数来获取：

```
CF_EXPORT CFRunLoopRef CFRunLoopGetCurrent(void);
CF_EXPORT CFRunLoopRef CFRunLoopGetMain(void);
```

`RunLoop`与线程之间是一一对应的，其关系保存在一个全局的`Dictionary`中。线程创建后，如果不主动获取，那么一直不会创建自己的`RunLoop`，而线程结束时，`RunLoop`也同时销毁。除了主线程以外，只能在当前线程中才能获取其`RunLoop`。

### RunLoop的API

在`CFRunLoop.h`类中可以看到`RunLoop`大致的结构：

```
typedef CFStringRef CFRunLoopMode CF_EXTENSIBLE_STRING_ENUM;
typedef struct CF_BRIDGED_MUTABLE_TYPE(id) __CFRunLoop * CFRunLoopRef;
typedef struct CF_BRIDGED_MUTABLE_TYPE(id) __CFRunLoopSource * CFRunLoopSourceRef;
typedef struct CF_BRIDGED_MUTABLE_TYPE(id) __CFRunLoopObserver * CFRunLoopObserverRef;
typedef struct CF_BRIDGED_MUTABLE_TYPE(NSTimer) __CFRunLoopTimer * CFRunLoopTimerRef;
```

在这张图中能看出它们之间的大致关系：

![runloop1](/assets/ArticleImg/runloop1.png)

一个`RunLoop`中包含若干个`Mode`，每个`Model`中包含若干个`Source/Timer/Observer`。每次调用`RunLoop`时只能指定一个`Mode`，如果需要切换`Mode`，只能退出重新进入，指定一个新的。

- CFRunLoopRef中可以有多个`mode`。一个`mode`可以将自己的`_name`标记为`Common`属性来将其加入到`_commonModes`，每当 `RunLoop` 的内容发生变化时，都会自动将` _commonModeItems`里的 `Source/Observer/Timer`同步到具有`Common`标记的所有`mode`里。

```
struct __CFRunLoop {
    CFMutableSetRef _commonModes;     // Set
    CFMutableSetRef _commonModeItems; // Set<Source/Observer/Timer>
    CFRunLoopModeRef _currentMode;    // Current Runloop Mode
    CFMutableSetRef _modes;           // Set
    ...
};

struct __CFRunLoopMode {
    CFStringRef _name;            // Mode Name, 例如 @"kCFRunLoopDefaultMode"
    CFMutableSetRef _sources0;    // Set
    CFMutableSetRef _sources1;    // Set
    CFMutableArrayRef _observers; // Array
    CFMutableArrayRef _timers;    // Array
    ...
};
```

- CFRunLoopSourceRef是事件源，分为`source0`和`source1`两种类型。其中`source0`不能主动触发事件，而`source1`中有一个`mach_port`，可以主动唤醒`RunLoop`的线程。
- CFRunLoopObserverRef是观察者，每个`Observer`都包含了一个回调，当观测的状态发生变化时回调这个变化，具体变化：

```
/* Run Loop Observer Activities */
typedef CF_OPTIONS(CFOptionFlags, CFRunLoopActivity) {
    kCFRunLoopEntry = (1UL << 0),
    kCFRunLoopBeforeTimers = (1UL << 1),
    kCFRunLoopBeforeSources = (1UL << 2),
    kCFRunLoopBeforeWaiting = (1UL << 5),
    kCFRunLoopAfterWaiting = (1UL << 6),
    kCFRunLoopExit = (1UL << 7),
    kCFRunLoopAllActivities = 0x0FFFFFFFU
};
```

- CFRunLoopTimerRef是触发器，包含了一个时间长度和一个回调，当其加入`RunLoop`中时会注册对应的时间点，到时`RunLoop`会被唤醒执行回调。

苹果公开提供了三个`mode`：

```
/// CoreFoundation
CF_EXPORT const CFRunLoopMode kCFRunLoopDefaultMode;
CF_EXPORT const CFRunLoopMode kCFRunLoopCommonModes;
/// Foundation
FOUNDATION_EXPORT NSRunLoopMode const NSDefaultRunLoopMode;
FOUNDATION_EXPORT NSRunLoopMode const NSRunLoopCommonModes API_AVAILABLE(macos(10.5), ios(2.0), watchos(2.0), tvos(9.0));
UIKIT_EXTERN NSRunLoopMode const UITrackingRunLoopMode;
```

`CommonModes`是一个集合，`DefaultMode`和`Tranking Mode`都被标记了`Common`属性。日常使用`NSTimer`时为了防止滑动失效，需要将`timer`加入到`RunLoop`并设置模式为`NSRunLoopCommonModes`。

### RunLoop内部逻辑

![runloop2](/assets/ArticleImg/runloop2.png)

`RunLoop`本身其实就是一个`do-while`的循环，当调用的时候会一直停留在这个循环里，直到超时或者手动停止。其核心是基于`mach_port`的，`source0`是在`RunLoop`开始的时候触发的，当它的回调处理结束之后，如果存在`source1`则进去等待状态，调用`mach_msg()`方法等待接收通过`mach_port`发来的消息，如果等待超时或者接收到消息则处理并执行下一条。

#### 与GCD

在`RunLoop`中大量用到了GCD，而GCD中也有用到`RunLoop`的地方。`RunLoop`的超时时间使用的是GCD的`dispatch_source_t`定时器实现的。同时GCD中的`dispatch_async()`也用到了`RunLoop`，这个仅仅限于在主线程中的使用`dispatch_get_main_queue()`。

#### 与Autoreleasepool

苹果在App启动后，在主线程的`RunLoop`中注册了两个`CFRunLoopObserver`，它们的回调均是`_wrapRunLoopWithAutoreleasePoolHandler`。

第一个`Observer`用来监视Entry事件，它的回调会调用`_objc_autoreleasePoolPush()`创建自动释放池并入栈，其序列优先级非常非常高，保证其创建的释放池发生在所有回调之前。

第二个`Observer`监视BeforeWaiting和Exit事件。当BeforeWaiting时，调用`_objc_autoreleasePoolPop()`和`_objc_autoreleasePoolPush()`，释放旧的池出栈并创建新的池入栈等待；当Exit时，直接调用`_objc_autoreleasePoolPop()`来释放池子。这个序列优先级非常非常低，保证其释放池子的行为发生在所有回调之后。

在主线程执行的代码，通常写在事件回调、计时器回调内的这些会被`RunLoop`创建好的释放池围绕着，所以不会出现内存泄漏。而子线程需要手动的调用`run()`方法来让`RunLoop`跑起来并创建释放池。

## 多线程



## 计时器相关



## 网络协议



## 事件响应与传递



## 图片解压缩



## UITableView优化



## 参考资料

[iOS 的架构模式](https://www.jianshu.com/p/b849c6972216)

[格物致知iOS类与对象](https://www.jianshu.com/p/0ba0b2bac78a)

[探究KVO的底层实现原理](https://www.jianshu.com/p/829864680648)

[iOS中autorelease的那些事儿](https://www.jianshu.com/p/5559bc15490d)

[深入理解RunLoop](https://blog.ibireme.com/2015/05/18/runloop/)