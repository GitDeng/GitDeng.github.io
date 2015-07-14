---
layout: post
title: "显示Intent与隐式Intent"
date: 2015-07-10 02:39:20 +0800
comments: true
categories: [android,activity,intent,显示Intent,隐式Intent,android_basic]
---
##显示Intent
即采用指定类的方式。
```
    findViewById(R.id.btnStartMyAty).setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            startActivity(new Intent(MainActivity.this,MyAty.class));
        }
    });
```
##隐式Intent
即采用指定字符串调用的方式。
###1 首先要在AndroidManifest.xml文件中指定对应字符串
```
    <!-- exported的值默认为true，其它应用程序是可以直接访问的，指定为false，反之 -->
    <activity android:name=".MyAty" android:exported="false">
        <intent-filter>
            <!-- 指定值为DEFAULT，则intent-filter的行为方式为Activity -->
            <category android:name="android.intent.category.DEFAULT"/>
            <!-- 指定启动字符串，其中约定成俗的字符串格式为：包名.intent.action.类名 -->
            <action android:name="cn.loverobots.learnintent.intent.action.MyAty"/>
        </intent-filter>
    </activity>
```
###2 为了避免反复输入冗长的字符串，一般在被调用类中创建一个静态常量保存字符串
```
public class MyAty extends Activity {
    //在类中创建静态常量ACTION，并赋值为启动字符串，方便其它Activity调用
    public static final String ACTION = "cn.loverobots.learnintent.intent.action.MyAty";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.myaty);//绑定myaty.xml视图文件
    }
}
```
###3 隐式Intent方式（程序LearnIntent中MainActivity.java调用）
```
    findViewById(R.id.btnStartMyAty).setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            //这种启动方法默认可以跨应用程序启动里面的Activity
            startActivity(new Intent(MyAty.ACTION));
        }
    });
```
###4 由于禁止跨程序调用（android:exported="false"），则需要在想要调用的程序中（另一个程序app1中MainActivity.java）捕获异常。
```
    findViewById(R.id.btnStartMyAty).setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            try {//由于不确定此字符串是否可调用，所以需要捕获异常
                startActivity(new Intent("cn.loverobots.learnintent.intent.action.MyAty"));
            }catch(Exception e){
                Toast.makeText(MainActivity.this,"无法启动指定的Activity",Toast.LENGTH_SHORT).show();
            }
        }
    });
```

