---
layout: post
title: "Activity之间的简单数据及数据包传递"
date: 2015-07-08 07:39:20 +0800
comments: true
categories: [android,activity,简单数据传递,数据包传递,android_basic]
---
##简单数据传递
###1 MainActivity
```
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        findViewById(R.id.btnStartAty).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent i = new Intent(MainActivity.this,TheAty.class);
                i.putExtra("data","Hello Loverobots");
                startActivity(i);
            }
        });
    }
```

###2 TheAty
```
    private TextView tv;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_the_aty);
        tv = (TextView) findViewById(R.id.tv);
        Intent i = getIntent();
        tv.setText(i.getStringExtra("data"));//i.putExtra("data","Hello Loverobots");的接收方法，简单传递参数
    }
```

##数据包传递
###1 MainActivity
```
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        findViewById(R.id.btnStartAty).setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View v) {
                Intent i = new Intent(MainActivity.this,TheAty.class);
                Bundle b = new Bundle();
                b.putString("name","loverobots");
                b.putInt("age",20);
                b.putString("name1","jackrobot");
//                i.putExtras(b);
                i.putExtra("data",b);

                startActivity(i);
            }
        });
    }
```

###2 TheAty
```
    private TextView tv;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_the_aty);
        tv = (TextView) findViewById(R.id.tv);

        Intent i = getIntent();
//        Bundle data = i.getExtras();//i.putExtras(b);对应的接收方法
        Bundle data = i.getBundleExtra("data");//i.putExtra("data",b);对应的接收方法

        tv.setText(String.format("name=%s , age=%d , name1=%s",
                   data.getString("name"),
                   data.getInt("age"),
                   data.getString("name1", "jack")));//这种形式中，如果name1不存在，则采用默认值jack
        tv.setText(String.format("User info(name=%s , age=%d)",user.getName(),user.getAge()));
    }
```