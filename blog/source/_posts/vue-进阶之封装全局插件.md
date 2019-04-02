---
title: vue 进阶之封装全局插件
date: 2018-08-06 12:34:06
tags:
---
刚接触vue 对里面的使用方法十分不熟练
从demo操作转到数据类型操作，中间还是有很多坑要填的0.0
今天，讲解的是，如何自己封装插件转换时间类型
首先，你的插件要能全局调用
在 config.js 创建插件  globalFunction.js  这里的命名最好驼峰式
有遇到“烤串”命名法，不被识别，好坑
 //写一个时间转化函数
 
 ```
let format = (value) => {
  return value >= 10 ? value : '0' + value
}

exports.install = function (Vue, options) {
  Vue.prototype.TimeOut = function (time, type){
    let date = new Date(time * 1000);
    let year = date.getFullYear()
    let month = date.getMonth() + 1
    let day = date.getDate()
    let hours = date.getHours()
    let minutes = date.getMinutes()
    let second = date.getSeconds()
    let result
    switch (type) {
      case 0: // 01-05
        result = `${format(month)}月${format(day)}日`
        break
      case 1: // 11:12
        result = `${format(hours)}:${format(minutes)}:${format(second)}`
        break
      case 2: // 2015-01-05
        result = `${year}-${format(month)}-${format(day)}`
        break
      case 3: // 2015-01-05 11:12
        result = `${year}-${format(month)}-${format(day)}  ${format(hours)}:${format(minutes)}`
        break
      case 4:// 2015-01-05 11:12:06
        result = `${year}-${format(month)}-${format(day)}  ${format(hours)}:${format(minutes)}:${format(second)}`
        break
      case 5:// Jan 05, 2015 11:12:06
        result = date.format('M dd, yyyy HH:mm:ss')
        break
      case 6:// Jan 05, 2015
        result = date.format('M dd, yyyy')
        break
      case 7:// Jan 05, 2015
        result = `${year}年${format(month)}月${format(day)}日  ${format(hours)}:${format(minutes)}`
        break
    }
    
    return result;

  };
};
```

Vue.prototype 指向你自己封装的函数，方面以后自己调用
然后在 main.js 里面 引入函数生成全局调用
import globalFunction from '@/assets/config/globalFunction'
Vue.use(globalFunction);   //这一步一定不能忘

接下来在子组件里面 任何一个函数，获取到时间值，直接调用
this.TimeOut()

例如  console.log(this.TimeOut(1533686888,4))  //2018-08-08  08:08:08

结尾：
对插件稍微介绍一下
timeOut 是封装的函数,通过传值和要转化的时间类型，对值进行解析转化，
这个函数还可以放在 filter里面 封装一个 html页面的时间过滤插件
```
export const dateFilter = (time, type) => {
  let date = new Date(time * 1000)
  let year = date.getFullYear()
  let month = date.getMonth() + 1
  let day = date.getDate()
  let hours = date.getHours()
  let minutes = date.getMinutes()
  let second = date.getSeconds()
  let result
  switch (type) {
    case 0: // 01-05
      result = `${format(month)}月${format(day)}日`
      break
    case 1: // 11:12
      result = `${format(hours)}:${format(minutes)}:${format(second)}`
      break
    case 2: // 2015-01-05
      result = `${year}-${format(month)}-${format(day)}`
      break
    case 3: // 2015-01-05 11:12
      result = `${year}-${format(month)}-${format(day)}  ${format(hours)}:${format(minutes)}`
      break
    case 4:// 2015-01-05 11:12:06
      result = `${year}-${format(month)}-${format(day)}  ${format(hours)}:${format(minutes)}:${format(second)}`
      break
    case 5:// Jan 05, 2015 11:12:06
      result = date.format('M dd, yyyy HH:mm:ss')
      break
    case 6:// Jan 05, 2015
      result = date.format('M dd, yyyy')
      break
    case 7:// Jan 05, 2015
      result = `${year}年${format(month)}月${format(day)}日  ${format(hours)}:${format(minutes)}`
      break
  }
  return result
}
```

然后在main.js 里面调用
//import * as filters from './filters'
//Vue.filter('date', filters.dateFilter)

vue页面里面直接 数据绑定，进行过滤  
scope.row.time | date(4)

(*^▽^*),结束...完美封装一个插件
推荐一首歌  Lifted -Allie X ,飘飘欲仙  让人觉得，放纵，是一种飘在
云端的逍遥