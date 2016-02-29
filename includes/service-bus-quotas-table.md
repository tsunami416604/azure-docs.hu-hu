下表列出服務匯流排訊息的特定配額資訊。 事件中樞限制會包含在此資料表中，但如需事件中樞的詳細資訊，請參閱 [事件中樞定價](http://azure.microsoft.com/pricing/details/event-hubs/)。 定價資訊及其他配額的服務匯流排，請參閱 [服務匯流排定價](http://azure.microsoft.com/pricing/details/service-bus/) 概觀。

|配額名稱|Scope|類型|超出時的行為|值|
|---|---|---|---|---|
| 每個 Azure 訂用帳戶的命名空間數目上限|命名空間|靜態|入口網站會拒絕後續對於更多命名空間的要求。|100|
|佇列/主題大小|實體|在建立佇列/主題時定義。|內送訊息將會遭到拒絕，而且呼叫端程式碼將會收到例外狀況。|1,2,3,4 或 5 GB。<br /><br />如果 [分割](service-bus-partitioning.md) 已啟用，佇列/主題大小上限是 80 GB。|
|命名空間上的並行連線數目|命名空間|靜態|後續對更多連接的要求將會遭到拒絕，而且呼叫端程式碼將會收到例外狀況。 REST 作業不會計入並行 TCP 連線內。|NetMessaging: 1000<br /><br />AMQP: 5000|
|佇列/主題/訂用帳戶實體上的並行連線數目|實體|靜態|後續對更多連接的要求將會遭到拒絕，而且呼叫端程式碼將會收到例外狀況。 REST 作業不會計入並行 TCP 連線內。|受限於每個命名空間的並行連線數限制。|
|佇列/主題/訂用帳戶實體上的並行接收要求數目|實體|靜態|後續接收要求將會遭到拒絕，而且呼叫端程式碼將會收到例外狀況。 這個配額套用至一個主題的所有訂用帳戶的並行接收作業數目合計。|5,000|
|轉送上的並行接聽程式數目|實體|靜態|後續對更多連接的要求將會遭到拒絕，而且呼叫端程式碼將會收到例外狀況。|25|
|並行轉送接聽程式數目|全系統|靜態|後續對更多連接的要求將會遭到拒絕，而且呼叫端程式碼將會收到例外狀況。|2,000|
|服務命名空間中的所有轉送端點的並行轉送連線數目|全系統|靜態|-|5,000|
|每個服務命名空間的轉送端點數目|全系統|靜態|-|10,000|
|每個服務命名空間的主題/佇列數目|全系統|靜態|新主題或佇列服務命名空間上的建立的後續要求將遭到拒絕。 如此一來，如果透過設定 [Azure 傳統入口網站][], ，將會產生錯誤訊息。 如果從管理 API 呼叫，呼叫程式碼會收到例外狀況。|10,000<br /><br />主題和佇列服務命名空間中的總數必須小於或等於 10000。|
|數字的分割主題/佇列每個服務命名空間|全系統|靜態|建立新的分割的主題或佇列服務命名空間上的後續要求將遭到拒絕。 如此一來，如果透過設定 [Azure 傳統入口網站][], ，將會產生錯誤訊息。 如果從管理 API，稱為 **QuotaExceededException** 程式呼叫的程式碼將會收到例外狀況。|100<br /><br />每個分割佇列或主題的配額計數為每個命名空間 10,000 個實體。|
|任何訊息的實體名稱的大小上限: 命名空間、 佇列、 主題、 訂閱或事件中心|實體|靜態|-|50 個字元|
|事件中心事件的大小上限|全系統|靜態|-|256 KB|
|佇列/主題/訂閱實體的訊息大小|全系統|靜態|超過這些配額的內送訊息將會遭到拒絕，並呼叫程式碼將會收到例外狀況。|最大訊息大小: 256 KB。 <br /><br />**請注意** 系統負擔，因為這項限制通常是略小於 256KB。<br /><br />標頭大小上限: 64 KB<br /><br />屬性包中的標頭屬性數目上限: **MaxValue**<br /><br />屬性包中屬性的最大: 沒有明確的限制。 受限於標頭大小上限。|
|訊息大小為 [NetOnewayRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.netonewayrelaybinding.aspx) 和 [NetEventRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.neteventrelaybinding.aspx) 轉送|全系統|靜態|超過這些配額的內送訊息將會遭到拒絕，並呼叫程式碼將會收到例外狀況。|64 KB
|訊息大小為 [HttpRelayTransportBindingElement](https://msdn.microsoft.com/library/microsoft.servicebus.httprelaytransportbindingelement.aspx) 和 [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) 轉送|全系統|靜態|-|無限|
|訊息佇列/主題/訂閱實體的屬性大小|全系統|靜態|A **SerializationException** 會產生例外狀況。|每個屬性的最大訊息屬性大小為 32k。 所有屬性的累計大小不得超過 64K。 這適用於整個標頭 [BrokeredMessage](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.aspx), ，其中同時包含使用者內容，以及系統屬性 (例如 [SequenceNumber](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.sequencenumber.aspx), ，[標籤](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.label.aspx), ，[MessageId](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx), ，依此類推)。|
|每個主題的訂用帳戶數目|全系統|靜態|建立主題的其他訂閱的後續要求將遭到拒絕。 因此，如果透過入口網站設定，將會顯示錯誤訊息。 如果從管理 API 呼叫將會收到例外狀況，呼叫程式碼。|2,000|
|每個主題的 SQL 篩選數目|全系統|靜態|建立主題的其他篩選的後續要求將遭到拒絕，並呼叫程式碼將會收到例外狀況。|2,000|
|每個主題的相互關聯篩選數目|全系統|靜態|建立主題的其他篩選的後續要求將遭到拒絕，並呼叫程式碼將會收到例外狀況。|100,000|
|SQL 篩選器動作的大小|全系統|靜態|建立其他篩選的後續要求將遭到拒絕，並呼叫程式碼將會收到例外狀況。|篩選條件字串的最大長度: 1024 (1k)。<br /><br />規則動作字串的最大長度: 1024 (1k)。<br /><br />每個規則動作的運算式數目上限: 32。|

[Azure classic portal]: http://manage.windowsazure.com
