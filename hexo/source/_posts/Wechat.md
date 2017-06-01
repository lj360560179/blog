---
title: 微信登录工具
layout: post
comments: true
tag: java
category: java
date: 2016.4.25 15:44:14
---
之前做微信登录的时候，看文档写的获取信息的工具类。微信登录就是传过去信息，然后返回信息，然后再根据返回的信息再传过去信息，然后再返回······，最后得到用户信息。
<!-- more -->
### 相关代码
```java
    /**
     * 通过code获取access_token
     *@param appId
     *         appId 
     * @param appSecret
     *         appSecret 
     * @param  code
     *           code
     * 
     */ 
    @SuppressWarnings("unchecked")
    public static Map<String, Object> GetWexinAccessToken(String  appid,String secret,String code){
        
        Map<String, Object> parameterMap = new HashMap<String, Object>();
        parameterMap.put("appid", appid);
        parameterMap.put("secret", secret);
        parameterMap.put("code", code);
        parameterMap.put("grant_type", "authorization_code");
        String url="https://api.weixin.qq.com/sns/oauth2/access_token";
        //发送请求并返回数据
        String result = HttpRequest.get(url,parameterMap);
        
        if (result.contains("errcode")){
            return null;    
        }else{  
            Map<String, Object> model = JsonUtils.toObject(result, Map.class);
            return model;
        }
    }   
    
    
    
    /**
     * 根据access_token判断access_token是否过期
     *@param access_token
     *         access_token
     * @param openid
     *         Opened 
     * 
     */
    @SuppressWarnings("unchecked")
    public static boolean CheckAccessToken(String access_token,String openid){
        
        
        Map<String, Object> parameterMap = new HashMap<String, Object>();
        parameterMap.put("access_token", access_token);
        parameterMap.put("openid", openid);

        String url = "https://api.weixin.qq.com/sns/auth";
        
        String result = HttpRequest.get(url,parameterMap);

        Map<String, Object>  model = JsonUtils.toObject(result, Map.class);
            
        if(model.containsKey("errmsg")){
                
            if (model.get("errmsg").equals("ok")){
                    return true;
                }else {
                    return false;
                }
        }else{
            return false;
        }

        
    }
    
    /**
     * 刷新或续期access_token使用
     *@param access_token
     *         access_token
     * @param openid
     *         Opened 
     * 
     */
    @SuppressWarnings("unchecked")
    public static Map<String, Object>  GetRefreshToken(String refresh_token,String  appid){
       
        Map<String, Object> parameterMap = new HashMap<String, Object>();
        parameterMap.put("refresh_token", refresh_token);
        parameterMap.put("appid", appid);
        parameterMap.put("grant_type", "refresh_token"); 
        String url = "https://api.weixin.qq.com/sns/oauth2/refresh_token";
       
        String result = HttpRequest.get(url,parameterMap);
        if (result.contains("errmsg")){
            return null;
        }else {
            Map<String, Object>  model = JsonUtils.toObject(result, Map.class);
            return model;   
        }
   }
   
    /**
     *获取登录信息
     *@param access_token
     *         access_token
     * @param openid
     *         Opened 
     * 
     */
    @SuppressWarnings("unchecked")
    public static Map<String, Object>  GetUserInfo(String access_token, String  openid){
        
        Map<String, Object> parameterMap = new HashMap<String, Object>();
        parameterMap.put("access_token", access_token);
        parameterMap.put("openid", openid);
        
        String url="https://api.weixin.qq.com/sns/userinfo";
        String result = HttpRequest.get(url,parameterMap);
        if (result.contains("errmsg")){
           return null;
        }
        Map<String, Object> model = JsonUtils.toObject(result, Map.class);
        return model;
   }
```
