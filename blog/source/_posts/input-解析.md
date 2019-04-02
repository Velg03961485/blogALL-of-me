---
title: input 解析
date: 2018-09-01 15:13:50
tags:
---

input 是最常用的前端标签，今天就要从它的样式，规范，兼容来详细的解析如何使用
input 给你带来不一样的效果

1.input样式优化
```
.input{
    border: 1px solid #ccc;
    padding: 7px 0px;
    border-radius: 3px;
    padding-left:5px;
    -webkit-box-shadow: inset 0 1px 1px rgba(0,0,0,.075);
    box-shadow: inset 0 1px 1px rgba(0,0,0,.075);
    -webkit-transition: border-color ease-in-out .15s,-webkit-box-shadow ease-in-out .15s;
    -o-transition: border-color ease-in-out .15s,box-shadow ease-in-out .15s;
    transition: border-color ease-in-out .15s,box-shadow ease-in-out .15s
  }
  .input:focus{
    border-color: #66afe9;
    outline: 0;
    -webkit-box-shadow: inset 0 1px 1px rgba(0,0,0,.075),0 0 8px rgba(102,175,233,.6);
    box-shadow: inset 0 1px 1px rgba(0,0,0,.075),0 0 8px rgba(102,175,233,.6)
  }
```

2.input 的type类型有 number，text，button，checkbox，file，hidden，image
password，radio，reset，submit
同时，根据input不同的类型，来配合使用它的属性，实现不同的效果

当为type='number'  IE火狐下会出现，后缀上下调数值按钮
可以使用以下样式去除
```
.input::-webkit-outer-spin-button,
  .input::-webkit-inner-spin-button{
    -webkit-appearance: none !important;
    margin: 0;
  }
```

3.input 设置后缀或者前缀
```
<div class="col-md-8 yuan">
                            <input type="number" step="0.00001" class="form-control" ng-model="OldList.originalPrice"/>
                        </div>
```
后缀名 为元 ，通过样式调整
```
.yuan{
     position: relative;
 }
.yuan:after{
    content:'元';
    display: inline-block;
    z-index:2;
    position: absolute;
    top:8px;
    right:24px;
    width:16px;
    height:16px;
    color:gray;
}
```
同时也可以通过前缀设置动画图标等 效果

4.vue中通过动态类名设置禁止输入
```
:class="{disabled: !this.canClick}"
```
```
.disabled{
      background-color: #ddd;
      border-color: #ddd;
      color:#57a3f3;
      cursor: not-allowed; // 鼠标变化
    }
```

