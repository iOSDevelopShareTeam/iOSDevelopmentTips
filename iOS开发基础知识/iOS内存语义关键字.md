# iOS内存语义关键字
## assign
指定setter方法用简单的赋值，这是默认操作。你可以对标量类型（如int）使用这个属性。你可以想象一个float，它不是一个对象，所以它不能retain、copy。可以简单赋值，不更改索引计数(Reference Counting)。使用assign: 对基础数据类型 （NSInteger）和C数据类型（int, float, double, char,等）
## retain
指定retain应该在后面的对象上调用，前一个值发送一条release消息。你可以想象一个NSString实例，它是一个对象，而且你可能想要retain它。 释放旧的对象，将旧对象的值赋予输入对象，再提高输入对象的索引计数为1 ，使用retain： 对其他NSObject和其子类 ，retain，是说明该属性在赋值的时候，先release之前的值，然后再赋新值给属性，引用再加1。
##  copy
指定应该使用对象的副本（深度复制），前一个值发送一条release消息。基本上像retain，但是没有增加引用计数，是分配一块新的内存来放置它。copy是创建一个新对象，retain是创建一个指针，引用对象计数加1。copy：建立一个索引计数为1的对象，然后释放旧对象，copy是创建一个新对象，retain是创建一个指针，引用对象计数加1。
## readonly
将只生成getter方法而不生成setter方法（getter方法没有get前缀)。
## readwrite
默认属性，将生成不带额外参数的getter和setter方法（setter方法只有一个参数）。
## atomic
对于对象的默认属性，就是setter/getter生成的方法是一个原子操作。如果有多个线程同时调用setter的话，不会出现某一个线程执行setter全部语句之前，另一个线程开始执行setter的情况，相关于方法头尾加了锁一样。
## nonatomic
不保证setter/getter的原子性，多线程情况下数据可能会有问题。
nonatomic，非原子性访问，不加同步，多线程并发访问会提高性能。先释放原先变量，再将新变量retain然后赋值；
## strong
ARC中默认的方式是Strong，所以一般情况下，你是不需要写Strong的，strong关键字与retain关似，用了它，引用计数自动＋1。
## weak
weak所指向的指针顾名思义，只有很多的引用关系，当指向的内存地址被释放掉，weak指针也就会被释放掉了，这些指针都将被赋值为nil，这样的好处能有效的防止野指针
## unsafe_unretained
unsafe_unretained与weak有点类似，但是当所指向的指针被释放后，这些指针不会被赋值为nil，而成为野指针。
