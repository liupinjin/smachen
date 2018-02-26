---
title: Angular CLI生产环境打包优化
date: 2018-02-05 14:54:41
tags: 
- Angular
- Angular cli
categories:
- 技术
- Angular
---
Angular开发前端记录，学习[Angular中文官网](https://angular.cn/)
<h2>搭建环境</h2>1.安装[Nodejs](http://nodejs.cn/)运行环境，运行node -v 和 npm -v命令检查是否安装成功
2.安装[Visul Studio Code](https://code.visualstudio.com/download)IDE前端开发工具（根据个人喜好）
3.安装Angular CLI自动化工具，[文档详见](https://github.com/angular/angular-cli/wiki)，当nodejs和npm安装成功执行如下命令：
```shell
npm install -g @angular/cli
```
<h2>Angular Cli创建模版项目</h2>使用自动化工具创建项目，[文档详见](https://github.com/angular/angular-cli/wiki/new)
```shell
ng new my-app  //my-app项目名称
cd my-app      //定位当前项目
```
<h2>Angular Cli项目启动开发</h2>启动开发环境，参数open是自动打开浏览器访问[http://localhost:4200/](http://localhost:4200/)，当项目内容有更改是会自动刷新代码，[文档详见](https://github.com/angular/angular-cli/wiki/serve)
```shell
ng serve --open/-o
```
<!-- more -->
命令的方式创建组件，样式，通道，指令，模版等等文件，[文档详见](https://github.com/angular/angular-cli/wiki/generate)
```shell
ng generate component  (生成组件名称)
ng generate directive  (生成指令名称)
ng generate pipe       (生成通道名称)
ng generate service    (生成服务名称)
ng generate class      (生成类名称)
ng generate interface  (生成接口名称)
ng generate enum       (生成枚举名称)
ng generate module     (生成模块名称)
```
<h2>Angular Cli项目打包发布</h2><h3>打包</h3>运行 ng build 命令打包项目，这个构建命令将在 dist/ 目录生成打包后的文件，使用 -prod 标识来打包生产环境。
```shell
ng build --prod
```
会生成如下文件列表
```shell
Date: 2018-02-05T07:19:17.379Z
Hash: 0255b68f13827b5d0e0e
Time: 121990ms
chunk {0} 0.ab5a554abcd0e5c4cf9a.chunk.js () 9.91 kB {2}  [rendered]
chunk {1} polyfills.1304402038f58f1772b1.bundle.js (polyfills) 103 kB {5} [initial] [rendered]
chunk {2} main.c7d04be13fb3f6c41fa6.bundle.js (main) 2.41 MB {4} [initial] [rendered]
chunk {3} styles.e676f919cb178a241a51.bundle.css (styles) 676 bytes {5} [initial] [rendered]
chunk {4} vendor.439a443b6e8bb5c365b2.bundle.js (vendor) 3.02 MB [initial] [rendered]
chunk {5} inline.3d593c8dabb000d94892.bundle.js (inline) 1.47 kB [entry] [rendered]
```
建议执行如下命令，执行摇树代码优化，[文档详见](https://github.com/angular/angular-cli/wiki/build)
```shell
ng build --prod --build-optimizer --vendor-chunk
```
使用此命令后项目会有很大的优化空间，详见vendor.xxxxx.js会压缩50%
```shell
Date: 2018-02-05T07:35:45.923Z
Hash: e3d0e40ca56c284418c3
Time: 148662ms
chunk {0} 0.8b6a1e1376cf5cfce80b.chunk.js () 9.89 kB {2}  [rendered]
chunk {1} polyfills.1304402038f58f1772b1.bundle.js (polyfills) 102 kB {5} [initial] [rendered]
chunk {2} main.e9174de888f70382719e.bundle.js (main) 2.4 MB {4} [initial] [rendered]
chunk {3} styles.e676f919cb178a241a51.bundle.css (styles) 676 bytes {5} [initial] [rendered]
chunk {4} vendor.f191a71a137df5d90b1b.bundle.js (vendor) 1.84 MB [initial] [rendered]
chunk {5} inline.3a45ddba3f4a68cf0543.bundle.js (inline) 1.47 kB [entry] [rendered]
```
<h3>发布</h3>将打包名称生成的dist/ 目录直接拷贝到你的服务器上，可以开发nginx方向代理服务器的资源压缩功能，来优化用户的访问速度,[详见Nginx的配置]()，开启此功能项目的访问速度控制才3s内访问。


