[官方文档](https://developer.android.com/reference/android/view/View.html)

[View的绘制过程](https://blog.csdn.net/guolin_blog/article/details/16330267)

### 绘制过程

#### onMeasure() 

measure是测量的意思，那么onMeasure()方法顾名思义就是用于测量视图的大小的。View系统的绘制流程会从ViewRoot的performTraversals()方法中开始，在其内部调用View的measure()方法。measure()方法接收两个参数，widthMeasureSpec和heightMeasureSpec，这两个值分别用于确定视图的宽度和高度的规格和大小。

    1. EXACTLY 表示父视图希望子视图的大小应该是由specSize的值来决定的，系统默认会按照这个规则来设置子视图的大小，开发人员当然也可以按照自己的意愿设置成任意的大小。
    2. AT_MOST 表示子视图最多只能是specSize中指定的大小，开发人员应该尽可能小得去设置这个视图，并且保证不会超过specSize。系统默认会按照这个规则来设置子视图的大小，开发人员当然也可以按照自己的意愿设置成任意的大小。
    3. UNSPECIFIED 表示开发人员可以将视图按照自己的意愿设置成任意的大小，没有任何限制。这种情况比较少见，不太会用到。

#### onLayout()

measure过程结束后，视图的大小就已经测量好了，接下来就是layout的过程了。正如其名字所描述的一样，这个方法是用于给视图进行布局的，也就是确定视图的位置。ViewRoot的performTraversals()方法会在measure结束后继续执行，并调用View的layout()方法来执行此过程

```
host.layout(0, 0, host.mMeasuredWidth, host.mMeasuredHeight); 
```

View中的onLayout()方法就是一个空方法，因为onLayout()过程是为了确定视图在布局中所在的位置，而这个操作应该是由布局来完成的，即父视图决定子视图的显示位置。既然如此，我们来看下ViewGroup中的onLayout()方法是怎么写的吧，代码如下：

```
protected abstract void onLayout(boolean changed, int l, int t, int r, int b);  
```


#### onDraw()

measure和layout的过程都结束后，接下来就进入到draw的过程了。同样，根据名字你就能够判断出，在这里才真正地开始对视图进行绘制。ViewRoot中的代码会继续执行并创建出一个Canvas对象，然后调用View的draw()方法来执行具体的绘制工作。draw()方法内部的绘制过程总共可以分为六步，其中第二步和第五步在一般情况下很少用到，因此这里我们只分析简化后的绘制过程。


### 事件分发

#### 点击事件

当用户触摸屏幕时（View 或 ViewGroup派生的控件），将产生点击事件（Touch事件） onTouchEvent() 

1. MotionEvent.ACTION_DOWN View被点下
2. MotionEvent.ACTION_UP 抬起
3. MotionEvent.ACTION_MOVE 移动
4. MotionEvent.ACTION_CANCEL 结束（非人为）

![onTouchEvent过程](https://upload-images.jianshu.io/upload_images/944365-79b1e86793514e99.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

##### TouchSlop

TouchSlop是系统所能识别出的被认为是滑动的最小距离，换句话说，当手指在屏幕上滑动时，如果两次滑动之间的距离小于这个常量，那么系统就不认为你是在进行滑动操作。


##### GestureDetector

手势检测，辅助用户的单击滑动行为。

```
“boolean consume = mGestureDetector.onTouchEvent(event);
    return consume;”
```



#### 事件分发

事件的分发过程由三个很重要的方法来共同完成：dispatchTouchEvent、onInterceptTouchEvent和onTouchEvent


1. dispatchTouchEvent() 
  来进行事件的分发。如果事件能够传递给当前View，那么此方法一定会被调用，返回结果受当前View的onTouchEvent和下级View的dispatchTouchEvent方法的影响，表示是否消耗当前事件
2. onTouchEvent() 处理点击事件
3. onInterceptTouchEvent() 判断是否拦截了某个事件 (在ViewGroup的dispatchTouchEvent()方法中调用)

