# 图层处理部分
## 长图psd目录结构示意图
<img src="./eg_psd.jpg"></br>

### --type scene
表示最外层容器，其他所有的长图图层内容都放置于这个容器中，代码框架中也是根据这个scene来进行UI部分的页面适配
### --type layer
上图中的*p0_0 --type layer*为一个透明的空白浮层，作为滑动浮层使用</br>
而*p0_2 --type layer*则是放置内容的图层文件，代表这个文件内的所有图层均处于同一页面上，长图项目则主要就为这一页。

## 长图部分图层命名规范示意图
<img src="./eg_btn_ani.jpg">
<img src="./eg_ani.jpg">
<img src="./eg_bg.jpg">
