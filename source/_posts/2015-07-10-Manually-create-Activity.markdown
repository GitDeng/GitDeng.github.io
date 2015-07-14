---
layout: post
title: "手动创建Activity的过程"
date: 2015-07-10 01:39:20 +0800
comments: true
categories: [android,activity,手动创建Activity,android_basic]
---
##1 创建一个类（MyAty.java）
```
package cn.loverobots.learnintent;

import android.app.Activity;
import android.os.Bundle;

public class MyAty extends Activity {//继承Activity类
    @Override
    protected void onCreate(Bundle savedInstanceState) {//重写onCreate方法
        super.onCreate(savedInstanceState);
        setContentView(R.layout.myaty);//绑定myaty.xml视图文件
    }
}
```
##2 创建视图文件（myaty.xml）
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="这是MyAty"
        android:id="@+id/textMyAty" />

</LinearLayout>
```
###3 在AndroidManifest.xml配置文件中注册新建的Activity
```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="cn.loverobots.learnintent" >

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" > 
        <activity
            android:name=".MainActivity"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
		<!-- 注册MyAty类，其中前面的“.”符号，实际上是用来与package连接的，即MyAty类的完整地址是“cn.loverobots.learnintent.MyAty” -->
        <activity android:name=".MyAty" />
    </application>
</manifest>
```
###4 测试新建Activity是否成功
####4.1 MainActivity.java
```
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        findViewById(R.id.btnStartMyAty).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                startActivity(new Intent(MainActivity.this,MyAty.class));//显示Intent
            }
        });
    }
```
####4.2 activity_main.xml
```
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="启动MyAty"
        android:id="@+id/btnStartMyAty" />

</LinearLayout>
```