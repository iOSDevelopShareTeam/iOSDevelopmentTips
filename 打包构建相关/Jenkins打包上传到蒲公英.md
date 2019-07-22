## 使用Jenkins 打包上传到蒲公英

所需工具：
* Jenkins
* Fastlane
* homebrew（安装插件用）

## Jenkins 安装
jenkins安装参考:[Mac下jenkins安装步骤](https://www.jianshu.com/p/8eee4b73db24)
homebrew 安装是为了安装fastlane以及其他插件.安装参考：
粘贴以下命令到命令行即可：
```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

fastlane 安装参考：[参考官网](https://docs.fastlane.tools/getting-started/ios/setup/)

安装完成之后打开jenkins进行配置,创建项目：
设置git或者gitlab地址： (项目地址)
然后设置凭据(校验使用)
然后进行项目配置构建脚本：
```
fastlane gym --silent --scheme appScheme --clean --xcargs 'GCC_PREPROCESSOR_DEFINITIONS="$GCC_PREPROCESSOR_DEFINITIONS DEBUG=1 COCOAPODS=1"' --export_method development --output_name outputName
curl -F "file=@$WORKSPACE/appName.ipa" -F "uKey=userkey" -F "_api_key=apikey" https://qiniu-storage.pgyer.com/apiv1/app/upload
```
这里的appScheme 指的是应用的scheme， DEBUG=1 构建的环境为debug--export_method development 这是线下开发的环境 如果是adhoc要改为:ad-hoc,outputName导出的名字，file=@$WORKSPACE/appName.ipa导出ipad的地址 userkey：蒲公英app信息的userkey
apikey每个app上传时候的apikey 用于校验





