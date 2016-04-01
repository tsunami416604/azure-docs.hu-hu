<properties
    pageTitle="開始使用適用於 Windows Phone 的 Azure 通知中樞 | Microsoft Azure"
    description="在本教學課程中，您會了解如何使用 Azure 通知中樞，將推播通知傳送到 Windows Phone 8 或 Windows Phone 8.1 Silverlight 應用程式。"
    services="notification-hubs"
    documentationCenter="windows"
    authors="wesmc7777"
    manager="dwrede"
    editor="dwrede"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="12/14/2015"
    ms.author="wesmc"/>

# 開始使用適用於 Windows Phone 的通知中樞

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##概觀

本教學課程將示範如何使用 Azure 通知中樞，將推播通知傳送到 Windows Phone 8 或 Windows Phone 8.1 Silverlight 應用程式。 如果您的目標 Windows Phone 8.1 (非 Silverlight)，請參閱 [Windows 通用](notification-hubs-windows-store-dotnet-get-started.md) 版本。
在本教學課程中，您將使用 Microsoft 推播通知服務 (MPNS)，建立可接收推播通知的空白 Windows Phone 8 應用程式。 完成時，您便能夠使用通知中樞，將推播通知廣播到所有執行您 app 的裝置。

> [AZURE.NOTE] 通知中樞 Windows Phone SDK 不支援使用 Windows 推播通知服務 (WNS) 與 Windows Phone 8.1 Silverlight 應用程式。 若要將 WNS (而非 MPNS) 與 Windows Phone 8.1 Silverlight app 搭配使用，請遵循使用 REST API 的 [通知中樞 - Windows Phone Silverlight 教學課程]。

本教學課程示範使用通知中樞的簡單廣播案例。

##先決條件

本教學課程需要下列各項：

+ [Visual Studio 2012 Express for Windows Phone], 或更新版本。

完成本教學課程是 Windows Phone 8 應用程式所有其他通知中樞教學課程的先決條件。

> [AZURE.NOTE] 若要完成本教學課程，您必須具有有效的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F)。

##建立您的通知中樞

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="7">
<li><p>按一下 [ <b>設定</b> 索引標籤，然後再按一下 <b>啟用未經驗證的推播通知</b> 中核取方塊 <b>Windows Phone 通知設定</b> 一節。</p>
</li>
</ol>

&emsp;&emsp;![](./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png)

現在已建立並設定您的中樞，以傳送未經驗證的 Windows Phone 通知。

> [AZURE.NOTE] 本教學課程使用未經驗證的模式的 MPNS。 MPNS 未通過驗證模式內含您可傳送至每個通道的通知限制。 通知中心支援 [MPNS 驗證模式](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx) 可以讓您上傳您的憑證。

##將您的應用程式連接到通知中樞

1. 在 Visual Studio 中建立新的 Windows Phone 8 應用程式。

    ![][13]

    在 Visual Studio 2013 Update 2 或更新版本中，您必須改為建立 Windows Phone Silverlight 應用程式。

    ![][11]

2. 在 Visual Studio 中，以滑鼠右鍵按一下方案，以及 [ **管理 NuGet 封裝**。

    這會顯示 **管理 NuGet 封裝** 對話方塊。

3. 搜尋 `WindowsAzure.Messaging.Managed` 按一下 **安裝**, ，然後接受使用規定。

    ![][20]

    這會使用 <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet 封裝</a>來下載、安裝並新增適用於 Windows 的 Azure 傳訊程式庫參考。

4. 開啟 App.xaml.cs 檔案，並新增下列 `using` 陳述式：

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. 在頂端加入下列程式碼 **Application_Launching** App.xaml.cs 中的方法 ︰

        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());

            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });

    請確定插入您的中心和名為連接字串名稱 **DefaultListenSharedAccessSignature** 您在上一節中取得。
    此程式碼會從 MPNS 中擷取 app 的通道 URI，然後向您的通知中樞註冊該通道 URI。 它也保證每次啟動應用程式時，便會在通知中樞中註冊通道 URI。

    >[AZURE.NOTE]本教學課程將快顯通知傳送至裝置。 當您傳送磚通知時，您必須改為呼叫 **BindToShellTile** 通道上的方法。 若要同時支援快顯和磚通知，同時呼叫 **BindToShellTile** 和  **BindToShellToast**。

6. 在 [方案總管] 中，展開 **屬性**, 、 開啟 WMAppManifest.xml 檔案、 按一下 **功能** ] 索引標籤，並確定 **ID_CAP_PUSH_NOTIFICATION** 已勾選功能。

    ![][14]


    This ensures that your app can receive push notifications.

7. 按 F5 鍵以執行應用程式。

    此時會顯示註冊訊息。

8. 關閉應用程式。  您必須關閉應用程式，才能接收快顯通知。

##從後端傳送通知

您可以透過 <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST 介面</a>，使用通知中樞從任何後端傳送通知。 在本教學課程中，您將使用 .NET 主控台應用程式來傳送通知。 如需如何從整合通知中心之 Azure 行動服務後端傳送通知的範例，請參閱 「 開始使用行動服務中的推播通知 」 ([.NET 後端](../mobile-services-javascript-backend-windows-phone-get-started-push.md) | [JavaScript 後端](../mobile-services-javascript-backend-windows-phone-get-started-push.md))。  如需如何使用 REST Api 傳送通知的範例，請參閱 < 如何從 Java/PHP 使用通知中樞 」 ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md))。

1. 以滑鼠右鍵按一下方案，請選取 **新增** 和 **新增專案...**, ，然後在 **Visual C#**, ，按一下 **Windows** 和 **主控台應用程式**, ，按一下 **確定**。

    ![][6]

    即會將新的 Visual C# 主控台應用程式新增到方案中。 您也可以在個別方案中進行此項作業。

4. 按一下 [ **工具**, ，按一下 [ **程式庫套件管理員**, ，然後按一下 [ **Package Manager Console**。

    這會顯示 [Package Manager Console]。

5.  在封裝管理員主控台] 視窗中，設定 **預設專案** 新的主控台應用程式專案，然後在主控台視窗中，執行下列命令 ︰

        Install-Package Microsoft.Azure.NotificationHubs

    這會使用 <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet 封裝</a> 加入對 Azure 通知中樞 SDK 的參考。

6. 開啟 Program.cs 檔案，並新增下列 `using` 陳述式：

        using Microsoft.Azure.NotificationHubs;

6. 在 **程式** 類別中，新增下列方法 ︰

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

    請務必使用出現在網站上的通知中心名稱來取代"hub name"預留位置 **通知中心** ] 索引標籤。 此外，將連接字串預留位置取代連接字串呼叫 **DefaultFullSharedAccessSignature** 您取得的一節中 「 設定您的通知中心 」。

    >[AZURE.NOTE]請確定您使用的連接字串具有 **完整** 存取權，而非 **接聽** 存取。 接聽存取權的字串沒有傳送通知的權限。

4. 加入下列這一行中的您 **Main** 方法 ︰

         SendNotificationAsync();
         Console.ReadLine();

5. 當您的 Windows Phone 模擬器正在執行且您的 app 已關閉時，將主控台應用程式專案設為預設啟動專案，然後按 F5 鍵來執行 app。

    您將會收到快顯通知。 點選快顯通知即可載入 app。

您可以找到所有可能的裝載中 [toast catalog] 和 [tile catalog] MSDN 上的主題。

##後續步驟

在此簡單範例中，您廣播通知到您的所有 Windows Phone 8 裝置。 若要鎖定特定使用者，請參閱本教學課程 [Use Notification Hubs to push notifications to users]。 如果您想要按興趣群組分隔使用者，您可以閱讀 [Use Notification Hubs to send breaking news]。 深入了解如何使用通知中心 [Notification Hubs Guidance]。



<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-phone-send-breaking-news.md
[toast catalog]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[tile catalog]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Notification Hub - WP Silverlight tutorial]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp


