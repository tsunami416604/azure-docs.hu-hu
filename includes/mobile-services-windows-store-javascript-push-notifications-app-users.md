
接下來，您需要變更註冊推播通知的方式，以便在嘗試註冊之前，能夠確定使用者已通過驗證。 用戶端應用程式更新會根據您實作推播通知的方法而定。

###在 Visual Studio 2013 Update 2 或更新版本中使用推播通知精靈

在這個方法中，精靈會在專案中產生新的 push.register.js 和 service.js 檔案。

1. 在 Visual Studio 方案總管] 中，開啟 push.register.js 專案檔，並在註解化或刪除呼叫 **addEventListener**。 

2. 在 default.js 專案檔案中，將現有的 **登入** 函式，以下列程式碼:
 
        // Request authentication from Mobile Services using a Facebook login.
        var login = function () {
            return new WinJS.Promise(function (complete) {
                client.login("facebook").done(function (results) {
                    userId = results.userId;
                    // Request a push notification channel.
                    Windows.Networking.PushNotifications
                        .PushNotificationChannelManager
                        .createPushNotificationChannelForApplicationAsync()
                        .then(function (channel) {
                            // Register for notifications using the new channel
                            client.push.registerNative(channel.uri);
                        });
                    refreshTodoItems();
                    var message = "You are now logged in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    userId = null;
                    var dialog = new Windows.UI.Popups
                        .MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        }  

###手動啟用推播通知      

在這個方法中，您已將從教學課程取得的註冊程式碼直接新增到 default.js 專案檔。

1. 在 Visual Studio 方案總管] 中，開啟 default.js 專案檔案並在 **onActivated** 事件處理常式中，尋找呼叫的程式碼行 **createPushNotificationChannelForApplicationAsync** 函式，如下列所示:

        // Request a push notification channel.
        Windows.Networking.PushNotifications
            .PushNotificationChannelManager
            .createPushNotificationChannelForApplicationAsync()
            .then(function (channel) {
                // Register for notifications using the new channel
                client.push.registerNative(channel.uri);
            }); 
 
2. 將這行程式碼貼入移 **登入** 函式，呼叫前面 **refreshTodoItems** 以便 **登入** 函式看起來像這樣:
 
        // Request authentication from Mobile Services using a Facebook login.
        var login = function () {
            return new WinJS.Promise(function (complete) {
                client.login("facebook").done(function (results) {
                    userId = results.userId;
                    // Request a push notification channel.
                    Windows.Networking.PushNotifications
                        .PushNotificationChannelManager
                        .createPushNotificationChannelForApplicationAsync()
                        .then(function (channel) {
                            // Register for notifications using the new channel
                            client.push.registerNative(channel.uri);
                        });
                    refreshTodoItems();
                    var message = "You are now logged in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    userId = null;
                    var dialog = new Windows.UI.Popups
                        .MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        }  

