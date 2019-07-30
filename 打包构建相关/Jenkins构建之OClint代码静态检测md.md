


# 使用Jenkins 构建之OClint代码静态检测脚本

```
#!/bin/sh
cd /Users/xxxx/.jenkins/workspace/companyName
export LANG=en_US.UTF-8
INFOPLIST_FILE="xxx/Info.plist"
bundleversion=$(/usr/libexec/PlistBuddy -c "print :CFBundleShortVersionString" "$INFOPLIST_FILE")  
buildnum=$(/usr/libexec/PlistBuddy -c "print :CFBundleVersion" "$INFOPLIST_FILE")
workspaceName="xxx.xcworkspace"
scheme="xxx"
#
if [ -d ./build/derivedData ]; then
 echo '-----清除上次编译数据derivedData-----'
 rm -rf ./build/derivedData
fi
#
xcodebuild -workspace $myworkspace -scheme $myscheme clean
#
xcodebuild -workspace "$workspaceName" \
-scheme "$scheme" \
-derivedDataPath ./build/derivedData \
COMPILER_INDEX_STORE_ENABLE=NO |
xcpretty -r json-compilation-database -o compile_commands.json
#
if [ -f ./compile_commands.json ]; then echo "编译生成数据成功"
else echo "编译生成数据失败"
fi
#
echo "分析中"
#
exclude_files=("Pods")
exclude=""
for i in ${exclude_files[@]}; do
exclude=${exclude}"-e "${i}" "
done
#
echo "排除目录：${exclude}"
#
echo "生成报表中"
#
oclint-json-compilation-database ${exclude} -- \
-verbose \
-stats \
-rc LONG_CLASS=1500 \
-rc LONG_LINE=200 \
-rc LONG_METHOD=100 \
-rc LONG_VARIABLE_NAME=20 \
-rc SHORT_VARIABLE_NAME=3 \
-rc CYCLOMATIC_COMPLEXITY=15 \
-max-priority-1=100000 \
-max-priority-2=100000 \
-max-priority-3=100000 \
-disable-rule=BrokenOddnessCheck \
-disable-rule=VerifyProhibitedCall \
-disable-rule=VerifyProtectedMethod \
-disable-rule=SubclassMustImplement \
-disable-rule=BaseClassDestructorShouldBeVirtualOrProtected \
-disable-rule=DestructorOfVirtualClass \
-disable-rule=ParameterReassignment \
-disable-rule=AvoidDefaultArgumentsOnVirtualMethods \
-disable-rule=AvoidPrivateStaticMembers \
-disable-rule=TooManyFields \
-disable-rule=TooManyMethods \
-disable-rule=TooManyParameters \
-disable-rule=UseEarlyExitsAndContinue \
-disable-rule=UnusedMethodParameter \
-report-type pmd -o pmd.xml
#
if [ -f ./pmd.xml ]
then
 rm compile_commands.json
 echo '-----分析完毕-----'
else
 rm compile_commands.json
 echo "-----分析失败-----"
fi
```