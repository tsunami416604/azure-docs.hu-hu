<properties
    pageTitle="開始使用適用於 Xamarin iOS 應用程式的通知中樞 | Microsoft Azure"
    description="在本教學課程中，您會了解如何使用 Azure 通知中樞將推播通知傳送至 Xamarin iOS 應用程式。"
    services="notification-hubs"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="11/17/2015"
    ms.author="yuaxu"/>

# 開始使用通知中心

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##概觀

本教學課程示範如何使用 Azure 通知中樞將推播通知傳送至 iOS 應用程式。
您將使用 Apple 推播通知服務 (APN)，建立可接收推播通知的空白 Xamarin.iOS app。 完成時，您便能夠使用通知中樞，將推播通知廣播到所有執行您 app 的裝置。 完成的程式碼位於 [NotificationHubs 應用程式][GitHub] 範例。

本教學課程示範使用通知中樞的簡單廣播案例。

##必要條件

本教學課程需要下列各項：

+ [XCode 6.0][Install Xcode]
+ 支援 iOS 7.0 (或更新版本) 的裝置
+ iOS Developer Program 成員資格
+ [Xamarin.iOS]
+ [Azure 行動服務元件]

   > [AZURE.NOTE] 基於推播通知組態需求，您必須部署和測試推播通知在 iOS 功能裝置 (iPhone 或 iPad) 而非在模擬器中。

完成本教學課程是 Xamarin.iOS app 所有其他通知中樞教學課程的必要條件。

> [AZURE.IMPORTANT] 若要完成本教學課程，您必須具有有效的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started)。

[AZURE.INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]


##設定您的通知中樞

本節將引導您建立新的通知中心，並使用 APNS 設定驗證 **.p12** 您所建立的推播憑證。 如果您想要使用已經建立的通知中樞，可以跳至步驟 5。

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li>
<p>按一下 [ <b>設定</b> 的頂端，索引標籤，然後按一下 <b>上載</b> 中上傳的憑證指紋 Apple 通知設定] 按鈕。 然後選取 <b>.p12</b> 您稍早匯出的憑證和憑證的密碼。</p>
<p>請務必選取 <b>沙箱</b> 模式，因為這是進行開發。 只使用 <b>生產</b> 如果您想要將推播通知傳送給使用者購買您的應用程式，從存放區。</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-upload-cert.png)

&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png)


現在已將您的通知中樞設定成使用 APNS，而且您已擁有可用來註冊應用程式和傳送通知的連接字串。





##將您的應用程式連接到通知中樞

#### 建立新專案

1. 在 Xamarin Studio 中，建立新的 iOS 專案，並選取 **統一 API** > **Single View Application** 範本。

    ![][31]

2. 新增 Azure 訊息元件的參考。 在 [方案] 檢視中，以滑鼠右鍵按一下 **元件** 專案的資料夾，然後選擇 **取得更多元件**。 搜尋 **Azure 訊息** 元件並將元件新增至您的專案。

3. 在 **AppDelegate.cs**, ，新增下列 using 陳述式:

        using WindowsAzure.Messaging;

4. 宣告的執行個體 **SBNotificationHub**:

        private SBNotificationHub Hub { get; set; }

5. 建立 **Constants.cs** 類別含有下列變數:

        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";


6. 在 **AppDelegate.cs**, ，更新 **finishedlaunching ()** 以符合下列:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());

                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }

            return true;
        }

7. 覆寫 **Appdelegate.cs** 方法中的 **AppDelegate.cs**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);

            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }

                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }

8. 覆寫 **Appdelegate.cs** 方法中的 **AppDelegate.cs**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

9. 建立下列 **Appdelegate.cs** 方法中的 **AppDelegate.cs**:

        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

                string alert = string.Empty;

                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();

                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }

    > [AZURE.NOTE] 您可以選擇覆寫 **failedtoregisterforremotenotifications ()** 處理情況，例如沒有網路連線。


10. 在您的裝置上執行應用程式。


## 傳送通知


您可以測試應用程式中接收通知，傳送通知給以 [Azure Classic Portal] 透過通知中心，如下列畫面所示的 [偵錯] 索引標籤。

![](./media/notification-hubs-ios-get-started/notification-hubs-debug-hub-ios.png)

推播通知通常會以後端服務傳送，例如行動服務或使用相容程式庫的 ASP.NET。 如果程式庫不適用於您的後端，也可以直接使用 REST API 來傳送通知訊息。 

在本教學課程中，為了簡單起見，我們只會在主控台應用程式 (而非後端服務) 中使用適用於通知中樞的 .NET SDK 傳送通知，示範如何測試您的用戶端應用程式。 我們建議 [使用通知中心來推播通知給使用者](notification-hubs-aspnet-backend-ios-notify-users.md) 教學課程的下一個步驟，從 ASP.NET 後端傳送通知。 不過，下列方法可用來傳送通知：

* **REST 介面**: 您可以使用您建立任何後端平台上支援通知 [REST 介面](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)。

* **Microsoft Azure 通知中樞 SDK**: 在 Nuget Package Manager for Visual Studio 中，執行 [Install-package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)。

* **Node.js** : [如何從 Node.js 使用通知中樞](notification-hubs-nodejs-how-to-use-notification-hubs.md)。

* **Azure 行動服務**: 如需如何從整合通知中心之 Azure 行動服務後端傳送通知的範例，請參閱 「 開始使用行動服務中的推播通知 」 ([.NET 後端](../mobile-services/mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) | [JavaScript 後端](../mobile-services/mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md))。

* **Java / PHP**: 如需如何使用 REST Api 傳送通知的範例，請參閱 < 如何從 Java/PHP 使用通知中樞 」 ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md))。


####(選用) 從 .NET 主控台應用程式傳送通知。

在本節中，您將使用 .NET 主控台應用程式來傳送通知。

1. 建立新的 Visual C# 主控台應用程式：

    ![][213]

2. 在 Visual Studio 中，按一下 [ **工具**, ，按一下 [ **NuGet 封裝管理員**, ，然後按一下 [ **Package Manager Console**。

    這會在 Visual Studio 中顯示 [封裝管理員主控台]。

3. 在封裝管理員主控台] 視窗中，設定 **預設專案** 新的主控台應用程式專案，然後在主控台視窗中，執行下列命令:

        Install-Package Microsoft.Azure.NotificationHubs

    這會加入到使用 Azure 通知中心 SDK 參考 <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification 中樞 NuGet 封裝</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)


4. 開啟 Program.cs 檔案，並新增下列 `using` 陳述式：

        using Microsoft.Azure.NotificationHubs;

3. 在 `Program` 類別中，新增下列方法：

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. 在您的 `Main` 方法中新增下列程式碼行：

         SendNotificationAsync();
         Console.ReadLine();

5. 按 F5 鍵以執行應用程式。 您應該會在裝置上收到警示。 如果您使用 Wi-Fi，請確定連線正在運作。

您可以找到所有可能的裝載在 Apple [Local and Push Notification Programming Guide]。


####(選用) 從行動服務傳送通知

在本節中，我們將使用行動服務透過節點指令碼傳送通知。

若要使用的行動服務來傳送通知，請遵循 [Get started with Mobile Services], ，然後:

1. 登入 [Azure Classic Portal], ，然後選取您的行動服務。

2. 選取 **排程器** 最上層顯示] 索引標籤。

    ![][215]

3. 建立新的排定的工作、 插入名稱，然後選取 **隨**。

    ![][216]

4. 在工作建立之後，按一下此工作名稱。 然後按一下 [ **指令碼** 頂列上的索引標籤。

5. 將下列指令碼插入您的排程器函數內。 請務必使用您的通知中心名稱和連接字串來取代預留位置 *DefaultFullSharedAccessSignature* 先前取得的。 按一下 [ **儲存**。

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
        notificationHubService.apns.send(
            null,
            {"aps":
                {
                "alert": "Hello from Mobile Services!"
                }
            },
            function (error)
            {
                if (!error) {
                    console.warn("Notification successful");
                }
            }
        );


6. 按一下 [ **執行一次** 底列上。 您應該會在裝置上收到警示。

##後續步驟

在此簡單範例中，您廣播通知到您的所有 iOS 裝置。 若要鎖定特定使用者，請參閱本教學課程 [Use Notification Hubs to push notifications to users]。 如果您想要按興趣群組分隔使用者，您可以閱讀 [Use Notification Hubs to send breaking news]。 深入了解如何使用通知中心 [Notification Hubs Guidance] 和 [Notification Hubs How-To for iOS]。


<!-- Images. -->

[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Azure Classic Portal]: https://manage.windowsazure.com/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Use Notification Hubs to push notifications to users]: /manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.iOS]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS

