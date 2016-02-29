<properties 
    pageTitle="如何將服務匯流排佇列搭配 Python 使用 | Microsoft Azure" 
    description="了解如何從 Python 使用 Azure 服務匯流排佇列。" 
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


# 如何使用服務匯流排佇列

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

本文說明如何使用服務匯流排佇列。 在 Python 中並使用這些範例 [Python Azure 封裝][]。 涵蓋的案例包括 **建立佇列、 傳送和接收訊息**, ，和 **刪除佇列**。

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

> [AZURE.NOTE] 若要安裝 Python 或 [Python Azure 封裝][], ，請參閱 [Python 安裝指南](../python-how-to-install.md)。

## 建立佇列

 **ServiceBusService** 物件可讓您能夠使用佇列。 將下列程式碼新增至您想要在其中以程式設計方式存取服務匯流排之任何 Python 檔案內的頂端附近：

```
from azure.servicebus import ServiceBusService, Message, Queue
```

下列程式碼會建立 **ServiceBusService** 物件。 請使用您的命名空間、共用存取簽章 (SAS) 金鑰名稱和值來取代 `mynamespace`、`sharedaccesskeyname` 和 `sharedaccesskey`。

```
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

SAS 金鑰名稱和值的值位於 [Azure 傳統入口網站][] 連接資訊，或在 Visual Studio **屬性** 窗格時 (如前一節所示)，伺服器總管] 中選取服務匯流排命名空間。

```
bus_service.create_queue('taskqueue')
```

**create_queue** 也支援其他選項，讓您覆寫訊息的時間 (TTL) 或佇列大小上限等預設佇列設定。 下列範例會將佇列大小上限設為 5GB，並將 TTL 時間設為 1 分鐘：

```
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

bus_service.create_queue('taskqueue', queue_options)
```

## 傳送訊息至佇列

若要將訊息傳送至服務匯流排佇列，應用程式會呼叫 **send\_queue\_message** 方法 **ServiceBusService** 物件。

下列範例示範如何將測試訊息傳送至佇列，名為 *taskqueue 使用* **send\_queue\_message**:

```
msg = Message(b'Test Message')
bus_service.send_queue_message('taskqueue', msg)
```

服務匯流排佇列最多可支援 256 KB 的訊息大小 (包含標準和自訂應用程式屬性的標頭可以容納 64 KB 的大小上限)。 佇列中所保存的訊息數目沒有限制，但佇列所保存的訊息大小總計會有最高限制。 此佇列大小會在建立時定義，上限是 5 GB。 如需配額的詳細資訊，請參閱 [Azure 佇列和服務匯流排佇列][]。

## 從佇列接收訊息

接收來自佇列使用的訊息 **receive\_queue\_message** 方法 **ServiceBusService** 物件:

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
print(msg.body)
```

從佇列刪除訊息時讀取時參數 **peek\_lock** 設為 **False**。 您可以讀取 (查看) 並鎖定訊息，方法將從佇列刪除 **peek\_lock** 至 **True**。

隨著接收作業讀取及刪除訊息之行為是最簡單的模型，且最適合可容許在發生失敗時不處理訊息的應用程式案例。 若要了解這一點，請考慮取用者發出接收要求，接著系統在處理此要求之前當機的案例。 因為服務匯流排會將訊息標示為已取用，當應用程式重新啟動並開始重新取用訊息時，它將會遺漏當機前已取用的訊息。

如果 **peek\_lock** 參數設為 **True**, ，接收會變成兩階段作業，因此可以支援無法容許遺漏訊息的應用程式。 當服務匯流排收到要求時，它會尋找要取用的下一個訊息、將其鎖定以防止其他取用者接收此訊息，然後將它傳回應用程式。 藉由呼叫應用程式完成處理訊息 (或可靠地儲存供未來處理) 之後，完成接收程序的第二個階段 **刪除** 方法 **訊息** 物件。  **刪除** 方法會標示為已取用的訊息，並將它從佇列移除。

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
print(msg.body)

msg.delete()
```

## 如何處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，可協助您從應用程式的錯誤或處理訊息的問題中順利復原。 如果接收者應用程式無法處理訊息，因為某種原因，則它可以呼叫 **解除鎖定** 方法 **訊息** 物件。 這將導致服務匯流排將佇列中的訊息解除鎖定，讓此訊息可以被相同取用應用程式或其他取用應用程式重新接收。

與在佇列內鎖定之訊息相關的還有逾時，如果應用程式無法在鎖定逾時到期之前處理訊息 (例如，如果應用程式當機)，則服務匯流排會自動解除鎖定訊息，並讓訊息可以被重新接收。

該應用程式當機之前處理訊息之後 **刪除** 呼叫方法，然後將訊息傳遞給應用程式重新啟動時。 這通常稱為 **至少處理一次**, ，也就是將至少一次處理每個訊息，但在某些情況下可能會重新傳遞相同訊息。 如果案例無法容許重複處理，則應用程式開發人員應在其應用程式中加入其他邏輯，以處理重複的訊息傳遞。 這通常您可使用 **MessageId** 會保持不變各個傳遞嘗試的訊息屬性。

## 後續步驟

了解基本的服務匯流排佇列之後，請參考下列連結以取得更多資訊。

-   請參閱 [佇列、 主題和訂閱][]。

[Azure classic portal]: http://manage.windowsazure.com
[Python Azure package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Azure Queues and Service Bus queues]: service-bus-azure-and-service-bus-queues-compared-contrasted.md#capacity-and-quotas
 

