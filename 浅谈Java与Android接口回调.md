> **目录**
> **1** 回调是什么
> **2**  回调的用处和好处
> **3**  Java中的接口回调
> **4**  Android中的接口回调

[TOC]

# 1 回调是什么？
一般来说，模块之间都存在一定的调用关系，从调用方式上看，可以分为三类：**同步调用**、**异步调用**和**回调**。

**同步调用**：是一种阻塞式调用，即在函数A的函数体里通过书写函数B的函数名来调用之，使内存中对应函数B的代码得以执行。
**异步调用**：是一种类似消息或事件的机制解决了同步阻塞的问题，例如 A通知 B后，他们各走各的路，互不影响，不用像同步调用那样， A通知 B后，非得等到 B走完后， A才继续走 。
**回调：是一种双向的调用模式，也就是说，被调用的接口被调用时也会调用对方的接口，例如A要调用B，B在执行完又要调用A。**

#2 回调的用处和好处
**回调一般用于层间协作**，上层将本层函数安装在下层，这个函数就是回调，而下层在一定条件下触发回调。例如作为一个驱动，是一个底层，他在收到一个数据时，除了完成本层的处理工作外，还将进行回调，将这个数据交给上层应用层来做进一步处理，这在分层的数据通信中很普遍。

**回调降低代码的耦合性，使代码更灵活、简洁**

#3 Java中的接口回调

**3-1 先创建一个回调接口CallBack**
> 场景：老板通知员工做事
```
  public interface CallBack {
     public void youNeedDo();
  }
```
**3-2 创建一个老板类 （Boss）**
> 简称它为A类，它实现了接口，因此也需要实现其方法
```
 public class Boss implements CallBack {
    public void youNeedDo() {
         System.out.println("最近项目A要上线，今晚你留下来加班");
    }
 }
```
**3-2 创建一个员工类 （Worker）**
> 简称它为B类，B类需要持有A类的引用，并在一些情况下，我们想要调用A类的方法，这个时候就用到了**回调**
```
public class Worker{
     CallBack callBack;
     public Worker(CallBack callBack) {
         this.callBack=callBack;
     }
     public void doWork() {
         callBack.youNeedDo();
     }
 }
```
**3-2 实现**

```
public class TestMain {
     public static void main(String[] args) {
         //创建控制器对象，将提供给他的回调对象传入
         Worker worker =new Worker(new Boss());
         //启动控制器对象运行
         worker.doWork();
     }
 }
```
> **最终输出结果**：
"最近项目A要上线，今晚你留下来加班"

# 4 Android中的接口回调
Android很多地方用到了回调机制。如Activity定义了大量的回调方法来完成整个生命周期，这些方法都是会自动调用的，可以在Activity的子类中重载。

**4-1 比如常见的点击事件OnClickListener**
OnClickListener 就是 Android 系统所约定好的接口，然后在传入回调对象（这里是直接new了一个接口的实现类），最终onClick方法会被回调并执行，这样就达到了接口统一，实现不同的效果。**详情可自行参考View源码中onTouchEvent里实现的performClick方法**
```
button.setOnClickListener(new OnClickListener() {
     @Override
     public void onClick(View v) {
 
     }
 });
```

**4-2 下面举个网络请求的例子**

**第一步：定义回调接口**
并含有 请求完成 、 请求失败 两个方法
```
/**
 * 网络请求回调接口
 */
public interface HttpCallBackListener {
    void onFinish(String respose);

    void onError(Exception e);
}
```

**第二步：定义回调方法（将接口作为参数传递）**
```
/**
 * 网络请求工具类
 */
public class HttpUtil {

    public static void requestData(final String urlStr, final HttpCallBackListener listener) {
        new Thread(new Runnable() {
            @Override
            public void run() {
                HttpURLConnection connection = null;
                try {
                    URL url = new URL(urlStr);
                    connection = (HttpURLConnection) url.openConnection();
                    connection.setRequestMethod("GET");
                    connection.setConnectTimeout(8000);
                    connection.setReadTimeout(8000);
                    connection.setDoInput(true);
                    connection.setDoOutput(true);
                    InputStream in = connection.getInputStream();
                    BufferedReader br = new BufferedReader(new InputStreamReader(in));
                    StringBuilder sb = new StringBuilder();
                    String line;
                    while ((line = br.readLine()) != null) {
                        sb.append(line);
                    }
                    if (listener != null) {
                        //回调onFinish方法
                        listener.onFinish(sb.toString());
                    }
                } catch (Exception e) {
                    if (listener != null) {
                        //回调onError方法
                        listener.onError(e);
                    }
                } finally {
                    if (connection != null) {
                        connection.disconnect();
                    }
                }
            }
        }).start();
    }
}
```
**第三步：使用回调方法**
这里直接new了一个接口实现类作为参数传了进入，当网络请求成功或失败会分别回调到这里的方法
```
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        HttpUtil.requestData("请求的网址", new HttpCallBackListener() {
            @Override
            public void onFinish(String respose) {
                //处理请求
            }

            @Override
            public void onError(Exception e) {
                //处理异常
            }
        });
    }

}
```

**此外，还可以通过当前类实现接口，传入this，实现回调**
修改一下最后一步：
```
public class MainActivity extends AppCompatActivity implements HttpCallBackListener {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        HttpUtil.requestData("请求的网址", this);
    }

    @Override
    public void onFinish(String respose) {
        //处理请求
    }

    @Override
    public void onError(Exception e) {
        //处理异常
    }

}
```

---
#参考声明：
本文参考了以下出处
http://www.cnblogs.com/Im-Victor/p/6305970.html
结合个人见解，加以整理，特此声明，感谢分享

---







