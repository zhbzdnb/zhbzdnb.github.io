---
title: 百度翻译开放平台api调用
date: 2019-10-11 23:08:41
tags:
- 接口相关
categories:
- 接口相关
---

首先是文档地址

http://api.fanyi.baidu.com/api/trans/product/apidoc#joinFile

然后配合md5加密类集成



	package com.vshow.control.tool.translate;
	
	import java.util.HashMap;
	import java.util.Map;
	
	import net.sf.json.JSONObject;
	
	public class TransApi {
		private static final String TRANS_API_HOST = "http://api.fanyi.baidu.com/api/trans/vip/translate";
	private String appid;
	private String securityKey;
	
	private static final String APP_ID = "";//appid
	
	private static final String SECURITY_KEY = "";//分配的密匙
	
	public TransApi(String appid, String securityKey) {
		this.appid = appid;
		this.securityKey = securityKey;
	}
		/**
		 * @param query 请求翻译的语言
		 * @param from  请求翻译的语言的语种
		 * @param to    翻译结果的语种
		 * @return
		 */
	public String getTransResult(String query, String from, String to) {
		Map<String, String> params = buildParams(query, from, to);
		return HttpGet.get(TRANS_API_HOST, params);
	}
	
	private Map<String, String> buildParams(String query, String from, String to) {
		Map<String, String> params = new HashMap<String, String>();
		params.put("q", query);
		params.put("from", from);
		params.put("to", to);
	
		params.put("appid", appid);
	
		// 随机数
		String salt = String.valueOf(System.currentTimeMillis());
		params.put("salt", salt);
	
		// 签名
		String src = appid + query + salt + securityKey; // 加密前的原文
		params.put("sign", MD5.md5(src));
	
		return params;
	}
	
	public static String getTransResultEn(String query) {
	
		TransApi api = new TransApi(APP_ID, SECURITY_KEY);
	
		String result = api.getTransResult(query, "auto", "en");
	
		JSONObject jsonObject = JSONObject.fromObject(result);
	
		try {
			String resultStr =JSONObject.fromObject(jsonObject.getJSONArray("trans_result").get(0)).get("dst")
			+ "";
			if(resultStr.equals("Yin")){
				resultStr="Cloudy";
			}else if(resultStr.equals("yin")){
				resultStr="Cloudy";
			}
			return resultStr;
		} catch (Exception e) {
			// TODO: handle exception
			return "";
		}
	
	}

}