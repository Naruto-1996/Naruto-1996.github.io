---
title: Android-如何监听前后台
date: 2020-10-21 16:32:40
categories:
- Android
tags:
- Android
---

#### Android-如何监听前后台

简单讲讲android如何判断应用切换到后台和应用切换到前台。这个其实很简单，之前需要做一个功能，当app由后台进入前台时需要完成一些逻辑操作，所以在网上查找如何判断app由后台进入前台，最终是解决了问题。这里记录一下。

##### 一、使用ActivityLifecycleCallbacks简单app进入后台

有时需要监听到应用在前后台切换并做些处理，一般的做法可能是建立一个BaseActivity，然后全部的Activity都继承它，在BaseActivity的onStart和onStop中计数去处理。这样并不是最好的方式，不做详细介绍，有更好的方式，道理其实差不多，就是借助ActivityLifecycleCallbacks来实现。

1、 写了一个帮助类

``` java
package com.cadae.helpers;

import android.app.Activity;
import android.app.Application;
import android.os.Bundle;

public class AppFrontBackHelper {
  private OnAppStatusListener mOnAppStatusListener;


  public AppFrontBackHelper() {

  }

  /**
   * 注册状态监听，仅在Application中使用
   * @param application
   * @param listener
   */
  public void register(Application application, OnAppStatusListener listener){
    mOnAppStatusListener = listener;
    application.registerActivityLifecycleCallbacks(activityLifecycleCallbacks);
  }

  public void unRegister(Application application){
    application.unregisterActivityLifecycleCallbacks(activityLifecycleCallbacks);
  }

  private Application.ActivityLifecycleCallbacks activityLifecycleCallbacks = new Application.ActivityLifecycleCallbacks() {
    //打开的Activity数量统计
    private int activityStartCount = 0;

    @Override
    public void onActivityCreated(Activity activity, Bundle savedInstanceState) {

    }

    @Override
    public void onActivityStarted(Activity activity) {
      activityStartCount++;
      //数值从0变到1说明是从后台切到前台
      if (activityStartCount == 1){
        //从后台切到前台
        if(mOnAppStatusListener != null){
          mOnAppStatusListener.onFront();
        }
      }
    }

    @Override
    public void onActivityResumed(Activity activity) {

    }

    @Override
    public void onActivityPaused(Activity activity) {

    }

    @Override
    public void onActivityStopped(Activity activity) {
      activityStartCount--;
      //数值从1到0说明是从前台切到后台
      if (activityStartCount == 0){
        //从前台切到后台
        if(mOnAppStatusListener != null){
          mOnAppStatusListener.onBack();
        }
      }
    }

    @Override
    public void onActivitySaveInstanceState(Activity activity, Bundle outState) {

    }

    @Override
    public void onActivityDestroyed(Activity activity) {

    }
  };

  public interface OnAppStatusListener{
    void onFront();
    void onBack();
  }
}

```

##### 二、Application中使用（注意：仅在Application中才能使用，因为Application的生命周期能监听到每个Activity）

``` java
public class MyApp extends Application {
    
    @Override
    public void onCreate() {
        super.onCreate();
 
        AppFrontBackHelper helper = new AppFrontBackHelper();
        helper.register(MyApp.this, new AppFrontBackHelper.OnAppStatusListener() {
            @Override
            public void onFront() {
                //应用切到前台处理
 
            }
 
            @Override
            public void onBack() {
                //应用切到后台处理
 
            }
        });
    }
 
}
```

这里在后台我们就可以去做一些事情比如开始一个计时任务 超时时退出登录

``` java
public class MyApp extends Application {

    private Timer mTimer; // 计时器，每1秒执行一次任务
    private MyTimerTask mTimerTask; // 计时任务，判断是否未操作时间到达ns
    private long mLastActionTime; // 上一次操作时间

    @Override
    public void onCreate() {
        super.onCreate();
 
        AppFrontBackHelper helper = new AppFrontBackHelper();
        helper.register(MyApp.this, new AppFrontBackHelper.OnAppStatusListener() {
            @Override
            public void onFront() {
                //应用切到前台处理
                Log.i("TAG", "在前台");
                mLastActionTime = System.currentTimeMillis();
                if (mTimer != null){
                     mTimer.cancel();
                 }
            }
 
            @Override
            public void onBack() {
                //应用切到后台处理
                Log.i("TAG", "在后台");
                if (SharedPrefsHelper.isLogin()) {
                  startTimer();
                }
            }
        });
    }

  // 登录成功，开始计时
  private void startTimer() {
    if (mTimer != null){
      mTimer.cancel();
    }
    mTimer = new Timer();
    mTimerTask = new MyTimerTask();
    // 初始化上次操作时间为登录成功的时间
    mLastActionTime = System.currentTimeMillis();
    // 每过1s检查一次
    mTimer.schedule(mTimerTask, 0, 1000);
    Log.i("TAG", "开始计时了。。。。。。。。。");
  }

  private class MyTimerTask extends TimerTask {

    @Override
    public void run() {
      Log.i("TAG", "计时中……");
      // 一小时未操作停止计时并退出登录
      if (System.currentTimeMillis() - mLastActionTime > 1000 * 3600) {
        stopTimer();// 停止计时任务
        LogOutAuto();//退出登录状态
      }
    }
  }

  // 停止计时任务
  public void stopTimer() {
    mTimer.cancel();
    // 计时任务是否完成 赋值
    SharedPrefsHelper.setBoolean(Constants.IS_TIMER_TASK_FINISHED,true);
    // 用户按下后退键 初始化值
    SharedPrefsHelper.setBoolean(Constants.USER_PRESSED_BACK,false);
    Log.i("TAG","结束了");
    Log.e("TAG", "取消计时");
  }
 
}
```
