---
layout: post
title: "启动Service并传递数据"
date: 2015-07-13 02:39:20 +0800
comments: true
categories: [android,service,android_basic]
---
##示例代码
###1 MainActivity（继承Activity,实现View.OnClickListener接口）
```
package cn.loverobots.connectservice;

import android.content.Intent;
import android.support.v7.app.ActionBarActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.EditText;

public class MainActivity extends ActionBarActivity implements View.OnClickListener {

    private EditText editText;
    private Intent intent;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        editText = (EditText) findViewById(R.id.editText);
        findViewById(R.id.btnStartService).setOnClickListener(this);
        findViewById(R.id.btnStopService).setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()){
            case R.id.btnStartService:
                System.out.println("启动服务");
                intent = new Intent(this,MyService.class);
                //传递名为“etData”的字符串类型的数据
                intent.putExtra("etData", editText.getText().toString());
                startService(intent);
                break;
            case R.id.btnStopService:
                System.out.println("停止服务");
                stopService(new Intent(this,MyService.class));
                break;
        }
    }
}
```
###2 MyService（继承Service）
```
package cn.loverobots.connectservice;

import android.app.Service;
import android.content.Intent;
import android.os.Binder;
import android.os.IBinder;

public class MyService extends Service {
    private static boolean ServiceRunning = false;
    private String etData = "默认文本";

    public MyService() {
    }

    @Override
    public IBinder onBind(Intent intent) {
        return new Binder();
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        etData = intent.getStringExtra("etData");
        return super.onStartCommand(intent, flags, startId);
    }

    @Override
    public void onCreate() {
        super.onCreate();
        System.out.println("服务创建");
        ServiceRunning = true;
        new Thread(){
            @Override
            public void run() {
                super.run();
                while (ServiceRunning){
                    System.out.println(etData);
                    try {
                        sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }.start();
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        System.out.println("服务停止");
        ServiceRunning = false;
    }
}
```
###3 activity_main.xml
```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
    android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:orientation="vertical"
    tools:context=".MainActivity">
    
    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="默认文本"
        android:id="@+id/editText" />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="启动服务"
        android:id="@+id/btnStartService" />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="停止服务"
        android:id="@+id/btnStopService" />
</LinearLayout>
```