---
title: Vue 小方法(处理)
date: 2018-08-13 15:16:47
tags:
---

最近在Vue 项目里，有遇到很多小细节处理
1.vue项目在IE下不能运行
抛出 ReferenceError: "Promise" 未定义
这里是因为 IE兼容的问题
最快速有效的解决方式
安装 Babel Polyfill
```
npm install --save babel-polyfill
```
这样就OK了

如果出现问题，可以继续用下面方式
在webpack.base.conf.js这个文件加入代码 require("babel-polyfill")

在main.js里面添加代码 import "babel-polyfill";

2.修改element默认样式
使用element模板是一种快速开发方式
但是，又不得不面对去改动样式的问题，尤其最近有遇到 要改变 el-input 标签的样式
分析标签层 发现 原生的input是包裹在div标签下面的
直接改动样式是无效的 同时抓到el-input 类名去更改也是无效的

解决方式
去掉 scoped ，让此样式能显示
但是这样做，回去全局样式造成污染

可以把样式放到全局控制样式文件里面
同时为了不让这样的样式影响到其他的el-input
需要给el-input 标签加一个 单独的类名 例如 styleInput
```
.styleInput .el-input_inner{
	border:1px solid red;
}
```
3.element-ui 图片上传成功之后，页面图片清除不掉
这个问题，实在是...无语
清除上传记录，清除数据，清除页面  皆无可用
其实element 有方法，但是，官网就是没有说明

在上传的el-upload标签上 绑定ref 
```
ref="upload"
```
然后上传成功函数，或者关闭函数里面
```
this.$refs.upload.clearFiles();
```
ok 这样就可以清除了，但是图片数组的数据也要记得清空一下，尤其是弹框下的
省的会造成数据污染

后记 
后面会有一篇专门介绍 el-upload 上传的使用方式
全面分析 el-upload 的各种事件以及处理方法
