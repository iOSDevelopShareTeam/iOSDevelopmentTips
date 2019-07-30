# Flutter学习之环境搭建

以下内容来自组内分享，如有侵权，请联系作者删除。

目录：

* 什么是Flutter？
* Flutter环境配置
* 如何创建一个Flutter app?
* Flutter开发语言以及代码结构
* Flutter优缺点

## 1.什么是Flutter？

**背景：**在全球，随着Flutter被越来越多的知名公司应用在自己的商业APP中，Flutter这门新技术也逐渐进入了移动开发者的视野，尤其是当Google在2018年IO大会上发布了第一个Preview版本后，国内刮起来一股学习Flutter的热潮。目前使用flutter 开发的app有闲鱼。

**简介：**Flutter 是 Google推出并开源的移动应用开发框架，主要是跨平台、高保真、高性能。开发者可以通过 Dart语言开发 App，一套代码同时运行在 iOS 和 Android平台。 Flutter提供了丰富的组件、接口，开发者可以很快地为 Flutter添加 native扩展。同时 Flutter还使用 Native引擎渲染视图，为用户提供良好的体验。

## 2.Flutter环境配置

* 操作系统:MACOS
* 磁盘空间：700M(不包括IDE所占空间)
* 工具：bash mkdir rm git curl unzip which
**镜像使用：**
* 国内访问Flutter受限制，Flutter官方为中国开发者搭建了临时镜像。把镜像添加到用户环境变量中。（临时镜像）
* export PUB_HOSTED_URL=https://pub.flutter-io.cn
* export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
{<1>}![](https://ws1.sinaimg.cn/large/006mQyr2ly1g4kc9xw40fj30op0dyjug.jpg)
* 因为flutter-io.cn服务器是GDG China 维护的Flutter依赖项和临时包的临时镜像。Flutter团队无法保证这个服务的长期可用性。如果有兴趣设置自己的影像可以联系flutter开发团队(lutter-dev@googlegroups.com)。

社区运行的镜像站点：
* 上海交通大学Linux用户组：FLUTTER_STORAGE_BASE_URL:https//mirrors.sjtug.sjtu.edu.cn
* PUB_HOSTED_URL: https://dart-pub.mirrors.sjtug.edu.cn

### 配置flutter环境（命令行）
 ```
 $ export PUB_HOSTED_URL=https://pub.flutter-io.cn
$ export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
$ git clone -b dev https://github.com/flutter/flutter.git
$ export PATH="$PWD/flutter/bin:$PATH"
$ cd ./flutter
$ flutter doctor
 flutter doctor  作用：检查是否需要安装其他依赖来完成安装
 ```
{<2>}![](http://ww1.sinaimg.cn/large/006mQyr2ly1g4kctgkj0vj30op0dyjug.jpg)
 
#### 环境配置注意项
 
使用zsh客户端的时候终端启动时~/.bash_profile将不会被加载，解决办法就是修改~./zshrc，在其中添加：source ~/.bash_profile

### iOS设置
* 安装xcode
* 设置iOS模拟器 open -a Simulator
* 启动应用程序flutter run命令(工程目录下)

### 配置编辑器

* 使用的是VS Code（版本1.20.1及以上版本）
* 安装flutter插件
* 通过flutter  doctor 验证设置

### PS:如何安装flutter 插件
* 启动VS Code
* 调用view->command palette
* 输入install 选择Extentions:Install Extention action
* 搜索flutter 选择后 点击install
* install完成之后点击OK重新启动VS Code

## 3.如何创建一个Flutter app?

VSCode->view->command palette->Flutter new project->输入项目名称(小写字母可以使用下划线)->等待项目创建完成

###工程结构
* .idea IntelliJ IDEA 
* .vscode  vscode 附属相关
* .android  安卓工程
* .build  编译产物
* .ios   iOS代码
* lib 写代码的地方
* test
* .packages
* pubspec.lock 编写文件配设的版本
* pubspec.yaml 编写文件配置的语言
* README.md
{<3>}![工程结构](https://ws1.sinaimg.cn/large/006mQyr2ly1g4kchtvmdkj30ey0j00td.jpg)

## 4.学习flutter需要了解什么？？
### 什么是widget?
Flutter Widget采用现代响应式框架构建，这是从 React 中获得的灵感，中心思想是用widget构建你的UI。 Widget描述了他们的视图在给定其当前配置和状态时应该看起来像什么。当widget的状态发生变化时，widget会重新构建UI，Flutter会对比前后变化的不同， 以确定底层渲染树从一个状态转换到下一个状态所需的最小更改
### 什么是dart？
Dart 是一个静态语言，这也是相对于js的一个优势。Dart可以被编译成js，虽然看起来像java。静态语言可以避免错误，获得更多的编辑器提示词。极大的增加了可维护性。

### pub依赖之yaml文件
Flutter工程之间的依赖管理是通过Pub来管理的，依赖的产物是直接源码依赖，这种依赖方式和iOS中的Pod有点像，都可以进行依赖库版本号的区间限定与Git远程依赖等，其中具体声明依赖是在pubspec.yaml文件中，其中的依赖编写是基于YAML语法，YAML是一个专门用来编写文件配置的语言，声明依赖后，通过运行flutter packages get命名，会从远程或本地拉取对应的依赖，同时会生成pubspec.lock文件，这个文件和IOS中的Podfile.lock极其相似，会在本地锁定当前依赖的库以及对应版本号，只有当执行flutter packages upgrade时，这时才会更新。

## 5.Flutter 优缺点
### Flutter优点
* 性能强大，流畅 

  * Flutter 和weex以及rn相比，性能好很多。谷歌直接在两个平台上重写了各自的UIKit。对接到平台的底层，减少了UI层的多层转换。UI性能可以和原生媲美。

* 优秀的动画设计
 * Flutter动画比较简单，只需要将一个组件的属性通过补间(Tween)关联到动画对象上，Flutter会确保每一帧渲染正确的组件上，形成连贯的动画。
* UI跨平台稳定
 * google直接在两个平台底层上重写了UIKit 不依赖于css等外部解释器。

###  Flutter缺点
* 假装跨平台，避不开原生代码组合而不是继承的思路
 * 这个框架其实就是UI跨平台，具有与原生代码交互的能力，硬性要求啊 前端同学别轻易入坑。。(前端同学那里知道UIViewcontroller和Activity。。成本略高)再说了交互问题 不明白原生的怎么玩的转交互
 
* 采用组合的方式而不是继承
 * Flutter提倡组合，而不是继承。原生开发中我们可能会继承自UIView在这个基础上进行自定义。而flutter中的属性都是final的。你继承一个Container，不能在它的生命周期来修改属性。所以需要组合嵌套多种widget比如说：row container listview等widget。
 
* Wall
 * 这点不用多说了。
 
#### 总结:
Flutter开发主要在于要了解原生的环境。(好像跨平台的框架都是这样)，想要通过跨平台的api拿下两端开发是不太现实的。(平台本身存在差异性)有点也很明显，动画比较流畅。
#### 参考链接:

https://flutterchina.club/setup-macos/
https://segmentfault.com/a/1190000017164263
