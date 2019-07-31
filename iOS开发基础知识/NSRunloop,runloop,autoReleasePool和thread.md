# NSRunloop,runloop,autoReleasePool和thread的关系理解

## 1.NSRunloop

NSRunloop顾名思义，就是一个消息循环，它会侦测输入源(input source)和定时源(timer source)，然后做回调处理。这和windows的消息处理非常类似，只不过你无法看到类似SendMessage，PostMessage，GetMessage的方法，NSRunloop已经封装了这些细节。那NSRunloop的好处是不是只有封装细节，然后方便调用呢？答案是否定的。看apple官方文档(多线程编程指南)描述: "run loop 是用来在线程上管理事件异步到达的基础设施......run loop在没有任何事件处理的时候会把它的线程置于休眠状态,它消除了消耗CPU周期轮询,并防止处理器本身进入休眠状态并节省电源。" 看见没，消除CPU空转才是它最大的用处。

所以NSRunloop的重点就是：

1.run loop 用来监听长耗时的异步事件，如果用不到异步事件，就不用扯这个东西了（给你的面试官这么说吧）。例如，网络回调，无论是apple提供的NSURL还是开源的ASIHttpRequest,都是用NSRunloop来监听网络事件(TCP/IP的堆栈)。(这个人的经历可以参考 http://www.blogjava.net/writegull/archive/2012/07/25/383926.html)

2.run loop解决了CPU空转。
工作原理图如下

![6849CCC2-777E-4C83-9C98-1980E0BC4DB9](https://wx1.sinaimg.cn/large/006mQyr2gy1g5iz6f3zqxj30co07f40n.jpg)

图 3-1显示了run loop的概念结构以及各种源。输入源传递异步消息给相应的处理例程,并调用runUntilDate:方法来退出(在线程里面相关的NSRunLoop对象调用)。定时源则直接传递消息给处理例程,但并不会退出run loop。 

从上图中我们可以看出，每个线程都有一个默认的NSRunloop。主线程的NSRunloop默认是运行的。非主线程的NSRunloop默认是没有运行的，需要为NSRunloop添加一个事件，然后去run，一般情况下没有必要启用线程的runloop，除非需要长久地监测某个异步事件。

拿具体的应用举个例子，NSURLConnection网络数据请求，默认是异步的方式，其实现原理就是创建之后将其作为事件源加入到当前的 RunLoop，而等待网络响应以及网络数据接受的过程则在一个新创建的独立的线程中完成，当这个线程处理到某个阶段的时候比如得到对方的响应或者接受完了网络数据之后便通知之前的线程去执行其相关的delegate方法。所以在Cocoa中经常看到scheduleInRunLoop:forMode: 这样的方法，这个便是将其加入到事件源中，当检测到某个事件发生的时候，相关的delegate方法便被调用。

## 2.runloop和 autorelease pool
先提出一个问题，在Iphone项目中，大家会看到一个默认的Autorelease pool，程序开始时创建，程序退出时销毁，按照对Autorelease的理解，岂不是所有autorelease pool里的对象在程序退出时才release， 这样跟内存泄露有什么区别？答案是，对于每一个Runloop， 系统会隐式创建一个Autorelease pool，这样所有的release pool会构成一个象CallStack一样的一个栈式结构，在每一个Runloop结束时，当前栈顶的Autorelease pool会被销毁，这样这个pool里的每个Object会被release。
那什么是一个runloop？一个UI事件，一个timer，一个系统delegate都称之为runloop(不是NSRunloop)，runloop实际上是从接收消息，然后处理完消息的一个完整过程。

为了更加形象说明auto release pool机制，下面举例：

NSString＊ str1是assign。

UI事件：UIButton的target－action机制，在action中创建一个autorelease的UILabel对象，并赋值，在action中打印出值，action执行完毕，这个时候runloop结束，autorelease pool被释放，label也被释放，所以再调用这个对象的值时，出现bad_exec_access。


### 3.autorelease pool和thread
多说一句，只有以上提到了3种runloop才会自动创建autorelease pool，thread是不会自动创建的，所以我们可以看到子线程中会有手动写的autorelease pool代码。这点以前搞混过，切记！
