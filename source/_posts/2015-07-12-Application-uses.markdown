---
layout: post
title: "Application的用途"
date: 2015-07-12 01:39:20 +0800
comments: true
categories: [android,application,context,android_basic]
---
##简  介
###1 Context的作用
访问全局信息的接口，例如访问文字、图片资源等。

###2 Application的用途
Application用于多个组件之间进行数据的共享，对于同一个应用来说是唯一的。

##代  码
###1 App(继承Application)
```
package cn.loverobots.learncontext;
import android.app.Application;
public class App extends Application {
    private String textData = "default";
    public void setTextData(String textData) {
        this.textData = textData;
    }
    public String getTextData() {
        return textData;
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
import android.view.View;
import android.widget.EditText;
import android.widget.ImageView;
import android.widget.TextView;

public class Main1 extends ActionBarActivity {
    private TextView textView;
    private EditText editText;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main1);//绑定视图main1
//        textView = new TextView(this);
////        textView.setText("Hello Android!");
//        textView.setText(R.string.hello_world);
//        setContentView(textView);
//
//        System.out.println(getResources().getText(R.string.hello_world));
//        ImageView iv = new ImageView(this);
//        iv.setImageResource(R.mipmap.ic_launcher);
//        setContentView(iv);

        textView = (TextView) findViewById(R.id.textView);
        editText = (EditText) findViewById(R.id.editText);

        textView.setText("共享的数据是："+getApp().getTextData());

        findViewById(R.id.btnSaveData).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //获取Application里的信息并保存到全局Context里面
                ((App)getApplicationContext()).setTextData(editText.getText().toString());
                //设置显示文本对象显示的文本
                textView.setText("共享的数据是："+editText.getText().toString());
            }
        });

    }
    /**
     * 获取Application中默认的数据
     * @return
     */
    public App getApp(){
        return (App)getApplicationContext();
    }
}
```
###2 Main2.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="New Text"
        android:id="@+id/textView" />

    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editText" />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="保存"
        android:id="@+id/btnSaveData" />
</LinearLayout>
```

###3 Main2(Activity)
```
package cn.loverobots.learncontext;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.EditText;
import android.widget.TextView;

public class Main2 extends Activity {
    private TextView textView;
    private EditText editText;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main2);//绑定视图main2

        textView = (TextView) findViewById(R.id.textView);
        editText = (EditText) findViewById(R.id.editText);

        textView.setText("共享的数据是："+getApp().getTextData());

        findViewById(R.id.btnSaveData).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                ((App)getApplicationContext()).setTextData(editText.getText().toString());
                textView.setText("共享的数据是："+editText.getText().toString());
            }
        });
    }
    public App getApp(){
        return (App)getApplicationContext();
    }
}
```
###3 main2.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="New Text"
        android:id="@+id/textView" />

    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editText" />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="保存"
        android:id="@+id/btnSaveData" />
</LinearLayout>
```