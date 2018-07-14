
## 如何安装指定版本的Cocoapods？
### 问题描述：
我们在使用`Cocoapods`的工程中进行协同开发的时候，避免不了对`podfile`文件的更改，当`podfile`文件发生改变的时候，对应的`pofile.lock`也会发生更改。当然团队在开发过程中也不希望每次拉一次代码就执行以下`pod install` 命令。正因为`podfile`以及`podfile.lock`的改变，我们才需要去执行`pod install` 或者`pod update`命令。`podfile`文件的更改，我们开发中可以控制，但是如果团队开发者`Cocoapods`版本不一致的时候。每更改一次都需要执行`pod install` 或者`pod update`命令。如果成员都不对`podfile`文件做更改，并且Cocoapods版本都一致的时候，每次进行代码提交以及代码拉取的时候都不需要执行pod 更新的命令，那么，如果版本不一致怎么办呢？统一版本呗。
### 解决方案：
`Cocoapods`版本的更新这里分两种情况：

Case1：当前安装的`Cocoapods`版本比目标版本低但是目标版本又不是最新的，不能够直接用更新命令(因为这样会更新到最新版本)。这个时候我们要怎么做呢？

假设当前`Cocoapods`版本为`1.5.0`,目标版本为`1.5.2`。

这个时候需要执行命令：

```
sudo gem install cocoapods --version 1.5.2
```

Case2：当前安装的`Cocoapods`版本比目标版本高，这个时候我们不能按照Case1的做法，因为这个时候执行的只是安装版本1.5.2,版本1.5.3还在。cocoapods在使用的时候默认的就是最高版本的cocoapods。那么，我们应该怎么做呢？
* 首先是卸载高版本`Cocoapods`。查找当前环境安装的cocoapods版本可以使用命令 `gem list`。此时控制台打印的可能为：
```
cocoapods (1.5.3,1.5.0, 1.3.1, 1.1.1)
cocoapods-core (1.5.3,1.5.0, 1.3.1, 1.1.1)
cocoapods-deintegrate (1.0.2, 1.0.1)
cocoapods-downloader (1.2.0, 1.1.3)
cocoapods-plugins (1.0.0)
cocoapods-search (1.0.0)
cocoapods-stats (1.0.0)
cocoapods-trunk (1.3.0, 1.2.0)
cocoapods-try (1.1.0)
```

   1.5.2才是我们的目标版本。1.5.3是最新的版本，我们需要把它卸载掉。命令如下：
```
sudo gem uninstall cocoapods -v 1.5.3
sudo gem uninstall  cocoapods-core -v 1.5.3
```

  注意：这里必须把cocoapods和cocoapods-core都卸载掉。
  
*  其次是安装指定版本。命令和Case1的命令一样。
  ```
  sudo gem install cocoapods --version 1.5.2
  ```
  
  这样，我们就安装了指定版本的`cocoapods`。
  
总结一下：

1. 当前版本比目标版本低的时候，直接安装指定版本的`cocoapods`。
```
sudo gem install cocoapods --version 目标版本
```

2. 当前版本比目标版本高的时候
分两步：
卸载当前版本：

```
sudo gem uninstall cocoapods -v 当前版本
sudo gem uninstall  cocoapods-core -v 当前版本
```

安装指定版本：

```
sudo gem install cocoapods --version 目标版本
```
## Swift引用报错的问题
### 问题描述:
当我们使用`cocoapods`安装一个第三方的时候，安装成功以后，在引用第三方头文件的时候可能会报错，报错信息的大概意思是找不到这个头文件(`"Cannot load underlying module for 'SnapKit'"` 类似这样的语句 )。
### 解决方案：
com+b(把工程编译一下就行了)
// Xcode编译器在第一次使用cocoapods的时候。提示不是很友好，编译一下就好了。
  