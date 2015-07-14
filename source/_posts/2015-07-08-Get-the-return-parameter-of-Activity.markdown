---
layout: post
title: "获取Activity的返回参数"
date: 2015-07-08 16:39:20 +0800
comments: true
categories: [android,activity,参数传递,返回参数,android_basic]
---
##1 MainActivity

```
    private TextView textView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        textView = (TextView) findViewById(R.id.textView);//获取Activity返回的数据

        findViewById(R.id.btnStartAty).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent i = new Intent(MainActivity.this,TheAty.class);
//                startActivity(i);
                startActivityForResult(i,0);//只有通过此种方法才能够接收带参数的Activity，其中0是请求代码
            }
        });
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        textView.setText("另一个Activity返回的数据是："+data.getStringExtra("data"));
    }
```

##1 activity_main.xml
```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
    android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="启动另一个Activity"
        android:id="@+id/btnStartAty" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="New Text"
        android:id="@+id/textView" />

</LinearLayout>
```

##2 TheAty

```
    private EditText editText;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_the_aty);
        tv = (TextView) findViewById(R.id.tv);

        editText = (EditText) findViewById(R.id.editText);

        Intent i = getIntent();

        findViewById(R.id.button).setOnClickListener(new View.OnClickListener() {//设置按钮动作
            @Override
            public void onClick(View v) {
                Intent i = new Intent();
                i.putExtra("data",editText.getText().toString());
                setResult(1, i);//将editText中字符串作为结果传递
                finish();//结束当前的Activity
            }
        });
    }
```

##2 activity_the_aty.xml
```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
    android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:orientation="vertical"
    tools:context="cn.loverobots.sendargs.TheAty">

    <TextView android:text="@string/hello_world" android:layout_width="wrap_content"
        android:layout_height="wrap_content" android:id="@+id/tv"/>

    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editText" />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Send Back"
        android:id="@+id/button" />

</LinearLayout>
```