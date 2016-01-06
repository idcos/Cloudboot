## Prerequisites

You will need the following things properly installed on your computer.

* [Git](http://git-scm.com/)
* [Node.js](http://nodejs.org/) (with NPM)
* [Bower](http://bower.io/)
* [Ember CLI](http://www.ember-cli.com/)
* [PhantomJS](http://phantomjs.org/)
* [Go](https://storage.googleapis.com/golang/go1.5.2.src.tar.gz)

## 1.初始化mysql数据
* 安装mysql(如mysql已部署则略过此步骤)
```
yum install mysql-server
service mysqld start
chkconfig mysqld on
```

* 从git上下载初始化表结构数据，导入到数据库
```
mkdir -p /usr/yunji/
cd /usr/yunji
git clone https://github.com/idcos/osinstall-server.git
mysql -uroot < /usr/yunji/osinstall-server/doc/db/idcos-osinstall.sql
```

## 2.部署server
* 配置Go语言环境(如已部署则略过此步骤)
下载最新稳定版源码
`wget https://storage.googleapis.com/golang/go1.5.2.src.tar.gz`

拷贝到用户工作目录
`/home/<USER>/`

解压
`tar xf go1.5.2.src.tar.gz`
编译
`cd go/src/ && ./make.bash`

* 配置环境变量
`vim ~/.bashrc`
追加以下内容
```
export GOROOT=~/go
export GOPATH=~/golibs
export PATH=$GOROOT/bin:$GOPATH/bin:$PATH
```
重新启动终端就可以开始进行Go开发了

安装gb工具
`go get github.com/constabulary/gb/...`


* 部署server代码
```
cd /usr/yunji
git clone https://github.com/idcos/osinstall-server.git
cd /usr/yunji/osinstall-server/
```

配置数据库连接:
`vim bin/idcos-os-install.json`
```
"repo": {
    "connection": "user:password@tcp(localhost:3306)/idcos-osinstall?charset=utf8&parseTime=True&loc=Local"
},
```

设置PXE配置文件目录:
```
"osInstall":{
    "pxeConfigDir":"/var/lib/tftpboot/pxelinux.cfg"
}
```

启动可执行文件
```
chmod 755 bin/os-install-server
nohup bin/os-install-server &>bin/os-install-server.log &
```


## 3.部署UI前端
```
cd /usr/yunji
git clone https://github.com/idcos/osinstall-ui.git
cd /usr/yunji/osinstall-ui/
npm install
bower install
ember server --proxy=http://localhost:8083/
```

* Visit your app at
`http://localhost:4200/#/dashboard/main`