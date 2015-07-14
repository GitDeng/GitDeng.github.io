---
layout: post
title: "通过浏览器链接启动本地Activity"
date: 2015-07-10 06:39:20 +0800
comments: true
categories: [android,浏览器启动本地Activity,android_basic]
---
##1 AndroidManifest.xml
```
    <activity
        android:name=".LocalAppAty"
        android:label="@string/title_activity_local_app_aty" >
        <intent-filter>
            <!-- 设置浏览器可启动 -->
            <category android:name="android.intent.category.BROWSABLE"/>
            <!-- 设置默认启动Activity -->
            <category android:name="android.intent.category.DEFAULT"/>
            <!-- 浏览器链接被点击后，会发送一个View的action -->
            <action android:name="android.intent.action.VIEW"/>
            <!-- 设置协议为app -->
            <data android:scheme="app"/>
        </intent-filter>
    </activity>
```
##2 LocalAppAty.java
```
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_local_app_aty);
        //接收从浏览器传过来的参数，接收到的是一个Uri（android.net）对象
        Uri uri = getIntent().getData();
		//通过浏览器链接打开应用后，会在控制台输出html页面中链接的uri地址“I/System.out﹕app://hello”
        System.out.println(uri);
    }
```
##3 写一个带链接的静态页面（test.html）
```
<!doctype html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="Generator" content="EditPlus®">
        <meta name="Author" content="">
        <meta name="Keywords" content="">
        <meta name="Description" content="">
        <style>
	      a{
		      font-size:50pt;
	      }
        </style>
        <title>Document</title>
    </head>
    <body>
      <a href="app://hello">Launch My App</a>
    </body>
</html>
```