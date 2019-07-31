# Xcode使用相关
## iOS开发中有用的终端命令

1. 查看静态库支持的CPU架构
2015年2月之后，AppStore上架审核要求必须支持arm64。Architecture这里选择standard architecture，Xcode6会默认支持armv7,arm64。对于arm7s，官方都已经放弃了，如果是打包上架可以不用支持，如果是提供支持库，则最好支持，让开发者选择。下面一栏的Valid architectures与Architecture的交集就是编译出来支持的架构。
查看test.framework支持架构：
cd test.framework
                                                 
xcrun lipo -info test

查看test.a支持架构：file test.a

1. 查看工程中是否使用了IDFA
2. 打开终端cd到项目根目录，然后执行：grep -r advertisingIdentifier 

##  xcode中用模拟器打开的时候，打开失败  

命令行执行下面命令(杀掉模拟器进程)
killall -9 com.apple.CoreSimulator.CoreSimulatorService
重启用xcode 打开即可

