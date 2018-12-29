
#### [原文](https://developer.android.com/topic/performance/vitals/launch-time)

# 应用启动时间


## App 的启动机制

App 有三种启动方式，分别是 Cold start、Warm start 和 Hot start，其中 Cold start 是将 App 从零开始（App 进程不存在）启动，而 Warm start 和 Hot start 都是将 App 从后台拉起到前台的过程。

- Cold start：自从设备启动后还未拉起过 App 或者 App 被系统杀死；

Cold start 方式下，1、系统会加载和启动 App；2、在启动 App 后，展示一个空的启动界面；3、创建 App 进程；

当系统创建 App 进程后，App 进程会开始：

1、创建 App 对象，并调用其 onCreate 方法；
2、创建并加载主线程；
3、创建主 Activity，并初始化参数，调用 onCreate 方法；

当 App 进程完成了主 Activity 的初始化后，将会自动替换之前的启动界面，用户就可以开始使用 app 了，整个过程如下图：

![](https://developer.android.com/topic/performance/images/cold-launch.png)

所以，整个创建的时间就是系统启动 App 到主 Activity 创建完毕的时间。

**这里有意思的是**

1、 虽然主线程是在 App 对象的 onCreate 方法之后创建的，但是主线程和 App 调用 onCreate 方法是同一个线程，应该是创建的过程中复用了之前的线程。

2、 Appliction 的 onCreate 方法 和 MainActivity 的 onCreate 方法是属于 App 启动时间，因此这里可以做一些比较耗时的初始化操作。

- Hot start 仅仅将 App 拉起到前台；
- Warm start 由部分 Cold start 的步骤组成；

常见的场景：1、用户按返回键退出 App，然后重新打开应用，虽然应用进程还存在，但是需要重新创建 Activity，但是；2、系统杀死应用进程，这时可能通过 savedInstanceState 来恢复状态；

一般来说，Cold start 不超过 5s、Warm start 不超过 2s、Hot start 不超过 1.5s，Logcat 会自动打印出 Cold start 的整体用时：

![](https://developer.android.com/topic/performance/images/displayed-logcat.png)

> 更多关于如何查看 Cold start 耗时的方法，请参考原文


## 如何优化 App 启动时间

- 不要在 App 类初始化中做过多的操作

对象的初始化可以用 Dagger 来进行懒初始化；


- 不要在 Activity 的初始化中做过多的操作

可以减少布局文件的复杂性，或者使用 [ViewStub](https://developer.android.com/reference/android/view/ViewStub.html) 来延时初始化；在子线程中初始化和更新资源。

## 启动界面

-  新建一个 launch.xml 的 drawable 文件

```
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:opacity="opaque">
    <item android:drawable="@color/colorPrimary"/>
    <item >
        <bitmap
            android:src="@drawable/background_launch"
            android:gravity="center" />
    </item>
</layer-list>
```

- 增加一个 LaunchStyle 的样式

```
<style name="LaunchStyle" parent="AppTheme">
	<item name="android:windowBackground">@drawable/launch</item>
</style>
```
- 给启动的 Activity 设置 theme

```
<activity android:name=".MainActivity"
    android:theme="@style/LaunchStyle">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />

        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```

- 最后别忘了给 Activity 恢复 style

```
 @Override
protected void onCreate(Bundle savedInstanceState) {
    // 增加这行
    setTheme(R.style.AppTheme);
    super.onCreate(savedInstanceState);
    .....
}
```
