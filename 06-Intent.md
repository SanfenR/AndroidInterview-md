[官方文档1](https://developer.android.com/guide/components/intents-common.html?hl=zh-cn)
[官方文档2](https://developer.android.com/guide/components/intents-filters.html?hl=zh-cn)



Intent 是一个消息传递对象，您可以使用它从其他应用组件请求操作。尽管 Intent 可以通过多种方式促进组件之间的通信，但其基本用例主要包括以下三个：

1. 启动Activty Activity 表示应用中的一个屏幕。通过将 Intent 传递给 startActivity()，您可以启动新的 Activity 实例。Intent 描述了要启动的 Activity，并携带了任何必要的数据。如果您希望在 Activity 完成后收到结果，请调用 startActivityForResult()。在 Activity 的 onActivityResult() 回调中，您的 Activity 将结果作为单独的 Intent 对象接收。如需了解详细信息，请参阅 Activity 指南。
2. 启动服务 Service 是一个不使用用户界面而在后台执行操作的组件。通过将 Intent 传递给 startService()，您可以启动服务执行一次性操作（例如，下载文件）。Intent 描述了要启动的服务，并携带了任何必要的数据。
3. 传递广播 广播是任何应用均可接收的消息。系统将针对系统事件（例如：系统启动或设备开始充电时）传递各种广播。通过将 Intent 传递给 sendBroadcast()、sendOrderedBroadcast() 或 sendStickyBroadcast()，您可以将广播传递给其他应用。


![link](https://developer.android.com/images/components/intent-filters@2x.png?hl=zh-cn)


Intent类型

1. 显式 Intent：按名称（完全限定类名）指定要启动的组件。 通常，您会在自己的应用中使用显式 Intent 来启动组件，这是因为您知道要启动的 Activity 或服务的类名。例如，启动新 Activity 以响应用户操作，或者启动服务以在后台下载文件。

2. 隐式 Intent ：不会指定特定的组件，而是声明要执行的常规操作，从而允许其他应用中的组件来处理它。 例如，如需在地图上向用户显示位置，则可以使用隐式 Intent，请求另一具有此功能的应用在地图上显示指定的位置。

