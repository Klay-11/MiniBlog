
>[《第一行代码》个人笔记系列](https://www.jianshu.com/nb/25402195)

---
![目录](https://upload-images.jianshu.io/upload_images/3787831-9b996ba24de207ba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/650)



#1 碎片（fragment）简介
> 可以看做**迷你型活动**，常嵌套在Activity中，有自己的生命周期

> Android 3.0开始引入，最初主要目的为了兼容手机和平板，充分利用平板的空间
目前在手机上用到的情况已经非常普遍和常见

#2 碎片使用方式

#####2-1 两种使用方式
> **方式一： 静态添加fragment，即在布局中引入**
- 首先分别新建2个碎片LeftFragment、RightFragment
```
package com.example.fragmentdemo;

import android.os.Bundle;
import android.support.v4.app.Fragment;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;


public class LeftFragment extends Fragment{

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.left_fragment,container,false);
        return view;
    }
}

```

```
package com.example.fragmentdemo;

import android.os.Bundle;
import android.support.v4.app.Fragment;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;


public class RightFragment extends Fragment{

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.right_fragment,container,false);
        return view;
    }
}

```

- 以及它们的布局
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:text="Button"/>
</LinearLayout>
```
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:background="#00ff00">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:textSize="20sp"
        android:text="右侧碎片"/>
</LinearLayout>
```

- 最后在MainActivity的主布局中引入这2个碎片，且平分布局
注意android:name必须指明fragment的全路径
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">
    <fragment
        android:id="@+id/left_fragment"
        android:layout_width="0dp"
        android:name="com.example.fragmentdemo.LeftFragment"
        android:layout_height="match_parent"
        android:layout_weight="1">
    </fragment>

    <fragment
        android:id="@+id/right_fragment"
        android:layout_width="0dp"
        android:name="com.example.fragmentdemo.RightFragment"
        android:layout_height="match_parent"
        android:layout_weight="1">
    </fragment>
</LinearLayout>
```

效果：
![](https://upload-images.jianshu.io/upload_images/3787831-cc03fa2b4615c0d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> **方式二： 动态添加fragment，即在代码中添加**

- 新建第三个fragment，以及它的布局
```
package com.example.fragmentdemo;

import android.os.Bundle;
import android.support.v4.app.Fragment;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;


public class AnotherRightFragment extends Fragment{

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.another_right_fragment,container,false);
        return view;
    }
}

```
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:background="#ffff00">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:textSize="20sp"
        android:text="这是另外一个右侧碎片"/>
</LinearLayout>
```

- 修改MainActivity的主布局，右侧改为放一个FrameLayout容器，我们后面会动态给这个容器里添加碎片
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">
    <fragment
        android:id="@+id/left_fragment"
        android:layout_width="0dp"
        android:name="com.example.fragmentdemo.LeftFragment"
        android:layout_height="match_parent"
        android:layout_weight="1">
    </fragment>

    <FrameLayout
        android:id="@+id/right_layout"
        android:layout_width="0dp"
        android:layout_weight="1"
        android:layout_height="match_parent">
    </FrameLayout>
</LinearLayout>

```

- 最后修改MainActivity
可以看到先是调用replaceFragment替换了RightFragment
点击button后接着替换成了AnotherRightFragment

```
package com.example.fragmentdemo;

import android.os.Bundle;
import android.support.v4.app.Fragment;
import android.support.v4.app.FragmentManager;
import android.support.v4.app.FragmentTransaction;
import android.support.v7.app.AppCompatActivity;
import android.view.View;
import android.widget.Button;

public class MainActivity extends AppCompatActivity {

    Button button;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        button = findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                replaceFragment(new AnotherRightFragment());
            }
        });
        replaceFragment(new RightFragment());
    }

    private void replaceFragment(Fragment fragment){
        FragmentManager fragmentManager = getSupportFragmentManager();
        FragmentTransaction transaction = fragmentManager.beginTransaction();
        transaction.replace(R.id.right_layout,fragment);
        transaction.commit();
    }
}

```

> 上述代码可以看出，动态添加fragment碎片可以分为5步：
**（1）创建待添加的碎片fragment
 （2）通过getSupportFragmentManager获取到FragmentManager
 （3）通过调用beginTransaction开启一个事务
 （4）向容器添加或替换碎片，一般使用replace方法，传入容器id和碎片实例
 （5）提交事务，调用commit方法**

点击button后的效果：
![](https://upload-images.jianshu.io/upload_images/3787831-d4b100513cda7e48.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#####2-2 在碎片中模拟返回栈
> 上面的例子，当替换成AnotherRightFragment后，如果此时按返回键，会直接退出
而我们有时候希望点击返回键，AnotherRightFragment会回退到上一个RightFragment
只需要加一行```transaction.addToBackStack(null);```即可
```
    private void replaceFragment(Fragment fragment){
        FragmentManager fragmentManager = getSupportFragmentManager();
        FragmentTransaction transaction = fragmentManager.beginTransaction();
        transaction.replace(R.id.right_layout,fragment);
        transaction.addToBackStack(null);
        transaction.commit();
    }
```
##### 2-3 碎片与活动之间通信

>**在活动里调用Fragment的方法：**
可以先通过FragmentManager 的 findFragmentById方法拿到 fragment的实例
然后就可以很容易调用 fragment 的方法
如：
```Java
LeftFragment leftFragment = (LeftFragment) getSupportFragmentManager().findFragmentById(R.id.left_fragment);
```

> **在Fragment里调用活动的方法：**
通过getActivity拿到activity实例，然后调用方法即可
```
MainActivity mainActivity = (MainActivity) getActivity();
```

> **Fragment与Fragment进行通信：**
思路：首先，一个Fragment可以得到和它相关联的Activity，然后通过这个活动去获取另一个Fragment的实例。这样就实现了不同Fragment之间的通信。

#####2-4 向碎片传值
- 当我们在Activity中调用 UserDetailFragment.newInstance 的时候
可以把参数带到 Fragment
利用 Bundle 和 setArguments，让 Fragment携带这些参数
接着可以通过getArguments拿到这些参数
```
    //参数名
    private static final String ARG_PARAM1 = "param1";
    private static final String ARG_PARAM2 = "param2";
   
   //参数值
    private String mParam1;
    private String mParam2;

    public static UserDetailFragment newInstance(String param1, String param2) {
        UserDetailFragment fragment = new UserDetailFragment();
        Bundle args = new Bundle();
        args.putString(ARG_PARAM1, param1);
        args.putString(ARG_PARAM2, param2);
        fragment.setArguments(args);
        return fragment;
    }

  public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        if (getArguments() != null) {
            mParam1 = getArguments().getString(ARG_PARAM1);
            mParam2 = getArguments().getString(ARG_PARAM2);
        }
    }
```

# 3 碎片的生命周期

![](https://upload-images.jianshu.io/upload_images/2244681-3685a0866eb07d3a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/317)

- 可以看到除了和Activity一样拥有onCreate、onStart、onResume、onPause、onStop、onDestroy等方法
碎片还附加了一些其他的方法
> **onAttach（）  当碎片和活动建立关联的时候调用
onCreateView（）  为碎片创建视图的时候调用
onActivityCreated（）  确保与碎片相关联的活动一定已经创建完毕的时候调用
onDestroyView（）  当与碎片关联的视图被移除的时候调用
onDetach（）  当碎片和活动解除关联的时候调用**

附：Activity和Fragment生命周期对比
![image](http://upload-images.jianshu.io/upload_images/3787831-cc58e4c39be98c18?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




--- 
#参考：《第一行代码》


