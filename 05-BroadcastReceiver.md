[官方文档](https://developer.android.com/reference/android/content/BroadcastReceiver.html)

### 广播的类型

* 普通广播
* 系统广播
* 有序广播
* 粘性广播
* App应用内广播


#### 1. 普通广播 

```
Intent intent = new Intent();
//对应BroadcastReceiver中intentFilter的action
intent.setAction(BROADCAST_ACTION);
//发送广播
sendBroadcast(intent);

```

若被注册了的广播接收者中注册时intentFilter的action与上述匹配，则会接收此广播（即进行回调onReceive())

#### 2. 系统广播

Android中内置了多个系统广播：只要涉及到手机的基本操作（如开机、网络状态变化、拍照等等），都会发出相应的广播

比如监听网络变化 -> android.net.conn.CONNECTIVITY_CHANGE

#### 3. 有序广播

发送出去的广播被广播接收者按照先后顺序接收（对于广播接受者而言）

广播接收者接受按照先后顺序接受



* 按照Priority属性值从大-小排序；
Priority属性相同者，
* 动态注册的广播优先；
* 接收广播按顺序接收
* 先接收的广播接收者可以对广播进行截断，即后接收的广播接收者不再接收到此广播；
* 先接收的广播接收者可以对广播进行修改，那么后接收的广播接收者将接收到被修改后的广播

```
sendOrderedBroadcast(intent);
```

#### App内广播

使用App应用内广播（Local Broadcast）

* App应用内广播可理解为一种局部广播，广播的发送者和接收者都同属于一个App。
* 相比于全局广播（普通广播），App应用内广播优势体现在：安全性高 & 效率高

1. 实现一

通过intent.setPackage(packageName)指定报名

    * 注册广播时将exported属性设置为false，使得非本App内部发出的此广播不被接收；
    * 在广播发送和接收时，增设相应权限permission，用于权限验证；
    * 发送广播时指定该广播接收器所在的包名，此广播将只会发送到此包中的App内与之相匹配的有效广播接收器中。 

2. 实现二
使用封装好的LocalBroadcastManager类 

```
//注册应用内广播接收器
//步骤1：实例化BroadcastReceiver子类 & IntentFilter mBroadcastReceiver 
mBroadcastReceiver = new mBroadcastReceiver(); 
IntentFilter intentFilter = new IntentFilter(); 

//步骤2：实例化LocalBroadcastManager的实例
localBroadcastManager = LocalBroadcastManager.getInstance(this);

//步骤3：设置接收广播的类型 
intentFilter.addAction(android.net.conn.CONNECTIVITY_CHANGE);

//步骤4：调用LocalBroadcastManager单一实例的registerReceiver（）方法进行动态注册 
localBroadcastManager.registerReceiver(mBroadcastReceiver, intentFilter);

//取消注册应用内广播接收器
localBroadcastManager.unregisterReceiver(mBroadcastReceiver);

//发送应用内广播
Intent intent = new Intent();
intent.setAction(BROADCAST_ACTION);
localBroadcastManager.sendBroadcast(intent);

```
    

#### 粘性广播

粘性广播会永久保存发出去的广播，当有新的广播接收者被注册的时候，会收到粘性广播。

由于在Android5.0 & API 21中已经失效，所以不建议使用，在这里也不作过多的总结。

