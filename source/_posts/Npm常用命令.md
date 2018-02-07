---
title: NPM常用命令
date: 2017-01-19 10:46:37
categories:
- 技术
- NPM
tags:
- npm
- 命令
---

整理常用的npm命令，以备记录
```shell
//安装模块 module_name 模块名称 version版本号 
//--save保存到配置文件package.json生产依赖，--save-dev开发依赖，--save-optional可选依赖
npm install <module_name>[@<version> --save[-S]|--save-dev[-D]|--save-optional[-O]]
//卸载模块 参数命令相同
npm uninstall <module_name>
//更新模块 scope 作用域 -g全局模块，否则当前作用域
npm update [<scope>] [<module_name> ...]
//检查模块是否已经过时
npm outdated [<module_name> ...]
//查看安装的模块列表
npm ls [[<scope>] <module_name> ...]
//创建package.json文件
npm init [-f|--force|-y|--yes]
//帮助命令 comm_name命令名称
npm help [<comm_name>]
//包的安装路径 
npm root [<scope>]
//npm配置文件管理
npm config [set,get,delete,list,edit]
  //设置代理 url镜像地址
  --npm config set proxy= <url>
  //设置镜像 https://registry.npm.taobao.org 淘宝镜像
  --npm config set registry="http://r.cnpmjs.org" 
  //临时镜像
  --npm install -g cnpm --registry=https://registry.npm.taobao.org
//管理模块的缓存
npm cache add <tarball file>
npm cache add <folder>
npm cache add <tarball url>
npm cache add <name>@<version>
npm cache ls [<path>]
npm cache clean [<path>]
//启动模块,可以自定义命令来配置一个服务器环境等 对应package.json文件scripts的start字段 args参数
npm start [-- <args>]
//停止、重启、测试
npm stop [-- <args>]
npm restart [-- <args>]
npm test [-- <args>]
//查看模块的注册信息 
//args参数信息 dependencies模块的依赖关系 repository.url模块的源文件地址 contributors模块的贡献者
npm view [<scope>/] <name>[@<version>] [args]...]
//发布模块
npm publish
```

[npm的package.json中文文档](https://github.com/ericdum/mujiang.info/issues/6)
