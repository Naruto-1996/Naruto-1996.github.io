---
title: Android-RecycleView内部item点击事件
date: 2020-10-21 16:11:14
categories:
- Android
tags:
- 安卓开发
- RecycleView
---

#### RecyclerView的使用——Item内部控件的点击事件

##### 一、在adapter中写item点击事件的接口供外部的activity使用

```` java
/**
* item内部的监听接口
*/

public interface ItemInnerOnclickListener {
    // 这个String id 是点击item是想要传的参数
    void onItemInnerOnClick(int position, String id);
}

private ItemInnerOnclickListener mItemInnerOnclickListener;

public void setOnItemOnClickListener(ItemInnerOnclickListener mItemInnerOnclickListener) {
    this.mItemInnerOnclickListener = mItemInnerOnclickListener;

  }
````

在holder中就可以这么去写：(这里我使用了CommonAdapter 所以holder的代码可能不太一样)

``` java
holder.setOnClickListener(R.id.cancel_withdraw, new OnMultiClickListener() {
  @Override
  public void onMultiClick(View v) {
    // record.id 是activity那边拿到数据后用bean来解析的 传递过来的数据
    // public class WithdrawRecordsAdapter extends CommonAdapter<WithdrawRecordBean.Record>
    mItemInnerOnclickListener.onItemInnerOnClick(position, record.id);
  }
});
```

##### 二、在activity中我们就可以这么去写

``` java
private WithdrawRecordsAdapter withdrawRecordsAdapter;
```

``` java
withdrawRecordsAdapter.setOnItemOnClickListener(new WithdrawRecordsAdapter.ItemInnerOnclickListener() {
          @Override
          public void onItemInnerOnClick(int position, String id) {
            showDialog(id,position);
          }
        });
```

这里我们设置点击事件的时候去new了一个adapter中点击事件的接口，并且我们还可以拿到adapter中点击时想要传递的String id 去做一些事情




