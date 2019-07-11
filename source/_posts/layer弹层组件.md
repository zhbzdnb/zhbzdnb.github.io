---
title: layer弹层组件
date: 2019-02-08 22:58:15
categories:
- js相关
tags: 
- js相关
---

### 目前，*layer已成为国内最多人使用的 Web 弹层组件*。

在工作项目中时常接触这个方法，给上[开发文档](https://www.layui.com/doc/modules/layer.html)。

看一下公司项目中随便抽出的layer使用的粒子

```
/*节目分组弹出框*/
function openItemGroup(){
	 layer.open({
 		type: 2,
         area: ['1100px', '590px'],
         offset: '10px',
         title:false,
         fix: false, //不固定
         shift: Math.ceil(Math.random()*6), //0-6的动画形式，-1不开启
         content: 'toitemg.vs',
         scrollbar: false,
         end: function(){ 
		   document.form1.target = "_self";
		   document.form1.action = "toitemlist.vs"; 
		   document.form1.submit();       
 		}
 	});               

}
```

## content - 内容

类型：String/DOM/Array，默认：''

content可传入的值是灵活多变的，不仅可以传入普通的html内容，还可以指定DOM，更可以随着type的不同而不同。​         

## **layer.open(options) - 原始核心方法**

基本上是露脸率最高的方法，不管是使用哪种方式创建层，都是走*layer.open()*，创建任何类型的弹层都会返回一个当前层索引，上述的*options即是基础参数*，另外，该文档*统一采用options作为基础参数的标识*例子：

```
codelayui.codevar index = layer.open({  content: 'test'});//拿到的index是一个重要的凭据，它是诸如layer.close(index)等方法的必传参数。               
```

噢，请等等，上面这位主角的介绍篇幅怎么看怎么都觉得跟它的地位不符，作者在文档中只给了它如此可怜的一块地？？这是因为，它真的已经大众得不能再大众了，你真正需要了解的，是它的内部器官，即上面一大篇幅介绍的各种基础参数。 ←_←

## **type - 基本层类型**

类型：Number，默认：0

layer提供了5种层类型。可传入的值有：*0*（信息框，默认）*1*（页面层）*2*（iframe层）*3*（加载层）*4*（tips层）。 若你采用*layer.open({type: 1})*方式调用，则type为必填项（信息框除外）

## **area - 宽高**

类型：String/Array，默认：'auto'

在默认状态下，layer是宽高都自适应的，但当你只想定义宽度时，你可以*area: '500px'*，高度仍然是自适应的。当你宽高都要定义时，你可以*area: ['500px', '300px']*

## **offset - 坐标**

类型：String/Array，默认：垂直水平居中

offset默认情况下不用设置。但如果你不想垂直水平居中，你还可以进行以下赋值：

| 值                        | 备注                        |
| ------------------------- | --------------------------- |
| offset: 'auto'            | 默认坐标，即垂直水平居中    |
| offset: '100px'           | 只定义top坐标，水平保持居中 |
| offset: ['100px', '50px'] | 同时定义top、left坐标       |
| offset: 't'               | 快捷设置顶部坐标            |
| offset: 'r'               | 快捷设置右边缘坐标          |
| offset: 'b'               | 快捷设置底部坐标            |
| offset: 'l'               | 快捷设置左边缘坐标          |
| offset: 'lt'              | 快捷设置左上角              |
| offset: 'lb'              | 快捷设置左下角              |
| offset: 'rt'              | 快捷设置右上角              |
| offset: 'rb'              | 快捷设置右下角              |

## **title - 标题**

类型：String/Array/Boolean，默认：'信息'

title支持三种类型的值，若你传入的是普通的字符串，如*title :'我是标题'*，那么只会改变标题文本；若你还需要自定义标题区域样式，那么你可以*title: ['文本', 'font-size:18px;']*，数组第二项可以写任意css样式；如果你不想显示标题栏，你可以*title: false*

## **fix - 固定**

类型：Boolean，默认：true

即鼠标滚动时，层是否固定在可视区域。如果不想，设置*fixed: false*即可

## **scrollbar - 是否允许浏览器出现滚动条**

类型：Boolean，默认：true

默认允许浏览器滚动，如果设定*scrollbar: false*，则屏蔽

## **end - 层销毁后触发的回调**

类型：Function，默认：null

无论是确认还是取消，只要层被销毁了，end都会执行，不携带任何参数。