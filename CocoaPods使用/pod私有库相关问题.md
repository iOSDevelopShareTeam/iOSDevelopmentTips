# pod私有库相关问题
## push本地pod到本地服务器

```
pod repo push LocalSpecsName XXX.podspec --sources=git@192.168.1.78:xxxx/xxxxgit,https://github.com/CocoaPods/Specs.git --allow-warnings
```

## 私有pod update

1.更改指定的文件 添加tag push
git tag -a 0.0.5 -m “message”
git tag push origin 0.0.5
2.验证
pod spec lint LPPushService.podspec --verbose --use-libraries --allow-warnings
3.注册电脑
pod trunk register shavekevin@gmail.com “shavekevin" 
4.push
pod trunk push SKNetworkRequest.podspec --use-libraries --allow-warnings
------------------------------------------
 🎉  Congrats
 🚀  SKNetworkRequest (0.0.7) successfully published
 📅  August 15th, 02:04
 🌎  https://cocoapods.org/pods/SKNetworkRequest
 👍  Tell your friends!
5. pod searc xxxx

pod spec lint SKDebugTool.podspec --verbose --use-libraries --allow-warnings
pod trunk push SKDebugTool.podspec --use-libraries --allow-warnings
pod spec lint SKAirSandbox.podspec --verbose --use-libraries --allow-warnings
pod trunk push SKAirSandbox.podspec --use-libraries --allow-warnings




