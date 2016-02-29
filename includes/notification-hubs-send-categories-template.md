
本節說明如何從 .NET 主控台應用程式將即時新聞以加註標記的範本通知形式傳送。

如果您使用行動服務，請參閱 [開始使用推播](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md) 教學課程。 

如果您想要使用 Java 或 PHP，請參閱 [如何從 Java/PHP 使用通知中樞](../articles/notification-hubs/notification-hubs-java-backend-how-to.md)。 您可以從任何後端使用傳送通知 [通知中樞 REST 介面](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)。

略過步驟 1-3，如果您建立主控台應用程式傳送通知，當您完成 [開始使用通知中樞] [入門]。

1. 在 Visual Studio 中建立新的 Visual C# 主控台應用程式： 

    ![][13]

2. 在 Visual Studio 主功能表中，按一下 [ **工具**, ，**程式庫套件管理員**, ，和 **Package Manager Console**, ，然後在主控台視窗中輸入下列命令並按下 **Enter**:

        Install-Package Microsoft.Azure.NotificationHubs
    
    這會加入到使用 Azure 通知中心 SDK 參考 <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification 中樞 NuGet 封裝</a>. 

3. 開啟 Program.cs 檔案，並新增下列 `using` 陳述式：

        using Microsoft.Azure.NotificationHubs;

4. 在 `Program` 類別中，新增或取代 (如果方法已存在) 下列方法：

        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub = 
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", 
                                            "Technology", "Science", "Sports"};

            // Sending the notification as a template notification. All template registrations that contain 
            // "messageParam" and the proper tags will receive the notifications. 
            // This includes APNS, GCM, WNS, and MPNS template registrations.

            Dictionary<string, string> templateParams = new Dictionary<string, string>();

            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";            
                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
         }

    此程式碼會分別將範本通知傳送給字串陣列中的六個標籤。 使用標籤可確保裝置只會收到已登錄類別的通知。 

6. 在上述程式碼，取代 `<hub name>` 和 `<connection string with full access>` 與通知中樞名稱和連接字串預留位置 *DefaultFullSharedAccessSignature* 從您的通知中心的儀表板。

7. 新增以下幾行，在 **Main** 方法:

         SendTemplateNotificationAsync();
         Console.ReadLine();

8. 建置主控台應用程式。

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

