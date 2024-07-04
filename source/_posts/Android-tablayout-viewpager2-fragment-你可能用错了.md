---
title: Android tablayout viewpager2 fragment 你可能用错了
date: 2021-06-25 17:11:29
category:
- Android
tags:
- tabLayout
- viewPager2
- fragment
---

### tabLayout + viewPager2 + fragment 用不好会导致内存泄漏

### 前言

tabLayout + viewPager2 + fragment 这是在app开发中非常常见的UI架构了 但是用不好的话 是会导致内存泄漏的

[Demo地址](https://github.com/Naruto-1996/ViewPager2_Demo)

先上图: 

(`app`模板使用`android studio` 创建一个就行 我只用 `HomeFragment`)

<img src="https://ae03.alicdn.com/kf/U3292cb58c99d44a3bcf092f9d0fba361h.png" width = "300" height = "600" alt="图片名称" align=center />

### 先了解一下viewPager

之前我们都是使用viewPager 但是 viewPager 有两个毛病 

* 不能关闭预加载

* 更新Adapter不生效

虽然能解决 但是很麻烦

viewPager 有个方法 `offscreenPageLimit` 用来设置 当前页前后预加载的数量 即使设置为0也不起任何作用 因为源码中默认值为1

先来看个图:

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210625175636.png)

上面是`ViewPager`默认情况下的加载示意图，当切换到当前页面时，会默认预加载左右两侧的布局到`ViewPager`中，
尽管两侧的`View`并不可见的，我们称这种情况叫预加载；由于`ViewPager`对`offscreenPageLimit`设置了限制，
页面的预加载是不可避免

如果使用 一定要使用viewPager 也可以做到 fragment 懒加载 主要是对`fragment`做手脚 结合生命周期方法和setUserVisibleHint状态，控制数据延迟加载，而布局只能提前进入

### viewPager2基本使用

1、 承载tabLayout 和 viewPager2 需要一个 fragment 或者 activity 这里我们使用 fragment

`HomeFragment` 如下:

```java

public class HomeFragment extends Fragment {

  private HomeViewModel homeViewModel;
  private TabLayout tabLayout;
  private ViewPager2 viewPager2;

  private final ArrayList<String> tabTitles = new ArrayList<>();

  // 用List 存放fragment 不推荐使用 用不好 导致内存泄漏
  private ArrayList<BlankFragment> fragments = new ArrayList<>();



  public View onCreateView(@NonNull LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
    homeViewModel = new ViewModelProvider(this).get(HomeViewModel.class);
    View root = inflater.inflate(R.layout.fragment_home, container, false);

    tabLayout = root.findViewById(R.id.tabs);
    viewPager2 = root.findViewById(R.id.viewPager);
    return root;
  }


  @Override
  public void onViewCreated(@NonNull View view, @Nullable Bundle savedInstanceState) {
    super.onViewCreated(view, savedInstanceState);

    // 初始化 tabTitles 和 fragments
    for (int i = 0; i < 15; i++) {
      tabTitles.add(String.valueOf(i));
      fragments.add(BlankFragment.newInstance(tabTitles.get(i)));
    }

    // 关闭预加载
    viewPager2.setOffscreenPageLimit(ViewPager2.OFFSCREEN_PAGE_LIMIT_DEFAULT);  // 可以不设置 因为默认是 -1 默认不进行预加载
    // 这个必须设置 不然仍然会启用预加载
    ((RecyclerView)viewPager2.getChildAt(0)).getLayoutManager().setItemPrefetchEnabled(false);
    // 设置缓存数量，对应 RecyclerView 中的 mCachedViews，即屏幕外的视图数量
    ((RecyclerView)viewPager2.getChildAt(0)).setItemViewCacheSize(0);

    // 第一种方法 (可能会内存泄漏)
    //FragmentAdapter adapter = new FragmentAdapter(this, fragments);
    //viewPager2.setAdapter(adapter);

    // 这里使用第二种方法
    viewPager2.setAdapter(new FragmentStateAdapter(this) {
      @NonNull
      @Override
      public Fragment createFragment(int position) {
        return BlankFragment.newInstance(tabTitles.get(position));
      }

      @Override
      public int getItemCount() {
        return tabTitles.size();
      }

    });
    // viewPager2 滑动监听
    viewPager2.registerOnPageChangeCallback(new ViewPager2.OnPageChangeCallback() {
      @Override
      public void onPageSelected(int position) {
        super.onPageSelected(position);

      }
    });

    // 这里第四个参数一定要设置为false  如果设置为true时 我们在滑动时 BlankFragment的创建 和 销毁 都很正常
    // 一旦 我们通过 点击tabLayout时 如果两个tab距离过远  那么所有划过的tabLayout 都会创建和销毁BlankFragment 这显然不是我们想要的

    // tabLayout 和 viewPager 联动
    new TabLayoutMediator(tabLayout, viewPager2,true,false, new TabLayoutMediator.TabConfigurationStrategy() {
      @Override
      public void onConfigureTab(@NonNull TabLayout.Tab tab, int position) {
        tab.setText(tabTitles.get(position));
      }
    }).attach();


  }

}

```

`fragment_home.xml` 布局文件:

```xml

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
  xmlns:app="http://schemas.android.com/apk/res-auto"
  xmlns:tools="http://schemas.android.com/tools"
  android:layout_width="match_parent"
  android:orientation="vertical"
  android:layout_height="match_parent"
  tools:context=".ui.home.HomeFragment">

  <com.google.android.material.tabs.TabLayout
    android:id="@+id/tabs"
    android:layout_width="match_parent"
    android:layout_height="40dp"
    app:tabGravity="center"
    app:tabIndicatorColor="#ff678f"
    app:tabIndicatorFullWidth="false"
    app:tabIndicatorHeight="2dp"
    app:tabMode="scrollable"
    android:layout_marginTop="0dp"
    app:tabSelectedTextColor="#ff678f"
    app:tabTextColor="#333333"
    app:tabUnboundedRipple="true" />


  <androidx.viewpager2.widget.ViewPager2
    android:id="@+id/viewPager"
    android:layout_width="match_parent"
    android:background="@color/purple_200"
    android:layout_height="0dp"
    android:layout_weight="1"/>
</LinearLayout>

```

2、viewPager2的 FragmentStateAdapter 创建fragment时 我们 复用一个fragment

`BlankFragment` 如下:

```java

public class BlankFragment extends Fragment {

  private String mParam1;
  private TextView mTextView;

  public static BlankFragment newInstance(String param1) {
    BlankFragment fragment = new BlankFragment();
    Bundle args = new Bundle();
    args.putString("param1", param1);
    fragment.setArguments(args);
    return fragment;
  }

  @Override
  public void onPause() {
    super.onPause();
    Log.i("TAG=======onPause", mParam1);
  }

  @Override
  public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    if (getArguments() != null) {
      mParam1 = getArguments().getString("param1");
    }

    Log.i("TAG=======onCreate", mParam1);
    Toast.makeText(getContext(), mParam1, Toast.LENGTH_SHORT).show();
  }



  @Override
  public void onDestroy() {
    super.onDestroy();
    Log.i("TAG=======onDestroy", mParam1);
  }

  @Override
  public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
    // Inflate the layout for this fragment
    View root = inflater.inflate(R.layout.fragment_blank, container, false);
    mTextView = root.findViewById(R.id.text1);
    mTextView.setText(mParam1);
    Log.i("TAG=======onCreateView", mParam1);
    return root;
  }

  // 对外提供一个刷新Ui的方法
  public void refreshUi() {
    if (getArguments() != null) {
      mParam1 = getArguments().getString("param1");
      mTextView.setText(mParam1 + "ddd");
    }

  }

  @Override
  public void onDestroyView() {
    super.onDestroyView();
    Log.i("TAG======onDestroyView", mParam1);
  }
}

```

`fragment_blank.xml` 如下:

```xml

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
  xmlns:tools="http://schemas.android.com/tools"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  android:orientation="vertical"
  android:gravity="center"
  tools:context=".ui.BlankFragment">

  <TextView
    android:id="@+id/text1"
    android:textSize="26sp"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="black"/>

</LinearLayout>

```

至此 我们已经完成了 tabLayout + viewPager2 + fragment 

### viewPager2常用方法

* `setAdapter()` 设置适配器
* `setOrientation()` 设置布局方向
* `setCurrentItem()` 设置当前Item下标
* `beginFakeDrag()` 开始模拟拖拽
* `fakeDragBy()` 模拟拖拽中
* `endFakeDrag()` 模拟拖拽结束
* `setUserInputEnabled()` 设置是否允许用户输入/触摸
* `setOffscreenPageLimit()` 设置屏幕外加载页面数量
* `registerOnPageChangeCallback()` 注册页面滑动监听回调
* `setPageTransformer()` 设置页面滑动时的变换效果


### ViewPager2 预加载和缓存

`viewPager2` 是存在预加载和缓存的 我们虽然可以通过`offscreenPageLimit` 去设置预加载的数量 但是 `viewPager2` 会默认帮我们
缓存两个`fragment` 意思就是我们再滑动回来时 不会再去走 fragment 创建的生命周期了 会走 onResume 生命周期函数

`one picture is worth a thousand words` :

`viewPager2` 默认开启预加载 

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210625182426.png)

**如何关闭预加载、并设置缓存数量**

**常见设置如下**

```java

//设置应保留在当前可见页面两侧的页面数
viewPager2.setOffscreenPageLimit(4);

//关闭预加载
((RecyclerView)viewPager2.getChildAt(0)).getLayoutManager().setItemPrefetchEnabled(false);

//设置缓存数量，对应 RecyclerView 中的 mCachedViews，即屏幕外的视图数量
((RecyclerView)viewPager2.getChildAt(0)).setItemViewCacheSize(4);

```

`viewPager2` 会默认缓存2个`ItemView`

我们将`((RecyclerView)viewPager2.getChildAt(0)).setItemViewCacheSize(0);` 设置为`0`即可 这样我们每次滑动一个新的`tab`时 会创建
新的`fragment`并销毁上一个`fragment`

### viewPager2 对 fragment 的支持

目前，`ViewPager2`对`Fragment`的支持只能使用`FragmentStateAdapter`，使用起来也是非常简单：


```java

viewPager2.setAdapter(new FragmentStateAdapter(this) {
  @NonNull
  @Override
  public Fragment createFragment(int position) {
    return BlankFragment.newInstance(tabTitles.get(position));
  }

  @Override
  public int getItemCount() {
    return tabTitles.size();
  }

});

```

### 另外还有一个小坑 

**ViewPager2+TabLayout懒加载问题，Fragment被创建多次**

`ViewPager2`默认只加载当前页面，相当于官方处理了`Fragment`的懒加载问题，当你使用代码

```

new TabLayoutMediator(tabLayout, viewPager, true, new TabLayoutMediator.TabConfigurationStrategy() {

       @Override

       public void onConfigureTab(@NonNull TabLayout.Tab tab, int position) {

           tab.setText(titles.get(position));

       }

}).attach();

```

此时当你滑动`ViewPager2`时，滑动到某个`Fragment`页面才会加载，执行onCreateView()方法，

但是当你手动点击`TabLayout`时，此时懒加载就会失效，`onCreateView()`会被执行多次，

原因就是...此时`ViewPager2`默认是平滑滚动的，滚动滑过的`Fragment`都会被加载，

**只需修改代码:**

```java

new TabLayoutMediator(tabLayout, viewPager, true,false, new TabLayoutMediator.TabConfigurationStrategy() {

            @Override

            public void onConfigureTab(@NonNull TabLayout.Tab tab, int position) {

                tab.setText(titles.get(position));

            }

}).attach();

```

其中，第二个`boolean`参数为`smoothScroll` 一定要填`false`  这时点击 哪个`tab` 就会创建 哪个 `fragment`

但是页面看起来没有那么平滑了 各有利弊吧

**---------------------------------------------------------------------------------------**

如果 需要点击tab无动画 滑动页面时有动画 怎么办呢?

TabLayoutMediator 是final类  且smoothScroll字段是私有的、我们没法继承重写、但是可以copy源码修改、不会有问题

我们将 源码copy下来 稍作修改就可以用了 下面是修改好的代码:

```java

import static androidx.viewpager2.widget.ViewPager2.SCROLL_STATE_DRAGGING;
import static androidx.viewpager2.widget.ViewPager2.SCROLL_STATE_IDLE;
import static androidx.viewpager2.widget.ViewPager2.SCROLL_STATE_SETTLING;

import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.recyclerview.widget.RecyclerView;
import androidx.viewpager2.widget.ViewPager2;

import com.google.android.material.tabs.TabLayout;

import java.lang.ref.WeakReference;

/**
 * A mediator to link a TabLayout with a ViewPager2. The mediator will synchronize the ViewPager2's
 * position with the selected tab when a tab is selected, and the TabLayout's scroll position when
 * the user drags the ViewPager2. TabLayoutMediator will listen to ViewPager2's OnPageChangeCallback
 * to adjust tab when ViewPager2 moves. TabLayoutMediator listens to TabLayout's
 * OnTabSelectedListener to adjust VP2 when tab moves. TabLayoutMediator listens to RecyclerView's
 * AdapterDataObserver to recreate tab content when dataset changes.
 *
 * <p>Establish the link by creating an instance of this class, make sure the ViewPager2 has an
 * adapter and then call {@link #attach()} on it. Instantiating a TabLayoutMediator will only create
 * the mediator object, {@link #attach()} will link the TabLayout and the ViewPager2 together. When
 * creating an instance of this class, you must supply an implementation of {@link
 * com.google.android.material.tabs.TabLayoutMediator.TabConfigurationStrategy} in which you set the text of the tab, and/or perform any styling of the
 * tabs that you require. Changing ViewPager2's adapter will require a {@link #detach()} followed by
 * {@link #attach()} call. Changing the ViewPager2 or TabLayout will require a new instantiation of
 * TabLayoutMediator.
 */
public final class TabLayoutMediators {
  @NonNull private final TabLayout tabLayout;
  @NonNull private final ViewPager2 viewPager;
  private final boolean autoRefresh;
  // 这里我们改为静态变量 以便于在静态方法中使用这个变量
  private static boolean smoothScroll;
  private final TabConfigurationStrategy tabConfigurationStrategy;
  @Nullable private RecyclerView.Adapter<?> adapter;
  private boolean attached;

  @Nullable private TabLayoutOnPageChangeCallback onPageChangeCallback;
  @Nullable private TabLayout.OnTabSelectedListener onTabSelectedListener;
  @Nullable private RecyclerView.AdapterDataObserver pagerAdapterObserver;

  /**
   * A callback interface that must be implemented to set the text and styling of newly created
   * tabs.
   */
  public interface TabConfigurationStrategy {
    /**
     * Called to configure the tab for the page at the specified position. Typically calls {@link
     * TabLayout.Tab#setText(CharSequence)}, but any form of styling can be applied.
     *
     * @param tab The Tab which should be configured to represent the title of the item at the given
     *     position in the data set.
     * @param position The position of the item within the adapter's data set.
     */
    void onConfigureTab(@NonNull TabLayout.Tab tab, int position);
  }

  public TabLayoutMediators(
    @NonNull TabLayout tabLayout,
    @NonNull ViewPager2 viewPager,
    @NonNull TabConfigurationStrategy tabConfigurationStrategy) {
    this(tabLayout, viewPager, /* autoRefresh= */ true, tabConfigurationStrategy);
  }

  public TabLayoutMediators(
    @NonNull TabLayout tabLayout,
    @NonNull ViewPager2 viewPager,
    boolean autoRefresh,
    @NonNull TabConfigurationStrategy tabConfigurationStrategy) {
    this(tabLayout, viewPager, autoRefresh, /* smoothScroll= */ true, tabConfigurationStrategy);
  }

  public TabLayoutMediators(
    @NonNull TabLayout tabLayout,
    @NonNull ViewPager2 viewPager,
    boolean autoRefresh,
    boolean smoothScroll,
    @NonNull TabConfigurationStrategy tabConfigurationStrategy) {
    this.tabLayout = tabLayout;
    this.viewPager = viewPager;
    this.autoRefresh = autoRefresh;
    TabLayoutMediators.smoothScroll = smoothScroll;
    this.tabConfigurationStrategy = tabConfigurationStrategy;
  }

  /**
   * Link the TabLayout and the ViewPager2 together. Must be called after ViewPager2 has an adapter
   * set. To be called on a new instance of TabLayoutMediator or if the ViewPager2's adapter
   * changes.
   *
   * @throws IllegalStateException If the mediator is already attached, or the ViewPager2 has no
   *     adapter.
   */
  public void attach() {
    if (attached) {
      throw new IllegalStateException("TabLayoutMediator is already attached");
    }
    adapter = viewPager.getAdapter();
    if (adapter == null) {
      throw new IllegalStateException(
        "TabLayoutMediator attached before ViewPager2 has an " + "adapter");
    }
    attached = true;

    // Add our custom OnPageChangeCallback to the ViewPager
    onPageChangeCallback = new TabLayoutOnPageChangeCallback(tabLayout);
    viewPager.registerOnPageChangeCallback(onPageChangeCallback);

    // Now we'll add a tab selected listener to set ViewPager's current item
    onTabSelectedListener = new ViewPagerOnTabSelectedListener(viewPager, smoothScroll);
    tabLayout.addOnTabSelectedListener(onTabSelectedListener);

    // Now we'll populate ourselves from the pager adapter, adding an observer if
    // autoRefresh is enabled
    if (autoRefresh) {
      // Register our observer on the new adapter
      pagerAdapterObserver = new PagerAdapterObserver();
      adapter.registerAdapterDataObserver(pagerAdapterObserver);
    }

    populateTabsFromPagerAdapter();

    // Now update the scroll position to match the ViewPager's current item
    tabLayout.setScrollPosition(viewPager.getCurrentItem(), 0f, true);
  }

  /**
   * Unlink the TabLayout and the ViewPager. To be called on a stale TabLayoutMediator if a new one
   * is instantiated, to prevent holding on to a view that should be garbage collected. Also to be
   * called before {@link #attach()} when a ViewPager2's adapter is changed.
   */
  public void detach() {
    if (autoRefresh && adapter != null) {
      adapter.unregisterAdapterDataObserver(pagerAdapterObserver);
      pagerAdapterObserver = null;
    }
    tabLayout.removeOnTabSelectedListener(onTabSelectedListener);
    viewPager.unregisterOnPageChangeCallback(onPageChangeCallback);
    onTabSelectedListener = null;
    onPageChangeCallback = null;
    adapter = null;
    attached = false;
  }

  @SuppressWarnings("WeakerAccess")
  void populateTabsFromPagerAdapter() {
    tabLayout.removeAllTabs();

    if (adapter != null) {
      int adapterCount = adapter.getItemCount();
      for (int i = 0; i < adapterCount; i++) {
        TabLayout.Tab tab = tabLayout.newTab();
        tabConfigurationStrategy.onConfigureTab(tab, i);
        tabLayout.addTab(tab, false);
      }
      // Make sure we reflect the currently set ViewPager item
      if (adapterCount > 0) {
        int lastItem = tabLayout.getTabCount() - 1;
        int currItem = Math.min(viewPager.getCurrentItem(), lastItem);
        if (currItem != tabLayout.getSelectedTabPosition()) {
          tabLayout.selectTab(tabLayout.getTabAt(currItem));
        }
      }
    }
  }

  /**
   * A {@link ViewPager2.OnPageChangeCallback} class which contains the necessary calls back to the
   * provided {@link TabLayout} so that the tab position is kept in sync.
   *
   * <p>This class stores the provided TabLayout weakly, meaning that you can use {@link
   * ViewPager2#registerOnPageChangeCallback(ViewPager2.OnPageChangeCallback)} without removing the
   * callback and not cause a leak.
   */
  private static class TabLayoutOnPageChangeCallback extends ViewPager2.OnPageChangeCallback {
    @NonNull private final WeakReference<TabLayout> tabLayoutRef;
    private int previousScrollState;
    private int scrollState;

    TabLayoutOnPageChangeCallback(TabLayout tabLayout) {
      tabLayoutRef = new WeakReference<>(tabLayout);
      reset();
    }

    @Override
    public void onPageScrollStateChanged(final int state) {
      // 根据 是滑动的viewPager还是 点击的 tab 去设置 是否需要平滑动画
      if (state == SCROLL_STATE_DRAGGING) {
        // 如果是滑动就设置平滑动画
        smoothScroll = true;
      } else if (state == SCROLL_STATE_IDLE) {
        // 点击的tab就不设置平滑动画
        smoothScroll = false;
      }
      previousScrollState = scrollState;
      scrollState = state;
    }

    @Override
    public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {
      TabLayout tabLayout = tabLayoutRef.get();
      if (tabLayout != null) {
        // Only update the text selection if we're not settling, or we are settling after
        // being dragged
        boolean updateText =
          scrollState != SCROLL_STATE_SETTLING || previousScrollState == SCROLL_STATE_DRAGGING;
        // Update the indicator if we're not settling after being idle. This is caused
        // from a setCurrentItem() call and will be handled by an animation from
        // onPageSelected() instead.
        boolean updateIndicator =
          !(scrollState == SCROLL_STATE_SETTLING && previousScrollState == SCROLL_STATE_IDLE);
        tabLayout.setScrollPosition(position, positionOffset, updateText, updateIndicator);
      }
    }

    @Override
    public void onPageSelected(final int position) {
      TabLayout tabLayout = tabLayoutRef.get();
      if (tabLayout != null
        && tabLayout.getSelectedTabPosition() != position
        && position < tabLayout.getTabCount()) {
        // Select the tab, only updating the indicator if we're not being dragged/settled
        // (since onPageScrolled will handle that).
        boolean updateIndicator =
          scrollState == SCROLL_STATE_IDLE
            || (scrollState == SCROLL_STATE_SETTLING
            && previousScrollState == SCROLL_STATE_IDLE);
        tabLayout.selectTab(tabLayout.getTabAt(position), updateIndicator);
      }
    }

    void reset() {
      previousScrollState = scrollState = SCROLL_STATE_IDLE;
    }
  }

  /**
   * A {@link TabLayout.OnTabSelectedListener} class which contains the necessary calls back to the
   * provided {@link ViewPager2} so that the tab position is kept in sync.
   */
  private static class ViewPagerOnTabSelectedListener implements TabLayout.OnTabSelectedListener {
    private final ViewPager2 viewPager;
    // 注释掉这个局部变量 使用最上边的那个全局变量
    //private final boolean smoothScroll;

    ViewPagerOnTabSelectedListener(ViewPager2 viewPager, boolean smoothScroll) {
      this.viewPager = viewPager;
      // 这一行也注释掉
      //this.smoothScroll = smoothScroll;
    }

    @Override
    public void onTabSelected(@NonNull TabLayout.Tab tab) {
      viewPager.setCurrentItem(tab.getPosition(), smoothScroll);
    }

    @Override
    public void onTabUnselected(TabLayout.Tab tab) {
      // No-op
    }

    @Override
    public void onTabReselected(TabLayout.Tab tab) {
      // No-op
    }
  }

  private class PagerAdapterObserver extends RecyclerView.AdapterDataObserver {
    PagerAdapterObserver() {}

    @Override
    public void onChanged() {
      populateTabsFromPagerAdapter();
    }

    @Override
    public void onItemRangeChanged(int positionStart, int itemCount) {
      populateTabsFromPagerAdapter();
    }

    @Override
    public void onItemRangeChanged(int positionStart, int itemCount, @Nullable Object payload) {
      populateTabsFromPagerAdapter();
    }

    @Override
    public void onItemRangeInserted(int positionStart, int itemCount) {
      populateTabsFromPagerAdapter();
    }

    @Override
    public void onItemRangeRemoved(int positionStart, int itemCount) {
      populateTabsFromPagerAdapter();
    }

    @Override
    public void onItemRangeMoved(int fromPosition, int toPosition, int itemCount) {
      populateTabsFromPagerAdapter();
    }
  }
}

```

具体用法为:

```java

    new TabLayoutMediators(tabLayout, viewPager2, new TabLayoutMediators.TabConfigurationStrategy() {
      @Override
      public void onConfigureTab(@NonNull TabLayout.Tab tab, int position) {
        tab.setText(tabTitles.get(position));
      }
    }).attach();

```

和官方的用法一样 没啥差别

**---------------------------------------------------------------------------------------**

如果即要求点击tab有动画 滑动页面也有动画 可以看下面这个 我们摒弃官方的 TabLayoutMediator 联动方法

我们自己来手动 让 tabLayout 和 viewPager2 联动起来

```java

  private void firstMethod() {

    // 设置 tabLayout 标题
    for (int i = 0; i < tabTitles.size(); i++) {
      tabLayout.addTab(tabLayout.newTab().setText(tabTitles.get(i)));
    }
    // 监听tabLayout事件 设置选中的viewPager2
    tabLayout.addOnTabSelectedListener(new TabLayout.OnTabSelectedListener() {
      @Override
      public void onTabSelected(TabLayout.Tab tab) {
        viewPager2.setCurrentItem(tab.getPosition(), false);
      }

      @Override
      public void onTabUnselected(TabLayout.Tab tab) {

      }

      @Override
      public void onTabReselected(TabLayout.Tab tab) {

      }
    });


    // viewPager2 滑动监听 设置tab选中
    viewPager2.registerOnPageChangeCallback(new ViewPager2.OnPageChangeCallback() {
      @Override
      public void onPageSelected(int position) {
        tabLayout.selectTab(tabLayout.getTabAt(position));
      }
    });

}


```


### 总结

viewPager2的优点还是有目共睹的, 但是还是要去踩坑啊，我之前也是用`viewPager2` 但是没用对的话是会产生内存泄露的
所以就研究了一下 在这里记录一下问题 参考了很多博客 基本上都不咋解决问题


只有下面这四篇有帮助 感谢!!!

第一篇、 [ViewPager2重大更新](https://hitendev.github.io/2019/05/14/ViewPager2%E9%87%8D%E5%A4%A7%E6%9B%B4%E6%96%B0%EF%BC%8C%E6%94%AF%E6%8C%81offscreenPageLimit/)

第二篇、 [ViewPager2主要功能](https://juejin.cn/post/6844904114124652558#heading-9)

第三篇、 [解决ViewPager2+TabLayout懒加载问题，Fragment被创建多次](https://www.jianshu.com/p/2cc7bdd50c6c)

第四篇、[android ViewPager2+TabLayout、滑动效果相关问题！](https://blog.csdn.net/BirdEatBug/article/details/117414954)

**另外**

LeakCanary --> [内存泄漏检测工具](https://square.github.io/leakcanary/getting_started/)

使用非常简单 

只需要 在build.gradle 中 添加这么一行就可以了 只会在debug模式下检测 不会影响到 正式环境

```
dependencies {
  // debugImplementation because LeakCanary should only run in debug builds.
  debugImplementation 'com.squareup.leakcanary:leakcanary-android:2.7'
}
```
