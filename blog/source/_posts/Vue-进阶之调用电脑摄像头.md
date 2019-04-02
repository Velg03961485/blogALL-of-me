---
title: Vue 进阶之调用电脑摄像头
date: 2018-07-25 12:07:52
tags:
---

在开发过程中，硬件是你不得不面对的一件事
软硬结合是现代发展趋势，今天
我就讲解一下，最近面对的一个问题
如何快速调用电脑的摄像头，拍照并生成一张图片进行人脸识别（识别这块放在下期）
最近在用vue开发，今天的主题也是在vue的基础上O(∩_∩)O哈哈~
先上代码w(ﾟДﾟ)w
<!--前端代码--/>
开启摄像头的时候，前端页面先给一个实时录像
<video id="video" autoplay="" style='width:640px;height:480px'></video>
点击拍照事件
<el-button id='picture' @click="pImage">拍摄</el-button>
生成的照片，用canvas生成
<canvas id="canvas" width="640" height="480"></canvas>

<!--js代码-->
实现起来超简单的

camera_process(){
        //访问用户媒体设备的兼容方法
        function getUserMedia(constraints, success, error) {
          if (navigator.mediaDevices.getUserMedia) {
            //最新的标准API
            navigator.mediaDevices.getUserMedia(constraints).then(success).catch(error);
          } else if (navigator.webkitGetUserMedia) {
            //webkit核心浏览器
            navigator.webkitGetUserMedia(constraints,success, error)
          } else if (navigator.mozGetUserMedia) {
            //firfox浏览器
            navigator.mozGetUserMedia(constraints, success, error);
          } else if (navigator.getUserMedia) {
            //旧版API
            navigator.getUserMedia(constraints, success, error);
          }
        }

        function success(stream) {
          //兼容webkit核心浏览器
          let CompatibleURL = window.URL || window.webkitURL;
          //将视频流设置为video元素的源
          //console.log(stream);

          //video.src = CompatibleURL.createObjectURL(stream);
          video.srcObject = stream;
          video.play();
        }

        function error(error) {
          console.log(`访问用户媒体设备失败${error.name}, ${error.message}`);
        }

        let video = document.getElementById('video');
        let canvas = document.getElementById('canvas');
        let context = canvas.getContext('2d');
        let image = new Image();

        if (navigator.mediaDevices.getUserMedia || navigator.getUserMedia || navigator.webkitGetUserMedia || navigator.mozGetUserMedia) {
          //调用用户媒体设备, 访问摄像头
          getUserMedia({video : {width: 480, height: 320}}, success, error);
        } else {
          alert('不支持访问用户媒体');
        }

       
      }

这里是调用实时摄像的代码，在vue中，最好把camera_process函数放在 mounted里面
进入页面就开始缓加载

pImage() {
        let context = canvas.getContext('2d');
        let image = new Image();
        context.drawImage(video, 0, 0, 480, 320);
        // console.log(context.drawImage);
        image = canvas.toDataURL("image/jpeg");//base64

        this.$data.takeImages = image;
        // console.log(this.$data.takeImages)

        document.getElementById('getVideo').style.display = 'none';
        //  获取头像
        this.getShowVideo = false;
        this.dialogVisible = true;
      }

点击开始拍摄，并生成canvas 照片

(*^▽^*)，这些都很简单的，只要代码copy一份就能实现
但是，接下来，你要传给后台了，万事离不开最终实践

canvas生成的图片是不能直接传给后台的，需要做一系列处理

1.首先 先把canvas转化为base64格式
image = canvas.toDataURL("image/jpeg");//base64

2.转化之后发现，卧槽，数据流也太长了吧，传过去完全是消耗性能
一定要转化为file模式才行
dataURLtoFile(dataurl, filename){
        var arr = dataurl.split(','), mime = arr[0].match(/:(.*?);/)[1],
          bstr = atob(arr[1]), n = bstr.length, u8arr = new Uint8Array(n);
        while(n--){
          u8arr[n] = bstr.charCodeAt(n);
        }
        return new File([u8arr], filename, {type:mime});
      }

这是从base64转化file的函数
3.调用函数，转化为file，发给后台
let file = this.dataURLtoFile(this.$data.takeImages,'testaaa.jpg');
let list = new FormData();
list.append('file', file);

(*^▽^*)简简单单的调用摄像头拍摄照片就这样完成了

后记：
稍微讲解一下关于 DataUrl 与 file，Blob，canvas之间的相互转化

1.canvas转化为dataURl
let a = canvas.toDataURl('b/png')  //base64

2.file转化为 dataURL / Blob 转化为dataURL (两者方式一样)
function readBlobAsDataURL(blob, callback) {
    var a = new FileReader();
    a.onload = function(e) {callback(e.target.result);};
    a.readAsDataURL(blob);
}

readBlobAsDataURL(blob, function (dataurl){
    console.log(dataurl);
});
readBlobAsDataURL(file, function (dataurl){
    console.log(dataurl);
});

3.dataURL转化为Blob / dataURL转化为File  （IE不支持File对象构造函数）
function dataURLtoBlob(dataurl) {
    var arr = dataurl.split(','), mime = arr[0].match(/:(.*?);/)[1],
        bstr = atob(arr[1]), n = bstr.length, u8arr = new Uint8Array(n);
    while(n--){
        u8arr[n] = bstr.charCodeAt(n);
    }
    return new Blob([u8arr], {type:mime});
}
function dataURLtoFile(dataurl, filename) {
    var arr = dataurl.split(','), mime = arr[0].match(/:(.*?);/)[1],
        bstr = atob(arr[1]), n = bstr.length, u8arr = new Uint8Array(n);
    while(n--){
        u8arr[n] = bstr.charCodeAt(n);
    }
    return new File([u8arr], filename, {type:mime});
}
//test:
var blob = dataURLtoBlob('data:text/plain;base64,YWFhYWFhYQ==');
var file = dataURLtoFile('data:text/plain;base64,YWFhYWFhYQ==', 'test.txt');

4.dataURL 转化为canvas  （(*^▽^*)很好玩的）
var img = new Image();
img.onload = function(){
    canvas.drawImage(img);
};
img.src = dataurl;

5.File/Blob 绘制到canvas上
readBlobAsDataURL(file, function (dataurl){
    var img = new Image();
    img.onload = function(){
        canvas.drawImage(img);
    };
    img.src = dataurl;
});

6.Canvas转换为Blob对象并使用Ajax发送
var dataurl = canvas.toDataURL('image/png');
var blob = dataURLtoBlob(dataurl);
//使用ajax发送
var fd = new FormData();
fd.append("image", blob, "image.png");
var xhr = new XMLHttpRequest();
xhr.open('POST', '/server', true);
xhr.send(fd);



