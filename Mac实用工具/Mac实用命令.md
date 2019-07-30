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

