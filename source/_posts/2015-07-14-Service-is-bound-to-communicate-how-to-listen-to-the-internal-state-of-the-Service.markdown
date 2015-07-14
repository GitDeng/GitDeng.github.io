---
layout: post
title: "绑定Service进行通信（如何侦听Service的内部状态）"
date: 2015-07-14 00:39:20 +0800
comments: true
categories: [android,service,android_basic]
---
通过回调机制实现。当内部数据发生改变时，通知外部进行数据修改。
##示例代码
###1 MainActivity（继承Activity，并实现View.OnClickListener, ServiceConnection）
```
	package cn.loverobots.connectservice;

    import android.content.ComponentName;
    import android.content.Context;
    import android.content.Intent;
    import android.content.ServiceConnection;
    import android.os.Handler;
    import android.os.IBinder;
    import android.os.Message;
    import android.os.Bundle;
    import android.support.v7.app.AppCompatActivity;
    import android.view.View;
    import android.widget.EditText;
    import android.widget.TextView;

    public class MainActivity extends AppCompatActivity implements View.OnClickListener, ServiceConnection {

    	private EditText editText;
    	private Intent intent;
    	private MyService.Binder binder = null;
    	//步骤1
    	private TextView textViewOut;

    	@Override
    	protected void onCreate(Bundle savedInstanceState) {
        	super.onCreate(savedInstanceState);
        	setContentView(R.layout.activity_main);
        	editText = (EditText) findViewById(R.id.editText);
        	textViewOut = (TextView) findViewById(R.id.textViewOut);
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
        	//步骤7
        	binder.getService().setCallBack(new MyService.CallBack() {
            	@Override
            	public void onDataChange(String data) {
                	//textViewOut.setText(data);
                	//此处不能直接写,因为Android中有一个安全机制，即UI线程不允许其它辅线程直接修改UI线程的资源
                	//步骤9
                	Message msg = new Message();
                	Bundle bundle = new Bundle();
                	bundle.putString("data",data);
                	msg.setData(bundle);
                	handler.sendMessage(msg);
            	}
        	});
    	}

    	@Override
    	public void onServiceDisconnected(ComponentName name) {

    	}
    	//步骤8
    	private Handler handler = new Handler(){
        	@Override
        	public void handleMessage(Message msg) {
            	super.handleMessage(msg);
            	//步骤10
            	textViewOut.setText(msg.getData().getString("data"));
        	}
    	};
	}
```
###2 MyService(继承Service)
```
	package cn.loverobots.connectservice;

	import android.app.Service;
	import android.content.Intent;
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
        	//步骤6
        	public MyService getService(){
            	return MyService.this;
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
                	//步骤4
                	int i = 0;
                	while (ServiceRunning){
                    	i++;
                    	String str = i+"："+etData;
                    	System.out.println(str);
                    	//步骤5
                    	if(callBack!=null){
                        	callBack.onDataChange(str);
                    	}
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
    	//步骤3
    	private CallBack callBack = null;

    	public void setCallBack(CallBack callBack) {
        	this.callBack = callBack;
    	}

    	public CallBack getCallBack() {
        	return callBack;
    	}
    	//步骤2
    	public static interface CallBack{
        	void onDataChange(String data);
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

    	<TextView
        	android:layout_width="wrap_content"
        	android:layout_height="wrap_content"
        	android:text="内部输出至外部测试文本"
        	android:id="@+id/textViewOut" />

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