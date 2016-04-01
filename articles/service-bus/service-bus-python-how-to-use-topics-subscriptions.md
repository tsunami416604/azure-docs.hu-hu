<properties 
    pageTitle="如何搭配使用服務匯流排主題與 Python | Microsoft Azure" 
    description="了解如何從 Python 使用 Azure 服務匯流排主題和訂閱。" 
    services="service-bus" 
    documentationCenter="python" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="10/08/2015" 
    ms.author="sethm"/>

# 如何使用服務匯流排主題和訂用帳戶

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

本文說明如何使用服務匯流排主題和訂用帳戶。 在 Python 中並使用這些範例 [Python Azure 封裝][]。 涵蓋的案例包括 **建立主題和訂閱**, ，**建立訂閱篩選器**, ，**傳送訊息至主題**, ，**從訂閱接收訊息**, ，和 **刪除主題和訂閱**。 如需主題和訂閱的詳細資訊，請參閱 [下一步](#next-steps) 一節。

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

**注意 ︰** 如果您需要安裝 Python 或 [Python Azure 封裝][], ，請參閱 [Python 安裝指南](../python-how-to-install.md)。

## 建立主題

 **ServiceBusService** 物件可讓您處理主題。 將下列內容新增至您想要在其中以程式設計方式存取服務匯流排之任何 Python 檔案內的頂端附近：

```
from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

下列程式碼會建立 **ServiceBusService** 物件。 請使用真實的命名空間、共用存取簽章 (SAS) 金鑰名稱和金鑰值來取代 `mynamespace`、`sharedaccesskeyname` 和 `sharedaccesskey`。

```
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

您可以取得 SAS 金鑰名稱的值，並從 [Azure 傳統入口網站][] **連接資訊** 視窗。

```
bus_service.create_topic('mytopic')
```

**create\_topic** 也支援其他選項，讓您覆寫訊息存留時間或主題大小上限等預設主題設定。 下列範例會將主題大小上限設為 5 GB，並將存留時間 (TTL) 的值設為 1 分鐘：

```
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## 建立訂用帳戶

也會建立主題訂閱 **ServiceBusService** 物件。 訂閱是具名的，它們能擁有選用的篩選器，以限制傳遞至訂閱之虛擬佇列的訊息集合。

> [AZURE.NOTE] 訂閱是持續性，將會持續存在，直到本身或它們的訂閱，會被刪除的主題。

### 使用預設 (MatchAll) 篩選器建立訂閱

 **MatchAll** 篩選器是預設篩選器，如果沒有指定篩選時建立新的訂閱。 當 **MatchAll** 篩選器時，發佈至主題的所有訊息都會被都置於訂閱的虛擬佇列。 下列範例會建立名為 'AllMessages' 的訂閱，並使用預設 **MatchAll**
篩選器。

```
bus_service.create_subscription('mytopic', 'AllMessages')
```

### 使用篩選器建立訂用帳戶

您也可以定義篩選器，讓您指定傳送至主題的哪些訊息應顯示在特定主題訂用帳戶中。

訂閱所支援的篩選器的最具彈性的型別是 **SqlFilter**, ，可用來實作 SQL92 的子集。 SQL 篩選器會對發佈至主題之訊息的屬性運作。 如需可與 SQL 篩選運算式的詳細資訊，請參閱 [SqlFilter.SqlExpression][] 語法。

可以透過將篩選器新增至訂閱 **create\_rule** 方法 **ServiceBusService** 物件。 此方法可讓您將篩選器新增至現有的訂閱中。

> [AZURE.NOTE] 由於預設篩選器會自動套用至所有新訂閱，您必須先移除預設篩選器或 **MatchAll** 會覆寫您指定的其他任何篩選器。 您可以使用，以移除預設規則 **delete\_rule** 方法 **ServiceBusService** 物件。

下列範例會建立名為 `HighMessages` 與 **SqlFilter** 只選取自訂的訊息 **messagenumber** 屬性大於 3:

```
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

同樣地，下列範例會建立名為 `LowMessages` 與 **SqlFilter** sqlfilter 只選取 **messagenumber** 屬性小於或等於 3:

```
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

現在，當訊息傳送到 `mytopic` 它一定會傳遞到已訂閱 **AllMessages** 主題訂用帳戶，並選擇性地傳遞至已訂閱 **HighMessages** 和 **LowMessages** 主題訂閱的接收者 （視訊息內容而定）。

## 傳送訊息至主題

若要將訊息傳送至服務匯流排主題，您的應用程式必須使用 **send\_topic\_message** 方法 **ServiceBusService** 物件。

下列範例說明如何將五個測試訊息傳送至 `mytopic`。 請注意， **messagenumber** 的每個訊息的屬性值的變化迴圈反覆運算上 （這可判斷哪些訂閱會接收） ︰

```
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
    bus_service.send_topic_message('mytopic', msg)
```

服務匯流排主題支援 256 Kb 的訊息大小上限 (包含標準和自訂應用程式屬性的標頭可以容納 64 Kb 的大小上限)。 主題中所保存的訊息數目沒有限制，但主題所保存的訊息大小總計會有最高限制。 此主題大小會在建立時定義，上限是 5 GB。 如需配額的詳細資訊，請參閱 [Azure 佇列和服務匯流排佇列][]。

## 自訂閱接收訊息

從訂閱接收訊息 **receive\_subscription\_message** 方法 **ServiceBusService** 物件 ︰

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

從訂閱刪除訊息時讀取時參數 **peek\_lock** 設為 **False**。 您可以讀取 （查看） 並鎖定訊息，方法將從佇列刪除 **peek\_lock** 至 **True**。

隨著接收作業讀取及刪除訊息之行為是最簡單的模型，且最適合可容許在發生失敗時不處理訊息的應用程式案例。 若要了解這一點，請考慮取用者發出接收要求，接著系統在處理此要求之前當機的案例。 因為服務匯流排會將訊息標示為已取用，當應用程式重新啟動並開始重新取用訊息時，它將會遺漏當機前已取用的訊息。

如果 **peek\_lock** 參數設為 **True**, ，接收會變成兩階段作業，因此可以支援無法容許遺漏訊息的應用程式。 當服務匯流排收到要求時，它會尋找要取用的下一個訊息、將其鎖定以防止其他取用者接收此訊息，然後將它傳回應用程式。 藉由呼叫應用程式完成處理訊息 （或可靠地儲存供未來處理） 之後，完成接收程序的第二個階段 **刪除** 方法 **訊息** 物件。  **刪除** 方法將訊息標示為已取用，並將它從訂閱移除。

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
print(msg.body)

msg.delete()
```

## 如何處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，可協助您從應用程式的錯誤或處理訊息的問題中順利復原。 如果接收者應用程式無法處理訊息，因為某種原因，則它可以呼叫 **解除鎖定** 方法 **訊息** 物件。 這將導致服務匯流排將訂用帳戶中的訊息解除鎖定，讓此訊息可以被相同取用應用程式或其他取用應用程式重新接收。

與在訂閱內鎖定訊息相關的還有逾時，如果應用程式無法在鎖定逾時到期之前處理訊息 (例如，如果應用程式當機)，則服務匯流排會自動解除鎖定訊息，並讓訊息可以被重新接收。

該應用程式當機之前處理訊息之後 **刪除** 呼叫方法，然後將訊息傳遞給應用程式重新啟動時。 這通常稱為 **至少處理一次**, ，也就是將至少一次處理每個訊息，但在某些情況下可能會重新傳遞相同訊息。 如果案例無法容許重複處理，則應用程式開發人員應在其應用程式中加入其他邏輯，以處理重複的訊息傳遞。 這通常您可使用 **MessageId** 會保持不變各個傳遞嘗試的訊息屬性。

## 刪除主題和訂用帳戶

主題和訂閱是持續性的必須明確地刪除透過 [Azure 傳統入口網站][] 或以程式設計的方式。 下列範例示範如何刪除名為 `mytopic` 的主題：

```
bus_service.delete_topic('mytopic')
```

刪除主題也將會刪除對主題註冊的任何訂用帳戶。 您也可以個別刪除訂閱。 下列程式碼示範如何從 `mytopic` 主題刪除名為 `HighMessages` 的訂用帳戶：

```
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## 後續步驟

了解基本的服務匯流排主題之後，請參考下列連結以取得更多資訊。

-   請參閱 [佇列、 主題和訂閱][]。
-   請參考 [SqlFilter.SqlExpression][]。

[Azure classic portal]: http://manage.windowsazure.com
[Python Azure package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Azure Queues and Service Bus queues]: service-bus-azure-and-service-bus-queues-compared-contrasted.md#capacity-and-quotas 


