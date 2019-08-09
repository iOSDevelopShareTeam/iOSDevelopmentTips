## Mac实用命令

##  描述性文件所在地址
```
~/Library/MobileDevice/Provisioning Profiles
~/Library/Application Support/Developer/Shared/Xcode/Plug-ins/  Xcode 
插件所在地址
```

## 显示隐藏文件：
defaults write com.apple.finder AppleShowAllFiles -bool true
## 隐藏：
defaults write com.apple.finder AppleShowAllFiles -bool false 
## 如何禁用仪表盘  

复制粘贴下面的命令到终端
defaults write com.apple.dashboard mcx-disabled -boolean YES && killall Dock
## 如何开启仪表盘
defaults write com.apple.dashboard mcx-disabled -boolean NO && killall Dock

## [Mac 预览查看描述性文件信息的插件](https://github.com/ealeksandrov/ProvisionQL)

## 如何查看代码行数

以下命令放到命令行中执行
```
find . -name "*.m" -or -name "*.h" -or -name "*.xib" -or -name "*.c" |xargs wc -l  

```

你可以将你需要的文件行数直接相加,或者你可以直接使用下面的命令
列出代码行数总和 :

```
find . -name "*.m" -or -name "*.h" -or -name "*.xib" -or -name "*.c" |xargs grep -v "^$"|wc -l  

```
当你的工程是swift 和OC 混编的情况下,你需要添加 -name "*.swift" 来统计swift 里面的行数

```
find . -name "*.m" -or -name "*.h" -or -name "*.xib" -or -name "*.c" -or  -name "*.swift" |xargs wc -l 

```
grep -v "^$"是去掉空行
注释也统计在代码量之内

