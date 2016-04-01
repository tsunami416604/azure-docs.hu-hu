<properties 
    pageTitle="如何搭配使用服務匯流排主題與 Node.js | Microsoft Azure" 
    description="了解如何從 Node.js 應用程式，在 Azure 中使用服務匯流排主題和訂用帳戶。" 
    services="service-bus" 
    documentationCenter="nodejs" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="10/07/2015" 
    ms.author="sethm"/>


# 如何使用服務匯流排主題和訂用帳戶

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

本指南說明如何使用服務匯流排主題和訂閱
從 Node.js 應用程式。 涵蓋的案例包括 **建立
主題和訂閱**, ，**建立訂閱篩選器**, ，**傳送
訊息** 至主題、 **從訂閱接收訊息**, ，並
**刪除主題和訂閱**。 如需主題的詳細資訊
和訂閱，請參閱 [後續步驟](#next-steps) 一節。

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## 建立 Node.js 應用程式

建立空白的 Node.js 應用程式。 如需建立 Node.js 應用程式的指示，請參閱 [Create and deploy a Node.js application to an Azure Web Site], ，[Node.js 雲端服務][Node.js Cloud Service] （使用 Windows PowerShell），或使用 WebMatrix 的網站。

## 設定應用程式以使用服務匯流排

若要使用服務匯流排，請下載 Node.js azure 封裝。 此套件包含一組程式庫，
通訊的便利程式庫。

### 使用 Node Package Manager (NPM) 取得封裝

1.  使用命令列介面，例如 **PowerShell** (Windows)、 **終端機** (Mac) 或 **Bash** (Unix)，瀏覽至您用來建立範例應用程式的資料夾。

2.  型別 **npm 安裝 azure** 應在命令視窗中，
    下例輸出：

    ```
        azure@0.7.5 node_modules\azure
    ├── dateformat@1.0.2-1.2.3
    ├── xmlbuilder@0.4.2
    ├── node-uuid@1.2.0
    ├── mime@1.2.9
    ├── underscore@1.4.4
    ├── validator@1.1.1
    ├── tunnel@0.0.2
    ├── wns@0.5.3
    ├── xml2js@0.2.7 (sax@0.5.2)
    └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```

3.  您可以手動執行 **ls** 命令，以驗證
    **node\_modules** 建立資料夾。 在該資料夾內找出
    **azure** 封裝，其中包含您需要存取的程式庫
    程式庫。

### 匯入模組

使用 [記事本] 或其他文字編輯器，將下列內容新增至
 **server.js** 應用程式檔案 ︰

```
var azure = require('azure');
```

### 設定服務匯流排連接

Azure 模組會讀取環境變數 AZURE\_SERVICEBUS\_NAMESPACE 和 AZURE\_SERVICEBUS\_ACCESS\_KEY 可連接至服務匯流排所需的資訊。 如果未設定這些環境變數，呼叫時，您必須指定帳戶資訊 **createServiceBusService**。

如需針對 Azure 雲端服務組態檔中設定環境變數的範例，請參閱 [使用儲存體的 Node.js 雲端服務][]。

如需範例中設定環境變數的 [Azure 傳統入口網站][] Azure 網站，請參閱 [與儲存體的 Node.js Web 應用程式][]。

## 建立主題

 **ServiceBusService** 物件可讓您處理主題。 Auch die Eigenschaften
下列程式碼會建立 **ServiceBusService** 物件。 請將程式碼
頂端 **server.js** 檔案之後的陳述式匯入 azure,
後方：

```
var serviceBusService = azure.createServiceBusService();
```

藉由呼叫 **createTopicIfNotExists** 上 **ServiceBusService**
（如果存在），指定的主題將傳回的物件，或建立具有
建立具有指定名稱的主題。 下列程式碼使用
**createTopicIfNotExists** 來建立或連接名為的主題
主題：

```
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

**createServiceBusService** 也支援其他選項，其中
可讓您覆寫預設主題設定，例如訊息的時間
預設主題設定。 下列範例會將主題大小上限設為 5GB，並將存留時間設為 1 分鐘：

```
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### 篩選器

選用的篩選作業可以套用至使用執行的作業 **ServiceBusService**。 篩選作業可包含記錄、自動重試等。篩選器是使用簽章實作方法的物件：

```
function handle (requestOptions, next)
```

對要求選項執行前置處理之後，方法會呼叫 `next`，並傳遞具有下列簽章的回呼：

```
function (returnObject, finalCallback, next)
```

在此回呼中，以及處理 returnObject (來自對伺服器之要求的回應) 之後，回呼需要叫用 next (如果存在) 以繼續處理其他篩選，或是就改為叫用 finalCallback 結束服務叫用。

實作重試邏輯的兩個篩選器隨附於 Azure SDK for Node.js **ExponentialRetryPolicyFilter** 和 **LinearRetryPolicyFilter**。 下列範例會建立 **ServiceBusService** 物件，使用 **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## 建立訂用帳戶

也會建立主題訂閱 **ServiceBusService**
。 訂閱是具名的，它們能擁有選用的篩選器，
限制的訊息傳遞至訂閱的虛擬
集合。

> [AZURE.NOTE] 訂閱是持續性，將會持續存在，
直到本身或相關主題遭到刪除為止。 如果
應用程式含有建立訂閱的邏輯，它應該會先
使用
**getSubscription** 方法。

### 使用預設 (MatchAll) 篩選器建立訂閱

 **MatchAll** 篩選器是預設篩選器，如果任何篩選條件
沒有指定篩選器，便會使用此篩選器。 當 **MatchAll**
篩選器時，所有發佈至主題的訊息都會被置於訂閱的
虛擬佇列中。 下列範例會建立
名為 'AllMessages' 的訂閱，並使用預設 **MatchAll**
篩選器。

```
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### 使用篩選器建立訂用帳戶

您也可以建立篩選器，讓您界定哪些傳送訊息
應出現在特定主題訂閱中。

訂閱所支援的最具彈性篩選器類型是實作 SQL92 子集的
**SqlFilter**, ，可用來實作 SQL92 的子集。 SQL 篩選器適用於
發佈至主題之訊息的屬性。 如需
可與 SQL 篩選器搭配使用的運算式詳細資料，
檢閱 [SqlFilter.SqlExpression][SqlFilter.SqlExpression] 語法。

篩選器可以使用新增至訂閱 **createRule**
方法的 **ServiceBusService** 物件。 此方法可讓您
篩選器新增至現有的訂閱。

> [AZURE.NOTE] 由於預設篩選器會自動套用至所有新
訂閱，您必須先移除預設篩選器或
**MatchAll** 會覆寫您指定的其他任何篩選器。 您可以
使用移除預設規則 **deleteRule** 方法
**ServiceBusService** 物件。

下列範例會建立名為 `HighMessages` 與
**SqlFilter** 只選取自訂的訊息 **messagenumber** 屬性大於 3:

```
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'HighMessages', 
            'HighMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

同樣地，以下範例將建立名為
`LowMessages` 使用 **SqlFilter** 只選取的訊息
 **messagenumber** 屬性小於或等於 3:

```
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'LowMessages', 
            'LowMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

現在，當訊息傳送至 `MyTopic`, ，一律會傳遞
若要訂閱的接收者 `AllMessages` 主題訂閱和
選擇性地傳遞至已訂閱 `HighMessages` 和
`LowMessages` 主題訂閱的接收者 （視訊息內容而定）。

## 如何傳送訊息至主題

若要傳送訊息至服務匯流排主題，應用程式必須使用
**sendTopicMessage** 方法 **ServiceBusService** 物件。
傳送至服務匯流排主題的訊息是 **BrokeredMessage** 物件。
**BrokeredMessage** 物件具有一組標準屬性 （例如
**加上標籤** 和 **TimeToLive**)，用來保存自訂字典
應用程式特定屬性，以及一堆字串資料。 「
應用程式可以藉由傳遞字串值，即可設定訊息內文
 **sendTopicMessage** 和任何必要的標準屬性
預設值填入。

下列範例說明如何將五個測試訊息傳送至
'MyTopic'。 請注意， **messagenumber** 每個屬性值
訊息變化 （這可判斷其迴圈的反覆項目
訂閱為何） ︰

```
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

服務匯流排主題支援 256 Kb 的訊息大小上限 (包含
標準和自訂應用程式屬性的標頭，可以容納
64 Kb 的大小上限)。 主題中的訊息數目沒有限制，
但是主題所保有的訊息總大小有
上限。 此主題大小會在建立時定義，
上限是 5 GB。

## 自訂閱接收訊息

從訂閱接收訊息
**receiveSubscriptionMessage** 方法 **ServiceBusService**
。 根據預設，您會看到訂閱中刪除郵件
讀取。不過，您可以讀取 （查看） 並鎖定訊息
刪除訂閱中的選擇性參數
**isPeekLock** 至 **true**。

隨著接收作業讀取及刪除訊息之預設行為是
最簡單的模型，且最適合可容許在發生失敗時
不處理訊息的應用程式
案例。 若要了解這點，請考慮以下案例：
取用者發出接收要求，然而在處理之前
當機。 因為服務匯流排會將訊息標示為已取用，
當應用程式重新啟動並開始重新取用訊息時，
它將會遺漏當機前已取用的訊息。

如果 **isPeekLock** 參數設為 **true**, ，接收會變成
兩階段作業，因此可以支援無法容許遺漏訊息
的應用程式。 當服務匯流排收到
要求時，它會尋找下一個要取用的訊息並加以鎖定，以免
其他取用者接收該訊息，然後將它傳回應用程式。
在應用程式完成處理訊息 (或可靠地儲存
此訊息以供未來處理) 之後，它可呼叫
接收處理序，藉由呼叫 **deleteMessage** 方法，並提供
以完成接收程序的第二個階段。  **DeleteMessage** 方法會
標示為已取用的訊息，並將它從訂閱移除。

下列範例示範可以收到訊息的方式，
使用處理 **receiveSubscriptionMessage**。 此範例第一次
接收及刪除訊息來自 'LowMessages' 訂用帳戶，
然後接收來自 'HighMessages' 訂閱的訊息
**isPeekLock** 設為 true。 接著再使用
**deleteMessage**:

    serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
        if(!error){
            // Message received and deleted
            console.log(receivedMessage);
        }
    });
    serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
        if(!error){
            // Message received and locked
            console.log(lockedMessage);
            serviceBusService.deleteMessage(lockedMessage, function (deleteError){
                if(!deleteError){
                    // Message deleted
                    console.log('message has been deleted.');
                }
            }
        }
    });

## 如何處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，協助您從應用程式的
錯誤或處理訊息的問題中順利復原。 如果
接收者應用程式因故無法處理訊息，
它可以呼叫 **unlockMessage** 方法
**ServiceBusService** 物件。 這將導致服務匯流排將佇列中的
將訂閱中訊息，並讓訊息可以被接收
同樣地，方法是相同取用應用程式或其他取用
重新接收。

與佇列內鎖定訊息相關的還有逾時，
訂用帳戶，如果應用程式無法處理訊息之前
在鎖定逾時到期 （例如，如果應用程式當機） 然後
服務匯流排會自動解除鎖定訊息，並使其可
重新接收。

如果應用程式在處理訊息之後，
之前 **deleteMessage** 呼叫方法時，則會在
重新啟動時將訊息重新傳遞給該應用程式。 這通常稱為
**至少處理一次**, ，也就是會在處理每個訊息
處理一次，但在特定狀況下，可能會重新傳遞相同
訊息。 如果案例無法容許重複處理，則
應用程式開發人員應將其他邏輯加入至其應用程式
來處理重複的訊息傳遞。 通常您可使用訊息的
**MessageId** 訊息，將會保持不變的屬性
保持不變。

## 刪除主題和訂用帳戶

主題和訂閱是持續性的，您必須透過 Azure 管理入口網站
透過 [Azure 傳統入口網站][] 或以程式設計的方式。
下列範例示範如何刪除名為 `MyTopic` 的主題：

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

刪除主題也將會刪除對主題註冊的
任何訂閱。 您也可以個別刪除訂用帳戶。 Auch die Eigenschaften
下列範例示範如何刪除名為的訂閱
`HighMessages` 從 `MyTopic` 主題 ︰

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

## 後續步驟

了解基本的服務匯流排主題之後，請參考下列連結以取得更多資訊。

-   請參閱 [佇列、 主題和訂閱][]。
-   API 參考 [SqlFilter][]。
-   請瀏覽 [Azure SDK for Node][] GitHub 上的儲存機制。

  [Azure SDK for Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Azure classic portal]: http://manage.windowsazure.com
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Create and deploy a Node.js application to an Azure Web Site]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Node.js Web Application with Storage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
 


