---
title: 小程序WePY框架
date: 2019-02-19 21:28:45
tags:
---
>WePY是腾讯官方对vue.js再次封装的框架，对小程序的一些内部方法进行了重构和改造，我们不用写一些纷繁复杂的代码就可以进行开发。
优点：代码简易，上手后可以体会到mvvm架构的便捷性。
缺点：需要一定的小程序开发基础和vue知识，客户端需要nodeJs环境，而且代码不能在小程序开发助手里写（小程序不支持打开wpy）,而是在后缀为wpy的文件里编写后生成对应的小程序语言。（这里后面会讲到）
## 一、WePY的搭建

### 1.npm的安装（如果有nodeJs环境则跳过这一步，用npm -v检测）
Node.js 安装包及源码下载地址为：https://nodejs.org/en/download/

安装步骤：[Node.js安装配置](http://www.runoob.com/nodejs/nodejs-install-setup.html)

### 2.利用npm(Nodejs Package Manager)全局安装wepy-cli
npm isntall -g wepy-cli

### 3.创建项目
wepy init standard my-project

### 4.安装依赖+编译
npm install
wepy build --watch(watch参数可以实时监听文件修改后进行编译)

## 二、WePY项目的目录结构

├── dist                   微信开发者工具指定的目录（Wepy编译生成）
├── node_modules           依赖的包
├── src                    开发目录
|   ├── components         WePY组件目录
|   |   ├── com_a.wpy      可复用的WePY组件a
|   |   └── com_b.wpy      可复用的WePY组件b
|   ├── pages              WePY页面目录
|   |   ├── index.wpy      index页面（经build后，会在dist目录下的pages目录生成index.js、index.json、index.wxml和index.wxss文件）
|   |   └── other.wpy      other页面
|   └── app.wpy            小程序配置项（全局数据、样式、声明钩子等；经build后，会在dist目录下生成app.js、app.json和app.wxss文件）
└── package.json           项目的package配置

## 三、构建第一个hello world
### 1./src/pages目录下新建demo.wpy
````html
<!-- 对应demo.css start -->
<style lang="less">
.userinfo {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.userinfo-avatar {
  width: 80rpx;
  height: 80rpx;
  border-radius: 50%;
}

.userinfo-nickname {
  color: #aaa;
}
</style>
<!-- 对应demo.css end -->

<!-- 对应demo.wxml start -->
<template>
<view>{{motto}}</view>
<button @tap="bindViewTap">click</button>
</template>
<!-- 对应demo.wxml end -->

<!-- 对应demo.js start -->
<script>
  import wepy from 'wepy'

  export default class Demo extends wepy.page {
     //可用于页面模板绑定的数据
    data = {
        motto: 'Hello World',
    };
       //这里可以监听data的attr改动情况
      watch = {
      motto (newValue, oldValue) {
          console.log(`num value: ${oldValue} -> ${newValue}`)
      }
  }

    //事件处理函数(集中保存在methods对象中)
    methods = {
        bindViewTap () {
          console.log('click');
          this.motto = 'Hello I Can Change The World!';
        }
    };

    //页面的生命周期函数
    onLoad() {
      console.log('onLoad');
      wepy.request('https://www.baidu.com').then((d) => console.log(d));
    };

    onShow() {
      console.log('onShow');
    };

    }
</script>
<!-- 对应demo.js end -->
````
2.app.wpy配置路径
````html
    pages: [
      'pages/index',
      'pages/demo',
    ],
````



