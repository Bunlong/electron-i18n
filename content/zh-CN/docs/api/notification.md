# Notification

> 创建OS(操作系统)桌面通知

线程：[主线程](../glossary.md#main-process)

## 在渲染进程中使用

如果要显示来自渲染进程的通知, 你应该使用 [ HTML5 Notification API ](../tutorial/notifications.md)

## Class: Notification

> 创建OS(操作系统)桌面通知

进程：[主进程](../glossary.md#main-process)

`Notification` 是 [EventEmitter](http://nodejs.org/api/events.html#events_class_events_eventemitter)

通过 ` options ` 来设置的一个新的原生 ` Notification `。

### 静态方法

`Notification` 类有以下静态方法：

#### `Notification.isSupported()`

Returns ` Boolean `-无论当前系统是否支持桌面通知

### `new Notification([options])` *Experimental*

* `选项` 对象 
  * ` title `String - 通知的标题, 将在通知窗口的顶部显示
  * ` subtitle `String (可选) 通知的副标题, 将显示在标题下面。* macOS *
  * ` body `String 通知的正文文本, 将显示在标题或副标题下面
  * ` silent `Boolean (可选) 在显示通知时是否发出 OS 提示音
  * `icon` (String | [NativeImage](native-image.md)) - (optional) An icon to use in the notification
  * ` hasReply `Boolean (可选) 是否向通知中添加内联答复选项。 * macOS *
  * ` replyPlaceholder `String (可选) 内联答复输入字段中的占位符。* macOS *
  * `sound `String (可选) 显示通知时播放的声音文件的名称。* macOS *
  * ` actions `[ NotificationAction [] ](structures/notification-action.md) (可选) 要添加到通知中的操作。 请在 ` NotificationAction ` 文档 中的查看可用操作和限制* macOS *

### 实例事件

用 `new Notification` 创建的对象触发以下事件：

** 注意: **某些事件仅在特定的操作系统上可用, 这些方法会被标记出来。

#### 事件: 'show'

返回:

* `event` Event

当通知向用户显示时触发, 请注意, 这可能会多次触发, 因为「通知」可以通过 ` show() ` 方法多次显示。

#### Event: 'click'

返回:

* `event` Event

在用户单击通知时触发。

#### 事件： 'close'

返回:

* `event` Event

当用户手动关闭通知时触发

This event is not guaranteed to be emitted in all cases where the notification is closed.

#### Event: 'reply' *macOS*

返回:

* `event` Event
* ` reply `String-用户在内联答复字段中输入的字符串

当用户单击 ` hasReply: true ` 的通知上的 "Reply" 按钮时触发。

#### Event: 'action' *macOS*

返回:

* `event` Event
* `index` Number - The index of the action that was activated

### 实例方法

用`new Notification` 创建的对象有以下实例方法：

#### `notification.show()`

立即显示通知给用户，请注意这一点不同于 HTML5通知实现，只实例化一个 `new Notification` 不会马上显示给用户，你需要在OS将要显示它之前调用这个方法将显示它。

If the notification has been shown before, this method will dismiss the previously shown notification and create a new one with identical properties.

#### `notification.close()`

Dismisses the notification.

### Playing Sounds

在 macOS 上, 您可以指定在显示通知时要播放的声音的名称。 除了自定义声音文件之外, 还可以使用任何默认声音 ("系统首选项" > "声音")。 请确保声音文件是在应用程序包(例如, ` YourApp.app/Contents/Resources`) 内存在副本, 或者是下列位置之一:

* `~/Library/Sounds`
* `/Library/Sounds`
* `/Network/Library/Sounds`
* `/System/Library/Sounds`

有关详细信息, 请参见 [` NSSound `](https://developer.apple.com/documentation/appkit/nssound) 文档。