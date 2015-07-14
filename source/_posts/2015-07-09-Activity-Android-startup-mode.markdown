---
layout: post
title: "Android中Activity启动模式"
date: 2015-07-09 12:39:20 +0800
comments: true
categories: [android,activity,activity启动模式,android_basic]
---
#Activity共分为四种启动模式
##启动模式可以在AndroidManifest.xml中进行配置
```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="cn.loverobots.launchmode" >
    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
 		<!-- launchMode分为四种模式：standard,singleTop,singleTask,singleInstance -->
        <activity
            android:name=".MainActivity"
            android:label="@string/app_name"
            android:launchMode="singleInstance" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name=".BAty"
            android:label="@string/title_activity_baty"
            android:launchMode="singleInstance" >
        </activity>
    </application>
</manifest>
```
*同一个文件中的ID不能一样，不同文件之间的ID可以一样
###1 Standard模式  
即标准启动模式，每次启动当前Activity，都会创建新的实例。
###2 SingleTop模式
如果当前Activity处于栈顶不会创建实例，如果当前Activity不处于栈顶，则会创建新的实例。
###3 SingleTask模式
SingleTask启动模式下，只会创建一个当前Activity的实例A。当从此Activity实例打开另一个Activity实例B，并在B中打开最开始的Activity时，不会创建新实例，而会回到最开始的第一个A实例，再次按后退按钮则会直接退出当前程序。
###4 SingleInstance模式
SingleInstance模式下，是独立的一个栈，即一个栈中只会存在一个Activity。

##示 例
###1 MainActivity.java
```
    private TextView tv;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        tv = (TextView) findViewById(R.id.tv);
        tv.setText(String.format("TaskID:%d\nCurrent Activity ID:%s",getTaskId(),toString()));

        findViewById(R.id.btnMainActivity).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                startActivity(new Intent(MainActivity.this, MainActivity.class));
            }
        });

        findViewById(R.id.btnBAty).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                startActivity(new Intent(MainActivity.this,BAty.class));
            }
        });
    }
```
###1 activity_main.xml
```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
    android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/tv"
        android:text="@string/hello_world"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

    <Button
        android:textAllCaps="false"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="启动MainActivity"
        android:id="@+id/btnMainActivity" />

    <Button
        android:textAllCaps="false"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="启动BAty"
        android:id="@+id/btnBAty" />

</LinearLayout>
```
###2 BAty.java
```
    private TextView tv;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_baty);

        tv = (TextView) findViewById(R.id.tv);
        tv.setText(String.format("TaskID:%d\nCurrent Activity ID:%s",getTaskId(),toString()));

        findViewById(R.id.btnMainActivity).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                startActivity(new Intent(BAty.this, MainActivity.class));
            }
        });

        findViewById(R.id.btnBAty).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                startActivity(new Intent(BAty.this, BAty.class));
            }
        });
    }
```
###2 activity_baty.xml
```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
    android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:orientation="vertical"
    tools:context="cn.loverobots.launchmode.BAty">

    <TextView
        android:id="@+id/tv"
        android:text="@string/hello_world"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

    <Button
        android:textAllCaps="false"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="启动MainActivity"
        android:id="@+id/btnMainActivity" />

    <Button
        android:textAllCaps="false"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="启动BAty"
        android:id="@+id/btnBAty" />

</LinearLayout>
```