---
title: 根据ip和时间戳生成唯一字符串
date: 2019-10-07 03:45:14
tags:
- 工具类
categories:
- 工具类
---

```
package tool;
import java.text.SimpleDateFormat;
import java.util.Date ;
import java.util.Random ;

/**
 * 按照日期时间戳生成随机字符串
 */
public class IPTimeStamp {

    private static SimpleDateFormat sdf = null ;
    public IPTimeStamp(){}



    /**
     * 按照日期时间戳拼凑出一个随机字符串
     * @return
     */
    public static String getIPTimeRand(String ip){
        StringBuffer buf = new StringBuffer() ;
        if(ip != null){
            String s[] = ip.split("\\.") ;
            for(int i=0;i<s.length;i++){
                buf.append(addZero(s[i],3)) ;
            }
        }
        buf.append(getTimeStamp()) ;
        Random r = new Random() ;
        for(int i=0;i<3;i++){
            buf.append(r.nextInt(10)) ;
        }
        return buf.toString() ;
    }

    /**
     * 按照指定的格式取得日期
     * @return
     */
    public String getDate(){
        this.sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS") ;
        return this.sdf.format(new Date()) ;
    }

    /**
     * 得到相应格式的日期
     * @return
     */
    public  static  String getTimeStamp(){
        sdf = new SimpleDateFormat("yyyyMMddHHmmssSSS") ;
        return sdf.format(new Date()) ;
    }

    /**
     * 给传入的字符串，按照指定的长度进行添0操作
     * @param str
     * @param len
     * @return
     */
    private static String addZero(String str,int len){
        StringBuffer s = new StringBuffer() ;
        s.append(str) ;
        while(s.length() < len){
            s.insert(0,"0") ;
        }
        //System.out.println(s.toString());
        return s.toString() ;
    }

    //测试结果
    public static void main(String args[]){
        System.out.println(IPTimeStamp.getIPTimeRand("192.168.1.1")) ;
    }
}

```

