# CALayer 和 CAGradientLayer

## CALayer  
CALayer 是一个很经常使用的到的 Object，很常用，也很重要，同时又有比较多的属性，嗯，一定要详细了解下。

CALayer 的原理很难懂，由于我目前只注重运用，所以就说那些在实际开发中能用到的一些地方。

如果要更改 UIView 的 CALayer，要引用：`#import <QuartzCore/QuartzCore.h>.`
下面先推荐篇文章: 文章:[官方文章](https://developer.apple.com/documentation/quartzcore/calayer)
 
### 为什么说 CALayer 很重要呢：
 
1. 每个UIView 都有 CALayer，即 UIView.layer，同时 UIView是iOS系统中界面元素的基础，所有的界面元素都是继承自它，所以，CALayer 应用很广泛
 
2. CALayer 能够对 UIView 做许多设定，如：阴影、边框、圆角和透明效果等，且这些设定都是很有用的
 
 
### 下面就逐个过下 CALayer 的一些重要属性:
 
1. shadowPath : 设置 CALayer 背景(shodow)的位置
 
2. shadowOffset : shadow 在 X 和 Y 轴 上延伸的方向，即 shadow 的大小
 
3. shadowOpacity : shadow 的透明效果
 
4. shadowRadius : shadow 的渐变距离，从外围开始，往里渐变 shadowRadius 距离
 
5. masksToBounds : 很重要的属性，可以用此属性来防止子元素大小溢出父元素，如若防止溢出，请设为 true
 
6. borderWidth 和 boarderColor : 边框颜色和宽度，很常用
 
7. bounds : 对于我来说比较难的一个属性，测了半天也没完全了解，只知道可以用来控制 UIView 的大小，但是不能控制 位置
 
8. opacity : UIView 的透明效果
 
9. cornerRadius : UIView 的圆角
 
 
### 测试代码：
```
- (void)viewDidLoad
{
    [super viewDidLoad];
   
    UIView *viewSample = [[UIView alloc] init];
    [self.view addSubview:viewSample];
 
    viewSample.backgroundColor = [UIColor greenColor];
    viewSample.frame = CGRectMake(100, 100, 400, 400);
   
   
    //Test 1 阴影
    //viewSample.layer.shadowPath = [UIBezierPath bezierPathWithRect:viewSample.bounds].CGPath;
    viewSample.layer.shadowPath = [UIBezierPath bezierPathWithRect:CGRectMake(0, 0, 400, 400)].CGPath;
    viewSample.layer.masksToBounds = NO;
    viewSample.layer.shadowOffset = CGSizeMake(10, 10);
    viewSample.layer.shadowRadius = 5;
    viewSample.layer.shadowOpacity = 0.5;
   
    //Test 2 边框
    viewSample.layer.borderWidth = 2;
    viewSample.layer.borderColor = [[UIColor redColor] CGColor];
   
    //Test 3 masksToBounds
    UIButton *btn = [[UIButton alloc] initWithFrame:CGRectMake(0,0, 500, 500)];
    btn.backgroundColor = [UIColor lightGrayColor];
    //[viewSample addSubview:btn];
    //viewSample.layer.masksToBounds = true;
   
    //Test 4 bounds
    //viewSample.layer.bounds = CGRectMake(200, 200, 500, 500);
 
    //Test 5
    viewSample.layer.opacity = 0.5;
    viewSample.layer.cornerRadius = 5;
}
```

CAGradientLayer是用来生成两种或更多颜色平滑渐变的。用Core Graphics复制一个CAGradientLayer并将内容绘制到一个普通图层的寄宿图也是有可能的，但是CAGradientLayer的真正好处在于绘制使用了硬件加速。

### 基础渐变

我们将从一个简单的红变蓝的对角线渐变开始（见清单6.6）.这些渐变色彩放在一个数组中，并赋给colors属性。这个数组成员接受CGColorRef类型的值（并不是从NSObject派生而来），所以我们要用通过bridge转换以确保编译正常。

CAGradientLayer也有startPoint和endPoint属性，他们决定了渐变的方向。这两个参数是以单位坐标系进行的定义，所以左上角坐标是{0, 0}，右下角坐标是{1, 1}。代码运行结果如图6.6

清单6.6 简单的两种颜色的对角线渐变
```
@interface ViewController ()
@property (nonatomic, weak) IBOutlet UIView *containerView;
@end
@implementation ViewController
- (void)viewDidLoad
{
  [super viewDidLoad];
  //create gradient layer and add it to our container view
  CAGradientLayer *gradientLayer = [CAGradientLayer layer];
  gradientLayer.frame = self.containerView.bounds;
  [self.containerView.layer addSublayer:gradientLayer];
  //set gradient colors
  gradientLayer.colors = @[(__bridge id)[UIColor redColor].CGColor, (__bridge id)[UIColor blueColor].CGColor];
  //set gradient start and end points
  gradientLayer.startPoint = CGPointMake(0, 0);
  gradientLayer.endPoint = CGPointMake(1, 1);
}
@end
```
![15D4E2CD-BAC7-490B-9CC0-C3832AC5DD06](https://ws1.sinaimg.cn/large/006mQyr2gy1g5iv8lu568j314c0jsgtf.jpg)

图6.6 用CAGradientLayer实现简单的两种颜色的对角线渐变

### 多重渐变

如果你愿意，colors属性可以包含很多颜色，所以创建一个彩虹一样的多重渐变也是很简单的。默认情况下，这些颜色在空间上均匀地被渲染，但是我们可以用locations属性来调整空间。locations属性是一个浮点数值的数组（以NSNumber包装）。这些浮点数定义了colors属性中每个不同颜色的位置，同样的，也是以单位坐标系进行标定。0.0代表着渐变的开始，1.0代表着结束。

locations数组并不是强制要求的，但是如果你给它赋值了就一定要确保locations的数组大小和colors数组大小一定要相同，否则你将会得到一个空白的渐变。

清单6.7展示了一个基于清单6.6的对角线渐变的代码改造。现在变成了从红到黄最后到绿色的渐变。locations数组指定了0.0，0.25和0.5三个数值，这样这三个渐变就有点像挤在了左上角。（如图6.7）.
![E0EA59AA-D033-404D-AC60-A9A00B458B92](https://wx3.sinaimg.cn/large/006mQyr2gy1g5iv8x8kszj314e0jwwl9.jpg)

图6.7 在渐变上使用locations
```
- (void)viewDidLoad {
  [super viewDidLoad];
  //create gradient layer and add it to our container view
  CAGradientLayer *gradientLayer = [CAGradientLayer layer];
  gradientLayer.frame = self.containerView.bounds;
  [self.containerView.layer addSublayer:gradientLayer];
  //set gradient colors
  gradientLayer.colors = @[(__bridge id)[UIColor redColor].CGColor, (__bridge id) [UIColor yellowColor].CGColor, (__bridge id)[UIColor greenColor].CGColor];
  //set locations
  gradientLayer.locations = @[@0.0, @0.25, @0.5];
  //set gradient start and end points
  gradientLayer.startPoint = CGPointMake(0, 0);
  gradientLayer.endPoint = CGPointMake(1, 1);
}
```
图6.7 用locations构造偏移至左上角的三色渐变

CAGradientLayer可以方便的处理颜色渐变。
Properties：
@property(copy) NSArray *colors
渐变颜色的数组
[cpp] view plaincopy
```
[NSArray arrayWithObjects:(id)[[[UIColor blackColor] colorWithAlphaComponent:1] CGColor],  
 (id)[[[UIColor yellowColor] colorWithAlphaComponent:1] CGColor],  
 (id)[[[UIColor blueColor] colorWithAlphaComponent:1] CGColor],  
 (id)[[UIColor clearColor] CGColor],  
nil]; 
``` 
如上定义了四种颜色（最后一种是无色）。
@property(copy) NSArray *locations
渐变颜色的区间分布，locations的数组长度和color一致，这个值一般不用管它，默认是nil，会平均分布。
[cpp] view plaincopy
```
 [NSArray arrayWithObjects:[NSNumber numberWithFloat:0.0],  
[NSNumber numberWithFloat:0.3],  
[NSNumber numberWithFloat:0.8],  
[NSNumber numberWithFloat:1.0],  
 nil];  
```
注意这几个数字在0到1之间单调递增。
@property CGPoint startPoint
映射locations中第一个位置，用单位向量表示，比如（0，0）表示从左上角开始变化。默认值是(0.5,0.0)。
@property CGPoint endPoint
映射locations中最后一个位置，用单位向量表示，比如（1，1）表示到右下角变化结束。默认值是(0.5,1.0)。

@property(copy) NSString *type
默认值是kCAGradientLayerAxial，表示按像素均匀变化。除了默认值也无其它选项。



## CALayer

CALayer有一个属性叫做mask可以解决这个问题。这个属性本身就是个CALayer类型，有和其他图层一样的绘制和布局属性。它类似于一个子图层，相对于父图层（即拥有该属性的图层）布局，但是它却不是一个普通的子图层。不同于那些绘制在父图层中的子图层，mask图层定义了父图层的部分可见区域。
mask图层的Color属性是无关紧要的，真正重要的是图层的轮廓。mask属性就像是一个饼干切割机，mask图层实心的部分会被保留下来，其他的则会被抛弃。（如图4.12）

![4.12](https://ws2.sinaimg.cn/large/006mQyr2gy1g5ivg8yutpj30xc0d80to.jpg)

mask 是用来做遮罩的 
和响应区域无关


# 更改CALayer 的frame

Core Animation的集成引擎会查看每个layer的 contentsScale属性，决定layer的contents在集成的时候是否需要缩放。如果创建没有关联至view的layers，每个新的layer对象的比例系数初始化为1.0。如果不改变初始的比例系数，接下来直接将这样的layer画到高分辨率的显示屏上去时，layer的contents会自动缩放来弥补因不同的比例系数所造成的差异。如果不想layer的 contents被缩放，可以设置layer的比例系数为2.0，但是设置完成后的内容如果没有添加至高分辨率的显示屏，那么现存的内容可能会显得比预期的要小。修复该问题，则需要在高分辨率下使用该layer即可。

