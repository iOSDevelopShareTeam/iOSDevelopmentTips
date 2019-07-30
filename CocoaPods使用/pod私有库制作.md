# 本地制作cocoapods库

~/.cocoapods   所在目录 
制作cocoa pods 本地库
1. 建立 一个地方专门存放 sepc 
2. 添加依赖库

### 一 Podspec文件作用
Podspec语法就是一个规范，这个规范声明你的库包含哪些文件，去哪里获得库的源代码，库的名字，版本，说明等等。
Podspecs 一定要放在仓库的根目录(如果不放，各种bug等着呢)，并且库文件应该存储于根目录。文件不支持遍历父目录。
### 二 如何生成 Pod Spec 文件
命令格式：pod spec create + 名字
例如： pod spec crate TestSpec
保证pod安装成功
在命令行执行
任何路径下都可以执行这个命令，并且帮你生成TestSpec.podspec的文件
### 三 上个实例
Pod::Spec.new do |spec|
spec.name = 'Reachability'
spec.version = '3.1.0'
spec.license = { :type => 'BSD' }
spec.homepage = 'https://github.com/tonymillion/Reachability'
spec.authors = { 'Tony Million' => 'tonymillion@gmail.com' }
spec.summary = 'ARC and GCD Compatible Reachability Class for iOS and OS X.'
spec.source = { :git => 'https://github.com/tonymillion/Reachability.git', :tag => 'v3.1.0' }
spec.source_files = 'Reachability.{h,m}'
spec.framework = 'SystemConfiguration'
end
### 四 在podspec文件里， # 后面的是注释
### 五 podspec文件的开始和结束
Pod::Spec.new do |s|
end
上面这两行是这个文件的开始和结束
### 六 name
是否必须声明：是
解释：Pod库的名字
例如：spec.name = 'AFNetworking'
### 七 version
是否必须声明： 是
解释：Pod 库的版本说明，支持语义化版本https://semver.org/lang/zh-CN/
例如：spec.version = '0.0.1'
### 八 swift_version
是否必须声明:否
解释： pod库支持的swift版本
例如：spec.swift_version = '3.2'
### 九 cocoapods_version
是否必须声明:否
解释： pod库支持的CocoaPods版本
例如：spec.cocoapods_version = '>= 0.36'
### 十 authors
是否必须声明：是
解释：pod库作者的名字和邮箱
例如：
spec.author = 'Darth Vader'
spec.authors = 'Darth Vader', 'Wookiee'
spec.authors = { 'Darth Vader' => 'darthvader@darkside.com',
'Wookiee' => 'wookiee@aggrrttaaggrrt.com' }
可以看出，可以设置多个
### 十一 social_media_url
是否必须声明：否
例如:
spec.social_media_url = 'https://twitter.com/cocoapods'
### 十二 license
是否必须声明：是
解释：pod库许可证的类型(也就是软件授权协议)，各个协议的不同https://www.cnblogs.com/Vito2008/p/4806677.html。
例如：
spec.license = 'MIT'
spec.license = { :type => 'MIT', :file => 'MIT-LICENSE.txt' }
spec.license = { :type => 'MIT', :text => <<-LICENSE
Copyright 2012
Permission is granted to...
LICENSE
}
### 十三 homepage
是否必须声明：是
解释：pod库的主页，主页用来说明这个库的，可以是githup上库的地址，没有可以乱写
例如：
spec.homepage = 'http://www.example.com'
### 十四 source
是否必须声明:是
解释：指定代码地址。一般是git地址，后面可以跟tag--标签,branch-分支,commit-版本号,submodules-子目录，有了这些类型才知道取哪次提交的代码。
例如：
spec.source = { :git => 'https://github.com/AFNetworking/AFNetworking.git',
:tag => spec.version.to_s }
spec.source = { :git => 'https://github.com/typhoon-framework/Typhoon.git',
:tag => "v#{spec.version}", :submodules => true }
spec.source = { :svn => 'http://svn.code.sf.net/p/polyclipping/code', :tag => '4.8.8' }
spec.source = { :svn => 'http://svn.code.sf.net/p/polyclipping/code', :tag => '4.8.8' }
spec.source = { :hg => 'https://bitbucket.org/dcutting/hyperbek', :revision => "#{s.version}" }
spec.source = { :http => 'http://dev.wechatapp.com/download/sdk/WeChat_SDK_iOS_en.zip' }
spec.source = { :http => 'http://dev.wechatapp.com/download/sdk/WeChat_SDK_iOS_en.zip',
:sha1 => '7e21857fe11a511f472cfd7cfa2d979bd7ab7d96' }
支持的key：
:git => :tag, :branch, :commit, :submodules
:svn => :folder, :tag, :revision
:hg => :revision
:http => :flatten, :type, :sha256, :sha1
### 十五 summary
是否必须声明：是
解释：Pod库的简要说明。字数最多为140字。描述应该剪短，但是内容丰富。
例如：
s.summary = 'A delightful iOS and OS X networking framework.这是iOS的网络库'
### 十六 description
是否必须声明：否
解释：Pod的描述（description）比摘要（summary）更详细。
比如：
```
spec.description = <<-DESC
Computes the meaning of life.
Features:
1. Is self aware
...
42. Likes candies.
DESC
```
### 十七 screenshots
是否必须：否
解释： 这个用的人很少，用图片的方式去介绍库
例如：
```
spec.screenshot = 'http://dl.dropbox.com/u/378729/MBProgressHUD/1.png'
spec.screenshots = [ 'http://dl.dropbox.com/u/378729/MBProgressHUD/1.png',
'http://dl.dropbox.com/u/378729/MBProgressHUD/2.png' ]
```
### 十八 documentation_url
是否必须：否
解释：
例如：
### 十九 prepare_command
是否必须：否
解释：
例如：
```
spec.prepare_command = 'ruby build_files.rb'
spec.prepare_command = <<-CMD
sed -i 's/MyNameSpacedHeader/Header/g' .//.h
sed -i 's/MyNameOtherSpacedHeader/OtherHeader/g' .//.h
CMD
```
### 二十 static_framework
是否必须：否
解释：过
例如：
spec.static_framework = true
### 二十一 deprecated
是否必须：否
解释：pod库是否被弃用
例如：
spec.deprecated = true
### 二十二 deprecated_in_favor_of
是否必须：否
解释：曾经被被弃用的名字
例如：
spec.deprecated_in_favor_of = 'NewMoreAwesomePod'
### 二十三 Platform
是否必须：否
解释：库支持的平台，如果空则默认支持所有的平台，iOS系统和osx系统。当你支持多系统时，应该用deployment_target代替
例如：
spec.platform = :osx, '10.8'
spec.platform = :ios
spec.platform = :osx
### 二十四 deployment_target
是否必须：否
解释：平台支持的最小版本
例如：
spec.ios.deployment_target = '6.0'
spec.osx.deployment_target = '10.8'
### 二十五 dependency
是否必须：否
解释：你的库依赖的其它库。
~> 符号是要求的，因为这个符号提供了很好的版本控制。
比如 ~> 1.0.1会要求版本是在>= 1.0.1,但不会大于 1.1
比如 ~> 1.0 将会匹配1.0，1.0.1,1.1,但不会大于2.0
例如：
spec.dependency 'AFNetworking', '~> 1.0'
spec.ios.dependency 'MBProgressHUD', '~> 0.5'
如果是多个依赖
s.dependency 'YYModel','~> 1.0.4'
s.dependency 'AFNetworking','~> 3.2.0'
s.dependency 'Reachability','~> 3.2
### 二十六 requires_arc
是否必须：否
解释：允许你指定哪个文件使用ARC。或者所有的文件都是用ARC。不支持ARC的文件会添加 -fno-objc-arc.
例如：
spec.requires_arc = true
spec.requires_arc = false
spec.requires_arc = 'Classes/Arc'
spec.requires_arc = ['Classes/*ARC.m', 'Classes/ARC.mm']
### 二十七 framworks
是否必须：否
解释:库需要添加的系统库
例如:
spec.ios.framework = 'CFNetwork'
spec.frameworks = 'QuartzCore', 'CoreData'
### 二十八 weak_frameworks
是否必须：否
解释：A list of frameworks that the user’s target needs to weakly link against.
例如：
spec.weak_framework = 'Twitter'
spec.weak_frameworks = 'Twitter', 'SafariServices'
### 二十九 libraries
是否必须: 否
解释: A list of system libraries that the user’s target (application) needs to link against.
例如：
spec.ios.library = 'xml2'
spec.libraries = 'xml2', 'z'
### 三十 compiler_flags
是否必须：否
解释: 应该传递给编译器的标志列表
例如:
spec.compiler_flags = '-DOS_OBJECT_USE_OBJC=0', '-Wno-format'
### 三十一 pod_target_xcconfig
是否必须：否
解释：Any flag to add to the final private pod target xcconfig file.
例如：
spec.pod_target_xcconfig = { 'OTHER_LDFLAGS' => '-lObjC' }
### 三十二 user_target_xcconfig
是否必须:否
解释:
例如：
spec.user_target_xcconfig = { 'MY_SUBSPEC' => 'YES' }
### 三十三 prefix_header_contents
是否必须：否
解释：
例如:
spec.prefix_header_contents = '#import <UIKit/UIKit.h>'
spec.prefix_header_contents = '#import <UIKit/UIKit.h>', '#import <Foundation/Foundation.h>'
### 三十四 prefix_header_file
是否必须：否
解释：
例如:
spec.prefix_header_file = 'iphone/include/prefix.pch'
spec.prefix_header_file = false
### 三十五 module_name
是否必须：否
解释:
例如：
spec.module_name = 'Three20'
### 三十六 header_dir
是否必须：否
解释:
例如：
spec.header_dir = 'Three20Core'
### 三十七
是否必须：否
解释:
例如：
spec.header_mappings_dir = 'src/include'
### 三十八 source_files
是否必须：否
解释:
例如:
spec.source_files = 'Classes//.{h,m}'
spec.source_files = 'Classes//.{h,m}', 'More_Classes/*/.{h,m}'
将匹配所有的文件
c 将匹配所有c开头的文件
*c 将匹配所有c结尾的文件
*c *将匹配所有有c的文件
### 三十九 public_header_files
是否必须： 否
解释：包含的这些头文件将暴露给用户并且从中生成文档，在构建时，这些头文件将出现在构建目录中。如果没有指定，那么source_files中的所有头文件都被认为是公众的。
例如：
规格。public_header_files = 'Headers / Public / *。h'


转至：[Podspec 语法说明](https://www.jianshu.com/p/75e19c92df50)






