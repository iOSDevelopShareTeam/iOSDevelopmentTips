# -ObjC的使用场景

据坊间说：如果你集成了有 category 的静态库，有可能出现上述错误。原因就是：
```
Technical Q&A QA1490 
"An impedance mismatch between UNIX static libraries and the 
dynamic nature of Objective-C can cause category methods in 
static libraries to not be linked into an app, resulting in 
"selector not recognized" exceptions when the methods aren't 
found at runtime."
```
这段话的意思就是：链接器在处理包含Category方法的UNIX的静态库时，没有将Category的方法链接到APP中，造成这个错误。具体的细节在本文的补充部分展开。 
可以看出，解决这个错误的方法就是：将Category的方法链接到APP中，这样APP运行时，就能够找到对应的selector。而 –ObjC就可以完成这个任务。

"-ObjC"的作用是：将静态库中任何Objective-C代码都链接到APP中。任何Objective-C代码当然也包括Category的方法。可以看出，使用-ObjC可能会链接很多静态库中未被使用的Objective-C代码，极大的增加APP的代码体积。

## "-ObjC" 的兄弟

![04D46B34-FB76-493B-8DEB-F540759AEFBE](https://ws2.sinaimg.cn/large/006mQyr2gy1g5j0arqzgsj30fs0d60u4.jpg)

和 "-ObjC"作用类似的有以上的五种方案。可以看出，从增加APP代码体积来看，伪符号方案增加得最少"Perform Single-Object Prelink"、 "-force_load" 和 "–ObjC" 次之，"-all_load" 增加得最多。在开发iOS SDK时，为了方便使用者手动集成，最好是减少使用者需要配置的信息，所以"伪符号"方案和 "Perform Single-Object Prelink"方案是推荐的。

另外，第三方SDK常常是闭源的，对于使用者来说，伪符号是透明的，所以从简便性角度看，推荐"Perform Single-Object Prelink"方案。
"selector not recognized"错误的产生根源


iOS工程，从源文件到生成最终的APP文件，通常要经过如下步骤：

源文件经过编译和优化后，会生成目标代码。目标代码中包括符号表，标示了此代码中的全局符号和静态符号，还标示了导入符号等，链接器会根据符号表分析各个目标代码之间的调用关系，然后将使用到的代码进行链接和重定位，最后生成可执行文件。

在编译Objective-C源文件到目标文件时，编译器并不知道方法的对应实现，只能在运行时才知道，所以编译器只会为类生成链接符号，对类中的方法不会生成链接符号。

由于Category方法并不对应一个新类，所以不会生成链接符号，链接器也不会将Category方法合并到原始的类中，最终导致链接器忽略了Category方法，不会将其链接到可执行文件中。
