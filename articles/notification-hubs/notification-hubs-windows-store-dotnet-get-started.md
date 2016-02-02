<properties
    pageTitle="開始使用適用於 Windows 市集應用程式的 Azure 通知中樞 | Microsoft Azure"
    description="在本教學課程中，您會了解如何使用 Azure 通知中樞，將推播通知傳送到 Windows 市集或 Windows Phone 8.1 (非 Silverlight) 應用程式。"
    services="notification-hubs"
    documentationCenter="windows"
    authors="wesmc7777"
    manager="dwrede"
    editor="dwrede"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="12/14/2015"
    ms.author="wesmc"/>


# 開始使用適用於 Windows 市集應用程式的通知中樞

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## 概觀

本教學課程將示範如何使用 Azure 通知中樞，以將推播通知傳送到 Windows 市集或 Windows Phone 8.1 (非 Silverlight) 應用程式。 如果您的目標 Windows Phone 8.1 Silverlight，請參閱 [Windows Phone](notification-hubs-windows-phone-get-started.md) 版本。
在本教學課程中，您將使用 Windows 推播通知服務 (WNS)，建立可接收推播通知的空白 Windows 市集應用程式。 完成時，您便能夠使用通知中樞，將推播通知廣播到所有執行您 app 的裝置。


## 開始之前

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

完成本教學課程中的程式碼，請參閱 GitHub [這裡](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/GetStartedWindowsUniversal)。



## 必要條件

本教學課程需要下列各項：

+ Microsoft Visual Studio Express 2013 for Windows with Update 2 或更新版本<br/>這個版本的 Visual Studio，才能建立通用應用程式專案。 如果您要建立 Windows 市集應用程式，您需要 Visual Studio 2012 Express for Windows 8。

+ 有效的 Windows 市集帳戶

+ 有效的 Azure 帳戶 <br/>如果您沒有帳戶，您可以建立免費試用帳戶只需要幾分鐘的時間。 如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-store-dotnet-get-started%2F)。

完成本教學課程是 Windows 市集應用程式所有其他通知中心教學課程的先決條件。

## 向 Windows 市集註冊應用程式

若要傳送推播通知給 Windows 市集應用程式，您必須將您的 App 與 Windows 市集產生關聯。 接著您必須設定您的通知中心，以便與 WNS 進行整合。

1. 如果您尚未註冊您的應用程式，瀏覽至 [Windows 開發人員中心](http://go.microsoft.com/fwlink/p/?LinkID=266582), ，使用您的 Microsoft 帳戶登入，然後按一下 **建立新的應用程式**。

2. 輸入您的 App 名稱，然後按一下 [保留應用程式名稱]****。

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hubs-win8-app-name.png)

    This creates a new Windows Store registration for your app.

3. 在 Visual Studio 中，使用 [空白應用程式]**** 範本來建立新的 Visual C# 市集應用程式專案。

    ![][2]

4. 在 [方案總管] 中，以滑鼠右鍵按一下 Windows 市集應用程式專案，然後依序按一下 [市集]**** 和 [將應用程式與市集建立關聯...]****。

    ![][3]

    隨即顯示 [將您的應用程式與 Windows 市集建立關聯]**** 精靈。

5. 在此精靈中，按一下 [登入]****，然後使用您的 Microsoft 帳戶登入。

6. 按一下您在步驟 2 中註冊的應用程式，按 [下一步]****，然後按一下 [關聯]****。

    ![][4]

    這會將所需的 Windows 市集註冊資訊新增至應用程式資訊清單。

7. (選用) 針對 Windows Phone 市集應用程式專案重複步驟 4–6。

8. 回到 [Windows 開發人員中心](http://go.microsoft.com/fwlink/p/?LinkID=266582) 新應用程式頁面上，按一下 **服務**, ，按一下 [ **推播通知**, ，然後按一下 [ **Live 服務網站** 下 **Windows 推播通知服務 (WNS) 和 Microsoft Azure 行動服務**。

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hubs-win8-app-live-services.png)

9. 在 [應用程式設定]**** 索引標籤上，記下 [用戶端密碼]**** 和 [封裝安全性識別碼 (SID)]**** 的值。

    ![][6]
    > [AZURE.WARNING]
    用戶端密碼和封裝 SID 是重要的安全性認證。 請勿與任何人共用這些值，或與您的應用程式一起散發密碼。

## 設定您的通知中樞

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="7">
<li><p>選取 <b>設定</b> 索引標籤頂端，輸入 <b>用戶端密碼</b> 和 <b>封裝 SID</b> 值，您在上一節中從 WNS 取得，然後按一下 <b>儲存</b>。</p>
</li>
</ol>

& emsp; & emsp;![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)


現在已將您的通知中心設定成使用 WNS，而且您已擁有可用來註冊應用程式和傳送通知的連接字串。

## 將您的應用程式連接到通知中樞

1. 在 Visual Studio 中，以滑鼠右鍵按一下方案，然後按一下 [管理 NuGet 封裝]****。

    此時會顯示 [管理 NuGet 封裝]**** 對話方塊。

2. 搜尋 `WindowsAzure.Messaging.Managed` 按一下 **安裝**, ，在方案中，選取所有專案，並接受使用規定。

    ![][20]

    這會下載、 安裝，並將參考加入所有專案中的 Azure 訊息程式庫適用於 Windows 使用 <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet 封裝</a>。

3. 開啟 App.xaml.cs 專案檔案並新增下列 `使用` 陳述式。在通用專案中，這個檔案位於 `< project_name >。共用` 資料夾。

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;

4. 接著，在 App.xaml.cs 中，將下列 **InitNotificationsAsync** 方法定義新增至 **App** 類別：

     private async void InitNotificationsAsync()
     {
         var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    
         var hub = new NotificationHub("<hub name>", "<connection string with listen access>");
         var result = await hub.RegisterNativeAsync(channel.Uri);
    
         // Displays the registration ID so you know it was successful
         if (result.RegistrationId != null)
         {
             var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
             dialog.Commands.Add(new UICommand("OK"));
             await dialog.ShowAsync();
         }
    
     }

 此程式碼會從 WNS 中擷取應用程式的通道 URI，然後向您的通知中樞註冊該通道 URI。
 >[AZURE.NOTE]請務必使用出現在 [Azure 傳統入口網站] 上的通知中心名稱來取代"hub name"預留位置 **通知中心** ] 索引標籤 (例如， **mynotificationhub2** 前一個範例中)。 另請使用在上一節中取得的 **DefaultListenSharedAccessSignature** 連線字串，來取代連線字串預留位置。

5. 在 App.xaml.cs 中的 **OnLaunched** 事件處理常式頂端，將下列呼叫新增至新 **InitNotificationsAsync** 方法：

        InitNotificationsAsync();

    這會保證每次啟動應用程式時，通道 URI 便會在通知中樞中註冊。

6. 在 [方案總管] 中，按兩下 Windows 市集應用程式的 **Package.appxmanifest**，然後將 [通知]**** 中的 [支援快顯通知]**** 設為 [是]****：

    ![][18]

    從 [檔案]**** 功能表中，按一下 [全部儲存]****。

7. (選用) 針對 Windows Phone 市集應用程式專案重複上一個步驟。

8. 按 **F5** 鍵以執行應用程式。 包含註冊金鑰的快顯對話方塊隨即顯示。

    ![][19]

9. (選擇性) 重複上一個步驟來執行 Windows Phone 專案，以在 Windows Phone 裝置上註冊應用程式。



您的應用程式現在已能夠接收快顯通知。

## 傳送通知

您可以測試應用程式中接收通知，如下列畫面所示，[Azure 傳統入口網站] 透過通知中心上的 [偵錯] 索引標籤，以傳送通知。

![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-debug.png)

推播通知通常會以後端服務傳送，例如行動服務或使用相容程式庫的 ASP.NET。 如果程式庫不適用於您的後端，也可以直接使用 REST API 來傳送通知訊息。

在本教學課程中，為了簡單起見，我們只會在主控台應用程式 (而非後端服務) 中使用適用於通知中樞的 .NET SDK 傳送通知，示範如何測試您的用戶端應用程式。 我們建議 [使用通知中心來推播通知給使用者] 教學課程的下一個步驟，從 ASP.NET 後端傳送通知。 不過，下列方法可用來傳送通知：

* **REST 介面**: 您可以使用您建立任何後端平台上支援通知 [REST 介面](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)。

* **Microsoft Azure 通知中樞 SDK**: 在 Nuget Package Manager for Visual Studio 中，執行 [Install-package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)。

* **Node.js** : [如何從 Node.js 使用通知中樞](notification-hubs-nodejs-how-to-use-notification-hubs.md)。

* **Azure 行動服務**: 如需如何從整合通知中心之 Azure 行動服務後端傳送通知的範例，請參閱 「 開始使用行動服務中的推播通知 」 ([.NET 後端](../mobile-services/mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) | [JavaScript backend](../mobile-services/mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md)).

* **Java / PHP**: 如需如何使用 REST Api 傳送通知的範例，請參閱 < 如何從 Java/PHP 使用通知中樞 」 ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)).


## (選擇性) 從主控台應用程式傳送通知

若要使用 .NET 主控台應用程式來傳送通知，請遵循下列步驟。

1. 以滑鼠右鍵按一下方案，選取 [加入]**** 和 [新增專案...]****，然後按一下 [Visual C#]**** 底下的 [Windows]**** 和 [主控台應用程式]****，再按一下 [確定]****。

    ![][13]

    即會將新的 Visual C# 主控台應用程式新增到方案中。 您也可以在個別方案中進行此項作業。

2. 在 Visual Studio 中，依序按一下 [工具]****、[NuGet 封裝管理員]**** 和 [封裝管理員主控台]****。

    這會在 Visual Studio 中顯示 [封裝管理員主控台]。

3. 在 [封裝管理員主控台] 視窗中，將 [預設專案]**** 設為新的主控台應用程式專案，然後在主控台視窗中執行下列命令：

        Install-Package Microsoft.Azure.NotificationHubs

    這會新增到使用 Azure 通知中心 SDK <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification 中樞 nuget</a>。

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

4. 開啟 Program.cs 檔案並新增下列 `使用` 陳述式:

        using Microsoft.Azure.NotificationHubs;

5. 在 **Program** 類別中，新增下列方法：

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

    請務必使用出現在 [Azure 傳統入口網站] 上的通知中心名稱來取代"hub name"預留位置 **通知中心** ] 索引標籤。 此外，請將連接字串預留位置取代為您在＜設定通知中樞＞一節中取得，且名為 **DefaultFullSharedAccessSignature** 的連接字串。
    >[AZURE.NOTE]請確定您使用的連接字串具有 [完整]**** 存取權，而非 [接聽]**** 存取權。 接聽存取權的字串沒有傳送通知的權限。

6. 在 **[主要]** 方法中新增下列命令列。

         SendNotificationAsync();
         Console.ReadLine();

7. 在 Visual Studio 中，以滑鼠右鍵按一下主控台應用程式專案，然後按一下 [設定為啟始專案]****，將它設為啟始專案。 然後按 **F5** 鍵執行應用程式。

    ![][14]

    您將會在所有註冊裝置上收到快顯通知。 按一下或點選快顯橫幅即會載入應用程式。

您可以找到所有支援的裝載中的 [快顯目錄]、 [並排顯示類別目錄]，和 MSDN 上的 [徽章概觀] 主題。

## 後續步驟

在此簡單範例中，您會使用入口網站或主控台應用程式，將廣播通知傳送到您的所有 Windows 裝置。 我們建議 [使用通知中心來推播通知給使用者] 教學課程的下一個步驟。 它會示範如何使用標記以特定使用者為目標，從 ASP.NET 後端傳送通知。

如果您想要按興趣群組分隔使用者，請參閱 [使用通知中心傳送即時新聞]。

若要了解通知中心的詳細資訊，請參閱 [通知中心指引]。









[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png 
[3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png 
[4]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png 
[6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png 
[11]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png 
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png 
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png 
[15]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler1.png 
[16]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler2.png 
[18]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-win8-app-toast.png 
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png 
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png 
[azure classic portal]: https://manage.windowsazure.com/ 
[notification hubs guidance]: http://msdn.microsoft.com/library/jj927170.aspx 
[use notification hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md 
[use notification hubs to send breaking news]: notification-hubs-windows-store-dotnet-send-breaking-news.md 
[toast catalog]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx 
[tile catalog]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx 
[badge overview]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx 

