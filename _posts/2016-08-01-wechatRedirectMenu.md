---
layout: post
title: "Wechat公众号重定向菜单配置"
date: 2016-08-01 
description: "How to config the redirect menu of Wechat"
tag: Wechat
---   

## 微信公众号开发架构

微信(Wechat) < == > 我们开发的服务(Provider) < == > HTML5

微信公众号的本质是：
微信提供第三方服务网页的接入入口和呈现容器（定制的Webview）。

## 点击菜单，实现用户信息的获取及页面重定向

整个过程使用OAuth2.0授权协议实现。
具体实现步骤如下：

### 微信公众号菜单配置 

通过自定义菜档创建接口，自定义菜单，菜单类型选择view。

*view类型菜单有重要一点需要我们利用：*
  《 view：跳转URL用户点击view类型按钮后，微信客户端将会打开开发者在按钮中填写的网页URL，可与网页授权获取用户基本信息接口结合，获得用户基本信息。》

  *url参数如下：*

  https://open.weixin.qq.com/connect/oauth2/authorize?appid=APPID&redirect_uri=REDIRECT_URI&response_type=code&scope=SCOPE&state=STATE#wechat_redirect 若提示“该链接无法访问”，请检查参数是否填写错误，是否拥有scope参数对应的授权作用域权限。

  *参考链接(请在微信客户端中打开此链接体验):*

  scope为snsapi_base

  https://open.weixin.qq.com/connect/oauth2/authorize?appid=wx520c15f417810387&redirect_uri=https%3A%2F%2Fchong.qq.com%2Fphp%2Findex.php%3Fd%3D%26c%3DwxAdapter%26m%3DmobileDeal%26showwxpaytitle%3D1%26vb2ctag%3D4_2030_5_1194_60&response_type=code&scope=snsapi_base&state=123#wechat_redirect

  scope为snsapi_userinfo
  https://open.weixin.qq.com/connect/oauth2/authorize?appid=wxf0e81c3bee622d60&redirect_uri=http%3A%2F%2Fnba.bluewebgame.com%2Foauth_response.php&response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect

### 后台服务开发实现用户身份识别和网页重定向

定制号微信公众号菜单后，用户点击菜单，实现用户身份判断，及根据用户身份实现网页的重定向。实现步骤：

-  第一步：用户同意授权，获取code
   用户点击菜单后，微信会生成code，该code会传递到菜单url配置的重定向地址（也就是我们自己开发的服务地址）。

- 第二步：通过code换取用户的openid  
   我们开发的服务接收到code后跑一个接口获取用户的openid：
   https://api.weixin.qq.com/sns/oauth2/access_token?appid=APPID&secret=SECRET&code=CODE&grant_type=authorization_code

- 第三步：获取到用户的openid后，再在我们自己开发的服务中重定向到想要的页面。
