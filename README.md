# 长图技术梳理
<a href="https://m.h5in.net/meituan_test/"><img src="./icon.jpg  " width="50px" height="50px" align="left" hspace="10" vspace="6"></a>
[美团美好生活长卷](https://m.h5in.net/meituan_test/)是我在学习长图项目中用来练手的第一个项目，接下来我将以这个项目为基础来梳理我对长图项目的技术梳理与理解。


## 长图H5项目
我所掌握的长图项目是基于公司所搭建的**cocos**框架来实现的，主要是通过*html*，*css*和*javascript*等一系列前端语言实现的一个移动端用户长屏H5互动页面。

### 1、项目关键代码文件
#### 1）index.html
设计实现项目加载页内容和适配原则以及引入原生css文件以及部分需要提前引入的js资源库。
```html
    <link type="text/css" rel="stylesheet" href="css/style.css">
    <div id="loading">
      //加载页面的设计与实现
    </div>
    <script src="libs/Stage.js"></script>
    <script src="libs/jquery-2.1.3.min.js"></script>
```
#### 2）BaseScene.js
长图最关键的适配原则就放置于这个文件夹中
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
在这个文件里则主要放置项目所需的接口函数以及一些功能函数
    
#### 4）Tlayer.js
---
### 2、项目主要技术代码
