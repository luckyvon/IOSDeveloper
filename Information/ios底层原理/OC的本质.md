<!-- TOC -->

- [1、OC对象的本质](#1oc%E5%AF%B9%E8%B1%A1%E7%9A%84%E6%9C%AC%E8%B4%A8)
  - [1.1 OC底层实现](#11-oc%E5%BA%95%E5%B1%82%E5%AE%9E%E7%8E%B0)
  - [1.2 将Objective-C代码转换为C\C++代码](#12-%E5%B0%86objective-c%E4%BB%A3%E7%A0%81%E8%BD%AC%E6%8D%A2%E4%B8%BAcc%E4%BB%A3%E7%A0%81)
  - [1.3 NSObject的底层实现](#13-nsobject%E7%9A%84%E5%BA%95%E5%B1%82%E5%AE%9E%E7%8E%B0)
  - [1.4 实时查看内存数据](#14-%E5%AE%9E%E6%97%B6%E6%9F%A5%E7%9C%8B%E5%86%85%E5%AD%98%E6%95%B0%E6%8D%AE)
    - [1.4.1 ViewMemory](#141-viewmemory)
    - [1.4.2 LLDB指令](#142-lldb%E6%8C%87%E4%BB%A4)
  - [1.5 结构体内存分配](#15-%E7%BB%93%E6%9E%84%E4%BD%93%E5%86%85%E5%AD%98%E5%88%86%E9%85%8D)
  - [1.6 sizeof注意点](#16-sizeof%E6%B3%A8%E6%84%8F%E7%82%B9)
  - [面试题](#%E9%9D%A2%E8%AF%95%E9%A2%98)
- [2、OC对象的分类](#2oc%E5%AF%B9%E8%B1%A1%E7%9A%84%E5%88%86%E7%B1%BB)
  - [2.1 Objective-C中的对象，简称OC对象，主要可以分为3种](#21-objective-c%E4%B8%AD%E7%9A%84%E5%AF%B9%E8%B1%A1%E7%AE%80%E7%A7%B0oc%E5%AF%B9%E8%B1%A1%E4%B8%BB%E8%A6%81%E5%8F%AF%E4%BB%A5%E5%88%86%E4%B8%BA3%E7%A7%8D)
    - [2.1.1 instance](#211-instance)
    - [2.1.2 class](#212-class)
    - [2.1.3 meta-class](#213-meta-class)
    - [2.1.4 注意](#214-%E6%B3%A8%E6%84%8F)
    - [2.1.5 查看Class是否为meta-class](#215-%E6%9F%A5%E7%9C%8Bclass%E6%98%AF%E5%90%A6%E4%B8%BAmeta-class)
  - [2.2 object_getClass内部实现](#22-objectgetclass%E5%86%85%E9%83%A8%E5%AE%9E%E7%8E%B0)
  - [2.3 isa指针](#23-isa%E6%8C%87%E9%92%88)
  - [2.4 class对象的superclass指针](#24-class%E5%AF%B9%E8%B1%A1%E7%9A%84superclass%E6%8C%87%E9%92%88)
  - [2.5 meta-class对象的superclass指](#25-meta-class%E5%AF%B9%E8%B1%A1%E7%9A%84superclass%E6%8C%87)
  - [2.6 isa、superclass总结](#26-isasuperclass%E6%80%BB%E7%BB%93)
  - [2.7 class结构体](#27-class%E7%BB%93%E6%9E%84%E4%BD%93)
    - [2.7.1 isa指针](#271-isa%E6%8C%87%E9%92%88)
    - [2.7.2 objc4源码下载](#272-objc4%E6%BA%90%E7%A0%81%E4%B8%8B%E8%BD%BD)
    - [2.7.3 窥探struct objc_class的结构](#273-%E7%AA%A5%E6%8E%A2struct-objcclass%E7%9A%84%E7%BB%93%E6%9E%84)
  - [面试题](#%E9%9D%A2%E8%AF%95%E9%A2%98-1)
- [3、KVO](#3kvo)
  - [3.1 未使用KVO监听的对象](#31-%E6%9C%AA%E4%BD%BF%E7%94%A8kvo%E7%9B%91%E5%90%AC%E7%9A%84%E5%AF%B9%E8%B1%A1)
  - [3.2 使用了KVO监听的对象](#32-%E4%BD%BF%E7%94%A8%E4%BA%86kvo%E7%9B%91%E5%90%AC%E7%9A%84%E5%AF%B9%E8%B1%A1)
  - [3.3 查看_NSSet*AndNotify的存在](#33-%E6%9F%A5%E7%9C%8Bnssetandnotify%E7%9A%84%E5%AD%98%E5%9C%A8)
  - [3.4 _NSSet*ValueAndNotify的内部实现](#34-nssetvalueandnotify%E7%9A%84%E5%86%85%E9%83%A8%E5%AE%9E%E7%8E%B0)
    - [3.5 KVO子类的内部方法](#35-kvo%E5%AD%90%E7%B1%BB%E7%9A%84%E5%86%85%E9%83%A8%E6%96%B9%E6%B3%95)
- [4、KVC](#4kvc)
  - [4.1 概述](#41-%E6%A6%82%E8%BF%B0)
  - [4.2 setValue:forKey:的原理](#42-setvalueforkey%E7%9A%84%E5%8E%9F%E7%90%86)
  - [4.3 valueForKey:的原理](#43-valueforkey%E7%9A%84%E5%8E%9F%E7%90%86)

<!-- /TOC -->

# 1、OC对象的本质

## 1.1 OC底层实现

我们平时编写的Objective-C代码，底层实现其实都是C\C++代码。Objective-C的对象、类主要是基于C\C++的结构体实现。

![screenshot](./imgs/1/1.1_1.png)

## 1.2 将Objective-C代码转换为C\C++代码

>xcrun  -sdk  iphoneos  clang  -arch  arm64  -rewrite-objc  OC源文件  -o  输出的CPP文件。	
>如果需要链接其他框架，使用-framework参数。比如-framework UIKit

## 1.3 NSObject的底层实现

![screenshot](./imgs/1/1.3_1.png)
![screenshot](./imgs/1/1.3_2.png)
![screenshot](./imgs/1/1.3_3.png)
![screenshot](./imgs/1/1.3_4.png)

## 1.4 实时查看内存数据

### 1.4.1 ViewMemory

![screenshot](./imgs/1/1.4_1.png)

### 1.4.2 LLDB指令

>print、p：打印
>po：打印对象
>
>读取内存
>memory read/数量格式字节数  内存地址
>x/数量格式字节数  内存地址
>x/3xw  0x10010
>
>格式
>x是16进制
>f是浮点
>d是10进制
>
>字节大小
>b：byte 1字节
>h：half word 2字节
>w：word 4字节
>g：giant word 8字节
>
>修改内存中的值
>memory  write  内存地址  数值
>memory  write  0x0000010  10

## 1.5 结构体内存分配

[结构体大小计算](http://www.cnblogs.com/xieyajie/p/8094788.html)

## 1.6 sizeof注意点

sizeof是编译器特性，在编译的时候直接进行常理替换，并不是函数。class_getInstanceSize需要在运行时确定大小。

## 面试题

* 1.1一个NSObject对象占用多少内存？

```
 NSObject *obj = [[NSObject alloc] init];
// 16个字节
    
// 获得NSObject实例对象的成员变量所占用的大小 =8
NSLog(@"%zd", class_getInstanceSize([NSObject class]));//8
    
// 系统分配了16个字节给NSObject对象
//CF requires all objects be at least 16 bytes
NSLog(@"%zd", malloc_size((__bridge const void *)obj));//16

苹果系统分配内存源码
https://opensource.apple.com/tarballs/libmalloc/
malloc.c/calloc  Buckets sized {16,32,64,80,96,112,...}
操作系统分配内存也有对齐，16的整数倍

创建一个实例对象，至少需要多少内存？//结构体内存对齐
#import <objc/runtime.h>
class_getInstanceSize([NSObject class]);

建一个实例对象，实际上分配了多少内存？//操作系统分配内存也会对齐，16的整数倍
#import <malloc/malloc.h>
malloc_size((__bridge const void *)obj);

sizeof()也能计算出大小

gnu（glibc/malloc/MALLOC_ALIGNMENT=16 c语言源码）是一个开源组织也提供了相关源码

```

# 2、OC对象的分类
## 2.1 Objective-C中的对象，简称OC对象，主要可以分为3种

* instance对象（实例对象）
* class对象（类对象）
* meta-class对象（元类对象） 

### 2.1.1 instance
![screenshot](./imgs/2/2.1_1.png)
### 2.1.2 class
![screenshot](./imgs/2/2.1_2.png)
### 2.1.3 meta-class
![screenshot](./imgs/2/2.1_3.png)

### 2.1.4 注意
![screenshot](./imgs/2/2.1_4.png)

### 2.1.5 查看Class是否为meta-class
![screenshot](./imgs/2/2.1_5.png)

## 2.2 object_getClass内部实现
https://opensource.apple.com/tarballs/	
objc4/objc-runtime.mm

```
/*
 1.Class objc_getClass(const char *aClassName)
 1> 传入字符串类名
 2> 返回对应的类对象
 
 2.Class object_getClass(id obj)
 1> 传入的obj可能是instance对象、class对象、meta-class对象
 2> 返回值
 a) 如果是instance对象，返回class对象
 b) 如果是class对象，返回meta-class对象
 c) 如果是meta-class对象，返回NSObject（基类）的meta-class对象
 
 3.- (Class)class、+ (Class)class
 1> 返回的就是类对象
 
 - (Class) {
     return self->isa;
 }
 
 + (Class) {
     return self;
 }
 */
```
## 2.3 isa指针
![screenshot](./imgs/2/2.3_1.png)

## 2.4 class对象的superclass指针
![screenshot](./imgs/2/2.4_1.png)
## 2.5 meta-class对象的superclass指
![screenshot](./imgs/2/2.5_1.png)
## 2.6 isa、superclass总结
![screenshot](./imgs/2/2.6_1.png)
## 2.7 class结构体
### 2.7.1 isa指针
![screenshot](./imgs/2/2.7_1.png)

```
struct mj_objc_class {
    Class isa;
    Class superclass;
};

        // MJPerson类对象的地址：0x00000001000014c8
        // isa & ISA_MASK：0x00000001000014c8
        
        // MJPerson实例对象的isa：0x001d8001000014c9
        
        struct mj_objc_class *personClass = (__bridge struct mj_objc_class *)([MJPerson class]);
        
        struct mj_objc_class *studentClass = (__bridge struct mj_objc_class *)([MJStudent class]);
        
        NSLog(@"1111");
        
//        MJPerson *person = [[MJPerson alloc] init];
//
        
//        Class personClass = [MJPerson class];
        
//        struct mj_objc_class *personClass2 = (__bridge struct mj_objc_class *)(personClass);
//
//        Class personMetaClass = object_getClass(personClass);
//
//        NSLog(@"%p %p %p", person, personClass, personMetaClass);
//        MJStudent *student = [[MJStudent alloc] init];

```
```
64bit之前isa = 对象地址，从64bit开始，isa需要进行一次位运算，才能计算出真实地址	
p/x (long)person->isa
输出
0x001d8001000014c9

p/x persionClass 
输出
0x00000001000014c8

p/x 0x001d8001000014c9 & 0x00007ffffffffff8（x86下ISA_MASK）
输出
0x00000001000014c8

```
### 2.7.2 objc4源码下载
* https://opensource.apple.com/tarballs/objc4/
![screenshot](./imgs/2/2.7_2.png)
* class、meta-class对象的本质结构都是struct objc_class

### 2.7.3 窥探struct objc_class的结构
![screenshot](./imgs/2/2.7_3.png)

[objc_class的结构项目](./project/objc_class的结构)

## 面试题 

* 对象的isa指针指向哪里？

```
instance对象的isa指向class对象
class对象的isa指向meta-class对象
meta-class对象的isa指向基类的meta-class对象
```

* OC的类信息存放在哪里？
  
```
对象方法、属性、成员变量、协议信息，存放在class对象中
类方法，存放在meta-class对象中
成员变量的具体值，存放在instance对象

```

# 3、KVO

>KVO的全称是Key-Value Observing，俗称“键值监听”，可以用于监听某个对象属性值的改变

![screenshot](./imgs/3/3.0_1.png)

```
@interface ViewController ()
@property (strong, nonatomic) MJPerson *person1;
@property (strong, nonatomic) MJPerson *person2;
@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    self.person1 = [[MJPerson alloc] init];
    self.person1.age = 1;
    self.person1.height = 11;
    
    self.person2 = [[MJPerson alloc] init];
    self.person2.age = 2;
    self.person2.height = 22;
    
    // 给person1对象添加KVO监听
    NSKeyValueObservingOptions options = NSKeyValueObservingOptionNew | NSKeyValueObservingOptionOld;
    [self.person1 addObserver:self forKeyPath:@"age" options:options context:@"123"];
    [self.person1 addObserver:self forKeyPath:@"height" options:options context:@"456"];
}

- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event
{
    self.person1.age = 20;
    self.person2.age = 20;
    
    self.person1.height = 30;
    self.person2.height = 30;
}

- (void)dealloc {
    [self.person1 removeObserver:self forKeyPath:@"age"];
    [self.person1 removeObserver:self forKeyPath:@"height"];
}

// 当监听对象的属性值发生改变时，就会调用
- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary<NSKeyValueChangeKey,id> *)change context:(void *)context
{
    NSLog(@"监听到%@的%@属性值改变了 - %@ - %@", object, keyPath, change, context);
}

```

[KVODemo](./project/3.0_1)

## 3.1 未使用KVO监听的对象

![screenshot](./imgs/3/3.1_1.png)

## 3.2 使用了KVO监听的对象

![screenshot](./imgs/3/3.2_1.png)

## 3.3 查看_NSSet*AndNotify的存在

```
@interface MJPerson : NSObject
@property (assign, nonatomic) int age;
@end

@implementation MJPerson

- (void)setAge:(int)age
{
    _age = age;
    
    NSLog(@"setAge:");
}

//- (int)age
//{
//    return _age;
//}

- (void)willChangeValueForKey:(NSString *)key
{
    [super willChangeValueForKey:key];
    
    NSLog(@"willChangeValueForKey");
}

- (void)didChangeValueForKey:(NSString *)key
{
    NSLog(@"didChangeValueForKey - begin");
    
    [super didChangeValueForKey:key];
    
    NSLog(@"didChangeValueForKey - end");
}

//ViewController
- (void)viewDidLoad {
    [super viewDidLoad];
    
    self.person1 = [[MJPerson alloc] init];
    self.person1.age = 1;
    
    self.person2 = [[MJPerson alloc] init];
    self.person2.age = 2;
    
    
//    NSLog(@"person1添加KVO监听之前1 - %@ %@",
//          object_getClass(self.person1),
//          object_getClass(self.person2));

//    NSLog(@"person1添加KVO监听之前2 - %p %p",
//          [self.person1 methodForSelector:@selector(setAge:)],
//          [self.person2 methodForSelector:@selector(setAge:)]);
    
    // 给person1对象添加KVO监听
    NSKeyValueObservingOptions options = NSKeyValueObservingOptionNew | NSKeyValueObservingOptionOld;
    [self.person1 addObserver:self forKeyPath:@"age" options:options context:@"123"];
    
//    NSLog(@"person1添加KVO监听之后1 - %@ %@",
//          object_getClass(self.person1),
//          object_getClass(self.person2));
//    NSLog(@"person1添加KVO监听之后2 - %p %p",
//          [self.person1 methodForSelector:@selector(setAge:)],
//          [self.person2 methodForSelector:@selector(setAge:)]);
//
//
//    NSLog(@"类对象 - %@ %@",
//          object_getClass(self.person1),  // self.person1.isa
//          object_getClass(self.person2)); // self.person2.isa
//    NSLog(@"类对象 - %p %p",
//          object_getClass(self.person1),  // self.person1.isa
//          object_getClass(self.person2)); // self.person2.isa
//
//    NSLog(@"元类对象 - %@ %@",
//          object_getClass(object_getClass(self.person1)), // self.person1.isa.isa
//          object_getClass(object_getClass(self.person2))); // self.person2.isa.isa
//    NSLog(@"元类对象 - %p %p",
//          object_getClass(object_getClass(self.person1)), // self.person1.isa.isa
//          object_getClass(object_getClass(self.person2))); // self.person2.isa.isa

//Log查看方法
persion1添加KVO监听之前2 - 0x1065687c0 0x1065687c0
persion1添加KVO监听之后2 - 0x1069189e4 0x1065687c0

p (IMP)0x1065687c0
(IMP) $0 0x00... (Interview01`-[MJPerson setAge:] at MJPerson.m13)

p (IMP)0x1069189e4
(IMP) $1 0x00... (Foundation`_NSSetIntValueAndNotify)
    
}

类对象 - NSKVONotifying_MJPerson MJPersion
元类对象 - NSKVONotifying_MJPerson MJPersion

- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event
{
    // NSKVONotifying_MJPerson是使用Runtime动态创建的一个类，是MJPerson的子类
    // self.person1.isa == NSKVONotifying_MJPerson
    [self.person1 setAge:21];
    
    // self.person2.isa = MJPerson
//    [self.person2 setAge:22];
}

- (void)dealloc {
    [self.person1 removeObserver:self forKeyPath:@"age"];
}

// 当监听对象的属性值发生改变时，就会调用
- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary<NSKeyValueChangeKey,id> *)change context:(void *)context
{
    NSLog(@"监听到%@的%@属性值改变了 - %@ - %@", object, keyPath, change, context);
}

```


![screenshot](./imgs/3/3.3_1.png)

## 3.4 _NSSet*ValueAndNotify的内部实现

![screenshot](./imgs/3/3.4_1.png)

```
调用willChangeValueForKey:
调用原来的setter实现
调用didChangeValueForKey:
didChangeValueForKey:内部会调用observer的observeValueForKeyPath:ofObject:change:context:方法
```

### 3.5 KVO子类的内部方法
 ```
 通过runtime获取方法类表。就知道有下面方法
 - (void)printMethodNamesOfClass:(Class)cls {
    unsigned int count;
    // 获得方法数组
    Method *methodList = class_copyMethodList(cls, &count);
    
    // 存储方法名
    NSMutableString *methodNames = [NSMutableString string];
    
    // 遍历所有的方法
    for (int i = 0; i < count; i++) {
        // 获得方法
        Method method = methodList[i];
        // 获得方法名
        NSString *methodName = NSStringFromSelector(method_getName(method));
        // 拼接方法名
        [methodNames appendString:methodName];
        [methodNames appendString:@", "];
    }
    
    // 释放
    free(methodList);
    
    // 打印方法名
    NSLog(@"%@ %@", cls, methodNames);
}

 @implementation NSKVONotifying_MJPerson

- (void)setAge:(int)age
{
    _NSSetIntValueAndNotify();
}

// 屏幕内部实现，隐藏了NSKVONotifying_MJPerson类的存在
- (Class)class
{
    return [MJPerson class];
}

- (void)dealloc
{
    // 收尾工作
}

- (BOOL)_isKVOA
{
    return YES;
}

@end
 ```

 ## 面试题
 
 * iOS用什么方式实现对一个对象的KVO？(KVO的本质是什么？)

```
利用RuntimeAPI动态生成一个子类，并且让instance对象的isa指向这个全新的子类
当修改instance对象的属性时，会调用Foundation的_NSSetXXXValueAndNotify函数
willChangeValueForKey:
父类原来的setter
didChangeValueForKey:
内部会触发监听器（Oberser）的监听方法( observeValueForKeyPath:ofObject:change:context:）
```

* 如何手动触发KVO？

```
手动调用willChangeValueForKey:和didChangeValueForKey:
```

* 直接修改成员变量会触发KVO么？

```
不会触发KVO
```

# 4、KVC

## 4.1 概述

>KVC的全称是Key-Value Coding，俗称“键值编码”，可以通过一个key来访问某个属性

常见的API有

```
- (void)setValue:(id)value forKeyPath:(NSString *)keyPath;
- (void)setValue:(id)value forKey:(NSString *)key;
- (id)valueForKeyPath:(NSString *)keyPath;
- (id)valueForKey:(NSString *)key; 
```
## 4.2 setValue:forKey:的原理

![screenshot](./imgs/4/4.2_1.png)

* accessInstanceVariablesDirectly方法的默认返回值是YES


## 4.3 valueForKey:的原理

![screenshot](./imgs/4/4.3_1.png)