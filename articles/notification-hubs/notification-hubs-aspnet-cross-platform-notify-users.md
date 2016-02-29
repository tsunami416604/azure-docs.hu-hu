<properties
    pageTitle="使用通知中樞向使用者傳送跨平台通知 (ASP.NET)" description="了解如何使用通知中樞範本，在單一要求中傳送以所有平台為目標的跨平台通知。"
    services="notification-hubs"
    documentationCenter=""
    authors="wesmc7777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="12/11/2015" 
    ms.author="wesmc"/>

# 使用通知中心向使用者傳送跨平台通知


在先前的教學課程 [Notify users with Notification Hubs], ，您學到如何推播通知給所有由特定經驗證使用者所註冊的裝置。 在該教學課程中，需要用多個要求來傳送通知給每個支援的用戶端平台。 通知中心可支援範本，讓您指定特定裝置接收通知的方式。 這使得傳送跨平台通知變得更簡單。 本主題示範如何運用範本，在單一要求中傳送以所有平台為目標的跨平台通知。 如需詳細範本的相關資訊，請參閱 [Azure 通知中心概觀][Templates]。

> [AZURE.NOTE] 通知中心可以讓裝置使用相同的標籤註冊多個範本。 在此情況下，當傳入的訊息符合該標籤時，就會有多個通知傳遞至裝置 (每個通知各用於一個範本)。 如此一來，您就能讓相同訊息顯示在多個視覺通知中，例如以徽章形式和 Windows 市集應用程式中的快顯通知形式。

完成下列步驟，可使用範本傳送跨平台通知：

1. 在 Visual Studio 中的 [方案總管] 中展開 **控制器** 資料夾，然後開啟 RegisterController.cs 檔案。

2. 找出程式碼區塊 **Post** 方法以建立新的登錄取代 `switch` 內容取代為下列程式碼:

        switch (deviceUpdate.Platform)
        {
            case "mpns":
                var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>$(message)</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
                registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "wns":
                toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "apns":
                var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{\"data\":{\"msg\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

    這段程式碼會呼叫平台特有方法來建立範本註冊，而非原生註冊。 不需要修改現有註冊，因為範本註冊源自原生註冊。

3. 在 **通知** 控制站，取代 **sendNotification** 方法取代下列程式碼:

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;

            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }

    這段程式碼會同時將通知傳送至所有平台，完全不需要指定原生裝載。 通知中心建置並傳遞正確的裝載到每個裝置，以提供 _標記_ 註冊的範本中所指定的值。

4. 重新發佈您的 WebApi 後端專案。

5. 重新執行用戶端應用程式，然後驗證註冊已成功。

6. (選用) 將此用戶端應用程式部署到第二個裝置，然後執行此應用程式。

    請留意到，每個裝置上都會顯示通知。

## 後續步驟

您已完成本教學課程，現在可參閱下列主題進一步了解通知中心和範本：

+ **[Use Notification Hubs to send breaking news]** <br/>示範另一個使用範本的案例

+  **[Azure 通知中心概觀][Templates]**<br/>概觀主題包含詳細範本的詳細資訊。


<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-store-dotnet-send-breaking-news.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx

