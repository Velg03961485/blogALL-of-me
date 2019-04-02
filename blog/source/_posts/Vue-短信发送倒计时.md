---
title: Vue 短信发送倒计时
date: 2018-08-16 19:24:08
tags:
---

项目里面，登录注册，都是需要短信验证的
倒计时，就是十分关键的了
倒计时要注意的
1.点击获取验证码之后，点击事件失效
2.60秒之后要回到最初状态
3.打破倒计时

```
<el-col :span="2"><el-button @click="code()" :class="{disabled: !this.canClick}">{{getClickName}}</el-button></el-col>
```
这里采用动态添加button内容和操作类名 实现倒计时展现和失效事件

失效样式
```
.disabled{
      background-color: #ddd;
      border-color: #ddd;
      color:#57a3f3;
      cursor: not-allowed; // 鼠标变化
    }
```
这个一定不能忘

js 这块
首先定义最初状态
```
data(){
	return{
		getClickName:'获取验证码',
      waitTime:60,
      canClick: true,
	}
}
```
定义点击事件，开始倒计时
```
getMsg(){
      if (!this.canClick) return  ;
      this.canClick = false
      this.$data.getClickName = this.$data.waitTime + 's后发送';
      clock = window.setInterval(() => {  //这里的colock，我在全局定义，方便打破
        this.$data.waitTime--;
        this.$data.getClickName = this.$data.waitTime + 's后发送';
        if (this.$data.waitTime < 0) {
          window.clearInterval(clock)
          this.$data.getClickName = '发送验证码';
          this.$data.waitTime = 60;
          this.canClick = true  //这里重新开启

        }
      },1000);
    }
```
接来下就是你是怎么调用倒计时事件了，根据不同的情况，有不同的调用方式
1.发送验证码成功的事件里调用倒计时
2.在点击开始倒计时的时候，调用发送验证码
这个两个不同的时候方法，一定要注意的是，不要重复调用或者调用冲突

打破倒计时，一般出现在，当你重新在手机输入框获取焦点，选择输入新的手机号的时候
倒计时回到最初状态
```
<el-col :span="15"><el-input v-model="passwordForm.phone" @focus="needsC"></el-input></el-col>
```
```
needsC(){
      window.clearInterval(clock)
      this.$data.getClickName = '发送验证码';
      this.$data.waitTime = 60;
      this.canClick = true  //这里重新开启
      this.$data.passwordForm.phone = '';
    }
```
这样一个倒计时功能就完美实现了