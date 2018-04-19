[Activity官方文档](https://developer.android.com/guide/components/activities.html?hl=zh-cn)


### 生命周期

![生命周期](https://developer.android.com/images/activity_lifecycle.png?hl=zh-cn)

1. **onCreate()** 首次创建 Activity 时调用。 您应该在此方法中执行所有正常的静态设置 — 创建视图、将数据绑定到列表等等。 
2. **onContentChanged()** 当Activity的布局改动时，即setContentView()或者addContentView()方法执行完毕时就会调用该方法， 例如，Activity中各种View的findViewById()方法都可以放到该方法中。
3. **onPostCreate()** onCreate方法彻底执行完毕的回调
4. **onRestart()** 在 Activity 已停止并即将再次启动前调用。
5. **onStart()** 在 Activity 即将对用户可见之前调用。
6. **onResume()** 在 Activity 即将开始与用户进行交互之前调用。 此时，Activity 处于 Activity 堆栈的顶层，并具有用户输入焦点。
7. **onPostResume()** onResume方法彻底执行完毕
8. **onPause()** 当系统即将开始继续另一个 Activity 时调用。 此方法通常用于确认对持久性数据的未保存更改、停止动画以及其他可能消耗 CPU 的内容，诸如此类。 它应该非常迅速地执行所需操作，因为它返回后，下一个 Activity 才能继续执行。
9. **onStop()** 在 Activity 对用户不再可见时调用。如果 Activity 被销毁，或另一个 Activity（一个现有 Activity 或新 Activity）继续执行并将其覆盖，就可能发生这种情况。
10. **onDestroy()** 在 Activity 被销毁前调用。

### 保存Activity状态

![save](https://developer.android.com/images/fundamentals/restore_instance.png?hl=zh-cn)

1. **onSaveInstanceState()** 系统会先调用 onSaveInstanceState()，然后再使 Activity 变得易于销毁。系统会向该方法传递一个 Bundle，您可以在其中使用 putString() 和 putInt() 等方法以名称-值对形式保存有关 Activity 状态的信息。
2. **onRestoreInstanceState()** 如果系统终止您的应用进程，并且用户返回您的 Activity，则系统会重建该 Activity，并将 Bundle 同时传递给 onCreate() 和 onRestoreInstanceState()。


### 启动模式

Activity的启动模式有4种，分别是standard， singleTop， SingleTask，singleInstance，可以在AndroidMainifest.xml文件中指定每一个Activity的启动模式。

1. Standard 是Android的默认启动模式，你不在配置文件中做任何设置，那么这个Activity就是standard模式，这种模式下，Activity可以有多个实例，每次启动Activity，无论任务栈中是否已经有这个Activity的实例，系统都会创建一个新的Activity实例，以下是实验验证。
2. SingleTop singleTop模式的Activity已经位于任务栈的栈顶，再去启动它时，不会再创建新的实例,如果不位于栈顶，就会创建新的实例，现在把配置文件中FirstActivity的启动模式改为SingleTop，我们的应用只有一个Activity，FirstActivity自然处于任务栈的栈顶。对于每次启动Activity，会调用onNewIntent()函数
3. SingleTask  SingleTask模式的Activity在同一个Task内只有一个实例，如果Activity已经位于栈顶，系统不会创建新的Activity实例，和singleTop模式一样。但Activity已经存在但不位于栈顶时，系统就会把该Activity移到栈顶，并把它上面的activity出栈。修改上面的程序，新建一个SecondActivity,将FirstActivity设置为singleTask启动模式，并让它启动SecondActivity，再让SecondActivity来启动FirstActivity。
4. SingleInstance 而singleInstance Activity在整个系统里只有一个实例，启动一singleInstanceActivity时，系统会创建一个新的任务栈，并且这个任务栈只有他一个Activity。SingleInstance模式并不常用，如果我们把一个Activity设置为singleInstance模式，你会发现它启动时会慢一些，切换效果不好，影响用户体验。它往往用于多个应用之间，例如一个电视launcher里的Activity，通过遥控器某个键在任何情况可以启动，这个Activity就可以设置为singleInstance模式，当在某应用中按键启动这个Activity，处理完后按返回键，就会回到之前启动它的应用，不影响用户体验。