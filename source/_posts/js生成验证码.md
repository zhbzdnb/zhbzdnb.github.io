---
title: js生成验证码
date: 2019-07-09 22:24:35
tags:
- js相关
categories:
- js相关
---





### 使用方法

直接在前端页面设置即可

##### js方法

​    /*切换验证码*/

```
 function loadimage(){ 
	     document.getElementById("rand_image").src = "login_yzcode.jsp?"+Math.random(); 
    }
```



#### html设置	

	<input style="border:1px solid #b5b5b5;"  type="text" name="yzcode" id="yzcode"value=""/>   <div class="check_code">
			<a href="javascript:loadimage();">
			       <img  id="rand_image" src="login_yzcode.jsp" /> 
			</a>  
	  </div>
​		

#### 源码login_yzcode.jsp

```
<%@ page contentType="image/JPEG"   
    import="java.awt.*,java.awt.image.*,java.util.*,javax.imageio.ImageIO"   
    pageEncoding="UTF-8"%>   
<%!Color getRandColor(int fc, int bc) {
        Random random = new Random();    
        if (fc > 255)     
            fc = 255;    
        if (bc > 255)    
            bc = 255;    
        int r = fc + random.nextInt(bc - fc);    
        int g = fc + random.nextInt(bc - fc);    
        int b = fc + random.nextInt(bc - fc);    
        return new Color(r, g, b);    
    }%>   
<%    
    response.setHeader("Pragma", "No-cache");    
    response.setHeader("Cache-Control", "no-cache");    
    response.setDateHeader("Expires", 0);    
    
    int width = 60, height = 20;    
    BufferedImage image = new BufferedImage(width, height,    
            BufferedImage.TYPE_INT_RGB);    
    Graphics g = image.getGraphics();    
    Random random = new Random();    
    g.setColor(getRandColor(200, 250));    
    g.fillRect(0, 0, width, height);    
   

    g.setFont(new Font("Times New Roman", Font.PLAIN, 18));    
   
  
    g.setColor(getRandColor(160, 200));    
    for (int i = 0; i < 100; i++) {    
        int x = random.nextInt(width);    
        int y = random.nextInt(height);    
        int xl = random.nextInt(12);    
        int yl = random.nextInt(12);    
        g.drawLine(x, y, x + xl, y + yl);    
    }    
   
  
    String sRand = "";    
    for (int i = 0; i < 4; i++) {    
        String rand = String.valueOf(random.nextInt(10));    
        sRand += rand;    
      
        g.setColor(new Color(20 + random.nextInt(110), 20 + random    
        .nextInt(110), 20 + random.nextInt(110))); 
        g.drawString(rand, 13 * i + 6, 16);    
    }    
   
    session.setAttribute("yzcode", sRand);    
    g.dispose();    
    ImageIO.write(image, "JPEG", response.getOutputStream());    
    out.clear(); 
    out=pageContext.pushBody(); 
%>  
```

#### 关键代码

 session.setAttribute("yzcode", sRand);    
 调用一次就把生成的值给放进session
 后台只需获得输入的验证码然后从session取出yzcode进行比较即可