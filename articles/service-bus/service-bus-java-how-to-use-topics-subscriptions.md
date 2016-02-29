<properties
    pageTitle="如何搭配使用服務匯流排主題與 Java | Microsoft Azure"
    description="了解如何在 Azure 使用服務匯流排主題及訂用帳戶。 程式碼範例專為 Java 應用程式撰寫。"
    services="service-bus"
    documentationCenter="java"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/07/2015"
    ms.author="sethm"/>

# 如何使用服務匯流排主題和訂用帳戶

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

本指南說明如何使用服務匯流排主題和訂用帳戶。 這些範例以 Java 撰寫並使用 [Azure SDK for Java][]。 涵蓋的案例包括 **建立主題和訂閱**, ，**建立訂閱篩選器**, ，**傳送訊息至主題**, ，**從訂閱接收訊息**, ，並
**刪除主題和訂閱**。

[AZURE.INCLUDE [service-bus-java-how-to-create-topic](../../includes/service-bus-java-how-to-create-topic.md)]

## 設定應用程式以使用服務匯流排

請確定您已安裝 [Azure SDK for Java][] 再建置此範例。 如果您使用 Eclipse，您可以安裝 [Azure Toolkit for Eclipse][] 包含 Azure SDK for Java。 您可以加入 **Microsoft Azure Libraries for Java** 至您的專案:

![](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

在 Java 檔案頂端新增下列 import 陳述式：

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.*;
    import com.microsoft.windowsazure.core.*;
    import javax.xml.datatype.*;

將 Azure Libraries for Java 新增至您的建置路徑，並將其納入專案部署組件中。

## 建立主題

服務匯流排主題的管理作業可透過
**ServiceBusContract** 類別。 A **ServiceBusContract** 物件
可使用含有權杖權限加以管理的
SAS 權杖具有權限加以管理，而 **ServiceBusContract** 類別
唯一的通訊點。

 **ServiceBusService** 類別提供方法來建立、 列舉
的方法。 下列範例將示範如何 **ServiceBusService** 物件
可用來建立名為 `TestTopic`, ，使用命名空間，稱為 `HowToSample`:

    Configuration config =
        ServiceBusConfiguration.configureWithSASAuthentication(
          "HowToSample",
          "RootManageSharedAccessKey",
          "SAS_key_value",
          ".servicebus.windows.net"
          );

    ServiceBusContract service = ServiceBusService.create(config);
    TopicInfo topicInfo = new TopicInfo("TestTopic");
    try  
    {
        CreateTopicResult result = service.createTopic(topicInfo);
    }
    catch (ServiceException e) {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

有方法 **TopicInfo** 可讓主題中的屬性
微調 (例如: 若要設定的預設存留時間 (TTL) 值是
[存留時間] 設定預設值)。 下列範例將示範如何
若要建立名為 `TestTopic` 5 GB 的大小上限:

    long maxSizeInMegabytes = 5120;  
    TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
    CreateTopicResult result = service.createTopic(topicInfo);

請注意，您可以使用 **listTopics** 方法
**ServiceBusContract** 物件來檢查是否已有指定名稱的主題
的主題存在。

## 建立訂用帳戶

也會建立主題訂閱 **ServiceBusService**
類別表示的物件容器檔案中。 訂閱是具名的，它們能擁有選用的篩選器，
以用來限制傳遞至訂閱的虛擬佇列的訊息
集合。

### 使用預設 (MatchAll) 篩選器建立訂用帳戶

 **MatchAll** 篩選器是預設篩選器，如果任何篩選條件
沒有指定篩選器，便會使用此篩選器。 當 **MatchAll**
篩選器時，所有發佈至主題的訊息都會被置於訂閱的
虛擬佇列中。 下列範例會建立
名為"AllMessages"的訂閱，並使用預設 **MatchAll**
篩選器。

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result =
        service.createSubscription("TestTopic", subInfo);

### 使用篩選器建立訂用帳戶

您也可以設定篩選器，讓您界定哪些傳送至主題的訊息
應出現在特定主題訂閱中。

訂閱所支援的最具彈性篩選器類型是實作 SQL92 子集的
[SqlFilter][], ，可用來實作 SQL92 的子集。 SQL 篩選器適用於
發佈至主題之訊息的屬性。 如需
可與 SQL 篩選器搭配使用的運算式詳細資料，
檢閱 [SqlFilter.SqlExpression][] 語法。

下列範例會建立名為 `HighMessages` 與
[SqlFilter][] 物件，只選取自訂的訊息
**MessageNumber** 屬性大於 3:

    // Create a "HighMessages" filtered subscription  
    SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
    CreateSubscriptionResult result =
        service.createSubscription("TestTopic", subInfo);
    RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
    ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
    CreateRuleResult ruleResult =
        service.createRule("TestTopic", "HighMessages", ruleInfo);
    // Delete the default rule, otherwise the new rule won't be invoked.
    service.deleteRule("TestTopic", "HighMessages", "$Default");

同樣地，下列範例會建立名為 `LowMessages` 與 [SqlFilter][] sqlfilter 只選取的物件 **MessageNumber** 屬性小於或等於 3:

    // Create a "LowMessages" filtered subscription
    SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
    CreateSubscriptionResult result =
        service.createSubscription("TestTopic", subInfo);
    RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
    ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
    CreateRuleResult ruleResult =
        service.createRule("TestTopic", "LowMessages", ruleInfo);
    // Delete the default rule, otherwise the new rule won't be invoked.
    service.deleteRule("TestTopic", "LowMessages", "$Default");


現在，當訊息傳送至 `TestTopic`, ，一律會
傳遞至已訂閱 `AllMessages` 訂用帳戶，並選擇性地傳遞至已訂閱 `HighMessages` 和 `LowMessages` 訂閱 (視
訊息內容而定)。

## 傳送訊息至主題

若要傳送訊息至服務匯流排主題，應用程式會取得
**ServiceBusContract** 物件。 下列程式碼示範如何傳送
訊息 `TestTopic` 內先前建立的主題 `HowToSample` 命名空間:

    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendTopicMessage("TestTopic", message);

傳送至服務匯流排主題的訊息是
[BrokeredMessage][] 類別。 [BrokeredMessage][]* 物件具有一組
標準方法 (例如 **setLabel** 和 **TimeToLive**)，字典
保存自訂應用程式特定屬性的字典，以及一些任意的
應用程式資料。 應用程式可以設定訊息內文，
方法是將任何可序列化的物件傳遞至
[BrokeredMessage][], ，適當 **DataContractSerializer** 將
用來序列化物件。 或者，
**java.io.InputStream** 可以提供。

下列範例說明如何將五個測試訊息傳送至上述程式碼片段中所取得的
`TestTopic` **MessageSender** 我們在上述程式碼片段中取得。
請注意如何 **MessageNumber** 的每個訊息的屬性值的變化上
的變化 (這可判斷接收訊息的訂閱
為何)：

    for (int i=0; i<5; i++)  {
        // Create message, passing a string message for the body
        BrokeredMessage message = new BrokeredMessage("Test message " + i);
        // Set some additional custom app-specific property
        message.setProperty("MessageNumber", i);
        // Send message to the topic
        service.sendTopicMessage("TestTopic", message);
    }

服務匯流排主題支援 256 Kb 的訊息大小上限 (包含
標準和自訂應用程式屬性的標頭，可以容納
64 Kb 的大小上限)。 主題中的訊息數目沒有限制，
但是主題所保有的訊息總大小有
上限。 此主題大小會在建立時定義，
上限是 5 GB。

## 如何自訂用帳戶接收訊息

從訂閱接收訊息的主要方式是使用
**ServiceBusContract** 物件。 接收的訊息可在兩種不同的
不同的模式: **ReceiveAndDelete** 和 **PeekLock**。

當使用 **ReceiveAndDelete** 模式中，接收是一次性
作業；也就是說，當服務匯流排收到訊息的讀取要求時，
它會將此訊息標示為已使用，並將它傳回
重新接收。 **ReceiveAndDelete** 模式是最簡單的模型和運作方式
最適合可容許在發生失敗時不處理訊息的
應用程式案例。 若要了解這點，請考慮
以下案例：取用者發出接收要求，然而
在處理之前便當機。 因為服務匯流排會將
訊息標示為已取用，當應用程式重新啟動並開始
重新取用訊息時，它將會遺漏當機前
已取用的訊息。

在 **PeekLock** 模式中，接收會變成兩階段作業，因此可以
可以支援無法容許遺漏訊息的應用
程式。 當服務匯流排收到要求時，它會尋找下一個要
要取用的訊息並加以鎖定，以免其他取用者接收該訊息，然後
將它傳回應用程式。 在應用程式完成
訊息處理 (或可靠地儲存此訊息以供未來處理) 之後，它可在接收的訊息上
呼叫以完成接收程序的第二個階段 **刪除**
方法，以完成接收程序的第二個階段。 當服務匯流排看到 **刪除** 呼叫時，它
它會將訊息標示為已取用，並將它從主題中移除。

下列範例將示範如何
使用處理 **PeekLock** 模式 (不是預設模式)。 下列範例
將執行迴圈，並處理 "HighMessages" 訂閱中的訊息，然後在沒有任何訊息時結束 (您也可以將其設為等待新訊息)。

    try
    {
        ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
        opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

        while(true)  {
            ReceiveSubscriptionMessageResult  resultSubMsg =
                service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
            BrokeredMessage message = resultSubMsg.getValue();
            if (message != null && message.getMessageId() != null)
            {
                System.out.println("MessageID: " + message.getMessageId());
                // Display the topic message.
                System.out.print("From topic: ");
                byte[] b = new byte[200];
                String s = null;
                int numRead = message.getBody().read(b);
                while (-1 != numRead)
                {
                    s = new String(b);
                    s = s.trim();
                    System.out.print(s);
                    numRead = message.getBody().read(b);
                }
                System.out.println();
                System.out.println("Custom Property: " +
                    message.getProperty("MessageNumber"));
                // Delete message.
                System.out.println("Deleting this message.");
                service.deleteMessage(message);
            }  
            else  
            {
                System.out.println("Finishing up - no more messages.");
                break;
                // Added to handle no more messages.
                // Could instead wait for more messages to be added.
            }
        }
    }
    catch (ServiceException e) {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }
    catch (Exception e) {
        System.out.print("Generic exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

## 如何處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，協助您從應用程式的
錯誤或處理訊息的問題中順利復原。 如果
接收者應用程式因故無法處理訊息，
它可以呼叫 **unlockMessage** 所接收訊息的方法
(而不是 **deleteMessage** 方法)。 這將導致服務匯流排
將主題中的訊息解除鎖定，讓此訊息可以被
相同取用應用程式或其他取用應用程式
重新接收。

與主題內鎖定訊息相關的還有逾時，
如果應用程式無法在鎖定逾時到期之前
處理訊息 (例如，如果應用程式當機)，則服務
匯流排會自動解除鎖定訊息，並讓訊息可以被
重新接收。

如果應用程式在處理訊息之後，
之前 **deleteMessage** 發出要求，然後將訊息
重新啟動時將訊息重新傳遞給該應用程式。 這通常
呼叫 **至少處理一次**, ，也就是將會是每個訊息
至少會被處理一次，但在特定狀況下，可能會重新傳遞相同
訊息。 如果案例無法容許重複處理，
則應用程式開發人員應將其他邏輯加入至其
應用程式來處理重複的訊息傳遞。 通常您可使用
使用 **getMessageId** 仍會對訊息的方法
將在各個傳遞嘗試中保持不變。

## 刪除主題和訂用帳戶

刪除主題和訂閱的主要方式是使用
**ServiceBusContract** 物件。 刪除主題也將會刪除對主題註冊的
任何訂閱。 您也可以個別刪除訂閱。

    // Delete subscriptions
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Delete a topic
    service.deleteTopic("TestTopic");

## 後續步驟

現在您已了解服務匯流排佇列的基本概念，請參閱 [服務匯流排佇列、 主題和訂閱][] 如需詳細資訊。

  [Azure SDK for Java]: http://azure.microsoft.com/develop/java/
  [Azure Toolkit for Eclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
  [Azure classic portal]: http://manage.windowsazure.com/
  [Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx 
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
