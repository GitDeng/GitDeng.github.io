---
layout: post
title: "Intent过滤器相关选项"
date: 2015-07-10 04:39:20 +0800
comments: true
categories: [android,intent,intent过滤器,android_basic]
---
*这里讨论当两个Activity的action相同的时候，如何启动相对应的Activity。
##1 AndroidManifest.xml
其中，两个Activity的action的name都是"cn.loverobots.learnintent.intent.action.MyAty"。
```
    <activity
        android:name=".MyAty"
        android:label="MyAty" >
        <intent-filter>
            <!-- 指定值为DEFAULT，则intent-filter的行为方式为Activity -->
            <category android:name="android.intent.category.DEFAULT" />
            <!-- 指定启动字符串，其中约定成俗的字符串格式为：包名.intent.action.类名 -->
            <action android:name="cn.loverobots.learnintent.intent.action.MyAty"/>
        </intent-filter>
    </activity>
    <activity
        android:name=".MyAty2"
        android:label="@string/title_activity_my_aty2" >
        <intent-filter>
            <category android:name="android.intent.category.DEFAULT"/>
            <action android:name="cn.loverobots.learnintent.intent.action.MyAty"/>
			<!-- 指定协议为app -->
            <data android:scheme="app"/>
        </intent-filter>
    </activity>
```
##2 当前应用程序的MainActivity.java
则从当前应用程序的MainActivity启动另一个action时，会出现选择界面，需要手动选择启动哪一个Activity。
```
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        findViewById(R.id.btnStartMyAty).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //这种启动方法默认可以跨应用程序启动里面的Activity
                startActivity(new Intent(MyAty.ACTION));
            }
        });
    }
```
##3 直接指定要启动的Activity
要指定默认启动哪个Activity，需要利用Intent的构造函数中的重载函数，其第二个参数采用Uri.parse("app://hello")即可，其中“app”是在AndroidManifest.xml中为Activity指定的data android:scheme="app"。
```
    findViewById(R.id.btnStartMyAty).setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            try {//由于不确定此字符串是否可调用，所以需要捕获异常
                startActivity(new Intent("cn.loverobots.learnintent.intent.action.MyAty", Uri.parse("app://hello")));
            }catch(Exception e){
                Toast.makeText(MainActivity.this,"无法启动指定的Activity",Toast.LENGTH_SHORT).show();
            }
        }
    });
```

