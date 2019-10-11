---
title: 接口返回json结果带html过滤工具类
date: 2019-10-11 23:10:08
tags:
- 工具类
categories:
- 工具类
---

在请求某接口，接收其返回结果，这个结果放在json里，你取你想要的内容，如果是html内容的话很多东西传过来其实已经给转义了。这时候就需要过滤一下了。

```
package com.vshow.control.tool;

import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class HtmlUtil {
	private static final String regEx_script = "<script[^>]*?>[\\s\\S]*?<\\/script>"; // 定义script的正则表达式  
    private static final String regEx_style = "<style[^>]*?>[\\s\\S]*?<\\/style>"; // 定义style的正则表达式  
    private static final String regEx_html = "<[^>]+>"; // 定义HTML标签的正则表达式  
    private static final String regEx_space = "\\s*|\t|\r|\n";//定义空格回车换行符  
      
    /** 
     * @param htmlStr 
     * @return 
     *  删除Html标签 
     */  
    public static String delHTMLTag(String htmlStr) {  
    	
    	htmlStr=htmlStr.replaceAll("<br>", "\n");
    	htmlStr=htmlStr.replaceAll("<BR>", "\n");
        Pattern p_script = Pattern.compile(regEx_script, Pattern.CASE_INSENSITIVE);  
        Matcher m_script = p_script.matcher(htmlStr);  
        htmlStr = m_script.replaceAll(""); // 过滤script标签  
  
        Pattern p_style = Pattern.compile(regEx_style, Pattern.CASE_INSENSITIVE);  
        Matcher m_style = p_style.matcher(htmlStr);  
        htmlStr = m_style.replaceAll(""); // 过滤style标签  
  
        Pattern p_html = Pattern.compile(regEx_html, Pattern.CASE_INSENSITIVE);  
        Matcher m_html = p_html.matcher(htmlStr);  
        htmlStr = m_html.replaceAll(""); // 过滤html标签  
  
        //Pattern p_space = Pattern.compile(regEx_space, Pattern.CASE_INSENSITIVE);  
        //Matcher m_space = p_space.matcher(htmlStr);  
        //htmlStr = m_space.replaceAll(""); // 过滤空格回车标签  
        return htmlStr; //返回文本字符串  
    }  
      
    public static String getTextFromHtml(String htmlStr){  
        htmlStr = delHTMLTag(htmlStr);  
        htmlStr = htmlStr.replaceAll(" ", "");  
        htmlStr = htmlStr.substring(0, htmlStr.indexOf("。")+1);  
        return htmlStr;  
    }  
}

```