<properties 
   pageTitle="服務匯流排配對的命名空間 |Microsoft Azure"
   description="配對的命名空間實作詳細資料和成本"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" /> 
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/18/2015"
   ms.author="sethm" />


# 配對命名空間實作詳細資料和成本影響

[PairNamespaceAsync []][] 方法中，使用 [SendAvailabilityPairedNamespaceOptions []][] 執行個體，代替您執行可見的工作。 因為使用此功能時有一些成本考量，了解這些工作會有所幫助，以便預期發生時的行為。 API 會代表您下列從事下列自動行為：

-   建立待處理項目佇列。
-   建立 [MessageSender []][] 交談佇列或主題的物件。
-   當傳訊實體變成無法使用時，在嘗試偵測該實體何時可再度使用時傳送 ping 訊息到實體。
-   選擇性建立一組可將訊息從待處理項目佇列移到主要佇列的「訊息幫浦」。
-   協調關閉/錯誤的主要和次要 [MessagingFactory []][] 執行個體。

概括而言，此功能的運作方式如下：當主要實體的狀況良好時，不會發生任何行為變更。 當 [FailoverInterval []][] 持續時間超過和不成功，主要實體所看到會在之後傳送非暫時性 [Istransient []][] 或 [TimeoutException []][], ，會發生下列行為:

1.  傳送至主要實體的作業會停用，而系統會 ping 主要實體，直到可以成功傳送 ping 為止。

2.  已選取隨機待處理項目佇列。

3.  [BrokeredMessage []][] 物件路由傳送至所選擇的待處理項目佇列。

1.  如果傳送至所選待處理項目佇列的作業失敗，則會從輪替提取該佇列並選取新的佇列。 所有寄件者在 [MessagingFactory []][] 獲悉失敗執行個體。

下圖描繪順序。 首先，傳送端會傳送訊息。

![配對的命名空間][0]

無法傳送到主要佇列時，傳送端會開始傳送訊息到隨機選擇的待處理項目佇列。 同時，它會開始進行 ping 工作。

![配對的命名空間][1]

此時訊息仍在次要佇列中，而尚未傳遞到主要佇列。 一旦主要佇列再度恢復良好狀況，至少有一個程序應該執行 Syphon。 Syphon 會將各種待處理項目佇列中的訊息傳遞至適當的目的地實體 (佇列和主題)。

![配對的命名空間][2]

本主題的其餘部分將討論這些片段運作方式的特定詳細資料。

## 建立待處理項目佇列

[SendAvailabilityPairedNamespaceOptions []][] 物件傳遞給 [PairNamespaceAsync []][] 方法會指出您想要使用的待處理項目佇列數目。 每個待處理項目佇列則明確地建立具有下列屬性設定 (其他所有值都會都設為 [QueueDescription []][] 預設值):

| Path| [primary namespace]/x-servicebus-transfer/[index]，其中 [index] 是 [0, BacklogQueueCount) 中的值|
|----------------------------------------|------------------------------------------------------------------------------------------------------|
| MaxSizeInMegabytes| 5120|
| MaxDeliveryCount| int.MaxValue|
| DefaultMessageTimeToLive| TimeSpan.MaxValue|
| AutoDeleteOnIdle| TimeSpan.MaxValue|
| LockDuration| 1 分鐘|
| EnableDeadLetteringOnMessageExpiration| true|
| EnableBatchedOperations| true|

例如，第一個待處理項目佇列建立命名空間 **contoso** 名為 `contoso/x-服務匯流排傳輸/0`。

建立佇列時，程式碼會先查看是否有此佇列存在。 如果此佇列不存在，則會建立佇列。 程式碼不會清除「額外的」待處理項目佇列。 具體來說，如果主要命名空間的應用程式 **contoso** 要求五個待處理項目佇列積存佇列的路徑，但 `contoso/x-服務匯流排-傳輸/7` 存在，仍會出現額外的待處理項目佇列，而不是。 系統明確允許額外的待處理項目佇列存在但不會使用。 身為命名空間擁有者，您必須負責清除任何未使用/不需要的待處理項目佇列。 此決策的原因是服務匯流排無法得知您的命名空間中所有佇列的目的為何。 此外，如果佇列存在具有指定名稱，但不符合所擔任 [QueueDescription []][], ，則原因取決於您本身變更預設行為。 不保證您的程式碼會修改待處理項目佇列。 請務必徹底測試您的變更。

## 自訂 MessageSender

傳送時，所有訊息都會都移到內部 [MessageSender []][] 事情會 「 中斷。 」，排入佇列的所有項目時運作，以及重新導向至待處理項目正常運作的物件 收到非暫時性失敗時，會啟動計時器。 之後 [[TimeSpan]][] 期間組成 [FailoverInterval []][] 仍與屬性值的不成功的訊息傳送，容錯移轉。 此時，每個實體會發生下列情況︰

- Ping 工作會執行每個 [PingPrimaryInterval []][] 來查看是否有可用的實體。 此工作一旦成功，使用該實體的所有用戶端程式碼會立即開始將新訊息傳送到主要命名空間。

- 未來的要求，從任何其他傳送者傳送給該相同的實體會導致 [BrokeredMessage []][] 傳送修改坐在待處理項目佇列。 修改移除某些屬性從 [BrokeredMessage []][] 物件，並將它們儲存到其他位置。 下列屬性已被清除並新增於新別名之下，可讓服務匯流排和 SDK 以一致的方式處理訊息︰

| 舊屬性名稱| 新屬性名稱|
|-------------------------|-------------------|
| SessionId| x-ms-sessionid|
| TimeToLive| x-ms-timetolive|
| ScheduledEnqueueTimeUtc| x-ms-path|

原始目的地路徑也會以名為 x-ms-path 的屬性形式儲存在訊息內。 這種設計可讓多個實體的訊息共存於單一待處理項目佇列中。 這些屬性會由 Syphon 轉譯回來。

自訂 [MessageSender []][] 物件可能會發生問題，當訊息達到 256 KB 的限制和容錯移轉。 自訂 [MessageSender []][] 物件會儲存所有的佇列和主題一起放在待處理項目佇列的訊息。 此物件會將待處理項目佇列中許多主要項目的訊息混合在一起。 若要處理許多用戶端不知道彼此間的負載平衡，SDK 隨機挑選一個待處理項目佇列每個 [QueueClient []][] 或 [TopicClient []][] 您在程式碼中建立。

## Ping

Ping 訊息是空白 [BrokeredMessage []][] 與其 [ContentType []][] 屬性設定為 application/vnd.ms-服務匯流排-ping 和 [TimeToLive []][] 值為 1 秒。 此 ping 服務匯流排中有一個特性: 伺服器永遠不會傳送 ping 要求的任何呼叫端時 [BrokeredMessage []][]。 因此，您永遠都不必了解如何接收並忽略這些訊息。 每個實體 (唯一佇列或主題)，每個 [MessagingFactory []][] 視為無法使用時，將會被 ping 每個用戶端的執行個體。 根據預設，這會每分鐘發生一次。 Ping 訊息會被視為一般的服務匯流排訊息，可能會導致頻寬和訊息的費用。 只要用戶端偵測到系統可以使用，訊息就會停止。

## Syphon

應用程式中至少有一個可執行程式應該主動執行 Syphon。 Syphon 會執行持續 15 分鐘的長時間輪詢接收。 當所有實體都可使用且有 10 個待處理項目佇列時，裝載 Syphon 的應用程式會呼叫接收作業︰每小時 40 次、每天 960 次和 30 天內 28800 次。 當 Syphon 主動將訊息從待處理項目移到主要佇列時，每則訊息會發生下列收費 (訊息大小和頻寬的標準收費適用於所有階段)︰

1.  傳送至待處理項目佇列。

2.  從待處理項目佇列接收。

3.  傳送至主要佇列。

4.  從主要佇列接收。

## 關閉/錯誤行為

在主控虹吸，一次在主要或次要資料庫的應用程式內 [MessagingFactory []][] 錯誤或已關閉無對方，也會發生錯誤/關閉，且虹吸偵測到此狀態，虹吸就會運作。 如果其他 [MessagingFactory []][] 不會關閉在 5 秒鐘之內，虹吸將會發生錯誤仍開啟 [MessagingFactory []][]。

## 後續步驟

如需服務匯流排非同步訊息的詳細討論，請參閱 [非同步傳訊模式和高可用性]。


[pairnamespaceasync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx 
[sendavailabilitypairednamespaceoptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx 
[messagesender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx 
[messagingfactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx 
[failoverinterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.failoverinterval.aspx 
[messagingexception]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx 
[timeoutexception]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx 
[brokeredmessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx 
[0]: ./media/service-bus-paired-namespaces/IC673405.png 
[1]: ./media/service-bus-paired-namespaces/IC673406.png 
[2]: ./media/service-bus-paired-namespaces/IC673407.png 
[queuedescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx 
[timespan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx 
[pingprimaryinterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.pingprimaryinterval.aspx 
[queueclient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx 
[topicclient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx 
[contenttype]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx 
[timetolive]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx 
[asynchronous messaging patterns and high availability]: service-bus-async-messaging.md 

