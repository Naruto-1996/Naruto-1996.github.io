---
title: Android自定义EditText一键清除和密文查看
date: 2020-12-18 16:26:41
categories:
- Android
tags:
- EditText
- 密文
- 一键清除
---


#### Android自定义EditText一键清除和密文查看

* 先上截屏看一下效果
![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/S01218-16245606.jpg)


##### 自定义步骤

##### 1、首先新建一个类PowerfulEditText.java 我放在了widgets目录中这个目录平时就用来存放一些自定义的类

```java

//  这个自定义EditText 有两个按钮 一键清空按钮 和 小眼睛查看明文
//  isPassword 字段会根据输入框是否是密码框来决定是否会绘制小眼睛 很好用
public class PowerfulEditText extends androidx.appcompat.widget.AppCompatEditText {

  private static final String TAG = "PowerfulEditText.java";

  private static final int DEFAULT_CLEAR_RES = R.drawable.delete_edit;
  private static final int DEFAULT_VISIBLE_RES = R.drawable.black_open_eyes;
  private static final int DEFAULT_INVISIBLE_RES = R.drawable.black_close_eyes;

  private static final int DEFAULT_STYLE_COLOR = Color.BLUE;

  private final int DEFAULT_BUTTON_PADDING =
    getResources().getDimensionPixelSize(R.dimen.btn_edit_text_padding);
  private final int DEFAULT_BUTTON_WIDTH =
    getResources().getDimensionPixelSize(R.dimen.btn_edit_text_width);

  private static final String STYLE_RECT = "rectangle";
  private static final String STYLE_ROUND_RECT = "roundRect";
  private static final String STYLE_HALF_RECT = "halfRect";
  private static final String STYLE_ANIMATOR = "animator";

  private static final int DEFAULT_ROUND_RADIUS = 20;
  private static final int ANIMATOR_TIME = 200;
  private static final int DEFAULT_FOCUSED_STROKE_WIDTH = 8;
  private static final int DEFAULT_UNFOCUSED_STROKE_WIDTH = 4;

  //按钮间隔
  private int mBtnPadding = 0;
  //按钮宽度
  private int mBtnWidth = 0;
  //右内边距
  private int mTextPaddingRight;

  private int mClearResId = 0;
  private int mVisibleResId = 0;
  private int mInvisibleResId = 0;
  private Bitmap mBitmapClear;
  private Bitmap mBitmapVisible;
  private Bitmap mBitmapInvisible;

  private String mBorderStyle = "";
  private int mStyleColor = -1;

  //出现和消失动画
  private ValueAnimator mGoneAnimator;
  private ValueAnimator mVisibleAnimator;
  //状态值
  private boolean isBtnVisible = false;
  // isPassword 字段会根据输入框是否是密码框来决定是否会绘制小眼睛
  private boolean isPassword = false;
  private boolean isPasswordVisible = false;

  private boolean isAnimatorRunning = false;
  private int mAnimatorProgress = 0;
  private ObjectAnimator mAnimator;

  //自定义属性动画
  private static final Property<PowerfulEditText, Integer> BORDER_PROGRESS
    = new Property<PowerfulEditText, Integer>(Integer.class, "borderProgress") {
    @Override
    public Integer get(PowerfulEditText powerfulEditText) {
      return powerfulEditText.getBorderProgress();
    }

    @Override
    public void set(PowerfulEditText powerfulEditText, Integer value) {
      powerfulEditText.setBorderProgress(value);
    }
  };

  private Paint mPaint;

  public PowerfulEditText(Context context) {
    this(context, null);
  }

  public PowerfulEditText(Context context, @Nullable AttributeSet attrs) {
    super(context, attrs);
    init(context, attrs);
  }

  public PowerfulEditText(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
    super(context, attrs, defStyleAttr);
    init(context, attrs);
  }

  private void init(Context context, AttributeSet attrs) {
    //抗锯齿和位图滤波
    mPaint = new Paint(Paint.ANTI_ALIAS_FLAG | Paint.FILTER_BITMAP_FLAG);

    //读取xml文件中的配置
    if (attrs != null) {
      TypedArray array = context.obtainStyledAttributes(attrs, R.styleable.PowerfulEditText);
      for (int i = 0; i < array.getIndexCount(); i++) {
        int attr = array.getIndex(i);

        switch (attr) {
          case R.styleable.PowerfulEditText_clearDrawable:
            mClearResId = array.getResourceId(attr, DEFAULT_CLEAR_RES);
            break;

          case R.styleable.PowerfulEditText_visibleDrawable:
            mVisibleResId = array.getResourceId(attr, DEFAULT_VISIBLE_RES);
            break;

          case R.styleable.PowerfulEditText_invisibleDrawable:
            mInvisibleResId = array.getResourceId(attr, DEFAULT_INVISIBLE_RES);
            break;

          case R.styleable.PowerfulEditText_BtnWidth:
            mBtnWidth = array.getDimensionPixelSize(attr, DEFAULT_BUTTON_WIDTH);
            break;

          case R.styleable.PowerfulEditText_BtnSpacing:
            mBtnPadding = array.getDimensionPixelSize(attr, DEFAULT_BUTTON_PADDING);
            break;

          case R.styleable.PowerfulEditText_borderStyle:
            mBorderStyle = array.getString(attr);
            break;

          case R.styleable.PowerfulEditText_styleColor:
            mStyleColor = array.getColor(attr, DEFAULT_STYLE_COLOR);
            break;
        }
      }
      array.recycle();
    }

    //初始化按钮显示的Bitmap
    mBitmapClear = createBitmap(context, mClearResId, DEFAULT_CLEAR_RES);
    mBitmapVisible = createBitmap(context, mVisibleResId, DEFAULT_VISIBLE_RES);
    mBitmapInvisible = createBitmap(context, mInvisibleResId, DEFAULT_INVISIBLE_RES);
    //如果自定义，则使用自定义的值，否则使用默认值
    if (mBtnPadding == 0) {
      mBtnPadding = DEFAULT_BUTTON_PADDING;
    }
    if (mBtnWidth == 0) {
      mBtnWidth = DEFAULT_BUTTON_WIDTH;
    }
    //给文字设置一个padding，避免文字和按钮重叠了
    mTextPaddingRight = mBtnPadding * 4 + mBtnWidth * 2;

    //按钮出现和消失的动画
    mGoneAnimator = ValueAnimator.ofFloat(1f, 0f).setDuration(ANIMATOR_TIME);
    mVisibleAnimator = ValueAnimator.ofFloat(0f, 1f).setDuration(ANIMATOR_TIME);

    //是否是密码样式
    isPassword =
      getInputType() == (InputType.TYPE_TEXT_VARIATION_PASSWORD | InputType.TYPE_CLASS_TEXT);

  }

  @Override
  protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
    super.onMeasure(widthMeasureSpec, heightMeasureSpec);

    //设置右内边距, 防止清除按钮和文字重叠
    setPadding(getPaddingLeft(), getPaddingTop(), mTextPaddingRight, getPaddingBottom());
  }

  @Override
  protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);

    mPaint.setStyle(Paint.Style.STROKE);

    //使用自定义颜色。如未定义，则使用默认颜色
    if (mStyleColor != -1) {
      mPaint.setColor(mStyleColor);
    } else {
      mPaint.setColor(DEFAULT_STYLE_COLOR);
    }

    //控件获取焦点时，加粗边框
    if (isFocused()) {
      mPaint.setStrokeWidth(DEFAULT_FOCUSED_STROKE_WIDTH);
    } else {
      mPaint.setStrokeWidth(DEFAULT_UNFOCUSED_STROKE_WIDTH);
    }

    //绘制边框
    drawBorder(canvas);
    //绘制清空和明文显示按钮
    drawButtons(canvas);
  }

  private void drawBorder(Canvas canvas) {
    int width = getWidth();
    int height = getHeight();

    switch (mBorderStyle) {
      //矩形样式
      case STYLE_RECT:
        setBackground(null);
        canvas.drawRect(0, 0, width, height, mPaint);
        break;

      //圆角矩形样式
      case STYLE_ROUND_RECT:
        setBackground(null);
        float roundRectLineWidth = 0;
        if (isFocused()) {
          roundRectLineWidth = DEFAULT_FOCUSED_STROKE_WIDTH / 2;
        } else {
          roundRectLineWidth = DEFAULT_UNFOCUSED_STROKE_WIDTH / 2;
        }
        mPaint.setStrokeWidth(roundRectLineWidth);
        if (Build.VERSION.SDK_INT >= 21) {
          canvas.drawRoundRect(
            roundRectLineWidth / 2, roundRectLineWidth / 2, width - roundRectLineWidth / 2, height - roundRectLineWidth / 2,
            DEFAULT_ROUND_RADIUS, DEFAULT_ROUND_RADIUS,
            mPaint);
        } else {
          canvas.drawRoundRect(
            new RectF(roundRectLineWidth / 2, roundRectLineWidth / 2, width - roundRectLineWidth / 2, height - roundRectLineWidth / 2),
            DEFAULT_ROUND_RADIUS, DEFAULT_ROUND_RADIUS,
            mPaint);
        }
        break;

      //半矩形样式
      case STYLE_HALF_RECT:
        setBackground(null);
        canvas.drawLine(0, height, width, height, mPaint);
        canvas.drawLine(0, height / 2, 0, height, mPaint);
        canvas.drawLine(width, height / 2, width, height, mPaint);
        break;

      //动画特效样式
      case STYLE_ANIMATOR:
        setBackground(null);
        if (isAnimatorRunning) {
          canvas.drawLine(width / 2 - mAnimatorProgress, height, width / 2 + mAnimatorProgress, height, mPaint);
          if (mAnimatorProgress == width / 2) {
            isAnimatorRunning = false;
          }
        } else {
          canvas.drawLine(0, height, width, height, mPaint);
        }
        break;
    }
  }

  private void drawButtons(Canvas canvas) {
    if (isBtnVisible) {
      //播放按钮出现的动画
      if (mVisibleAnimator.isRunning()) {
        float scale = (float) mVisibleAnimator.getAnimatedValue();
        drawClearButton(scale, canvas);
        if (isPassword) {
          drawVisibleButton(scale, canvas, isPasswordVisible);
        }
        invalidate();
        //绘制静态的按钮
      } else {
        drawClearButton(1, canvas);
        if (isPassword) {
          drawVisibleButton(1, canvas, isPasswordVisible);
        }
      }
    } else {
      //播放按钮消失的动画
      if (mGoneAnimator.isRunning()) {
        float scale = (float) mGoneAnimator.getAnimatedValue();
        drawClearButton(scale, canvas);
        if (isPassword) {
          drawVisibleButton(scale, canvas, isPasswordVisible);
        }
        invalidate();
      }
    }
  }

  private void drawClearButton(float scale, Canvas canvas) {

    int right = (int) (getWidth() + getScrollX() - mBtnPadding - mBtnWidth * (1f - scale) / 2f);
    int left = (int) (getWidth() + getScrollX() - mBtnPadding - mBtnWidth * (scale + (1f - scale) / 2f));
    int top = (int) ((getHeight() - mBtnWidth * scale) / 2);
    int bottom = (int) (top + mBtnWidth * scale);
    Rect rect = new Rect(left, top, right, bottom);
    canvas.drawBitmap(mBitmapClear, null, rect, mPaint);
  }

  private void drawVisibleButton(float scale, Canvas canvas, boolean isVisible) {

    int right = (int) (getWidth() + getScrollX() - mBtnPadding * 3 - mBtnWidth - mBtnWidth * (1f - scale) / 2f);
    int left = (int) (getWidth() + getScrollX() - mBtnPadding * 3 - mBtnWidth - mBtnWidth * (scale + (1f - scale) / 2f));
    int top = (int) ((getHeight() - mBtnWidth * scale) / 2);
    int bottom = (int) (top + mBtnWidth * scale);
    Rect rect = new Rect(left, top, right, bottom);
    if (isVisible) {
      canvas.drawBitmap(mBitmapVisible, null, rect, mPaint);
    } else {
      canvas.drawBitmap(mBitmapInvisible, null, rect, mPaint);
    }

  }

  // 清除按钮出现时的动画效果
  private void startVisibleAnimator() {
    endAllAnimator();
    mVisibleAnimator.start();
    invalidate();
  }

  // 清除按钮消失时的动画效果
  private void startGoneAnimator() {
    endAllAnimator();
    mGoneAnimator.start();
    invalidate();
  }

  // 结束所有动画
  private void endAllAnimator() {
    mGoneAnimator.end();
    mVisibleAnimator.end();
  }

  @Override
  protected void onFocusChanged(boolean focused, int direction, Rect previouslyFocusedRect) {
    super.onFocusChanged(focused, direction, previouslyFocusedRect);

    //播放按钮出现和消失动画
    if (focused && getText().length() > 0) {
      if (!isBtnVisible) {
        isBtnVisible = true;
        startVisibleAnimator();
      }
    } else {
      if (isBtnVisible) {
        isBtnVisible = false;
        startGoneAnimator();
      }
    }

    //实现动画特效样式
    if (focused && mBorderStyle.equals(STYLE_ANIMATOR)) {
      isAnimatorRunning = true;
      mAnimator = ObjectAnimator.ofInt(this, BORDER_PROGRESS, 0, getWidth() / 2);
      mAnimator.setDuration(ANIMATOR_TIME);
      mAnimator.start();
    }
  }

  protected void setBorderProgress(int borderProgress) {
    mAnimatorProgress = borderProgress;
    postInvalidate();
  }

  protected int getBorderProgress() {
    return mAnimatorProgress;
  }

  @Override
  protected void onTextChanged(CharSequence text, int start, int lengthBefore, int lengthAfter) {
    super.onTextChanged(text, start, lengthBefore, lengthAfter);

    if (text.length() > 0 && isFocused()) {
      if (!isBtnVisible) {
        isBtnVisible = true;
        startVisibleAnimator();
      }
    } else {
      if (isBtnVisible) {
        isBtnVisible = false;
        startGoneAnimator();
      }
    }
  }

  @Override
  public boolean onTouchEvent(MotionEvent event) {
    if (event.getAction() == MotionEvent.ACTION_UP) {

      boolean clearTouched =
        (getWidth() - mBtnPadding - mBtnWidth < event.getX())
          && (event.getX() < getWidth() - mBtnPadding)
          && isFocused();
      boolean visibleTouched =
        (getWidth() - mBtnPadding * 3 - mBtnWidth * 2 < event.getX())
          && (event.getX() < getWidth() - mBtnPadding * 3 - mBtnWidth)
          && isPassword && isFocused();

      if (clearTouched) {
        setError(null);
        setText("");
        return true;
      } else if (visibleTouched) {
        if (isPasswordVisible) {
          isPasswordVisible = false;
          setInputType(InputType.TYPE_TEXT_VARIATION_PASSWORD | InputType.TYPE_CLASS_TEXT);
          setSelection(getText().length());
          invalidate();
        } else {
          isPasswordVisible = true;
          setInputType(InputType.TYPE_TEXT_VARIATION_VISIBLE_PASSWORD);
          setSelection(getText().length());
          invalidate();
        }
        return true;
      }
    }
    return super.onTouchEvent(event);
  }

  // 开始晃动的入口
  public void startShakeAnimation() {
    if (getAnimation() == null) {
      setAnimation(shakeAnimation(4));
    }
    startAnimation(getAnimation());
  }

  /**
   * 晃动动画
   *
   * @param counts 0.5秒钟晃动多少下
   * @return
   */
  private Animation shakeAnimation(int counts) {
    Animation translateAnimation = new TranslateAnimation(0, 10, 0, 0);
    translateAnimation.setInterpolator(new CycleInterpolator(counts));
    translateAnimation.setDuration(500);
    return translateAnimation;
  }

  private Bitmap createBitmap(Context context, int resId, int defResId) {
    if (resId != 0) {
      return BitmapFactory.decodeResource(context.getResources(), resId);
    } else {
      return BitmapFactory.decodeResource(context.getResources(), defResId);
    }
  }
}

```

这个类里面会用到values目录中的attrs.xml 和 dimens.xml 如果没有就自行创建

##### 2、往attrs.xml中写入自定义EditText的属性

```xml

<resources>
 <!--  自定义EditText的属性-->
  <declare-styleable name="PowerfulEditText">
    <!--图片资源-->
    <attr name="clearDrawable" format="reference" />
    <attr name="visibleDrawable" format="reference" />
    <attr name="invisibleDrawable" format="reference" />
    <!--按钮宽度大小-->
    <attr name="BtnWidth" format="dimension" />
    <!--按钮间距大小-->
    <attr name="BtnSpacing" format="dimension" />
    <!--边框颜色和样式-->
    <attr name="styleColor" format="color" />
    <attr name="borderStyle" format="string" />
  </declare-styleable>
</resources>

```

##### 3、往dimens.xml中写入一些大小配置

```xml

<resources>
  <!-- Default screen margins, per the Android Design guidelines. 自定义editText的属性设置 -->
  <dimen name="activity_horizontal_margin">16dp</dimen>
  <dimen name="activity_vertical_margin">16dp</dimen>
  <!--按钮间距大小-->
  <dimen name="btn_edit_text_padding">8dp</dimen>
  <!--按钮宽度大小-->
  <dimen name="btn_edit_text_width">16dp</dimen>
</resources>

```

##### 4、在布局文件中使用

````xml

<com.你的包名.widgets.PowerfulEditText
          android:id="@+id/password_edit"
          android:layout_width="match_parent"
          android:layout_height="45dp"
          android:background="@null"
          android:paddingStart="12dp"
          android:textSize="15sp"
          android:hint="请输入密码"
          app:BtnWidth="@dimen/btn_edit_text_width"
          app:BtnSpacing="@dimen/btn_edit_text_padding"
          android:inputType="textPassword"
          tools:ignore="RtlSymmetry" />

````

一共是7个样式可以在xml中使用

```

app:clearDrawable="@drawable/clear_all"  
app:visibleDrawable="@drawable/visible"  
app:invisibleDrawable="@drawable/invisible"  
app:BtnWidth="@dimen/btn_edittext_width"  
app:BtnSpacing="@dimen/btn_edittext_padding"  
app:borderStyle="halfRect"  
app:styleColor="#ff0000"  

```

其中，边框样式的对应规则如下。

```

1、矩形样式：         app:borderStyle="rectangle"

2、半矩形样式：       app:borderStyle="halfRect"

3、圆角矩形样式：     app:borderStyle="roundRect"

4、动画特效样式：     app:borderStyle="animator"

控件抖动的效果也是利用动画实现的。调用view实例的以下方法即可实现抖动。

mPEditText.startShakeAnimation()

```

也可以使用依赖的方式去项目中用，不过如果作者不维护, 那你的项目也就需要改动了

作者的[github链接](https://github.com/AndroidWJC/PowerfulEditText)
