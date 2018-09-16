---
layout: post
title: "Weex 开发常见问题"
date: 2018-02-25 
description: "The FAQ of weex development"
tag: WEEX
---   

## 安装Weex失败的问题：

一般归结为node.js版本的问题
可以安装下node.js的版本控制工具
https://github.com/creationix/nvm
这样的话，可以来回切换不同版本的node.js。

## Weex压缩打包

    # 1.可以直接对着整个目录src目录下面的所有前端文件打包
    weex compile -m src destinationJSPath

    # 2.打包整个src目录下面的文件耗时太长，也可以选择单个打（比如你只改了单个vue，可以就打包这个vue）
    weex compile -m theVueYouWantCompile.vue destinationJS.js

## Weex前端代码的调试

使用Chrome调试

建议开发的时候在js里面多写console.log("XXXX日志XXX")
在Xcode或Android Studio的输出终端中跟踪这些日志
方便查找问题

## npm install 问题：
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!! WARNING: You are on OS X 10.11 El Capitan or greater, you may need to add the
!!!! WARNING:   `--unsafe-perm=true` flag when running `npm install`

可以多加个flag：

    npm install --unsafe-perm=true

## weex插件开发
https://github.com/weexteam/weex-pack/blob/master/doc/plugin-devloping-weexpack.md
https://www.darkhandz.com/2017/11/17/weex插件开发（iOS）/

http://weex.apache.org/guide/create-a-plugin.html  *


## npm install出现"Unexpected end of JSON input while parsing near"错误解决方法

见：https://blog.csdn.net/ITLionWoo/article/details/78632643

    npm cache clean --force

## Android - Error:(30, 0) Could not set unknown property 'outputFileName' 

Error:(30, 0) Could not set unknown property 'outputFileName' for object of type com.android.build.gradle.internal.api.ApplicationVariantImpl. <a href="openFile:D:\qscwork\weexwallet\platforms\android\app\build.gradle">Open File</a>

解决方法：

这个时候需要在Android Studio中把app目录下的build.gradle文件中的variant.outputs.each { output ->
    def outputFile = output.outputFile
    if (outputFile != null && outputFile.name.equals('app-debug.apk')) {
        def fileName = outputFile.name.replace("app-debug.apk", "weex-app.apk")
        output.outputFile = new File(outputFile.parent, fileName)
    }
}改成variant.outputs.all { output ->
    def outputFile = output.outputFile
    if (outputFile != null && outputFile.name.equals('app-debug.apk')) {
        def fileName = outputFile.name.replace("app-debug.apk", "weex-app.apk")
        outputFileName = fileName
    }
}


## Android Error:Execution failed for task ':app:javaPreCompileDebug'

Error:Execution failed for task ':app:javaPreCompileDebug'.Annotation processors must be explicitly declared now.  The following dependencies on the compile classpath are found to contain annotation processor.  Please add them to the annotationProcessor configuration.
- weexplugin-processor-1.3.jar (com.taobao.android:weexplugin-processor:1.3)
Alternatively, set android.defaultConfig.javaCompileOptions.annotationProcessorOptions.includeCompileClasspath = true to continue with previous behavior.  Note that this option is deprecated and will be removed in the future.
See https://developer.android.com/r/tools/annotation-processor-error-message.html for more details.

解决方法：

在Android Studio的app目录下的build.gradle文件中，在defaultConfig下加入：

javaCompileOptions { 
    annotationProcessorOptions { 
        includeCompileClasspath = true
    } 
}

## Android Navigator 推入页面不成功

解决方法：
1. 修改AndroidManifest

    <activity
            android:name="com.weex.app.WXPageActivity"
            android:label="@string/app_name" android:exported="false"
            android:screenOrientation="portrait">
            <intent-filter>
                <action android:name="com.weex.app.WXPageActivity" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="com.taobao.android.intent.category.WEEX" />
                <action android:name="android.intent.action.VIEW" />
                <data android:scheme="http" />
                <data android:scheme="https" />
                <data android:scheme="file" />
                <data android:scheme="wxpage" />
            </intent-filter>
    </activity>

2. 修改原生代码 WXPageActivity   

3. build.gradle修改 targetSdkVersion:22     



## Weex工程创建，开发：

    weex create awesome-app

    cd awesome-app
    npm install

    npm install出现"Unexpected end of JSON input while parsing near"错误,解决方法：
    npm cache clean --force

    #开发：
    The source code is located in src/ folder. You can develop it as normal Vue.js project.

    #在浏览器上跑下看看
    npm start   

    weex platform add ios
    weex platform add android

    weex run ios
    #直接跑不起来（或报错），解决方法：
    cd ios 
    pod install
    open XXX.workspace

    weex run android
    #直接跑不起来，解决方法：
    使用android studio打开android工程

## 拓展(component, modules, handlers)
Weex SDK provides only rendering capabilities, rather than have other capabilities. There are some internal components, modules and handlers. If you want these features which weexSDK doesn’t provide, you can to extend.

iOS拓展简单的教程：
http://weex.apache.org/guide/extend-ios.html

android拓展简单的教程：
http://weex.apache.org/guide/extend-android.html

我们要达到的目的是：
iOS Android商量好，暴露给javascript相同的接口调用。

## JS调用Native可以，使用封装Module来实现。 Native通知JS，则可以通过globalEvent来实现。

    //globalEvent的使用,[具体说明见](http://weex.apache.org/references/modules/globalevent.html)
    //--1,在原生里面发起通知,如IOS OC：使用WXSDKInstance的 - (void)fireGlobalEvent:(NSString *)eventName params:(NSDictionary *)params；
    //在类WXDemoViewController中在viewDidLoad调用[self render]后：
    [_instance fireGlobalEvent:@"zjcglobalEvent" params:@{@"key":@"zhangjiacheng"}];

    //--2,在JS里面注册接收通知。
    var globalEvent = weex.requireModule('globalEvent');
    globalEvent.addEventListener("zjcglobalEvent", function (e) {
        console.log("get zjcglobalEvent");
        modal.toast({ message: "zjcglobalEvent value="+e.key});
    });

## 页面间传参
    //当我们用navigator push页面的时候想传递参数到下一个页面  
    //有两种传递参数的方式
    //1, 使用storage，这种方式很简单，也很粗暴。
    //2, 使用url加问号的方式，比如：

    //Page A -> Page B

    //Page A 中：
    navigator.push({
              url: bundleUrl +  "factorymanager/FMTabs.js?zjckey="+encodeURIComponent("张家成zhangjiacheng"),
              animated: "true"}, event => {} );

    //Page B中(其中GetParamValue为我们定义的一个工具方法)：
    GetParamValue(weex.config.bundleUrl,"zjckey");

    //其中工具方法GetParamValue：
    export let  GetParamValue =  function(url, param) {
      param = param.replace(/[\[\]]/g, "\\$&");
      var regex = new RegExp("[?&]" + param + "(=([^&#]*)|&|#|$)"),
            results = regex.exec(url);
      if (!results) return null;
      if (!results[2]) return '';
      return decodeURIComponent(results[2].replace(/\+/g, " "));
    }

## weex使用字体图片的引用问题，解决见：
http://www.iconfont.cn/help/detail?spm=a313x.7781069.1998910419.15&helptype=code
https://segmentfault.com/a/1190000011852209  

## VS code格式化VUE代码，快捷方式：

https://blog.csdn.net/tangletao/article/details/78799459

文章有个地方对不上
文件->首选项->设置  
Code->首选项->设置     

## Vue 

    //-------- 环境 -----------

    #Vue-cli安装
    npm install -g vue-cli

    #Vue工程创建 template有webpack,browserify等
    vue init [template] [project-name]

    # Enter the project rort folder
    cd [project-name]

    # Run install command
    npm install

    # Run the Vue app
    npm run dev

 ## Weex插件的开发
 1. 网上有一些开源的插件供使用
    http://natjs.com/#/README 
 2. 自己开发可以参考开源的插件。写好原生代码部分后，前端部分写好JS调用的定义。     


    //----------- 开发 -----------
http://192.168.1.209:7093/tree/weextraing.git/master/docs;jsessionid=1fi3ccugb5c5oji5kn5fqxlnc
    //------ 路由介绍文章
    //https://scotch.io/tutorials/getting-started-with-vue-router

    //------ <router-view> 
    /*
    These are entry points where Vue can mount the configured routes. A route outlet is simply a component

    The important piece though is the <router-view> tag. The router uses this tag as a container for rendering the different components tied to the different routes. Just think of it as a placeholder.
    */
    
    //------ instance of Vue
    /*
    Your app needs to instantiate Vue before anything can go on.

    The data function returns an object. Any property in the object can be bound to the view and also accessible via this. Other functions that are available in the Vue instance object like data can access the data properties using this.
    */

    //------- Vue && Component
    /*
    Since components are reusable Vue instances, they accept the same options as new Vue, such as data, computed, watch, methods, and lifecycle hooks. The only exceptions are a few root-specific options like el.
    */

    //------- Component
    /*
    It’s common for an app to be organized into a tree of nested components:
    for example, you might have components for a header, sidebar, and content area, each typically containing other components for navigation links, blog posts, etc.

    Passing Data to Child Components with Props
    */

  ## git .gitignore的使用

  工程目录中不需要做版本控制的文件或目录全部声明在根目录下面的.gitignore中。
  [git官方解决办法](https://help.github.com/articles/ignoring-files/)
  如果有文件已经被git跟踪，后面即使在.gitignore中声明不要再去跟踪，也不能立即起作用。
  还需要手动跑句命令告知git不要再跟踪这些文件或目录。

      # 实践证明这样做有问题，导致其他成员代码不能用。

      # 不再跟踪某个文件
      git rm --cached 不要再跟踪的文件

      # 不再跟踪某个目录
      git rm --cached -r 不再跟踪的目录

## iOS CocoaPods 找不到包，报连不上github问题：

见
https://stackoverflow.com/questions/38993527/cocoapods-failed-to-connect-to-github-to-update-the-cocoapods-specs-specs-repo

## iOS_重新安装cocoapods:

    gem install cocoapods -n /usr/local/bin

## __（MAC系统）__ANDROID_HOME的配置：

    vim  ~/.bash_profile

    ANDROID_HOME="你SDK的目录地址，比如/Users/Android/sdk"
    export ANDROID_HOME
    export PATH=$PATH:$ANDROID_HOME/tools
    export PATH=$PATH:$ANDROID_HOME/platform-tools

## iOS_重置repository

    pod repo remove master
    pod setup

## iOS Pod install报错：
Installing WeexSDK (0.18.0)

[!] Error installing WeexSDK
[!] /usr/bin/git clone https://github.com/apache/incubator-weex.git /var/folders/3w/4d1zf5z52_nfds5hx23c2wg40000gn/T/d20180503-12392-7g4f5x --template= --single-branch --depth 1 --branch 0.18.0

Cloning into '/var/folders/3w/4d1zf5z52_nfds5hx23c2wg40000gn/T/d20180503-12392-7g4f5x'...
error: RPC failed; curl 18 transfer closed with outstanding read data remaining
fatal: The remote end hung up unexpectedly
fatal: early EOF
fatal: index-pack failed

解决办法：
https://stackoverflow.com/questions/38618885/error-rpc-failed-curl-transfer-closed-with-outstanding-read-data-remaining
