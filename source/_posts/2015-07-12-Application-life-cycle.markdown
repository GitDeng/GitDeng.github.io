---
layout: post
title: "Application的生命周期"
date: 2015-07-12 02:39:20 +0800
comments: true
categories: [android,application,生命周期,android_basic]
---
##简  介
当一个应用程序中有多个Activity时，不管先启动哪个Activity，Application里的onCreate函数都会先执行，这样就可以利用Application进行初始化操作。
##代  码
###1 App(继承Application)
```
package cn.loverobots.learncontext;

import android.app.Application;
import android.content.res.Configuration;

public class App extends Application {

    private String textData = "default";
    public void setTextData(String textData) {
        this.textData = textData;
    }
    public String getTextData() {
        return textData;
    }

    //Application中生命周期相关的函数
    //在创建程序时执行
    @Override
    public void onCreate() {
        super.onCreate();
        System.out.println("App onCreate");//当Application中onCreate执行时，会在控制台输出
    }
    //程序结束时执行
    @Override
    public void onTerminate() {
        super.onTerminate();
    }
    //在低内存时执行
    @Override
    public void onLowMemory() {
        super.onLowMemory();
    }
    //程序在进行内存清理时执行
    @Override
    public void onTrimMemory(int level) {
        super.onTrimMemory(level);
    }
    //程序配置发生改变时执行
    @Override
    public void onConfigurationChanged(Configuration newConfig) {
        super.onConfigurationChanged(newConfig);
    }
}
```
###2 Main1(Activity)
```
package cn.loverobots.learncontext;

import android.support.v7.app.ActionBarActivity;
import android.os.Bundle;
import android.view.Menu;
import android.view.MenuItem;

public class Main1 extends ActionBarActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        System.out.println("Main1 onCreate");//当Main1的onCreate函数执行时会在控制台输出
}
```
###3 Main2(Activity)
```
package cn.loverobots.learncontext;

import android.support.v7.app.ActionBarActivity;
import android.os.Bundle;
import android.view.Menu;
import android.view.MenuItem;

public class Main2 extends ActionBarActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        System.out.println("Main2 onCreate");
}
```