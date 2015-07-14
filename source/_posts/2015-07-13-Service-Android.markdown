---
layout: post
title: "认识Android Service"
date: 2015-07-13 01:39:20 +0800
comments: true
categories: [android,service,android_basic]
---
##1 使用Service
###使用场景不需要有可以与用户交互的界面，只需要在后台一直运行，做一些事务的处理，比如说Socket超链接、Http的网络通信以及与服务器保持推送的一个链接。  
**注意**：Service实例在一个应用程序中只能有一个。

##2 绑定Service
启动服务也可以通过绑定服务的方式启动。

##3 使用与绑定Service示例代码
###3.1 MainActivity（继承Activity，实现View.OnClickListener, ServiceConnection）
```
package cn.loverobots.learnservice;

import android.content.ComponentName;
import android.content.Context;
import android.content.Intent;
import android.content.ServiceConnection;
import android.os.IBinder;
import android.support.v7.app.ActionBarActivity;
import android.os.Bundle;
import android.view.View;

public class MainActivity extends ActionBarActivity implements View.OnClickListener, ServiceConnection {

    private Intent intent;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        intent = new Intent(MainActivity.this,MyService.class);
        //启动Service
        findViewById(R.id.btnStartService).setOnClickListener(this);
        //停止Service
        findViewById(R.id.btnStopService).setOnClickListener(this);
        //绑定Service
        findViewById(R.id.btnBindService).setOnClickListener(this);
        //解除绑定Service
        findViewById(R.id.btnUnbindService).setOnClickListener(this);
    }
    //实现OnClickListener接口需要重写的方法
    @Override
    public void onClick(View v) {
        switch (v.getId()){
            case R.id.btnStartService:
                startService(intent);
                break;
            case R.id.btnStopService:
                stopService(intent);
                break;
            case R.id.btnBindService:
                bindService(intent,this, Context.BIND_AUTO_CREATE);
                break;
            case R.id.btnUnbindService:
                unbindService(this);
                break;
        }
    }
    //实现ServiceConnection接口需要重写的方法
    //服务被绑定成功执行
    @Override
    public void onServiceConnected(ComponentName name, IBinder service) {
        System.out.println("Service Connected");
    }
    //实现ServiceConnection接口需要重写的方法
    //服务解除绑定或者被杀掉时执行
    @Override
    public void onServiceDisconnected(ComponentName name) {

    }
}
```
###3.2 activity_main.xml
```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
    android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <TextView android:text="@string/hello_world" android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

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

</LinearLayout>
```
###3.3 MyService（继承Service）
```
package cn.loverobots.learnservice;

import android.app.Service;
import android.content.Intent;
import android.os.Binder;
import android.os.IBinder;

public class MyService extends Service {
    public MyService() {
    }

    @Override
    public IBinder onBind(Intent intent) {
        //返回一个实现了IBinder接口的对象
        return new Binder();
    }
    //服务启动时所执行的命令示例
    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        new Thread() {
            @Override
            public void run() {
                super.run();
                while (true) {
                    System.out.println("服务正在运行...");
                    try {
                        sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }.start();

        return super.onStartCommand(intent, flags, startId);
    }
}
```
##4 Service的生命周期
与Service生命周期相关的函数为onCreate和onDestroy两个函数，其规律如下。  
4.1 启动服务再绑定服务后，只能先解除绑定服务再停止服务才能真正停止掉服务。  
4.2 启动服务后，退出程序，服务不会停止；再次进入程序停止服务，服务才会停止。  
4.3 启动服务并绑定服务后，退出程序，也不会停止服务。需要解除绑定服务，并进行服务停止，才能真正停止服务。  
4.4 Service中onCreate函数只会在最开始时执行一次，之后反复执行启动服务，只会反复执行onStartCommand函数。

##5 Service的停止与启动代码示例
###5.1 MyService（继承Service）
```
package cn.loverobots.learnservice;

import android.app.Service;
import android.content.Intent;
import android.os.Binder;
import android.os.IBinder;

public class MyService extends Service {
    //控制服务运行的开关
    private boolean serviceRunning = false;

    public MyService() {
    }

    @Override
    public IBinder onBind(Intent intent) {
        //返回一个实现了IBinder接口的对象
        return new Binder();
    }
    //服务启动时所执行的命令示例
    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        System.out.println("Service onStartCommand");
        return super.onStartCommand(intent, flags, startId);
    }

    @Override
    public void onCreate() {
        super.onCreate();
        System.out.println("Service onCreate");
        serviceRunning = true;
        new Thread() {
            @Override
            public void run() {
                super.run();
                while (serviceRunning) {
                    System.out.println("服务正在运行...");
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
        System.out.println("Service onDestory");
        serviceRunning = false;
        System.out.println("服务停止运行！");
    }
}

```