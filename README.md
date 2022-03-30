# 长图技术梳理
<a href="https://m.h5in.net/meituan_test/"><img src="./icon.jpg  " width="50px" height="50px" align="left" hspace="10" vspace="6"></a>
[美团美好生活长卷](https://m.h5in.net/meituan_test/)是我在学习长图项目中用来练手的第一个项目，接下来我将以这个项目为基础来梳理我对长图项目的文件目录和技术的理解。
***
## 长图H5项目
我所掌握的长图项目是公司基于[cocos](https://www.cocos.com/)所搭建的框架来实现的，主要是通过**html**，**css**和**javascript**等一系列前端语言实现的一个移动端用户长屏H5互动页面。

### 1、项目关键代码文件
#### 1）index.html
设计实现项目加载页内容和适配原则以及引入原生css文件以及部分需要提前引入的js资源库，下方为简单示例
```html
<link type="text/css" rel="stylesheet" href="css/style.css">
<div id="loading">
  //加载页面的设计与实现
</div>
<script src="libs/Stage.js"></script>
<script src="libs/jquery-2.1.3.min.js"></script>
```
#### 2）BaseScene.js
长图最关键的适配原则就放置于这个文件夹中,以下则是实现适配的关键函数
```javascript
resize: function () {
    var w1 = window.innerWidth;
    var h1 = window.innerHeight;
    var w2 = cc.winSize.width; //750
    var h2 = cc.winSize.height; //1240
    var w3 = cc.visibleRect.width;
    var h3 = cc.visibleRect.height;
    var r1 = w1 / h1;
    var s = 1;
    var scale_level = -1;
    if (w1 > h1) {
        s = w3 / 750; //横屏
    } else {
        s = w3 / 750; //竖屏
    }
    this.scale = s;
    return;
}
```
#### 3）game.js
在这个文件里则主要放置项目所需的接口函数以及一些功能函数，以下是部分示例代码

* 微信端分享部分
```javascript
setShare: function (title, desc, desc2) {
    var link = h5_config.baseLink + h5_config.para;
    var imgUrl = (h5_config.baseUrl || h5_config.baseLink) + 'images/icon.jpg';
    var success1 = function () {
        game.log('meituan_test', "share_sussess" + "_time");
    };
    var success2 = function () {
        game.log('meituan_test', "share_sussess" + "_friend");
    };
    var cancel = function () {
        game.log('meituan_test', "share_sussess" + "_cancel");
    };
    wx.updateTimelineShareData({
        title: title, // 分享标题
        link: link, // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
        imgUrl: imgUrl, // 分享图标
        success: success1,
        cancel:cancel
    });
    wx.updateAppMessageShareData({ 
        title: title, // 分享标题
        desc: desc, // 分享描述
        link: link, // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
        imgUrl: imgUrl, // 分享图标
        success: success2,
        cancel: cancel
    })
}
```
* 通过后端给到的接口将图片转化成base64
```javascript
getBase64:function(data,callback){
    $.get('./api/getBase64/', {
        signature: data.signature,
        timestamp: data.timestamp,
        nonceStr: data.nonceStr,
        ukey:h5_config.ukey,
        url:h5_config.headimgurl
    }, function (res) {
        callback && callback(res);
    }, 'json');
},

```    
#### 4）Tlayer.js
项目中的逻辑代码部分则主要在这个文件中实现，在这个文件中来绘制出UI部分页面，每个项目的代码基本上都不太一样，这边就不过多展示。

### 2、项目关键技术点
#### 1）[长图设计稿处理规范问题](https://github.com/Jjing95/md-s-study/blob/main/organizeLayers.md)
#### 2）ani_config、effect_config以及res_config(帧动画、音效以及分部加载)的配置规范
* 帧动画配置文件定义规范</br>
`[name,left(帧动画的top值),center(帧动画的中心位置),0(移动的x),0(移动的y),0(移动的时间),isplayed(是否已经播放过),isloop(是否循环播放),times(如果为非循环的帧动画，需要重复展示帧动画的次数)]`
```javascript
var anis=[
[name,left,center,0,0,0,isplayed,isloop,1],
[name,left,center,0,0,0,isplayed,isloop,1],
...
]
```
* 音效配置文件定义规范</br>
`[name,y（音乐开始播放的y）,y+h（音乐结束播放的y),0,null,isloop(是否循环播放)]`
```javascript
var effects=[
[name,y,y+h,0,null,loop],
[name,y,y+h,0,null,loop],
...
]
```
* 分部加载配置文件**res_config**上方的定义规范</br>
```javascript
game.resList=[
	['1',0,4800,false,8], //fales可以直接改为true
	['2',500,10000,false,2],
	['3',2900,14318,false,2]
];
//123是psd处理时a后面的数字 0，500，2900是指开始加载的位置（提前加载），14318是最大的位置（也就是最后的位置），false代表是否加载过，8，2，2对应精灵图数分别有多少
```
#### 3）实现长图滑动的关键代码函数，具体解释有在代码上进行注释
```javascript
this.setCallback(
    function (touch, event) {
        // console.log('touch end');
        canAutoScroll = true;
    },
    function (touch, event) {
        var delta = touch.getDelta();
        var nowTime = +new Date();
        if (lastTime) {
            velY = (delta.y / (nowTime - lastTime)) * 1000;
            velY = Math.max(Math.min(velY, 3000), -3000);
        }
        lastTime = nowTime;
        // p0_1.y += delta.y;
        if (game['isMove']) {
            p0_1.moveY(delta.y);//调用case‘p0_1’中定义的moveY函数，并且给它一个delta.y的参数
        }
    },
    function (touch, event) {
        game['isMove'] = true;
    }
);
this.moveY = function (deltaY) {
    //deltay是滑动距离
    //minY为初始y值，maxY为当前能滑动的最大y值
    self.y = Math.min(Math.max(self.y + deltaY, minY), maxY)
    self.percent = (self.y - minY) / (maxY - minY)
    game['nowY'] = self.y
    //game['nowY']及时存储滑动到哪了
    checkY(self.y, self.percent)
    // console.log(self.y,maxY,'y,maxY');
}
```
#### 4）长图中的按钮点击与实现
* 起初在项目图层处理时，需要将每个按钮的图层名称后面加上--**button**
* 然后再通过在**按钮元素.setCallback**的回调函数中控制点击以后触发的事件
```javascript
setCallback: function (endCallback, moveCallback, startCallback) {
	this.callback = endCallback;
	this.moveCallback = moveCallback;
	this.startCallback = startCallback;
}
```
#### 5）[长图资源过多时的分部加载](https://github.com/Jjing95/md-s-study/blob/main/partial_loading.md)

至此，以上就是我在长图项目的学习过程中，针对于我碰到的技术难点以及对一系列重要技术点展开的知识梳理。
