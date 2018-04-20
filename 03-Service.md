://developer.android.com/guide/components/services.html?hl=zh-cn)

[top]

### 简介

Service 是一个可以在后台执行长时间运行操作而不提供用户界面的应用组件。服务可由其他应用组件启动，而且即使用户切换到其他应用，服务仍将在后台继续运行。 此外，组件可以绑定到服务，以与之进行交互，甚至是执行进程间通信 (IPC)。 例如，服务可以处理网络事务、播放音乐，执行文件 I/O 或与内容提供程序交互，而所有这一切均可在后台进行。

### 生命周期

![image](https://developer.android.com/images/service_lifecycle.png?hl=zh-cn)

1. onStartCommand() 当另一个组件（如Activity）通过调用startService()请求启动服务时，系统将调用此方法。一旦执行此方法，服务即会启动并可在后台无限期运行。如果您实现此方法，则在服务工作完成后，需要由您通过调用 stopSelf() 或 stopService() 来停止服务。（如果您只想提供绑定，则无需实现此方法。）
2. onBind() 当另一个组件想通过调用bindService()与服务绑定（例如执行RPC）时，系统将调用此方法。在此方法的实现中，您必须通过返回 IBinder 提供一个接口，供客户端用来与服务进行通信。请务必实现此方法，但如果您并不希望允许绑定，则应返回 null。
3. onCreate() 首次创建服务时，系统将调用此方法来执行一次性设置程序（在调用onStartCommand()或onBind()之前）。如果服务已在运行，则不会调用此方法。
4. onDestroy() 当服务不再使用且将被销毁时，系统将调用此方法。服务应该实现此方法来清理所有资源，如线程、注册的侦听器、接收器等。 这是服务接收的最后一个调用。


### 创建启动service


#### 启动服务

onCreate() -> onStartCommand() -> onDestory()

1. startService() 服务启动之后，其生命周期即独立于启动它的组件，并且可以在后台无限期地运行，即使启动服务的组件已被销毁也不受影响。 因此，服务应通过调用 stopSelf() 结束工作来自行停止运行，或者由另一个组件通过调用 stopService() 来停止它。
2. stopService() 关闭服务

#### 绑定服务

onCreate() -> onBind() -> onUnBind() -> onDestory()

1. bindService() 该服务在另一个组件（客户端）调用 bindService() 时创建。然后，客户端通过 IBinder 接口与服务进行通信。客户端可以通过调用 unbindService() 关闭连接。多个客户端可以绑定到相同服务，而且当所有绑定全部取消后，系统即会销毁该服务。 （服务不必自行停止运行。）
2. unBindService() 关闭连接。

#### 注意

这两条路径并非完全独立。也就是说，您可以绑定到已经使用 startService() 启动的服务。例如，可以通过使用 Intent（标识要播放的音乐）调用 startService() 来启动后台音乐服务。随后，可能在用户需要稍加控制播放器或获取有关当前播放歌曲的信息时，Activity 可以通过调用 bindService() 绑定到服务。在这种情况下，除非所有客户端均取消绑定，否则 stopService() 或 stopSelf() 不会实际停止服务。


### IntentService

由于大多数启动服务都不必同时处理多个请求（实际上，这种多线程情况可能很危险），因此使用IntentService类实现服务也许是最好的选择。

* 创建默认的工作线程，用于在应用的主线程外执行传递给 onStartCommand() 的所有 Intent。
* 创建工作队列，用于将 Intent 逐一传递给 onHandleIntent() 实现，这样您就永远不必担心多线程问题。
* 在处理完所有启动请求后停止服务，因此您永远不必调用 stopSelf()。
* 提供 onBind() 的默认实现（返回 null）。
* 提供 onStartCommand() 的默认实现，可将 Intent 依次发送到工作队列和 onHandleIntent() 实现。


### onStartCommand() Flag

1. START_NOT_STICKY 如果系统在 onStartCommand() 返回后终止服务，则除非有挂起 Intent 要传递，否则系统不会重建服务。这是最安全的选项，可以避免在不必要时以及应用能够轻松重启所有未完成的作业时运行服务。
2. START_STICKY 如果系统在 onStartCommand() 返回后终止服务，则会重建服务并调用 onStartCommand()，但不会重新传递最后一个 Intent。相反，除非有挂起 Intent 要启动服务（在这种情况下，将传递这些 Intent ），否则系统会通过空 Intent 调用 onStartCommand()。这适用于不执行命令、但无限期运行并等待作业的媒体播放器（或类似服务）。
3. START_REDELIVER_INTENT 如果系统在 onStartCommand() 返回后终止服务，则会重建服务，并通过传递给服务的最后一个 Intent 调用 onStartCommand()。任何挂起 Intent 均依次传递。这适用于主动执行应该立即恢复的作业（例如下载文件）的服务。






