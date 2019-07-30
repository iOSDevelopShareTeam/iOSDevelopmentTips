## 使用charles抓包 （https）
 抓http的包这个不用多讲，抓https的包注意一下几点:
* 安装charles证书
* 手机描述性文件信任设置 设置->通用->描述性文件与设备管理->charles xxxx
* 证书信任设置  设置->通用->关于本机->证书信任设置->charles 信任开状态


## Chisel 开发工具安装

* 第一步：安装homebrew
* 第二步： 执行brew  update 
* 第三步：执行命令 brew  install chisel  加载chisel
![AC7E137A-DF54-4703-9219-2072588722CD](https://ws2.sinaimg.cn/large/006mQyr2gy1g5hxlxnc40j30x4086gox.jpg)
* 第四步：  创建lldbinit 文件 执行：# ~/.lldbinit   
 touch  .lldbinit
* 第五步：打开.lldbinit 文件执行 open  .lldbinit   操作
* 第六步： 把第三步加载的  command script import /usr/local/opt/chisel/libexec/fblldb.py 粘贴到 .lldbinit 文件中并保存
* 第七步：结束xcode进程  重新打开xcode
