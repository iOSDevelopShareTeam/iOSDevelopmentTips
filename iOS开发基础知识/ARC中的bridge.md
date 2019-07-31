## ARC中的bridge


http://blog.sina.com.cn/s/blog_65a8ab5d0101c8k2.html
总结于 IOS Tuturial 中 ARC两章，详细在dropbox pdf 文档。

###To ll-Free Bridging
当你在 Objective-C 和 Core Foundation 对象之间进行转换时,就需要使用 Bridge cast。
今天的多数应用很少需要使用 Core Foundation,大多数工作都可以直接使 用 Objective-C 类来完成。但是某些底层 API,如 Core Graphics 和 Core Text, 都基于 Core Foundation,而且不太可能会有 Objective-C 的版本。幸运的是, iOS 的设计使得这两种类型的对象非常容易转换。
例如 NSString 和 CFStringRef 就可以同等对待,在任何地方都可以互换使 用,背后的设计就是toll-free bridging。在 ARC 之前,只需要使用一个简单 的强制类型转换即可:
当然,alloc 分配了 NSString 对象,你需要在使用完之后进行释放,注意是 释放转换后的CFStringRef 对象:
CFRelease(s1);
反过来,从 Core Foundation 到 Objective-C 的方向也类似:
现在我们使用了 ARC,情况变得不一样!以下代码在手动内存管理中是完全 合法的,但在 ARC 中却存在问题:
```
- (NSString *)escape:(NSString *)text {
CFStringRef s1 = (CFStringRef) [[NSString alloc]
initWithFormat:@"Hello, %@!", name];
CFStringRef s2 = CFStringCreateWithCString(kCFAllocatorDefault, bytes,
kCFStringEncodingMacRoman);
NSString *s3 = (NSString *)s2;
// release the object when you're done
[s3 release];
return [(NSString *)CFURLCreateStringByAddingPercentEscapes(
NULL,
(CFStringRef)text,
NULL,
(CFStringRef)@"!*'();:@&=+$,/?%#[]", // 这里不需要 bridging
casts,因为这是一个常量,不需要释放!
CFStringConvertNSStringEncodingToEncoding(NSUTF8StringEncodin
g))
autorelease];
}
```
首先需要移除 autorelease 调用。然后编译器还会报两个类型转换错误:
* Cast of C pointer type 'CFStringRef' to Objective-C pointer type 'NSString *' requires a bridged cast
* Cast of Objective-C pointer type 'NSString *' to C pointer type 'CFStringRef' requires a bridged cast

错误分别来自以下两行代码:
编译器必须知道由谁来负责释放转换后的对象,如果你把一个 NSObject 当 作 Core Foundation对象来使用,则 ARC 将不再负责释放该对象。但你必须明确 地告诉 ARC 你的这个意图,编译器没办法自己做主。同样如果你创建一个 Core Foundation 对象并把它转换为 NSObject 对象,你也必须告诉 ARC 占据对象的所 有权,并在适当的时候释放该对象。这就是所谓的 bridging casts。

CFURLCreateStringByAddingPercentEscapes()函数的参数需要两个 CFStringRef 对象,其中常量NSString 可以直接转换,因为不需要进行对象释 放;但是 text 参数不一样,它是传递进来的一个NSString 对象。而函数参数 和局部变量一样,都是 strong 指针,这种对象在函数入口处会被retain,并且 对象会持续存在直到指针被销毁(这里也就是函数返回时)。

对于 text 参数,我们希望 ARC 保持这个变量的所有权,同时又希望临时将 它当作 CFStringRef 对象来使用。这种情况下可以使用__bridge 说明符,它告 诉 ARC 不要更改对象的所有权,按普通规则释放该对象即可。

多数情况下,Objective-C 对象和 Core Foundation 对象之间互相转换时, 我们都应该使用__bridge。但是有时候我们确实需要给予 ARC 某个对象的所有权, 或者解除 ARC 对某个对象的所有权。这种情况下我们就需要使用另外两种 bridging casts:

__bridge_transfer:给予 ARC 所有权
__bridge_retained:解除 ARC 所有权

在上面代码中,`"return (NSString *)CFURLCreateStringByAddingPercentEscapes"`,编译器弹出的修复提示有两个:

两个解决办法:__bridge 和 __bridge_transfer,正确的选择应该 是 __bridge_transfer。

因为 CFURLCreateStringByAddingPercentEscapes() 函数创建了一个新 的 CFStringRef 对象,当然我们要的是 NSString 对象,因此我们使用了强制转 换。实际上我们真正想要做的是:
从 CFURLCreateStringByAddingPercentEscapes 函数的 create 可以看 出,函数会返回一个retain 过的对象。某个人需要负责在适当的时候释放该对 象,如果我们不把这个对象返回为NSString,则通常我们需要自己调用:
```
{
}
```
不过 ARC 只能作用于 Objective-C 对象,不能释放 Core Foundation 对象。 因此这里你仍然需要调用 CFRelease()来释放该对象。
这里我们的真实意图是:转换新创建的 CFStringRef 对象为 NSString 对象, 并且当我们不再需要使用这个 NSString 对象时,ARC 能够适当地释放它。
因此我们使用 __bridge_transfer告诉ARC:"嘿!ARC,这个CFStringRef 对象现在是一个NSString 对象了,我希望你来销毁它,我这里就不调用 CFRelease()来释放它了"。

如果我们使用 __bridge,就会导致内存泄漏。ARC并不知道自己应该在使 用完对象之后释放该对象,也没有人调用 CFRelease()。结果这个对象就会永远 保留在内存中。因此选择正确的bridge 说明符是至关重要的。

为了代码更加可读和容易理解,iOS 还提供了一个辅助函数: 

CFBridgingRelease()。函数所做事情和 __bridge_transfer 强制转换完全一 样,但更加简洁和清晰。CFBridgingRelease() 函数定义为内联函数,因此不会 导致额外的开销。函数之所以命名为 CFBridgingRelease(),是因为一般你会在 需要使用 CFRelease()释放对象的地方,调用 CFBridgingRelease()来传递对象的所有权。
因此最后我们的代码如下:

```
- (NSString *)escape:(NSString *)text
{
}
```
另一个常见的需要 CFBridgingRelease 的情况是 AddressBook framework:
```
- (NSString *)firstName
{
}
```
只要你调用命名为 Create, Copy, Retain 的 Core Foundation 函数,你都 需要使用CFBridgingRelease()安全地将值传递给ARC。

__bridge_retained 则正好相反,假设你有一个 NSString 对象,并且要将 它传递给某个 Core Foundation API,该函数希望接收这个 string 对象的所有 权。这时候你就不希望 ARC 也去释放该对象,否则就会对同一对象释放两次,而 且必将导致应用崩溃!换句话说,使用__bridge_retained 将对象的所有权给 予 Core Foundation,而 ARC 不再负责释放该对象。

如下面例子所示:
```
return CFBridgingRelease(CFURLCreateStringByAddingPercentEscapes(
NULL,
(__bridge CFStringRef)text,
NULL,
CFSTR("!*'();:@&=+$,/?%#[]"),
CFStringConvertNSStringEncodingToEncoding(NSUTF8StringEncodin
g)));
return CFBridgingRelease(ABRecordCopyCompositeName(...));
NSString *s1 = [[NSString alloc] initWithFormat:@"Hello, %@!", name];
CFStringRef s2 = (__bridge_retained CFStringRef)s1;
// do something with s2
// . . .
CFRelease(s2);
```
 
一旦 (__bridge_retained CFStringRef) 转换完成,ARC 就不再负责释放该 对象。如果你在这里使用 __bridge,应用就很可能会崩溃。ARC 可能在 Core Foundation 正在使用该对象时,释放掉它。

同样__bridge_retained 也有一个辅助函数:CFBridgingRetain()。从名字 就可以看出,这个函数会让 Core Foundation 执行 retain,实际如下:

现在你应该明白了,上面例子的 CFRelease()是和 CFBridgingRetain()对应 的。你应该很少需要使用__bridge_retained 或 CFBridgingRetain()。

__bridge 转换不仅仅局限于 Core Foundation 对象,某些 API 使用 void * 指针作为参数,允许你传递任何东西的引用:Objective-C 对象、Core Foundation 对象、malloc()内存缓冲区等等。void *表示这是一个指针,但实际的数据类型 可以是任何东西!

要将 Objective-C 对象和 void *互相转换,你也需要使用__bridge 转换, 如下:
在 animation delegate 方法中,你再将对象强制转回来:
```
{
}
```
总结:
使用 CFBridgingRelease(),从 Core Foundation 传递所有权给 Objective-C;
使用 CFBridgingRetain(),从 Objective-C 传递所有权给 Core Foundation;
使用__brideg,表示临时使用某种类型,不改变对象的所有权
