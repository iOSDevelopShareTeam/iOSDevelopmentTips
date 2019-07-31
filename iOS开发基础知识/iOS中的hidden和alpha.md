# iOS中的hidden和alpha
## 一、alpha
液晶显示器是由一个个的像素点组成的，每个像素点都可以显示一个由RGBA颜色空间组成的一种色值。其中的A就表示透明度alpha，UIView中alpha是一个浮点值，取值范围0~1.0,表示从完全透明到完全不透明。
当把alpha的值设置成0以后：

1. 当前的UIView和subview都会被隐藏，而不管subview的alpha值为多少。
2. 当前UIView会从响应者链中移除，而响应者链中的下一个会成为第一响应者


alpha的默认值是1.0。
另外，更改alpha值时，默认是有动画效果的，这是因为图层在Cocoa中是由Core Animation中CALayer表示的，该动画效果是CALayer的隐含动画。当然也有办法禁用此动画效果，在这就不多述了。
## 二、hidden
该属性为BOOL值，用来表示UIView是否隐藏，默认值是NO。
当值设为YES时:
1. 当前的UIView和subview都会被隐藏，而不管subview的hidden值为多少。
2. 当前UIView会从响应者链中移除，而响应者链中的下一个会成为第一响应者

总之，同alpha为0时的显示效果相同。具体两者之间有什么区别就不清楚了，如果有知道的还望不吝赐教！

## 三、opaque

该属性为BOOL值，UIView的默认值是YES，但UIButton等子类的默认值都是NO。opaque表示当前UIView是否不透明，不过搞笑的是事实上它却决定不了当前UIView是不是不透明，比如你将opaque设为NO，该UIView照样是可见的（上文说过，是否可见是由alpha或hidden属性决定的），照理说为NO就表示透明，那就应该是不可见的呀？
卖个关子，先看下图：

![C0275284-325C-4310-8602-8FAA1B3246A6](https://ws1.sinaimg.cn/large/006mQyr2gy1g5itdcp3rtj308h063aa2.jpg)

前面讲过，显示器中的每个像素点都可以显示一个由RGBA颜色空间组成的色值，比如上图中有红色和绿色两个图层色块，对于没有交叉的部分，即纯红色和绿色部分来说，对应位置的像素点只需要简单的显示红或绿，对应的RGBA为（1，0，0，1）和（0，1，0，1）就行了，负责图形显示的GPU需要很小的计算量就可以确定像素点对应的显示内容。
问题是红色和绿色还有相交的一块，其相交的颜色为黄色。这里的黄色是怎么来的呢？原来，GPU会通过图层一和图层二的颜色进行图层混合，计算出混合部分的颜色，最理想情况的计算公式如下：
R = S + D * ( 1 – Sa )
其中，R表示混合结果的颜色，S是源颜色(位于上层的红色图层一)，D是目标颜色(位于下层的绿色图层二)，Sa是源颜色的alpha值，即透明度。公式中所有的S和D颜色都假定已经预先乘以了他们的透明度。
知道图层混合的基本原理以后，再回到正题说说opaque属性的作用。当UIView的opaque属性被设为YES以后，按照上面的公式，也就是Sa的值为1，这个时候公式就变成了：
R = S
即不管D为什么，结果都一样。因此GPU将不会做任何的计算合成，不需要考虑它下方的任何东西(因为都被它遮挡住了)，而是简单从这个层拷贝。这节省了GPU相当大的工作量。由此看来，opaque属性的真实用处是给绘图系统提供一个性能优化开关！
按照前面的逻辑，当opaque属性被设为YES时，GPU就不会再利用图层颜色合成公式去合成真正的色值。因此，如果opaque被设置成YES，而对应UIView的alpha属性不为1.0的时候，就会有不可预料的情况发生，这一点苹果在官方文档中有明确的说明：

An opaque view is expected to fill its bounds with entirely opaque content—that is, the content should have an alpha value of 1.0. If the view is opaque and either does not fill its bounds or contains wholly or partially transparent content,the results are unpredictable. You should always set the value of this property to NO if the view is fully or partially transparent.

大家切记！！！！


## 四、最后
当把UIView的alpha属性设成0，或者把hidden设成YES的时候，当前UIView和它所包含的子UIView都会变成不可见，同时也不会再响应event事件。注意这里是或的关系，即只要设置了其中的一个都会有此效果，而不管另外一个属性的值是什么。
