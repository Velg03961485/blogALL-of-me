---
title: URl 取值传值
date: 2018-08-25 14:28:13
tags:
---

JQuery下
动态生成的数据 onclick 函数下面 传两个动态的值给函数
结果报错 一个值为 is not defined ，开发者查询页面 在函数里面两个值是存在的

```
for (var i = 0;i < Data.length ;i++){
    html = '<div class="guarant"> <div class="guarantBalck"><div class="guarantInfo"><p class="infoTime">维修日期:'+ Common.changemsTodate(Data[i].addtime) + '</p><p class="infoPart">'+ Data[i].beginDepartment +'</p></div><div class="guarantForm"><p class="infoName">商品名称:'+ Data[i].goodsName +'</p><p class="glyphicon glyphicon-chevron-right infoMark"></p></div></div><div class="btn" onclick="postCheck('+ Data[i].id+','+ Data[i].goodsName +')">查看进度</div></div>';
    $('.guaranteeBox').append(html);
}
```
经排查传入的值是需要用转义字符来转义一次的
于是把函数修改为
```
onclick="postCheck(\''+ Data[i].id+','+ Data[i].goodsName +'\')"
```
这样postCheck 传值就成功了，但是还有问题
输出的值 为一个参数，用逗号隔开
这样还是不符合要求
再次修改
```
onclick="postCheck('+ Data[i].id+',\''+ Data[i].goodsName +'\')"
```
这样两个值就能传值成功了
```
$(window).attr('location',"/web/certification/miantenanceRecordDetail?id="+ id +"&&name="+ name +"");
```


传到另一个页面，分别获取两个值
```
var id = window.location.href.split('?')[1].split('=')[1].split('&&')[0];
var name = window.location.href.split('&&')[1].split('=')[1].split('#')[0];
```
输出id正确，但是name，因为传值为中文，取到的值乱码

what a fuck!
意外总是意想不到，老司机开始发车，js 带有转码解析函数
一共有三种，总有一款适合你
1. escape 和 unescape
介绍:是对ASCII字母、数字、标点符号@*_+-./以外的其他字符进行编码
escape 编码
```
console.log(escape('懵可乐'));  %u61F5%u53EF%u4E50
```
unescape 解码
```
console.log(unescape('%u61F5%u53EF%u4E50'));   懵可乐
```
2.encodeURI 和 decodeURI
介绍:返回编码为有效的统一资源标识符（URL）的字符串，不会被编码的字符！@#$&*()=:/;?='  encodeURI 是js中真正用来对URL进行编码的函数
encodeURI 编码
```
console.log(encodeURI('https://velg03961485.github.io'));  https://velg03961485.github.io
console.log(encodeURI('%u61F5%u53EF%u4E50'));  懵可乐
```
decodeURI 解码
```
console.log(decodeURI('https://velg03961485.github.io'));  https://velg03961485.github.io
console.log(decodeURI('%u61F5%u53EF%u4E50'));  报错
Uncaught URIError: URI malformed
    at decodeURI
```
当编码的URL不是真正的URL，会报错此编译为malformed（畸形的地址）
3.encodeURIComponent 和 decodeURIComponent
介绍:对URL的组成部分进行个别编码，而不是对于整个URL进行编码
encodeURIComponent 编码
```
console.log(encodeURIComponent('%u61F5%u53EF%u4E50'));  %25u61F5%25u53EF%25u4E50
console.log(encodeURIComponent('https://velg03961485.github.io'));  https%3A%2F%2Fvelg03961485.github.io
```
decodeURIComponent  解码
```
console.log(decodeURIComponent('https://velg03961485.github.io'));  https://velg03961485.github.io
console.log(decodeURIComponent('https%3A%2F%2Fvelg03961485.github.io'));  https://velg03961485.github.io
console.log(decodeURIComponent('%u61F5%u53EF%u4E50'));  报错 
Uncaught URIError: URI malformed
    at decodeURIComponent
```
 






