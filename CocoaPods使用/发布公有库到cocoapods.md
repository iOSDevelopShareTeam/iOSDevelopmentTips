## 发布公有库到cocoapods
1. 创建.podspec
2. 编辑.podspec
3. 将自己的项目打成tag
4. 验证
5. 注册CocoaPods
6. 发布
其中1和2可以copy别人写好的。没什么技术含量
3.就是把项目打tag
4.就是验证podspec 的正确性
5.就是要发布到cocoapods 官方库里面 需要注册一个账号而已 
pod trunk register  shavekevin@gmail.com ’shavekevin' —verbose
（如果之前注册过 可以检查一下） pod trunk me
![屏幕快照 2018-01-17 11.01.03](https://ws2.sinaimg.cn/large/006mQyr2gy1g5j0hxivw6j319t09ldoa.jpg)
6.就是发布，在发布工程的目录下。pod trunk push xxx.podspec 
成功以后是这个样子的
![屏幕快照 2018-01-17 10.58.45](https://wx4.sinaimg.cn/large/006mQyr2gy1g5j0i7asw7j31h60niax6.jpg)
然后在终端搜索 就行了。
