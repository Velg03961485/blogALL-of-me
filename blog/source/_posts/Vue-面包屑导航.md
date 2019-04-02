---
title: Vue 面包屑导航
date: 2018-08-14 15:28:54
tags:
---

最近在写vue的面包屑导航菜单 
要实现 在面包屑的基础上能来回跳转页面（页面间交互很多，需要传值）
1.首先 明确页面布局
在 main.vue 里面 调整顶部栏，侧边栏以及content
```
<template>
    <div class="main-box">
      <navbar ref="header"></navbar>
      <sidebar ref="leftmenu"></sidebar>
      <div class="content" ref="content">
        <router-view></router-view>
      </div>
    </div>
</template>
```
在这里还有一个重要的函数，当路由发生变化的时候，要让面包屑也发生改变
```
fetchData: function () {
      let pathName = this.$route.name
      // 判断当前路径，设置菜单效果
      let usercenter = this.$refs.content
      let sidebar = this.$refs.leftmenu

      if (usercenter) {
        usercenter.navMenu = pathName
      }

      switch (pathName) {
        // 商家
        case 'BusinessList':
        case 'Modules':
          sidebar.defaultActive = '2-1'
          sidebar.imgSrcHome = 'static/images/main/home.png'
          break
        // 设备
        case 'Pallet':
        case 'Store':
        case 'Device':
          sidebar.defaultActive = '3-1'
          sidebar.imgSrcHome = 'static/images/main/home.png'
          break
        case 'Flow':
          sidebar.defaultActive = '3-2'
          sidebar.imgSrcHome = 'static/images/main/home.png'
          break
        case 'Face':
          sidebar.defaultActive = '3-3'
          sidebar.imgSrcHome = 'static/images/main/home.png'
          break
        case 'Counter':
          sidebar.defaultActive = '3-4'
          sidebar.imgSrcHome = 'static/images/main/home.png'
          break
        // 设置
        case 'AccountNumber':
          sidebar.defaultActive = '4-1'
          sidebar.imgSrcHome = 'static/images/main/home.png'
          break
        case 'Role':
          sidebar.defaultActive = '4-2'
          sidebar.imgSrcHome = 'static/images/main/home.png'
          break
        case 'Power':
          sidebar.defaultActive = '4-3'
          sidebar.imgSrcHome = 'static/images/main/home.png'
          break
        // 工作日志
        case 'LoginList':
          sidebar.defaultActive = '5-1'
          sidebar.imgSrcHome = 'static/images/main/home.png'
          break
        case 'ActiveList':
          sidebar.defaultActive = '5-2'
          sidebar.imgSrcHome = 'static/images/main/home.png'
          break
        //鹰眼人脸数据测试
        case 'FaceSampleGrouping':
          sidebar.defaultActive = '6-1'
          sidebar.imgSrcHome = 'static/images/main/home.png'
          break
        case 'FaceSampleManage':
        case 'RecognitionResult':
        case 'RecognitionList':
          sidebar.defaultActive = '6-2'
          sidebar.imgSrcHome = 'static/images/main/home.png'
          break
        case 'RecognitionResultsFound':
        //   usercenter.topMenu = ''
        //   usercenter.left_menu_active = '0'
        //   usercenter.top_menu_active = '0'
        //   break
      }
    }
```
在mounted调用 此函数
2.在router.js 里面设置路由
要记得，把面包屑引入
```
import Templates from '../views/template/Templates'
```
在 new Router 的路由配置里面 component为 Tempaltes
例如：
```
{
      path: 'equipment',
      component: Templates,
      children: [
          // 设备
          { path: 'counter', name: 'Counter', component: Counter },
          { path: 'face', name: 'Face', component: Face },
          { path: 'flow', name: 'Flow', component: Flow },
          { path: 'pallet', name: 'Pallet', component: Pallet },
          { path: 'store/:storeId', name: 'Store', component: Store },
          { path: 'device/:shopId', name: 'Device', component: Device }
      ]
    }
```
3.然后在 Tempaltes.vue 写要呈现的面包屑
主要实现方式，根据URL的地址来判断 菜单点击到哪一步，把需要的面包屑呈现出来
```
<template>
    <div class="templates">
      <!-- 导航 start -->
      <div class="nav-menu">
        <!-- 商家 start -->
        <el-breadcrumb v-if="navMenu === 'BusinessList'" separator="/">
          <el-breadcrumb-item>商家</el-breadcrumb-item>
          <el-breadcrumb-item>商家管理</el-breadcrumb-item>
        </el-breadcrumb>

        <el-breadcrumb v-if="navMenu === 'Modules'" separator="/">
          <el-breadcrumb-item>商家</el-breadcrumb-item>
          <el-breadcrumb-item><router-link :to="{ name: 'BusinessList' }" replace>商家管理</router-link></el-breadcrumb-item>
          <el-breadcrumb-item>模块&nbsp;&nbsp;{{ name }}</el-breadcrumb-item>
        </el-breadcrumb>
        <!--鹰眼人脸数据测试-->
        <el-breadcrumb v-if="navMenu === 'FaceSampleGrouping'" separator="/">
          <el-breadcrumb-item>鹰眼人脸数据测试</el-breadcrumb-item>
          <el-breadcrumb-item>人脸样本分组管理</el-breadcrumb-item>
        </el-breadcrumb>
        <el-breadcrumb v-if="navMenu === 'FaceSampleManage'" separator="/">
          <el-breadcrumb-item>鹰眼人脸数据测试</el-breadcrumb-item>
          <el-breadcrumb-item>
            <router-link :to="{ name: 'FaceSampleGrouping' }" replace>人脸样本分组管理</router-link>
          </el-breadcrumb-item>
          <el-breadcrumb-item>人脸样本管理</el-breadcrumb-item>
        </el-breadcrumb>
        <el-breadcrumb v-if="navMenu === 'RecognitionResult'" separator="/">
          <el-breadcrumb-item>鹰眼人脸数据测试</el-breadcrumb-item>
          <el-breadcrumb-item>
            <router-link :to="{ name: 'FaceSampleGrouping' }" replace>人脸样本分组管理</router-link>
          </el-breadcrumb-item>
          <el-breadcrumb-item>
            <router-link :to="{ name: 'FaceSampleManage', params: {'id': id}}" replace>人脸样本管理</router-link>
          </el-breadcrumb-item>
          <el-breadcrumb-item>识别结果查看</el-breadcrumb-item>
        </el-breadcrumb>
        <el-breadcrumb v-if="navMenu === 'RecognitionList'" separator="/">
          <el-breadcrumb-item>鹰眼人脸数据测试</el-breadcrumb-item>
          <el-breadcrumb-item>识别结果列表</el-breadcrumb-item>
        </el-breadcrumb>
        <el-breadcrumb v-if="navMenu === 'RecognitionResultsFound'" separator="/">
          <el-breadcrumb-item>鹰眼人脸数据测试</el-breadcrumb-item>
          <el-breadcrumb-item>
            <router-link :to="{ name: 'RecognitionList' }" replace>识别结果列表</router-link>
          </el-breadcrumb-item>
          <el-breadcrumb-item>识别结果查看</el-breadcrumb-item>
        </el-breadcrumb>
      </div>
      <!-- 导航 end -->
      <!-- 主要内容 start -->
      <div class="main">
        <router-view></router-view>
      </div>
      <!-- 主要内容 end -->
    </div>
</template>
```
这里还有的点，就是 面包屑的来回跳转
使用包裹 router-link 进行跳转，简单的传值，直接使用重url取值再传过去
建议使用 storage.setLocalStorage 或者 vuex
4.最后就需要在你跳转的页面，进行接参，把面包屑展现出来
```
created: function () {
        // 刷新时，获取动态数据 设置navmenu
        let templates = this.$parent
        templates.navMenu = this.$route.name
        templates.upperLevelMenu = ''
        this.faceListAll()

      }
```