### 1、OC对象的本质
* 1.1 我们平时编写的Objective-C代码，底层实现其实都是C\C++代码。Objective-C的对象、类主要是基于C\C++的结构体实现

>OC -> C/C++ -> 汇编语言 -> 机器语言

* 1.2 将Objective-C代码转换为C\C++代码

>xcrun  -sdk  iphoneos  clang  -arch  arm64  -rewrite-objc  OC源文件  -o  输出的CPP文件。
>
>如果需要链接其他框架，使用-framework参数。比如-framework UIKit

* 1.3 NSObject的底层实现

```
typedef struct objc_class *Class;
@interface NSObject {
	Class isa;
}
转成结构体
struct NSObject_IMPL {
	Class isa;
}
```





