##  node 安装
1. 安装node.js 需要sudo apt-get install software-properties-common
然后sudo add-apt-repository ppa:chris-lea/node.js 
2. 修改utf8的部分 需要重新启动mysql 输入
`#在[client]下添加`
default-character-set=utf8
  执行命令`# service mysql restart`
3. 安装ghost 需要安装curl
先输入＃sudo apt-get install curl libcurl3 libcurl3-dev php5-curl
