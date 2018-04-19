[官方文档](https://developer.android.com/guide/components/fragments.html?hl=zh-cn)

### 生命周期

![生命周期](https://developer.android.com/images/fragment_lifecycle.png?hl=zh-cn)

![生命周期](https://raw.githubusercontent.com/xiazdong/blog-image/master/fragment-02.png)

1. **onAttach()** 在片段已与 Activity 关联时调用（Activity 传递到此方法内）。
2. **onCreate()** 系统会在创建片段时调用此方法。您应该在实现内初始化您想在片段暂停或停止后恢复时保留的必需片段组件。
3. **onCreateView()** 系统会在片段首次绘制其用户界面时调用此方法。 要想为您的片段绘制 UI，您从此方法中返回的 View 必须是片段布局的根视图。如果片段未提供 UI，您可以返回 null。
4. **onActivityCreated()** 在 Activity 的 onCreate() 方法已返回时调用。
5. **onPause()** 系统将此方法作为用户离开片段的第一个信号（但并不总是意味着此片段会被销毁）进行调用。 您通常应该在此方法内确认在当前用户会话结束后仍然有效的任何更改（因为用户可能不会返回）。
6. **onDestroyView()** 在移除与片段关联的视图层次结构时调用。
7. **onDetach()** 在取消片段与 Activity 的关联时调用。

### 在Activity中添加Fragment

通过 FragmentTransaction transaction = fm.benginTransatcion() 开启事物添加Fragment，最后执行commit()

1. add() 往Activity中添加一个Fragment
2. remove() 从Activity中移除一个Fragment，如果被移除的Fragment没有添加到回退栈（回退栈后面会详细说），这个Fragment实例将会被销毁。
3. replace() 使用另一个Fragment替换栈顶的fragment
4. hide() 隐藏当前的Fragment，仅仅是设为不可见，并不会销毁
5. show() 显示之前隐藏的Fragment
6. detach() 会将view从UI中移除,和remove()不同,此时fragment的状态依然由FragmentManager维护。
7. attach() 重建view视图，附加到UI上并显示。
8. commit() 提交一个事务


### ViewPager + Fragment

ViewPager是support v4库中提供界面滑动的类，继承自ViewGroup。PagerAdapter是ViewPager的适配器类，为ViewPager提供界面。但是一般来说，通常都会使用PagerAdapter的两个子类：FragmentPagerAdapter和FragmentStatePagerAdapter作为ViewPager的适配器，他们的特点是界面是Fragment。

默认，ViewPager会缓存当前页相邻的界面，比如当滑动到第2页时，会初始化第1页和第3页的界面（即Fragment对象，且生命周期函数运行到onResume()），可以通过setOffscreenPageLimit(count)设置离线缓存的界面个数。

#### FragmentPagerAdapter&FragmentStatePagerAdapter

1. FragmentPagerAdapter(FragmentManager fm) 构造函数，参数为FragmentManager。如果是嵌套Fragment场景，子PagerAdapter的参数传入getChildFragmentManager()。
2. Fragment getItem(int position) 返回第position位置的Fragment，必须重写。
3. int getCount() 返回ViewPager的页数，必须重写。
4. Object instantiateItem(ViewGroup container, int position) container是ViewPager对象，返回第position位置的Fragment。
5. void destroyItem(ViewGroup container, int position, Object object) container是ViewPager对象，object是Fragment对象。
6. getItemPosition(Object object)  object是Fragment对象，如果返回POSITION_UNCHANGED，则表示当前Fragment不刷新，如果返回POSITION_NONE，则表示当前Fragment需要调用destroyItem()和instantiateItem()进行销毁和重建。 默认情况下返回POSITION_UNCHANGED。

#### 懒加载

懒加载主要用于ViewPager且每页是Fragment的情况，场景为微信主界面，底部有4个tab，当滑到另一个tab时，先显示”正在加载”，过一会才会显示正常界面。

默认情况，ViewPager会缓存当前页和左右相邻的界面。实现懒加载的主要原因是：用户没进入的界面需要有一系列的网络、数据库等耗资源、耗时的操作，预先做这些数据加载是不必要的。

懒加载主要依赖Fragment的setUserVisibleHint(boolean isVisible)方法，当Fragment变为可见时，会调用setUserVisibleHint(true)；当Fragment变为不可见时，会调用setUserVisibleHint(false)，且该方法调用时机：

* onAttach()之前，调用setUserVisibleHint(false)。
* onCreateView()之前，如果该界面为当前页，则调用setUserVisibleHint(true)，否则调用setUserVisibleHint(false)。
* 界面变为可见时，调用setUserVisibleHint(true)。
* 界面变为不可见时，调用setUserVisibleHint(false)。


