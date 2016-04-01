<properties
    pageTitle="Azure 通知中心安全推播"
    description="了解如何在 Azure 中傳送安全的推播通知。 程式碼範例是以 C# 撰寫並使用 .NET API。"
    documentationCenter="windows"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs" 
    ms.workload="mobile"
    ms.tgt_pltfrm="windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/05/2015"
    ms.author="wesmc"/>

#Azure 通知中心安全推播

> [AZURE.SELECTOR]
- [Windows Universal](notification-hubs-windows-dotnet-secure-push.md)
- [iOS](notification-hubs-aspnet-backend-ios-secure-push.md)
- [Android](notification-hubs-aspnet-backend-android-secure-push.md)


##概觀

Microsoft Azure 中的推播通知支援可讓您存取易於使用、多重平台的大規模推播基礎結構，因而可大幅簡化消費者和企業應用程式在行動平台上的推播通知實作。

基於法規或安全性限制，應用程式有時會想要在通知中加入無法透過標準推播通知基礎結構傳送的內容。 本教學課程說明如何透過用戶端裝置和應用程式後端之間的安全、已驗證連線來傳送敏感資訊，以達到相同體驗。

概括而言，流程如下所示：

1. 應用程式後端：
    - 在後端資料庫中儲存安全裝載。
    - 將此通知的識別碼傳送至裝置 (不會傳送安全資訊)。
2. 收到通知時，裝置上的應用程式會執行下列動作：
    - 裝置會連絡後端並要求安全裝載。
    - 應用程式會以通知的形式在裝置上顯示裝載。

請務必注意在上述流程 (與本教學課程) 中，我們假設使用者登入後，裝置會將驗證權杖儲存在本機儲存體中。 由於裝置可使用此權杖擷取通知的安全裝載，因此可保證完全順暢的體驗。 如果您的應用程式沒有將驗證權杖儲存在裝置上，或如果這些權杖可能會過期，裝置應用程式應在收到通知時顯示一般通知，以提示使用者啟動應用程式。 應用程式會接著驗證使用者，並顯示通知裝載。

本安全推播教學課程說明如何以安全的方式傳送推播通知。 本教學課程是根據 [通知使用者](notification-hubs-aspnet-backend-windows-dotnet-notify-users.md) 教學課程中，因此您應先完成該教學課程中的步驟第一次。

> [AZURE.NOTE] 本教學課程假設您已建立並設定通知中心中所述 [開始使用通知中心 （Windows 市集）](notification-hubs-windows-store-dotnet-get-started.md)。
另請注意，Windows Phone 8.1 需要 Windows (不是 Windows Phone) 認證，且無法在 Windows Phone 8.0 或 Silverlight 8.1 上使用背景工作。 若是 Windows 市集應用程式，只有當應用程式已啟用鎖定畫面 (按一下 Appmanifest 中的核取方塊) 時，您才可以透過背景工作接收通知。

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## 修改 Windows Phone 專案

1. 在 **NotifyUserWindowsPhone** 專案中，加入下列程式碼至 App.xaml.cs 以註冊推播背景工作。 在 `OnLaunched()` 方法的結尾新增下列程式碼行：

        RegisterBackgroundTask();

2. 仍在 App.xaml.cs 中，在 `OnLaunched()` 方法後面立即新增下列程式碼：

        private async void RegisterBackgroundTask()
        {
            if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
            {
                var result = await BackgroundExecutionManager.RequestAccessAsync();
                var builder = new BackgroundTaskBuilder();

                builder.Name = "PushBackgroundTask";
                builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
                builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
                BackgroundTaskRegistration task = builder.Register();
            }
        }

3. 在 App.xaml.cs 檔案開頭處新增下列 `using` 陳述式：

        using Windows.Networking.PushNotifications;
        using Windows.ApplicationModel.Background;

4. 從 **檔案** ] 功能表，在 Visual Studio 中，按一下 [ **全部儲存**。

## 建立推播背景元件

下一個步驟說明如何建立推播背景元件。

1. 在 [方案總管中以滑鼠右鍵按一下解決方案的最上層節點 (**Solution SecurePush** 在此情況下)，然後按一下 [ **新增**, ，然後按一下 [ **新的專案**。

2. 展開 **市集應用程式**, ，然後按一下 [ **Windows Phone 應用程式**, ，然後按一下 [ **Windows 執行階段元件 (Windows Phone)**。 將專案命名為 **PushBackgroundComponent**, ，然後按一下 [ **確定** 以建立專案。

    ![][12]

3. 在 [方案總管] 中，以滑鼠右鍵按一下 **PushBackgroundComponent (Windows Phone 8.1)** 專案，然後按一下 **新增**, ，然後按一下 [ **類別**。 將新類別 **PushBackgroundTask.cs**。 按一下 [ **新增** 以產生類別。

4. 整個內容取代 **PushBackgroundComponent** 命名空間定義取代下列程式碼取代預留位置 `{back-end endpoint}` 部署後端時所取得的後端端點 ︰

        public sealed class Notification
            {
                public int Id { get; set; }
                public string Payload { get; set; }
                public bool Read { get; set; }
            }

            public sealed class PushBackgroundTask : IBackgroundTask
            {
                private string GET_URL = "{back-end endpoint}/api/notifications/";

                async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
                {
                    // Store the content received from the notification so it can be retrieved from the UI.
                    RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                    var notificationId = raw.Content;

                    // retrieve content
                    BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                    var httpClient = new HttpClient();
                    var settings = ApplicationData.Current.LocalSettings.Values;
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                    var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

                    var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

                    ShowToast(notification);

                    deferral.Complete();
                }

                private void ShowToast(Notification notification)
                {
                    ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                    XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                    XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                    toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                    ToastNotification toast = new ToastNotification(toastXml);
                    ToastNotificationManager.CreateToastNotifier().Show(toast);
                }
            }

5. 在 [方案總管] 中，以滑鼠右鍵按一下 **PushBackgroundComponent (Windows Phone 8.1)** 專案，然後按一下 **管理 NuGet 封裝**。

6. 在左側，按一下 [ **線上**。

7. 在 **搜尋** 方塊中，輸入 **Http 用戶端**。

8. 在 [結果] 清單中，按一下 [ **Microsoft HTTP 用戶端程式庫**, ，然後按一下 [ **安裝**。 完成安裝。

9. 在 [NuGet **搜尋** 方塊中，輸入 **Json.net**。 安裝 **Json.NET** 套件，然後關閉 [NuGet Package Manager] 視窗。

10. 新增下列 `using` 上方的陳述式 **PushBackgroundTask.cs** 檔案 ︰

        using Windows.ApplicationModel.Background;
        using Windows.Networking.PushNotifications;
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;
        using Windows.UI.Notifications;
        using Windows.Data.Xml.Dom;

11. 在 [方案總管中 **NotifyUserWindowsPhone (Windows Phone 8.1)** 專案中，以滑鼠右鍵按一下 **參考**, ，然後按一下 [ **加入參考...**。 在 [參考管理員] 對話方塊中，核取方塊旁 **PushBackgroundComponent**, ，然後按一下 [ **確定**。

12. 在 [方案總管] 中，按兩下 **Package.appxmanifest** 中 **NotifyUserWindowsPhone (Windows Phone 8.1)** 專案。 在 **通知**, ，請將 **顯** 至 **是**。

    ![][3]

13. 仍在 **Package.appxmanifest**, ，按一下 [ **宣告** 最上方的功能表。 在 **可用宣告** 下拉式清單中，按一下 [ **背景工作**, ，然後按一下 [ **新增**。

14. 在 **Package.appxmanifest**, 下 **屬性**, ，檢查 **推播通知**。

15. 在 **Package.appxmanifest**, 下 **應用程式設定**, ，型別 **PushBackgroundComponent.PushBackgroundTask** 中 **進入點** 欄位。

    ![][13]

16. 從 **檔案** ] 功能表上，按一下 [ **全部儲存**。

## 執行應用程式

若要執行應用程式，請執行下列動作：

1. 在 Visual Studio 中，執行 **AppBackend** Web API 應用程式。 [ASP.NET Web] 頁面便會隨即顯示。

2. 在 Visual Studio 中，執行 **NotifyUserWindowsPhone (Windows Phone 8.1)** Windows Phone 應用程式。 Windows Phone 模擬器便會執行，並自動載入此應用程式。

3. 在 **NotifyUserWindowsPhone** 應用程式 UI 中，輸入使用者名稱和密碼。 這些可以是任何字串，但必須是相同值。

4. 在 **NotifyUserWindowsPhone** 應用程式 UI 中，按一下 **登入並註冊**。 然後按一下 [ **傳送推播**。

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png


