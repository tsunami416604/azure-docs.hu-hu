<properties
    pageTitle="如何搭配 Node.js 使用通知中樞"
    description="了解如何使用通知中樞，從 Node.js 應用程式傳送推播通知。"
    services="notification-hubs"
    documentationCenter="nodejs"
    authors="wesmc7777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="12/10/2015"
    ms.author="wesmc"/>

# 如何從 Node.js 使用通知中樞
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

##概觀

本指南將示範如何使用通知中心
從 Node.js 應用程式。 涵蓋的案例包括 **傳送通知到 Android、 iOS、 Windows Phone 和 Windows 市集應用程式**。 如需通知中心的詳細資訊，請參閱 [下一步](#next) 一節。

##什麼是通知中心？

Azure 通知中樞提供易用、多平台、可擴充的基礎結構，用以將推播通知傳送至行動裝置。 如需詳細資訊，請參閱 [Azure 通知中樞](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx)。

##建立 Node.js 應用程式

建立空白的 Node.js 應用程式。 如需建立 Node.js 應用程式的指示，請參閱 [建立並部署 Node.js 應用程式至 Azure 網站][nodejswebsite], ，[Node.js 雲端服務][Node.js Cloud Service] （使用 Windows PowerShell） 或 [Web Site with WebMatrix]。

##將應用程式設為使用通知中心

若要使用 Azure 通知中心，您必須下載並使用
Node.js azure 套件。 這包含一組便利的程式庫，
可用來與 REST 服務進行通訊。

### 使用 Node Package Manager (NPM) 取得封裝

1.  使用命令列介面，例如 **PowerShell** (Windows)、 **終端機** (Mac) 或 **Bash** (Unix)，瀏覽至您用來建立範例應用程式的資料夾。

2.  型別 **npm 安裝 azure** 應在命令視窗中，
    下例輸出：

        azure@0.7.0 node_modules\azure
        |-- dateformat@1.0.2-1.2.3
        |-- xmlbuilder@0.4.2
        |-- node-uuid@1.2.0
        |-- mime@1.2.9
        |-- underscore@1.4.4
        |-- validator@0.4.28
        |-- tunnel@0.0.2
        |-- wns@0.5.3
        |-- xml2js@0.2.6 (sax@0.4.2)
        |-- request@2.16.6 (forever-agent@0.2.0, aws-sign@0.2.0, tunnel-agent@0.2.0, oauth-sign@0.2.0, json-stringify-safe@3.0.0, cookie-jar@0.2.0, node-uuid@1.4.0, qs@0.5.5, hawk@0.10.2, form-data@0.0.7)

3.  您可以手動執行 **ls** 或 **dir** 命令，以驗證
    **node\_modules** 建立資料夾。 在該資料夾內找出
    **azure** 封裝，其中包含您需要存取的程式庫
    通知中心。

### 匯入模組

使用文字編輯器，將下列內容新增至
 **server.js** 應用程式檔案 ︰

    var azure = require('azure');

### 設定 Azure 通知中樞連線

 **NotificationHubService** 物件可讓您使用通知中心。 Auch die Eigenschaften
下列程式碼會建立 **NotificationHubService** 物件名為的通知中心 **hubname**。 請將程式碼
頂端 **server.js** 檔案之後的陳述式匯入 azure,
後方：

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

連接 **connectionstring** 值可以取自 [Azure Classic Portal] 藉由執行下列步驟 ︰

1. 從 [Azure Classic Portal], ，請選取 **服務匯流排**, ，然後選取包含通知中心的命名空間。

2. 選取 **通知中心**, ，然後選取您想要使用的中心。

3. 選取 **檢視連接字串** 從 **快速瀏覽** 區段，然後複製連接字串值。

> [AZURE.NOTE] 您也可以擷取連接字串使用 **Get-azuresbnamespace** Azure PowerShell 所提供的指令程式或 **azure sb 命名空間顯示** 使用 Azure 命令列介面 (Azure CLI) 命令。

</div>

##如何傳送通知

 **NotificationHubService** 物件會公開下列物件執行個體，將通知傳送至特定裝置和應用程式 ︰

* **Android** -使用 **GcmService** 物件，可在 **notificationHubService.gcm**
* **iOS** -使用 **ApnsService** 物件，可從 **notificationHubService.apns**
* **Windows Phone** -使用 **MpnsService** 物件，可在 **notificationHubService.mpns**
* **Windows 市集應用程式** -使用 **WnsService** 物件，可在 **notificationHubService.wns**

### 如何傳送 Android 應用程式通知

 **GcmService** 物件提供 **傳送** 可用來將通知傳送至 Android 應用程式的方法。  **傳送** 方法可接受下列參數 ︰

* Tags - 標籤識別碼。 若未提供標籤，通知將會傳送至所有用戶端
* Payload - 訊息的 JSON 或字串裝載
* Callback - 回呼函數

如需裝載格式的詳細資訊，請參閱 < 裝載 」 一節 [實作 GCM 伺服器](http://developer.android.com/google/gcm/server.html#payload)。

下列程式碼會使用 **GcmService** 所公開的執行個體 **NotificationHubService** 將訊息傳送至所有用戶端。

    var payload = {
      data: {
        msg: 'Hello!'
      }
    };
    notificationHubService.gcm.send(null, payload, function(error){
      if(!error){
        //notification sent
      }
    });

### 如何傳送 iOS 應用程式通知

 **ApnsService** 物件提供 **傳送** 可用來將通知傳送至 iOS 應用程式的方法。  **傳送** 方法可接受下列參數 ︰

* Tags - 標籤識別碼。 若未提供標籤，通知將會傳送至所有用戶端
* Payload - 訊息的 JSON 或字串裝載
* Callback - 回呼函數

裝載格式的詳細資訊，請參閱通知裝載 」 一節 [本機和推播通知程式設計指南](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html)。

下列程式碼會使用 **ApnsService** 所公開的執行個體 **NotificationHubService** 警示訊息傳送至所有用戶端 ︰

    var payload={
        alert: 'Hello!'
      };
    notificationHubService.apns.send(null, payload, function(error){
      if(!error){
        // notification sent
      }
    });

### 如何傳送 Windows Phone 通知

 **MpnsService** 物件提供 **傳送** 可用來將通知傳送至 Windows Phone 應用程式的方法。  **傳送** 方法可接受下列參數 ︰

* Tags - 標籤識別碼。 若未提供標籤，通知將會傳送至所有用戶端
* Payload - 訊息的 XML 裝載
* TargetName - 快顯通知的「快顯」。 磚通知的「權杖」。
* NotificationClass - 通知的優先順序。 請參閱 HTTP 標頭元素 」 一節 [從伺服器推播通知](http://msdn.microsoft.com/library/hh221551.aspx) 值是否有效。
* Options - 選用的要求標頭
* Callback - 回呼函數

如需有效 TargetName、 NotificationClass 和標頭選項的清單，請參閱 [從伺服器推播通知](http://msdn.microsoft.com/library/hh221551.aspx)。

下列程式碼會使用 **MpnsService** 所公開的執行個體 **NotificationHubService** 來傳送快顯通知警示 ︰

    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });

### 如何傳送 Windows 市集應用程式通知

 **WnsService** 物件提供 **傳送** 可用來將通知傳送給 Windows 市集應用程式的方法。   **傳送** 方法可接受下列參數 ︰

* Tags - 標籤識別碼。 若未提供標籤，通知將會傳送至所有用戶端
* Payload - XML 訊息裝載
* Type - 通知類型
* Options - 選用的要求標頭
* Callback - 回呼函數

如需有效類型和要求標頭的清單，請參閱 [推播通知服務要求和回應標頭](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx)。

下列程式碼會使用 **WnsService** 所公開的執行個體 **NotificationHubService** 來傳送快顯通知警示 ︰

    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
        // notification sent
      }
    });

## 後續步驟

現在，您已了解使用通知中心的基本概念，請參閱下列
連結以了解更多資訊。

-   請參閱 MSDN 參考：[Azure 通知中樞][]。
-   請瀏覽 [Azure SDK for Node] GitHub 上的儲存機制。

  [Azure SDK for Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Next Steps]: #nextsteps
  [What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [Create a Node.js Application]: #Create_a_Nodejs_Application
  [Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [How to: Create a Topic]: #How_to_Create_a_Topic
  [How to: Create Subscriptions]: #How_to_Create_Subscriptions
  [How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
  [How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
  [How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Azure Classic Portal]: http://manage.windowsazure.com
  [image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Web Site with WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
  [Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/


