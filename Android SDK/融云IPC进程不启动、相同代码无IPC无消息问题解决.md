> 近期项目用到了融云IM  
反复出现了一个问题：  
即 **融云IM的IPC进程始终没有启动**  
过程很纠结，这里记录一下，分享出来

# 1 找不到IPC进程
### 1-1 第一种情况
- **集成错误，需要按照官网的指示
在libs或者jniLibs里配至好对应的so库libsqlite.so**
![](https://upload-images.jianshu.io/upload_images/3787831-7d5dcdd837488438.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)

参考这里：https://support.rongcloud.cn/kb/NTQw
- **需要注意区分：**
- 如果全放到 jniLibs则无需其他操作
在Android Studio中，则会默认匹配main下的jniLibs目录，如果没有目录需要自己手动创建。并且库的名称也不能随便更改。
- 如果全放到 libs里，需要加入
```
   android {
        ......
        sourceSets {
            main {
                jniLibs.srcDirs = ['libs']
                ......
            }
            ......
        }
        ......
    }
```
### 1-2 第二种情况 （参考的某博客）
- 把ndk这一块的配置去掉 
```
defaultConfig {
    applicationId "XXX"
    ...
    ndk {
        abiFilters "armeabi-v7a", "arm64-v8a"
    }
}
```


# 2 同样的代码（SVN弊端导致）
>本人打的apk包有IPC进程，可以正常接收聊天消息  
>另外同事拉取我svn的代码后，无IPC进程，收不到聊天消息  
- 即项目前期由我维护，版本控制工具，公司用的SVN
我按正常流程，加入过滤规则，过滤了一些不必要的文件
而后接手的同事，拉取我最新的代码后，反映收不到聊天消息
**且在logcat里可以看到没有IPC进程**
- **我首先定位看是不是少了 so库**，然后按照第一种方法尝试解决，结果发现没问题，配置都有
- 陷入了困境僵局，也参考了很多资料，未果
- 经过费时费力的尝试，**最后发现是IMLib缺少文件，jniLibs里空有x86等文件夹，里面却缺少so库文件**
- 而我本地的代码，**这些so库显示为暗黄色**
![](https://upload-images.jianshu.io/upload_images/3787831-e555cae731a3ea11.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)
- 仔细一想，不能啊，每次提交都没发现有问题，怎么回事呢？
难道SVN不识别？自动过滤了？
- **最后发现还真是SVN的问题！ 默认会过滤掉so文件
也就是说一直以来我都上传的都不是完整的代码，无语了！**
![](https://upload-images.jianshu.io/upload_images/3787831-d5f6188393d8a300.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/500)



---
# 参考
（融云官网）https://www.rongcloud.cn/docs/android.html
（博客）https://juejin.im/post/5b6e91caf265da0f7c4ff60d
