---
layout: post
title: "Activity之间的值传递（实现Serializable）"
date: 2015-07-08 13:39:20 +0800
comments: true
categories: [android,activity,值传递,serializable,android_basic]
---
##1 值对象

```
package cn.loverobots.sendargs;
import java.io.Serializable;
public class User implements Serializable{
    private String name;
    private int age;
    public void setName(String name) {
        this.name = name;
    }
    public void setAge(int age) {
        this.age = age;
    }
    public String getName() {
        return name;
    }
    public int getAge() {
        return age;
    }
    public User (String name,int age){
        this.name = name;
        this.age = age;
    }
}
```

##2 MainActivity

```
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        findViewById(R.id.btnStartAty).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent i = new Intent(MainActivity.this,TheAty.class);
                i.putExtra("user",new User("jackrobot",20));//传递值对象，同时对值对象进行初始化
                startActivity(i);
            }
        });
    }
```

##3 TheAty

```
    private TextView tv;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_the_aty);
        tv = (TextView) findViewById(R.id.tv);
        Intent i = getIntent();
        User user = (User) i.getSerializableExtra("user");//以Serializable的方式获取值对象
        tv.setText(String.format("User info(name=%s , age=%d)",user.getName(),user.getAge()));
    }
```