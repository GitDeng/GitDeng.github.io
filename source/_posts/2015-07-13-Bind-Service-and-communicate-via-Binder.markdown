---
layout: post
title: "绑定Service通信（Binder方式）"
date: 2015-07-13 04:39:20 +0800
comments: true
categories: [android,service,binder,android_basic]
---
绑定Service时，通过Binder方式进行数据传递，比通过启动Service的方式更方便也更高效。
##示例代码
###1 MainActivity（继承Activity，实现View.OnClickListener, ServiceConnection接口）
```
package cn.loverobots.connectservice;

import android.content.ComponentName;
import android.content.Context;
import android.content.Intent;
import android.content.ServiceConnection;
import android.os.IBinder;
import android.support.v7.app.ActionBarActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.EditText;

public class MainActivity extends ActionBarActivity implements View.OnClickListener, ServiceConnection {

    private EditText editText;
    private Intent intent;
    private MyService.Binder binder = null;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        editText = (EditText) findViewById(R.id.editText);
        findViewById(R.id.btnStartService).setOnClickListener(this);
        findViewById(R.id.btnStopService).setOnClickListener(this);
        findViewById(R.id.btnBindService).setOnClickListener(this);
        findViewById(R.id.btnUnbindService).setOnClickListener(this);
        findViewById(R.id.btnSyncData).setOnClickListener(this);
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
            case R.id.btnBindService:
                bindService(new Intent(this,MyService.class),this, Context.BIND_AUTO_CREATE);
                break;
            case R.id.btnUnbindService:
                unbindService(this);
                break;
            case R.id.btnSyncData:
                if(binder!=null){
                    binder.setData(editText.getText().toString());
                }
                break;
        }
    }
	
    @Override
    public void onServiceConnected(ComponentName name, IBinder service) {
        //接收来自MyService的Binder中的数据
		binder = (MyService.Binder) service;
    }

    @Override
    public void onServiceDisconnected(ComponentName name) {

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
    //创建一个Binder对象继承系统中的Binder类，并创建一个传递数据的方法setData
    public class Binder extends android.os.Binder{
        public void setData(String etData){
            MyService.this.etData = etData;
        }
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

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="绑定服务"
        android:id="@+id/btnBindService" />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="解除绑定服务"
        android:id="@+id/btnUnbindService" />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="同步数据"
        android:id="@+id/btnSyncData" />
</LinearLayout>
```