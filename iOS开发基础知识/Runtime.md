# Runtime 相关

相信大家都看过以下两道 runtime 的题:
// 第一道题
```
@implementation Son : Father
- (id)init {
   self = [super init]; 
   if (self) { 
       NSLog(@"%@", NSStringFromClass([self class])); 
       NSLog(@"%@", NSStringFromClass([super class]));
   }
   return self;
}
@end
```
// 第二道题
```
BOOL res1 = [(id)[NSObject class] isKindOfClass:[NSObject class]];
BOOL res2 = [(id)[NSObject class] isMemberOfClass:[NSObject class]];
BOOL res3 = [(id)[Sark class] isKindOfClass:[Sark class]];
BOOL res4 = [(id)[Sark class] isMemberOfClass:[Sark class]];
```
第一道题的答案都是son这个类(看过的都知道);

第二道题的答案只有第一个是YES;

我看过很多网上的解释, 综合的来谈一谈吧.~

第一道题主要是runtime中的隐藏参数self 和 super的理解, super是编译器指令, 和self是指向的同一个消息接收者,接受消息的对象都是当前 Son ＊xxx 这个对象。

当 [self class]的时候, 会转化成 objc_msgSend函数, 并把self作为第一个参数穿进去:
id objc_msgSend(id self, SEL op, ...)

对了在这里我要问一下大家, 我们经常在方法中使用self关键字来引用实例本身，但从没有想过为什么self就能取到调用当前方法的对象吧。其实self的内容是在方法运行时被偷偷的动态传入的。

当objc_msgSend找到方法对应的实现时，它将直接调用该方法实现，并将消息中所有的参数都传递给方法实现,同时,它还将传递两个隐藏的参数:

* 接收消息的对象（也就是self指向的内容）
* 方法选择器（_cmd指向的内容）

之所以说它们是隐藏的是因为在源代码方法的定义中并没有声明这两个参数。它们是在代码被编译时被插入实现中的。尽管这些参数没有被明确声明，在源代码中我们仍然可以引用它们。在下面的例子中，self引用了接收者对象，而_cmd引用了方法本身的选择器

而在调用[super class]时，会转化成 objc_msgSendSuper函数, 其实编译器会根据情况在objc_msgSend,objc_msgSend_stret, objc_msgSendSuper, 或objc_msgSendSuper_stret 四个方法中选择一个来调用。如果消息是传递给超类，那么会调用名字带有"Super"的函数, 由于没有返回值所以这里会转化成objc_msgSendSuper, 因为"stret"指的就是"struct" + "return":
id objc_msgSendSuper(struct objc_super *super, SEL op, ...)
当方法中的super关键字接收到消息时，
编译器会创建一个objc_super结构体：
struct objc_super { id receiver; Class class; };
结构体有两个成员，第一个成员是receiver, 类似于上面的 objc_msgSend函数第一个参数self 。第二个成员是记录当前类的父类是什么。

当我们调用[self class]的时候, 实际上是先去调用的objc_msgSend, 其第一个参数传入的是Son这个类的实例对象, 然后去Son类中查找- (Class)class这个方法, 没有找到, 再去父类Father中查找, 也没有找到, 最终在NSObject这个类中找到了, 
而- (Class)class的实现就是返回self的类别，故上述输出结果为 Son。

objc Runtime开源代码对- (Class)class方法的实现:
```
- (Class)class {
    return object_getClass(self);
}
```
当调用 [super class]时，会转换成objc_msgSendSuper函数。
其中第一个参数是创建一个objc_super的结构体, 结构体中的reseiver就是self, 
第二个参数是一个方法选择器, 它会调用(id)class_getSuperclass(objc_getClass(“Son”))这个函数, 这个得到的就是父类Father(通俗点说这个函数就是super是告诉编译器，调用 class 这个方法时，要去父类的方法中查找), 在父类中查找 - (Class)class这个方法, 最终依旧实在NSObject这个类中找到了, 
接着调用objc_msgSend(objc_super->receiver, @selector(class))，传入的第一个参数是指向self的id指针，与调用[self class]相同，所以我们得到的永远都是self的类型。
第二道题其实就是在考Object & Class & Meta Class的关系
主要是掌握runtime中的这些术语, 可以去我的简书看这篇文章
对于runtime术语有一定了解之后再来看这道题:
// 第二道题
```
BOOL res1 = [(id)[NSObject class] isKindOfClass:[NSObject class]];
BOOL res2 = [(id)[NSObject class] isMemberOfClass:[NSObject class]];
BOOL res3 = [(id)[Sark class] isKindOfClass:[Sark class]];
BOOL res4 = [(id)[Sark class] isMemberOfClass:[Sark class]];
```
再把metaclass的图片放在这里一下
注意看这个回路~, 到root meta class 就产生了回路, 这个root就是NSObject, 再看一下类名调用的方法:
```
+ (Class)class {
    return self;
}
```
由于self在类方法中代表的就是类本身, 所以返回了一个self的类, 我们再看一下isKindOfClass这个方法源代码:
```
- (BOOL)isKindOf:aClass
{
    Class cls;
    for (cls = isa; cls; cls = cls->superclass) 
        if (cls == (Class)aClass)
            return YES;
    return NO;
}
```
对着上面Meta Class的图和实现，我们可以看出
* 这个函数的实现, 是在循环找父类, 直到cls不存在或者找到想要的传参的类了
* 当 NSObject Class对象第一次进行比较时，得到它的isa为 NSObject的Meta Class， 这个时候 NSObject Meta Class 和 NSObject Class不相等。
* 然后取NSObject 的Meta Class 的Super class，这个时候又变成了 NSObject Class， 所以返回相等。
所以上述第一个输出结果是 YES , 而第三个则是找到最后也没有一样的:
Sark Class 的isa指向的是 Sark的Meta Class，和Sark Class不相等
Sark Meta Class的super class 指向的是 NSObject Meta Class， 和 Sark Class不相等
NSObject Meta Class的 super class 指向 NSObject Class，和 Sark Class 不相等
NSObject Class 的super class 指向 nil， 和 Sark Class不相等
然后我们再看一下isMemberOfClass这个方法源代码:

```
- (BOOL)isMemberOf:aClass
{
    return isa == (Class)aClass;
}
```
它就是返回了一个isa指向的类, 即Meta Class;
所以第二个的 NSObject 的 Meta Class， 和 NSObject Class不相等, res2 == NO,
第四个也是如此!

PS:注意哦~将类强转为id类型就是对象化，类对象(Class 对象)的isa指针指向的是 Meta Class，Meta Class 是不可能和本身的 Class 相同的
