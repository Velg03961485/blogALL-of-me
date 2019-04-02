---
title: angular 图片上传orientation旋转属性
date: 2018-08-20 17:27:28
tags:
---

今天讲一个以前遇到的一个懵逼问题
一张后台返回的图片，显示在安卓的内嵌前端页面，倒转了90度（在上传的时候保证是正的）
在浏览器中，查看也是正的，单独的在html查看也是正的（jpeg格式）
各种懵，怎么会出现在这种情况
了解到这是因为 图片orientation的原因，横拍的图片，正常显示，但是这个旋转
属性值为90，造成在安卓上显示就倒转90了
1.最快的解决方式，重新让UI生成一张png格式的图片就ok了
2.如何知道自己上传的图片orientation 不正常（上传拦截这种图片）
因为这种图片很少，情况不建议写大量代码就更改图片的orientation值
后面有更改orientation的写法
因为是 写在angualr项目里面的，所以以angualr为主

*1.首先了解一下orientation属性
旋转0度    orientation参数为1
旋转90度   orientation参数为6
旋转180度  orientation参数为3
旋转270度  orientation参数为8
*2.这里我们用的是exif.js来获取图片的orientation值
https://github.com/Velg03961485/exif 插件的地址
*3在main.js中 paths 写入插件地址
```
require.config({
paths:{
//    查取图片的orientation属性
        'exif':'assets/exif',
}
})
```
*4在页面的controller js里面先引入 
```
require('exif');
```
*5前端页面
```
<div class="col-md-12">
                    <div class="form-group">
                        <label class="control-label col-md-2">上传缩略图：</label>
                        <form name="uploadForm">
                            <div class="col-md-2">
                                <ul style="list-style-type:none; margin:0;width:800px;">
                                    <li ng-repeat="img in imgs" style="width:130px;float: left;display:inline;margin-right: 10px;position:relative">
                                        <img ng-src="{{ img }}" width="120" height="120" />
                                        <span class="icon-close img-close" style='position:absolute;right:-2px;top:-4px;' ng-click="removeImg($index)"></span>
                                    </li>
                                    <li ng-if="idOlder == 1" ngf-select="uploadPic(file)" ng-model="file" name="file" ngf-pattern="'image/*'" ngf-accept="'image/*'" ngf-max-size="20MB" style="width:200px;float: left;display:inline">
                                        <img style="border:1px;cursor:pointer" src="css/img/updatepic.png" width="120" height="120" alt />
                                    </li>
                                </ul>
                            </div>
                            <!--<input type="hidden" ng-model="pics" name="pics" value="{{pics}}"/>-->
                        </form>
                    </div>
                </div>
```
*6app.controller
```
//上传图片
            $scope.selectUpload = function() {
                $scope.uploadTab.active = true;
            };


$scope.uploadPic = function(file) {
                angular.element(document.getElementById('landmark')).css('display','block');
                console.log(file)
                EXIF.getData(file, function() {
                    var Orientation = EXIF.getTag(this, 'Orientation');
                    if(Orientation == '3' || Orientation == '6' || Orientation == '8'){
                        alert('您上传的图片有问题，建议采用PNG格式');
                    }else{
                        if(file.type == 'image/png' || file.type == 'image/jpeg' || file.type == 'image/gif'){
                            if(file.size < 1*1024*1024){
                                file.upload = Upload.upload({
                                    url: '/admin/new/uploadImage',
                                    headers: {
                                        'optional-header': 'header-value'
                                    },
                                    data: {
                                        file: file
                                    }
                                });
                                file.upload.then(function(response) {
                                    console.log(response.statusText);
                                    //response.statusText=='OK'  此状态不可用了，可能是因为更新
                                    if(response.status== 200){
                                        //toastr.success("图片上传成功");
                                        $scope.imgs.push(response.data.url);
                                        $scope.idOlder = 0;
                                    }else{
                                        toastr.error("图片上传失败");
                                        // $scope.imgs.push(response.data.url);
                                    }
                                    angular.element(document.getElementById('landmark')).css('display','none');
                                    //$scope.defaultPicFile = "css/img/updatepic.png";
                                }, function(response) {
                                    if(response.status > 0)
                                        $scope.errorMsg = response.status + ': ' + response.data;
                                }, function(evt) {
                                    file.progress = Math.min(100, parseInt(100.0 * evt.loaded / evt.total));
                                });
                                file.upload.xhr(function(xhr) {});
                            }else{
                                alert('您选择的文件过大');
                                angular.element(document.getElementById('landmark')).css('display','none');
                            }
                        }else{
                            alert('您输入的文件不合法，请选择正确文件');
                            angular.element(document.getElementById('landmark')).css('display','none');
                        }
                    }
                });

            };
```
这里分别对图片的Orientation 判断并进行拦截和限制图片大小 以及图片格式
angular 简单的图片获取旋转属性并进行判断 就弄好了

3.对于发生旋转的图片如何去纠正，这就要使用canvas对图片进行更改了
CSND上面有各种解决方案，这里暂时就不先做出具体详解
同时还用到一个 mobileBUGFix.mini.js 插件，这里不太熟悉，只能等下期讲解

后记
Orientation 除了这四种属性之外，还有镜像的旋转属性，有兴趣的可以了解一下
对于canvas生成照片以及转化base64，前面的博客有介绍，几乎概括全部的文件来回转换类型
