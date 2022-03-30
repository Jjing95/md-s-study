# 分部加载

## 1、在何处调用最先需要加载的内容
在BaseLayer.js里面的onEnter函数里，调用this.initUI()函数的时候则会一次性的将我们所需要最先加载的内容加载进来。

## 2、长图加载资源过多时如何进行分部加载
* 在处理图层时需要注意所有元素都需要处理，但是不需要写入信息和打包，背景和所有按钮都放在一开始就加载的psd里;</br>
* 将需要分步加载的资源整理分成不同的psd文件，然后剩下必须要加载的整合成一个psd，处理记录信息，并分开来拼合成精灵图，这样需要使用哪个资源就只需要加载该资源所在的精灵图，剩下来的就不需要加载了;</br>
* 在处理完图层和数据之后要在config.js里面把和后面加载的东西一样的部分给删掉，防止预加载
* 代码部分需要注意,滑动的时候异步加载，通过传参调用tlayer.js中封装好的addEles函数，实则调用的是game.js中的loadELse函数
```javascript
//tlayer.js
addElse: function (num, name, callback) {
	var self = this;
	game['loadElse'](num, name, function (num, name) {
    		// console.log(game['resources']['res' + name])
    		cc.loader.load(game['resources']['res' + name],
		function (result, count, loadedCount) {
	    		// console.log(result)
		},
		function () {
	    		for (var i = 0; i < num; i++) {
				cc.spriteFrameCache.addSpriteFrames(game['baseUrl'] + 'res/' + name + '/GameAssets_' + i + '.plist', game['baseUrl'] + 'res/' + name + '/GameAssets_' + i + '.png');

	    		}
	    		self.initUI(name);
	    		callback && callback()

		});
	})
},
//game.js
loadElse: function (num, name, callback) {
	var g_resources2 = this['resources']['res' + name];
        // console.log(num)
        //加载对应年代的精灵图
        for (var i = 0; i < num; i++) {
            g_resources2.push(game['baseUrl'] + 'res/' + name + '/GameAssets_' + i + '.png')
            g_resources2.push(game['baseUrl'] + 'res/' + name + '/GameAssets_' + i + '.plist')

        }
        // console.log(g_resources2)
        callback && callback(num, name);
},
```
