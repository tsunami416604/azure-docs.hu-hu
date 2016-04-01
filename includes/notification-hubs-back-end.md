
本節說明如何從 .NET 主控台應用程式和任何其他應用程式傳送通知。
如果您使用行動服務，請參閱 [開始使用推播](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md) 教學課程。 如果您想要使用 Java 或 PHP，請參閱 [如何從 Java/PHP 使用通知中樞](../articles/notification-hubs/notification-hubs-java-backend-how-to.md)。 您可以從任何後端使用傳送通知 [通知中樞 REST 介面](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)。

下列程式碼會傳送通知給 Windows 市集、Windows Phone、iOS 和 Android 裝置。 

如果您在完成時，建立主控台應用程式，請略過步驟 1-3 [開始使用通知中樞][get-started]。

1. 在 Visual Studio 中建立新的 Visual C# 主控台應用程式： 

    ![][13]

2. 在 Visual Studio 主功能表中，按一下 [ **工具**, ，**程式庫套件管理員**, ，和 **Package Manager Console**, ，然後在主控台視窗中輸入下列命令並按下 **Enter**:

        Install-Package Microsoft.Azure.NotificationHubs
    
    這會使用 <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet 封裝</a> 加入對 Azure 通知中樞 SDK 的參考。 

3. 開啟 Program.cs 檔案，並新增下列 `using` 陳述式：

        using Microsoft.Azure.NotificationHubs;

4. 在 `Program` 類別中，新增或取代 (如果方法已存在) 下列方法：

        private static async void SendNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub = 
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");
        
            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", 
                "Technology", "Science", "Sports"};
        
            foreach (var category in categories)
            {
                try
                {
                    // Define a Windows Store toast.
                    var wnsToast = "<toast><visual><binding template=\"ToastText01\">" 
                        + "<text id=\"1\">Breaking " + category + " News!" 
                        + "</text></binding></visual></toast>";         
                    await hub.SendWindowsNativeNotificationAsync(wnsToast, category);

                    // Define a Windows Phone toast.
                    var mpnsToast =
                        "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                        "<wp:Notification xmlns:wp=\"WPNotification\">" +
                            "<wp:Toast>" +
                                "<wp:Text1>Breaking " + category + " News!</wp:Text1>" +
                            "</wp:Toast> " +
                        "</wp:Notification>";         
                    await hub.SendMpnsNativeNotificationAsync(mpnsToast, category);

                    // Define an iOS alert.
                    var alert = "{\"aps\":{\"alert\":\"Breaking " + category + " News!\"}}";
                    await hub.SendAppleNativeNotificationAsync(alert, category);

                    // Define an Android notification.
                    var notification = "{\"data\":{\"msg\":\"Breaking " + category + " News!\"}}";
                    await hub.SendGcmNativeNotificationAsync(notification, category);
                }
                catch (ArgumentException)
                {
                    // An exception is raised when the notification hub hasn't been 
                    // registered for the iOS, Windows Store, or Windows Phone platform. 
                }
            }
         }

    此程式碼會將字串陣列中每個標籤 (共六個) 的通知傳送給 Windows 市集、Windows Phone 和 iOS 裝置。 使用標籤可確保裝置只會收到已登錄類別的通知。
    
    > [AZURE.NOTE] 此後端程式碼支援 Windows 市集、 Windows Phone、 iOS 和 Android 用戶端。 當尚未針對特定用戶端平台設定通知中樞時，傳送方法會傳回錯誤回應。 

6. 在上述程式碼，取代 `<hub name>` 和 `<connection string with full access>` 與通知中樞名稱和連接字串預留位置 *DefaultFullSharedAccessSignature* 先前取得的。

7. 新增以下幾行，在 **Main** 方法 ︰

         SendNotificationAsync();
         Console.ReadLine();

<!-- Anchors -->
[From a console app]: #console
[From Mobile Services]: #mobile-services
[Run the app and generate notifications]: #test-app

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png

<!-- URLs. -->
[get-started]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started.md
[Use Notification Hubs to send notifications to users]: ../articles/tutorial-notify-users-mobileservices.md
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Notification Hubs REST interface]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx


