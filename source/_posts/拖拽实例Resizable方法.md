---
title: jqueryUi拖拽实例Resizable方法
date: 2019-05-12 19:21:21
tags: 
- js相关
categories:
- js相关
---

```
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>jQuery UI 缩放（Resizable） - 动画</title>
  <link rel="stylesheet" href="//code.jquery.com/ui/1.10.4/themes/smoothness/jquery-ui.css">
  <script src="//code.jquery.com/jquery-1.9.1.js"></script>
  <script src="//code.jquery.com/ui/1.10.4/jquery-ui.js"></script>
  <link rel="stylesheet" href="http://jqueryui.com/resources/demos/style.css">
  <style>
  #resizable { width: 150px; height: 150px; padding: 0.5em; }
  #resizable h3 { text-align: center; margin: 0; }
  .ui-resizable-helper { border: 1px dotted gray; }
  </style>
  <script>
  $(function() {
    $( "#resizable" ).resizable({
      animate: true
    });
  });
  </script>
</head>
<body>
 
<div id="resizable" class="ui-widget-content">
  <h3 class="ui-widget-header">动画</h3>
</div>
 
 
</body>
</html>
```

此方法调用后会生成对应上下左右的div，这个可以自己测试看看，发现问题，删除此方法调用的四个div的方式来使这个功能失效后，再次调用无效。

[关于此方法的使用教材以及实践](https://www.runoob.com/jqueryui/example-resizable.html)