<properties
    pageTitle="通知中樞 - 企業推送架構"
    description="在企業環境中使用 Azure 通知中樞的指引"
    services="notification-hubs"
    documentationCenter=""
    authors="wesmc7777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="11/20/2015" 
    ms.author="wesmc"/>

# 企業推送架構指引

當代的企業正逐漸朝著為使用者 (外部) 或員工 (內部) 建立行動應用程式的方向邁進。 他們擁有現成的後端系統 (大型主機或一些 LoB 應用程式)，而這些系統必須整合到行動應用程式架構中。 本指南將討論如何以最佳方式進行整合，以及推薦常見案例適用的可行方案。

常見的需求是當後端系統發生使用者感興趣的事件時，透過行動應用程式將推播通知傳送給使用者。 例如 iPhone 具有銀行之網路銀行應用程式的銀行客戶想要收到通知扣金額超過一定數量她的帳戶或內部網路案例的財務部門的員工預算核准應用程式已在他的 Windows Phone 要收到他取得核准要求的通知。

銀行帳戶或核准處理有可能是在必須發出推播給使用者的後端系統中完成。 若要在事件觸發通知時實作推播，企業必須具備多部建置相同邏輯的後端系統。 其複雜度在於利用一個推播系統將數個後端系統整合在一起，而在此案例中，使用者可能訂閱了不同的通知且甚至擁有多個行動應用程式。例如，在內部網路行動應用程式案例中，某個行動應用程式想要收到由多部上述後端系統所傳送的通知。 由於後端系統不知道 (或不需要知道) 推播語意/技術，因此在傳統上，常用方案是採用輪詢後端系統是否有任何使用者感興趣之事件的元件，並將傳送推播訊息給用戶端的職責交由它來完成。
在下文中我們將探討一個更好的方案，其使用 Azure 服務匯流排 - 主題/訂閱模式來降低複雜度，同時賦予方案擴充性。

以下是方案的一般架構 (我們已利用多個行動應用程式將其一般化，不過該架構亦適用於只有一個行動應用程式的案例)。

## 架構

![][1]

本架構圖中的關鍵是 Azure 服務匯流排提供主題/訂閱程式撰寫模型 (如需詳細資料在 [服務匯流排發行/訂閱程式撰寫])。 收件者，即在此情況下，行動後端 (通常是 [Azure 行動服務]，這將會將推播發送給行動應用程式) 直接從後端系統接收訊息但方法是把 [Azure 服務匯流排] 所提供的中繼抽象層可讓行動後端以接收來自一或多個後端系統的訊息。 您需要為每個後端系統建立服務匯流排主題 (如客戶、人事、財務)，基本上它們是讓訊息能以推播通知形式傳送的興趣「主題」。 後端系統會將訊息傳送到這些主題。 藉由建立服務匯流排訂閱，行動後端能訂閱一或多個這類型的主題。 如此一來，行動後端便能接收由對應後端系統所傳送的通知。 行動後端會持續接聽與其訂閱相關的訊息，待訊息抵達後，它會立即轉向並以通知形式將訊息傳送到通知匯流排。 通知匯流排再接著將訊息傳遞給行動應用程式。 總結以上關鍵元件，我們可以得出：

1. 後端系統 (LoB/舊版系統)
    - 建立服務匯流排主題
    - 傳送訊息
2. 行動後端
    - 建立服務訂閱
    - 接收訊息 (來自後端系統)
    - 將通知傳送給用戶端 (透過 Azure 通知中樞)
3. 行動應用程式
    - 接收及顯示通知

###優點：

1. 接收器 (行動應用程式/透過通知中樞傳送的服務) 與傳送器 (後端系統) 的解離可讓您在變更少量架構的情況下整合額外的後端系統。
2. 含有多個行動應用程式的案例也能藉此接收來自一或多個後端系統的事件。  

## 範例：

###必要條件
您應該先完成下列教學課程以熟悉概念，以及常用的建立和組態步驟：

1. [服務匯流排發行/訂閱程式撰寫]-這說明工作與服務匯流排主題/訂閱的詳細資料如何建立命名空間來容納主題/訂閱、 如何傳送訊息及接收他們的訊息。
2. [通知中樞-Windows 通用教學課程]-說明如何設定 Windows 市集應用程式，並使用通知中心註冊和接收通知。

###範例程式碼

完整範例程式碼的 [通知中樞範例]。 其可劃分為三個元件：

1. **EnterprisePushBackendSystem**

    a. 本專案使用 *WindowsAzure.ServiceBus* Nuget 封裝，並且根據在 [服務匯流排發行/訂閱程式撰寫]。

    b. 此為簡易的 C# 主控台應用程式，可用來模擬讓訊息得以傳遞到行動應用程式的 LoB 系統。

        static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

            // Create the topic where we will send notifications
            CreateTopic(connectionString);

            // Send message
            SendMessage(connectionString);
        }

    c. `CreateTopic` 用來建立服務匯流排主題傳送訊息。

        public static void CreateTopic(string connectionString)
        {
            // Create the topic if it does not exist already

            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.TopicExists(sampleTopic))
            {
                namespaceManager.CreateTopic(sampleTopic);
            }
        }

    d. `SendMessage` 用來將訊息傳送到此服務匯流排主題。 基於示範目的，在這裡我們只定期傳送一組隨機訊息給主題。 在一般情況下，我們會有在事件發生時傳送訊息的後端系統。

        public static void SendMessage(string connectionString)
        {
            TopicClient client =
                TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

            // Sends random messages every 10 seconds to the topic
            string[] messages =
            {
                "Employee Id '{0}' has joined.",
                "Employee Id '{0}' has left.",
                "Employee Id '{0}' has switched to a different team."
            };

            while (true)
            {
                Random rnd = new Random();
                string employeeId = rnd.Next(10000, 99999).ToString();
                string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

                // Send Notification
                BrokeredMessage message = new BrokeredMessage(notification);
                client.Send(message);

                Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

                System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
            }
        }

2. **ReceiveAndSendNotification**

    a. 本專案使用 *WindowsAzure.ServiceBus* 和 *Microsoft.Web.WebJobs.Publish* Nuget 套件，並會根據 [服務匯流排發行/訂閱程式撰寫]。

    b. 這是另一個 C# 主控台應用程式，因為它必須連續執行以便接聽來自 LoB/後端系統的訊息，我們會執行為 [Azure WebJob]。 它將會是行動後端的一部分。

        static void Main(string[] args)
        {
            string connectionString =
                     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

            // Create the subscription which will receive messages
            CreateSubscription(connectionString);

            // Receive message
            ReceiveMessageAndSendNotification(connectionString);
        }

    c. `CreateSubscription` 用來建立服務匯流排訂閱相關的主題為後端系統傳送訊息的位置。 由於商務案例不盡相同，此元件將會建立一或多個對應主題的訂閱 (例如，有些訂閱會接收來自人事系統的訊息，有些則接收來自財務系統的訊息等)。

        static void CreateSubscription(string connectionString)
        {
            // Create the subscription if it does not exist already
            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
            {
                namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
            }
        }

    d. ReceiveMessageAndSendNotification 可用來使用其訂閱讀取來自主題的訊息，如果讀取成功，便會製作通知 (即範例案例中的 Windows 原生快顯通知) 以供使用 Azure 通知中樞傳送給行動應用程式。

        static void ReceiveMessageAndSendNotification(string connectionString)
        {
            // Initialize the Notification Hub
            string hubConnectionString = CloudConfigurationManager.GetSetting
                    ("Microsoft.NotificationHub.ConnectionString");
            hub = NotificationHubClient.CreateClientFromConnectionString
                    (hubConnectionString, "enterprisepushservicehub");

            SubscriptionClient Client =
                SubscriptionClient.CreateFromConnectionString
                        (connectionString, sampleTopic, sampleSubscription);

            Client.Receive();

            // Continuously process messages received from the subscription
            while (true)
            {
                BrokeredMessage message = Client.Receive();
                var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

                if (message != null)
                {
                    try
                    {
                        Console.WriteLine(message.MessageId);
                        Console.WriteLine(message.SequenceNumber);
                        string messageBody = message.GetBody<string>();
                        Console.WriteLine("Body: " + messageBody + "\n");

                        toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                        SendNotificationAsync(toastMessage);

                        // Remove message from subscription
                        message.Complete();
                    }
                    catch (Exception)
                    {
                        // Indicate a problem, unlock message in subscription
                        message.Abandon();
                    }
                }
            }
        }
        static async void SendNotificationAsync(string message)
        {
            await hub.SendWindowsNativeNotificationAsync(message);
        }

    e. 發行為 **WebJob**, ，Visual Studio 中的方案上按一下滑鼠右鍵，然後選取 **發行為 WebJob**

    ![][2]

    f. 選取發行設定檔，並建立新的 Azure 網站不存在，其可用來裝載此 WebJob 如果一旦網站然後 **發行**。

    ![][3]

    g. 將工作設定為 [連續執行]，讓您登入 [Azure 傳統入口網站] 時您應該會看到類似下列畫面:

    ![][4]


3. **EnterprisePushMobileApp**

    a. 此為 Windows 市集應用程式，它能接收隨附於行動後端運作之 WebJob 發出的快顯通知並加以顯示。 這根據 [通知中樞-Windows 通用教學課程]。  

    b. 確認應用程式可接收快顯通知。

    c. 確認以下通知中樞註冊程式碼呼叫應用程式啟動 (更換後 *HubName* 和 *DefaultListenSharedAccessSignature*:

        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
            var result = await hub.RegisterNativeAsync(channel.Uri);

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

### 執行範例：

1. 確認 WebJob 成功執行，並已排定為 [連續執行]。
2. 執行 **EnterprisePushMobileApp** 以啟動 Windows 市集應用程式。
3. 執行 **EnterprisePushBackendSystem** 主控台應用程式以模擬 LoB 後端，它會開始傳送訊息，您應該會看到以下快顯通知:

    ![][5]

4. 這些訊息最初是傳送給受到 WebJob 中服務匯流排訂閱監視的服務匯流排主題。 待服務匯流排主題接收到訊息後，它會建立通知並傳送給行動應用程式。 您可以瀏覽 WebJob 記錄檔來確認處理，當您移到 [Azure 傳統入口網站] 中的記錄檔連結您的 Web 工作:

    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Notification Hub Samples]: https://github.com/Azure/azure-notificationhubs-samples
[Azure Mobile Service]: http://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Service Bus Pub/Sub programming]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Azure WebJob]: http://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/
[Notification Hubs - Windows Universal tutorial]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Azure Classic Portal]: https://manage.windowsazure.com/

