---
title: Vue 小方法2
date: 2018-08-24 13:04:20
tags:
---

这里主要是记录一下使用Vue的小方法（element下）
1.默认勾选-2.保存的富文本过滤标签以及转化文本-3.登录监听-4.本地模拟编译-5.表格label如何居中

1.默认勾选
使用element的表格（带有勾选的），查看已经勾选状态，后台返回的数据
前端要有默认勾选的
element 有专用的默认勾选方法 toggleRowSelection
刚开始的时候在请求数据返回的之后，写上
```
this.$refs.table.toggleRowSelection(this.tableData3[this.$data.index],true);
```
报错 toggleRowSelection undfined
十分纳闷，element官网给的方法，为什么会报错找不到
一层一层打印输出
console.log(this.$refs)
console.log(this.$refs.table)
console.log(this.$refs.table.toggleRowSelection)
发现在this.$refs.table里面toggleRowSelection方法存在
但是一输出 this.$refs.table.toggleRowSelection 方法就找不到了
备注：这里 table 指向的是 el-table ref="table" 不要自己定义了但是没有换

这种事情怎么会发生？上一级有下面函数就丢了

我的返回数据函数，是在 created函数里面调用的，一进页面就触发
突然想到了，是不是生命周期的问题，是在是新手对Vue的各种触发函数（生命周期）不熟
试了一下，不在数据返回后直接调取勾选，而采用间接调用
```
checked(){
          //首先el-table添加ref="table"引用标识
          this.$refs.table.toggleRowSelection(this.tableData3[this.$data.index],true);
        },
```
methods 函数
然后在数据获取之后再调用 checked方法
```
this.$nextTick(function(){
                this.checked();//每次更新了数据，触发这个函数即可。
              })
```
完美解决问题


2.保存的富文本过滤标签以及转化文本

使用富文本添加数据，展现列表，会把content内容返回到列表里面
但是由于你编辑的内容过多，不能全部展现在列表里面，同时展现的时候，后台给你返回的数据是带有标签的（标签是不能让后台过滤的，存储的标签是你文档编辑的格式，后台是不能给你删除的）
为了方便一些，这里封装了一个过滤函数，在列表展现的时候，过滤标签和限制文本长度
其余部分以...显示
首先在 vue 的filter index.js 里面 创建过滤函数
```
//文本过滤长度以及标签 
export const fontFilter = (value,num) =>{
  value = value.replace(/<[^>]+>/gim, '');
  value = value.substr(0, num)
  return value + ('...');
}
```
然后在 main.js里面 把函数设置为全局
```
Vue.filter('fonts',filters.fontFilter)
```
过滤的函数具体使用以及介绍，在前面的博客有

接着在列表里面
```
item.content | fonts(10)
```
这里传入的 10 是截取的文本长度

列表有了，查看和修改就少不了
修改还好，你只要把获取的数据重新填回富文本就好了（这里就显示出来后台不能删标签的作用了），文本编辑的样式会重现出来

但是查看，你不能还填回富文本里面吧
后台返回的是一串带标签的字符串，这怎么展示啊
使用div包裹数据，同时使用html编译，自动生成前端文本
这里element有自带的方法
v-html 太好用了，直接转化标签为文本模式，同时样式还保留着
```
<div v-html="checkForm.content">checkForm.content</div>
```
尤其一些富文本插件，上传的图片是base64的


3.监听登录

在input 监听enter键登录  
原生标签下 
```
<input placeholder="请输入密码" type="password" @keyup.enter="login">
```

element 下
```
<el-input type="password" v-model="loginInfo.password" auto-complete="off" @keyup.enter.native="login" placeholder="密码" />
```
监听在输入密码的input监听keyup事件，加enter修饰符。如果input是组件(element)，要加上.native修饰符

同时在vue文档里面 对监听事件有详细的介绍：
在监听原生 DOM 事件时，方法以事件为唯一的参数。如果使用内联语句，语句可以访问一个 $event 属性：v-on:click="handle('ok', $event)"。

.stop - 调用 event.stopPropagation()。
.prevent - 调用 event.preventDefault()。
.capture - 添加事件侦听器时使用 capture 模式。
.self - 只当事件是从侦听器绑定的元素本身触发时才触发回调。
.{keyCode | keyAlias} - 只当事件是从特定键触发时才触发回调。
.native - 监听组件根元素的原生事件。
.once - 只触发一次回调。
.left - (2.2.0) 只当点击鼠标左键时触发。
.right - (2.2.0) 只当点击鼠标右键时触发。
.middle - (2.2.0) 只当点击鼠标中键时触发。
.passive - (2.3.0) 以 { passive: true } 模式添加侦听器


4.本地模拟编译
vue npm run dev 运行项目的时候在开发模式是可以的

但是线上是需要编译的，前端为了模拟一下编译环境，是可以直接执行下面的方法的

首先，你要安装一个anywhere 这个会自动在你本地生成一个服务环境
当你运行编译的时候，能直接把项目启动起来

终端  npm install anywhere -g 安装anywhere
项目下 npm run build  编译完后项目会出现一个 dist文件夹
在文件内 执行命令 anywhere 就可以了


5.表格label如何居中

在使用element的模板的时候，el-table-column 标签里面的 是左对齐的
为了样式美化，都是居中的
这里不要想着覆盖样式，element有自带的 居中 align="center"
如果这样
```
<el-table-column
      label="编号" width="50" align="center"
    >
      <template slot-scope="scope">scope.row.customer_id </template>
    </el-table-column>
```
那就在 el-table 定义style 居中就行了