<properties 
   pageTitle="使用服務匯流排的共用存取簽章驗證 | Microsoft Azure"
   description="使用服務匯流排的 SAS 驗證詳細資訊"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/09/2015"
   ms.author="sethm" />


# 使用服務匯流排的共用存取簽章驗證

[共用存取簽章 (SAS)](service-bus-sas-overview.md) 驗證可讓應用程式使用命名空間，或訊息實體 (佇列或主題) 上設定的存取金鑰的特定權限相關聯的服務匯流排驗證。 您可以接著使用此金鑰來產生 SAS 權杖，以便用戶端用來向服務匯流排進行驗證。

SAS 驗證支援包含在 Azure SDK 2.0 版或更新版本中。 如需服務匯流排驗證的詳細資訊，請參閱 [服務匯流排驗證與授權](service-bus-authentication-and-authorization.md)。

## 概念

服務匯流排中的 SAS 驗證牽涉到在服務匯流排資源上設定具有相關權限的密碼編譯金鑰。 用戶端會藉由出示 SAS 權杖來宣告服務匯流排資源的存取權。 此權杖包含所存取的資源 URI 以及使用設定的金鑰所簽署的有效期限。

您可以設定服務匯流排上的共用存取簽章授權 [轉送](service-bus-fundamentals-hybrid-solutions.md/#relays), ，[佇列](service-bus-fundamentals-hybrid-solutions.md/#queues), ，[主題](service-bus-fundamentals-hybrid-solutions.md/#topics), ，和 [事件中樞](https://azure.microsoft.com/documentation/services/event-hubs/)。

SAS 驗證會使用下列元素︰

- [共用存取授權規則](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx): 256 位元 Base64 表示法中的主要密碼編譯金鑰、 選用的次要金鑰，以及索引鍵的名稱和相關聯的權限 (一堆 *接聽*, ，*傳送*, ，或 *管理* 權限)。

- [共用存取簽章](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.sharedaccesssignature.aspx) 語彙基元: 使用 hmac-sha256 的資源字串，包含存取資源的 URI、 過期以及加密編譯金鑰產生。 簽章和以下各節說明其他項目會被格式化成一個字串加入表單 [SharedAccessSignature](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.sharedaccesssignature.aspx) 語彙基元。

## 共用存取簽章驗證的設定

您可以設定 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) 服務匯流排命名空間、 佇列或主題中的規則。 設定 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) 服務匯流排訂用帳戶目前不支援，但您可以使用命名空間或主題上設定的規則來保護存取訂用帳戶。 說明此程序的工作範例，請參閱 [使用共用存取簽章 (SAS) 驗證服務匯流排訂閱](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c) 範例。

在服務匯流排命名空間、佇列或主題上最多可以設定 12 條這類規則。 在服務匯流排命名空間上設定的規則可套用到該命名空間中的所有實體。

![SAS](./media/service-bus-shared-access-signature-authentication/IC676272.gif)

在此圖中，manageRuleNS**、sendRuleNS** 和 listenRuleNS** 授權規則會套用到佇列 Q1 和主題 T1，而 listenRuleQ** 和 sendRuleQ** 只會套用到佇列 Q1，*sendRuleT* 只會套用到主題 T1。

主要參數 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) 如下:

| 參數| 說明|
|---|---|
| *KeyName*| 用以描述授權規則的字串。|
| *PrimaryKey*| 用來簽署和驗證 SAS 權杖的 Base64 編碼 256 位元主要金鑰。|
| *SecondaryKey*| 用來簽署和驗證 SAS 權杖的 Base64 編碼 256 位元次要金鑰。|
| *AccessRights*| 授權規則所授與的存取權限清單。這些權限可以是任何接聽、傳送和管理權限的集合。|

佈建服務匯流排命名空間時， [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx), ，與 [KeyName](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.keyname.aspx) 設 **RootManageSharedAccessKey**, ，預設會建立。 兩個預設 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) 物件也會設定為通知中心: 一個接聽、 傳送和管理權限，另一個具有唯一的接聽權限。

## 重新產生並撤銷共用存取授權規則的金鑰

建議您定期重新產生金鑰用於 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) 物件。 應用程式，通常應該使用 [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) 來產生 SAS 權杖。 當重新產生金鑰，您應該取代 [SecondaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.secondarykey.aspx) 與舊的主要索引鍵，並產生新的金鑰，為新的主索引鍵。 如此一來，您即可繼續使用以舊的主要金鑰簽發但尚未到期的權杖進行授權。

如果金鑰遭到入侵，因此您必須以撤銷金鑰，您可以重新產生兩者 [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) 和 [SecondaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.secondarykey.aspx) 的 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx), ，並取代成新的金鑰。 此程序會讓所有以舊金鑰簽章的權杖失效。

## 產生共用存取簽章權杖

任何有權存取在共用存取授權規則中指定的簽署金鑰的用戶端都可以產生 SAS 權杖。 格式如下：

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

**簽章** 的 SAS 權杖會使用計算來簽署字串的 hmac-sha256 雜湊 [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) 授權規則的屬性。 string-to-sign 是由資源 URI 和有效期限所組成，其格式如下：

```
StringToSign = <resourceURI> + "\n" + expiry;
```

請注意，您應該使用編碼的資源 URI 進行此操作。資源 URI 是宣告其存取權之服務匯流排資源的完整 URI。例如， `http://&lt;namespace&gt;.servicebus.windows.net/&lt;entityPath&gt;` 或 `sb: / / < 命名空間 >.servicebus.windows.net/ < entityPath >`，也就是 `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`。

有效期限會以從新紀元時間 (Epoch) 1970 年 1 月 1日 00:00:00 UTC 時間至今的秒數表示。

用於簽署的共用存取授權規則必須設定於此 URI 或其中一個階層式上層所指定的實體。 例如， `http://contoso.servicebus.windows.net/contosoTopics/T1` 或 `http://contoso.servicebus.windows.net` 前一個範例中。

SAS 權杖的有效之下的所有資源的 `< resourceURI >` 至簽署字串中使用。

[KeyName](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.keyname.aspx) 在 SAS 權杖指的是 **keyName** 用來產生權杖的共用的存取授權規則。

*URL-encoded-resourceURI* 必須與簽章預算期間 string-to-sign 中所用的 URI 相同。 它應該是 [百分比編碼](https://msdn.microsoft.com/library/4fkewx0t.aspx)。

## 如何搭配使用共用存取簽章驗證與服務匯流排

下列案例包括授權規則的設定、SAS 權杖的產生以及用戶端授權。

如需完整的說明設定，並使用 SAS 授權的服務匯流排應用程式的工作範例，請參閱 [使用服務匯流排的共用存取簽章驗證](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8)。 相關的範例，說明如何使用命名空間或主題，以保護服務匯流排訂用帳戶上設定 SAS 授權規則位於這裡: [使用共用存取簽章 (SAS) 驗證服務匯流排訂閱](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c)。

## 存取命名空間上的共用存取授權規則

服務匯流排命名空間根目錄上的作業需要憑證驗證。 您必須針對您的 Azure 訂用帳戶上傳管理憑證。 若要上傳管理憑證，請按一下 [ **設定** 的左窗格中 [Azure 傳統入口網站 []][]。 如需 Azure 管理憑證的詳細資訊，請參閱 [建立 Azure 的管理憑證](https://msdn.microsoft.com/library/azure/gg551722.aspx)。

可供存取服務匯流排命名空間上共用存取授權規則的端點如下所示：

```
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/
```

To create a [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) object on a Service Bus namespace, execute a POST operation on this endpoint with the rule information serialized as JSON or XML. For example:
```
存取命名空間上的授權規則的起始位址
字串 baseAddress = @"https://management.core.windows.net/<subscriptionId>/服務/服務匯流排命名空間/<namespace>/AuthorizationRules/ 」。

以 base64 編碼的 256 位元金鑰和傳送權限來設定授權規則
var sendRule = 新 Keyname (「 contosoSendAll 」，
    SharedAccessAuthorizationRule.GenerateRandomKey()，
    new [] {AccessRights.Send})。

服務匯流排命名空間根目錄上的作業需要憑證驗證。
WebRequestHandler 處理常式 = 新 WebRequestHandler
{
    ClientCertificateOptions = ClientCertificateOption.Manual
};
主體名稱來存取管理憑證
處理常式。ClientCertificates.Add (getcertificate 時發生 (<certificateSN>))。

HttpClient httpClient = 新 HttpClient(handler)
{
    BaseAddress = 新 Uri(baseAddress)
};
httpClient.DefaultRequestHeaders.Accept.Add (
    新 MediaTypeWithQualityHeaderValue("application/json"));
httpClient.DefaultRequestHeaders.Add ("x ms 版本"，"2015年-01-01");

執行 POST 作業上 baseAddress 上面建立驗證規則
var postResult = httpClient.PostAsJsonAsync (""，sendRule)。結果;
```

Similarly, use a GET operation on the endpoint to read the authorization rules configured on the namespace.

To update or delete a specific authorization rule, use the following endpoint:
```
https://management.core.windows.net/ {subscriptionId} / 服務/服務匯流排命名空間 / {命名空間} /AuthorizationRules/ {KeyName}
```

## Accessing Shared Access Authorization rules on an entity

You can access a [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) object configured on a Service Bus queue or topic through the [AuthorizationRules](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.authorizationrules.aspx) collection in the corresponding [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx), [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx), or [NotificationHubDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.notifications.notificationhubdescription.aspx) objects.

The following code shows how to add authorization rules for a queue.
```
建立 NamespaceManager 作業的執行個體
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString ( 
    <connectionString> );
QueueDescription qd = 新 QueueDescription ( <qPath> );

建立為"contosoQSendKey"keyname send 權限的規則
並將它新增至佇列描述。
qd。Authorization.Add (新 Keyname (「 contosoSendKey 」， 
    SharedAccessAuthorizationRule.GenerateRandomKey()， 
    new [] {AccessRights.Send}))。

建立為"contosoQListenKey"keyname 接聽權限的規則
並將它新增至佇列描述。
qd。Authorization.Add (新 Keyname (「 contosoQListenKey 」，
    SharedAccessAuthorizationRule.GenerateRandomKey()，
    new [] {AccessRights.Listen}))。

建立為"contosoQManageKey"keyname 管理權限的規則
並將它新增至佇列描述。
管理的權限的規則也必須擁有的傳送與接收權限。
qd。Authorization.Add (新 Keyname (「 contosoQManageKey 」，
    SharedAccessAuthorizationRule.GenerateRandomKey()，
    new [] {AccessRights.Manage，AccessRights.Listen，AccessRights.Send}))。

建立佇列。
nsm。CreateQueue(qd)
```

## Using Shared Access Signature authorization

Applications using the Azure .NET SDK with the Service Bus .NET libraries can use SAS authorization through the [SharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.aspx) class. The following code illustrates the use of the token provider to send messages to a Service Bus queue.
```
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri (「 sb 」， 
    <yourServiceNamespace>, 字串。空白);
MessagingFactory mf = MessagingFactory.Create (runtimeUri， 
    TokenProvider.CreateSharedAccessSignatureTokenProvider (keyName，索引鍵))。
QueueClient sendClient = mf。CreateQueueClient(qPath)

傳送 hello 訊息至佇列。
BrokeredMessage helloMessage = 新 BrokeredMessage ("Hello，服務匯流排!");
helloMessage.MessageId = 「 SAS-範例-訊息 」。
sendClient.Send(helloMessage)
```

應用程式在接受連接字串的方法中使用 SAS 連接字串，也可以使用 SAS 進行驗證。

請注意，若要搭配使用 SAS 授權與服務匯流排轉送，您可以使用在服務匯流排命名空間上設定的 SAS 金鑰。 如果您明確建立轉送的命名空間 ([NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 與 [RelayDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.relaydescription.aspx)) 物件時，您可以設定該轉送的 SAS 規則。 若要搭配使用 SAS 授權與服務匯流排訂用帳戶，您可以使用在服務匯流排命名空間或主題上設定的 SAS 金鑰。

## 服務匯流排作業所需的權限

下表顯示服務匯流排資源上各種作業所需的存取權限。

| 作業| 所需的宣告| 宣告範圍|
|---|---|---|
| **命名空間**| | |
| 在命名空間上設定授權規則| 管理| 任何命名空間位址|
| **服務登錄**| | |
| 列舉私人原則| 管理| 任何命名空間位址|
| 轉送| | |
| 開始在命名空間上接聽| 接聽| 任何命名空間位址|
| 將訊息傳送至命名空間上的接聽程式| 傳送| 任何命名空間位址|
| **佇列**| | |
| 建立佇列| 管理| 任何命名空間位址|
| 刪除佇列| 管理| 任何有效的佇列位址|
| 列舉佇列| 管理| /$Resources/Queues|
| 取得佇列描述| 管理或傳送| 任何有效的佇列位址|
| 設定佇列的授權規則| 管理| 任何有效的佇列位址|
| 傳送到佇列中| 傳送| 任何有效的佇列位址|
| 從佇列接收訊息| 接聽| 任何有效的佇列位址|
| 在 peek-lock 模式中接收訊息後放棄或完成訊息| 接聽| 任何有效的佇列位址|
| 延遲訊息以便稍後擷取| 接聽| 任何有效的佇列位址|
| 讓訊息寄不出去| 接聽| 任何有效的佇列位址|
| 取得與訊息佇列工作階段相關聯的狀態| 接聽| 任何有效的佇列位址|
| 設定與訊息佇列工作階段相關聯的狀態| 接聽| 任何有效的佇列位址|
| **主題**| | |
| 建立主題| 管理| 任何命名空間位址|
| 刪除主題| 管理| 任何有效的主題位址|
| 列舉主題| 管理| /$Resources/Topics|
| 取得主題描述| 管理或傳送| 任何有效的主題位址|
| 設定主題的授權規則| 管理| 任何有效的主題位址|
| 傳送至主題| 傳送| 任何有效的主題位址|
| **訂用帳戶**| | |
| 建立訂閱| 管理| 任何命名空間位址|
| 刪除訂用帳戶| 管理| ../myTopic/Subscriptions/mySubscription|
| 列舉訂用帳戶| 管理| ../myTopic/Subscriptions|
| 取得訂用帳戶描述| 管理或接聽| ../myTopic/Subscriptions/mySubscription|
| 在 peek-lock 模式中接收訊息後放棄或完成訊息| 接聽| ../myTopic/Subscriptions/mySubscription|
| 延遲訊息以便稍後擷取| 接聽| ../myTopic/Subscriptions/mySubscription|
| 讓訊息寄不出去| 接聽| ../myTopic/Subscriptions/mySubscription|
| 取得與主題工作階段相關聯的狀態| 接聽| ../myTopic/Subscriptions/mySubscription|
| 設定與主題工作階段相關聯的狀態| 接聽| ../myTopic/Subscriptions/mySubscription|
| **規則**| | |
| 建立規則| 管理| ../myTopic/Subscriptions/mySubscription|
| 刪除規則| 管理| ../myTopic/Subscriptions/mySubscription|
| 列舉規則| 管理或接聽| ../myTopic/Subscriptions/mySubscription/Rules|
| **通知中樞**| | |
| 建立通知中樞| 管理| 任何命名空間位址|
| 建立或更新使用中裝置的註冊| 接聽或管理| ../notificationHub/tags/{tag}/registrations|
| 更新 PNS 資訊| 接聽或管理| ../notificationHub/tags/{tag}/registrations/updatepnshandle|
| 傳送至通知中樞| 傳送| ../notificationHub/messages|

## 後續步驟

在服務匯流排 SAS 的高階概觀，請參閱 [共用存取簽章](service-bus-sas-overview.md)。

請參閱 [服務匯流排驗證與授權](service-bus-authentication-and-authorization.md) 如需有關服務匯流排驗證的詳細背景。


[azure classic portal]: http://manage.windowsazure.com 

