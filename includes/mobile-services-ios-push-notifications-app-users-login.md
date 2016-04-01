
接下來，您需要變更註冊推播通知的方式，如此可以在使用者通過驗證後，再嘗試註冊。

1. 在 **QSAppDelegate.m**, ，移除的實作 **didFinishLaunchingWithOptions** 完全。

2. 開啟 **QSTodoListViewController.m** 結尾加入下列程式碼和 **viewDidLoad** 方法 ︰

```
// Register for remote notifications
[[UIApplication sharedApplication] registerForRemoteNotificationTypes:
UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
```


