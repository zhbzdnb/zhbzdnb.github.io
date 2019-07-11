---
title: 新手引导intro.js
date: 2019-03-03 22:09:28
categories:
- js相关
tags: 
- js相关
---

这个东西非常简单用于项目中面向客户的新手引导

[intro.js官方快速入门文档](https://introjs.com/docs/getting-started/start)

简单使用：引入 intro.js 、intro.css、jquery.min.js

```
<html>
  <head>
     <link href="intro.css" rel="stylesheet" type="text/css" />
     <script src="jquery.min.js" type="text/javascript"></script>
     <script src="intro.js" type="text/javascript"></script>

  </head>
  <body>

    <div id="demo1" data-step="1" data-intro="步骤一" data-position="right">步骤一
    </div>
    <div id="demo2" data-step="2" data-intro="步骤二" data-position="top">步骤二
    </div>
    <div id="demo3" data-step="3" data-intro="步骤三" data-position="left">步骤三
    </div> 
https://introjs.com/docs/getting-started/start
  </body>

 <script type="text/javascript">
         introJs().start();
     </script>
</html>
```

效果参考

![](新手引导intro-js\QQ图片20190303223213.png)

