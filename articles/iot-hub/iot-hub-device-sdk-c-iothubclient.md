<properties
    pageTitle="適用於 C 的 Azure IoT 裝置 SDK - IoTHubClient | Microsoft Azure"
    description="深入了解在適用於 C 的 Azure IoT 裝置 SDK 中使用 IoTHubClient 程式庫"
    services="iot-hub"
    documentationCenter=""
    authors="MichelBarnett"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="11/10/2015"
     ms.author="michelb"/>

# 適用於 C 的 Microsoft Azure IoT 裝置 SDK - 深入了解 IoTHubClient

 [先文章](iot-hub-device-sdk-c-intro.md) 中導入這一系列 **C 的 Microsoft Azure IoT 裝置 SDK**。 該文章已說明 SDK 中有兩個架構層。 基底是 **IoTHubClient** 程式庫直接管理 [IoT 中心之間的通訊。 另外還有 **序列化程式** 組建，除此之外，以提供序列化服務的程式庫。 在本文中我們將提供其他詳細資料上 **IoTHubClient** 程式庫。

前一篇文章說明如何使用 **IoTHubClient** 將事件傳送到 IoT 中心和接收訊息的程式庫。 這篇文章解釋如何更精確地管理延伸討論 *時* 您傳送和接收資料，引入您 **較低層級 Api**。 我們也將說明如何將屬性附加至事件 (和擷取訊息) 使用內容處理功能 **IoTHubClient** 程式庫。 最後，我們將提供其他說明，以不同的方式來處理從 IoT 中樞接收的訊息。

文件結束時，會涵蓋幾個其他的主題，包括更相關的裝置認證，以及如何變更的行為 **IoTHubClient** 透過組態選項。

我們將使用 **IoTHubClient** SDK 範例來說明這些主題。 如果您想要跟著做，請參閱 **iothub\_client\_sample\_http** 和 **iothub\_client\_sample\_amqp** c 的所有項目以下各節說明這些範例中示範的 Azure IoT 裝置 SDK 中包含的應用程式。

## 較低層級的 API

前一篇文章所述的基本作業 **IotHubClient** 脈絡 **iothub\_client\_sample\_amqp** 應用程式。 比方說，該文說明如何使用下列程式碼初始化程式庫。

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

此外，還會說明如何使用這個函式呼叫來傳送事件。

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

本文還會說明如何藉由註冊回呼函式來接收訊息。

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

本文同時示範了如何使用如下的程式碼釋放資源。

```
IoTHubClient_Destroy(iotHubClientHandle);
```

但是每個 API 都有隨附函式：

-   IoTHubClient\_LL\_CreateFromConnectionString

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy


這些函式的 API 名稱中都包含 "LL"。 除此之外，這其中每個函式的參數都會與其非 LL 的對應項目相同。 不過，這些函式的行為有一個重要的差異。

當您呼叫 **IoTHubClient\_CreateFromConnectionString**, ，基礎的程式庫建立新的執行緒在背景中執行。 此執行緒會將事件傳送到 IoT 中樞以及接收其訊息。 使用 "LL" API 時不會建立這類執行緒。 背景執行緒的建立是為了方便開發人員。 您完全不必擔心要明確地將事件傳送到 IoT 中樞以及從中接收訊息 -- 此動作會在背景中自動進行。 相對地，"LL" API 會在您需要時，讓您能夠明確控制與 IoT 中樞的通訊。

若要更深入了解，讓我們看看一個範例：

當您呼叫 **IoTHubClient\_SendEventAsync**, ，您在實際執行的動作在緩衝區中放置的事件。 建立當您呼叫的背景執行緒 **IoTHubClient\_CreateFromConnectionString** 持續監視這個緩衝區，並將它包含的任何資料傳送至 IoT 中心。 這些動作會在主執行緒執行其他工作的同時在背景中進行。

同樣地，當您註冊使用訊息的回呼函式 **IoTHubClient\_SetMessageCallback**, ，您所指示的 SDK，讓訊息接收，獨立於主執行緒時所叫用的回呼函式的背景執行緒。

"LL" API 不會建立背景執行緒。 而是必須呼叫新的 API 明確地與 IoT 中樞之間傳送和接收資料。 下列範例就將此進行示範。

 **Iothub\_client\_sample\_http** 隨附於 SDK 的應用程式示範較低層級 Api。 在該範例中，我們使用如下的程式碼，將事件傳送到 IoT 中樞：

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

前三行會建立訊息，而最後一行會傳送事件。 不過，如先前所述，「傳送」事件表示只是將資料放置於緩衝區中。 不會在網路上傳輸當我們呼叫 **IoTHubClient\_LL\_SendEventAsync**。 為了實際輸入 IoT 中心的資料，您必須呼叫 **IoTHubClient\_LL\_DoWork**, ，如這個範例所示:

```
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

此程式碼 (從 **iothub\_client\_sample\_http** 應用程式) 重複呼叫 **IoTHubClient\_LL\_DoWork**。 每次 **IoTHubClient\_LL\_DoWork** 是呼叫，它將某些事件從緩衝區傳送至 IoT 中心，而且它會擷取佇列的訊息傳送至裝置。 在後者的情況下，這表示如果我們已註冊訊息的回呼函式，則會叫用回呼 (假設所有訊息皆已加入佇列)。 我們會使用如下的程式碼來註冊這類回呼函式：

```
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

原因， **IoTHubClient\_LL\_DoWork** 通常稱為迴圈是每次呼叫時，它會傳送 *一些* IoT 中心的事件緩衝處理，並擷取 *下一個* 訊息加入佇列並等候裝置。 每次呼叫都不保證會傳送所有緩衝的事件或擷取所有加入佇列的訊息。 如果您想要傳送緩衝區中的所有事件，並繼續進行其他處理程序，您可以使用如下程式碼來取代此迴圈：

```
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

此程式碼呼叫 **IoTHubClient\_LL\_DoWork** 直到緩衝區中的所有事件都傳送到 IoT 中心。 請注意，這也不意味著已接收所有已加入佇列的訊息。 部分原因是因為檢查「所有」訊息的決定性並不如動作一般。 如果您擷取「所有」訊息，但另一個訊息又隨即傳送至裝置，會發生什麼事？ 更好的處理方法是利用程式化的逾時。 例如，每次叫用訊息回呼函式時，它可以重設計時器。 然後，您可以撰寫邏輯，以繼續處理，如果沒有訊息，例如接收到最後一個 *X* 秒。

當您完成輸入事件和接收訊息時，請務必呼叫相對應的函式來清除資源。

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

基本上，只有一組 API 會利用背景執行緒來傳送和接收資料，另一組 API 不會利用背景執行緒來執行相同的動作。 許多開發人員可能會偏好非 LL API，但是當開發人員想要明確控制網路傳輸時，較低層級 API 會很實用。 例如，有些裝置會隨時間收集資料，並且只在指定的時間間隔輸入事件 (例如，每小時一次或一天一次)。 較低層級 API 可在您與 IoT 中樞之間傳送和接收資料時，提供您明確控制的功能。 其他人純粹偏好較低層級 API 提供的簡單性 一切動作都發生在主執行緒上，而不是有些工作會在背景中發生。

無論您選擇哪種模型，請務必與您使用的 API 一致。 如果您透過呼叫啟動 **IoTHubClient\_LL\_CreateFromConnectionString**, ，請務必只使用對應的較低層級 Api 任何待處理的工作:

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy

-   IoTHubClient\_LL\_DoWork

相反的情況也成立。 如果您開始使用 **IoTHubClient\_CreateFromConnectionString**, ，然後使用非市的 Api 進行任何額外的處理。

在 c 中的 Azure IoT 裝置 SDK，請參閱 **iothub\_client\_sample\_http** 較低層級 Api 的完整範例應用程式。  **Iothub\_client\_sample\_amqp** 應用程式可以參考非-LL Api 的完整範例。

## 屬性處理

到我們描述了傳送資料的目前為止，我們也已經參考訊息的內文。 例如，請思考下列程式碼：

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

此範例會傳送訊息給 IoT 中樞，包含文字 "Hello World"。 不過，IoT 中樞也允許屬性附加至每個訊息。 這些屬性是可附加至訊息的名稱/值組。 例如，我們可以修改上述程式碼，將屬性附加至訊息：

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

我們一開始呼叫 **IoTHubMessage\_Properties** 並將它傳遞訊息的控制代碼。 我們得到傳回的是 **MAP\_HANDLE** 讓我們開始新增內容的參考。 後者透過呼叫 **Map\_AddOrUpdate**, ，後者會採用 MAP\_HANDLE、 屬性名稱和屬性值的參考。 利用此 API，我們可以依意願新增應用程式。

從讀取事件時 **事件中心**, ，接收者可以列舉屬性並擷取其對應的值。 例如，在.NET 中這會可藉由存取 [EventData 物件上的屬性集合](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx)。

在上述範例中，我們會將屬性附加至我們傳送給 IoT 中樞的事件。 屬性也可以附加至從 IoT 中樞接收的訊息。 如果我們想要從訊息擷取屬性，可以在訊息回呼函式中使用如下的程式碼：

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

若要呼叫 **IoTHubMessage\_Properties** 傳回 **MAP\_HANDLE** 參考。 然後我們將傳遞至該參考 **Map\_GetInternals** 來取得陣列的名稱/值組 (以及屬性的計數) 的參考。 此時就可以很容易地列舉屬性來取得我們想要的值。

您不必在應用程式中使用屬性。 不過，如果您需要在事件上設定或擷取訊息， **IoTHubClient** 程式庫可讓您輕鬆。

## 訊息處理

如先前所述，當訊息從送達 IoT 中心 **IoTHubClient** 回應叫用註冊的回呼函式的程式庫。 此函式有一個傳回參數值得額外說明。 以下是中的回呼函式的摘錄， **iothub\_client\_sample\_http** 範例應用程式:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

請注意，傳回的型別是 **IOTHUBMESSAGE\_DISPOSITION\_RESULT** ，在此案例中就會傳回 **IOTHUBMESSAGE\_ACCEPTED**。 有其他我們可以從這個函式傳回的值變更了 **IoTHubClient** 程式庫回應訊息的回呼。 選項如下。

-   **IOTHUBMESSAGE\_ACCEPTED** – 成功處理訊息。  **IoTHubClient** 程式庫不會叫用的回呼函式，再次使用相同的訊息。

-   **IOTHUBMESSAGE\_REJECTED** – 不處理訊息，並沒有任何想要執行這項操作在未來。  **IoTHubClient** 程式庫不應該叫用的回呼函式，再次使用相同的訊息。

-   **IOTHUBMESSAGE\_ABANDONED** – 不成功，處理訊息，但 **IoTHubClient** 程式庫應該叫用的回呼函式，再次使用相同的訊息。

前兩個傳回碼， **IoTHubClient** 程式庫傳送訊息至 IoT 中心指出應該從裝置佇列中刪除訊息，並不會傳遞一次。 最終結果一樣 (從裝置佇列刪除訊息)，但仍會記錄是否已接受或拒絕訊息。  對於可聽取意見回應並了解裝置已接受或拒絕特定訊息的訊息傳送者而言，記錄這項區別的功能非常實用。

在最後一個案例中，訊息也會傳送至 IoT 中樞，但它表示應重新傳遞訊息。 如果您遇到某個錯誤但想要再次嘗試處理訊息，您通常會放棄訊息。 相對地，當您遇到無法復原的錯誤時 (或者如果您只是決定不想處理訊息)，拒絕訊息是適當的方式。

在任何情況下，留意不同的傳回碼，以便您可以引發您想要的行為 **IoTHubClient** 程式庫。

## 替代裝置認證

如前所述，首先来使用 **IoTHubClient** 程式庫，是取得 **IOTHUB\_CLIENT\_HANDLE** 的呼叫如下所示:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

引數 **IoTHubClient\_CreateFromConnectionString** 是我們的裝置和參數，表示我們使用的 IoT 中心進行通訊的通訊協定的連接字串。 此連接字串的格式如下所示：

```
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

這個字串中包含四項資訊：IoT 中樞名稱、IoT 中樞尾碼、裝置識別碼和共用存取金鑰。 當您在 Azure 入口網站中建立 IoT 中樞執行個體時，可以取得 IoT 中樞的完整網域名稱 (FQDN) - 這可為您提供 IoT 中樞名稱 (FQDN 的第一個部分) 和 IoT 中樞尾碼 (FQDN 的其餘部分)。 當您註冊您的裝置 IoT 中心時，取得裝置識別碼和共用存取金鑰 (如中所述 [前一篇文章](iot-hub-device-sdk-c-intro.md))。

**IoTHubClient\_CreateFromConnectionString** 提供您一個方法來初始化程式庫。 您也可以建立新 **IOTHUB\_CLIENT\_HANDLE** 使用這些個別的參數，而不是連接字串。 使用下列程式碼即可達成：

```
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

這會與相同的工作，以完成 **IoTHubClient\_CreateFromConnectionString**。

很明顯，您會想要使用 **IoTHubClient\_CreateFromConnectionString** 而不是這種初始化更多詳細資料的方法。 但請記住，當您在 IoT 中樞註冊裝置時，您得到的是裝置識別碼和裝置金鑰 (不是連接字串)。  **裝置管理員** SDK 工具中導入 [前一篇文章](iot-hub-device-sdk-c-intro.md) 使用媒體櫃中的 **Azure IoT 服務 SDK** 從裝置識別碼、 裝置，以及 IoT 中心主機名稱建立的連接字串。 因此呼叫 **IoTHubClient\_LL\_Create** 可能是比較理想，因為它可以節省產生連接字串的步驟。 使用任何方法都很方便。

## 組態選項

到目前為止的所有項目所述的方式 **IoTHubClient** 程式庫的運作方式會反映其預設行為。 不過，您可以設定幾個選項來變更程式庫的運作方式。 這可以藉由運用 **IoTHubClient\_LL\_SetOption** API。 請思考此範例：

```
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

有一些常用的選項：

-   **SetBatching** (bool) – 如果 **true**, ，然後傳送到 IoT 中心的資料會以批次傳送。 如果 **false**, ，則會分別傳送訊息。 預設值是 **false**。

-   **逾時** (不帶正負號的 int) – 此值會表示以毫秒為單位。 如果傳送 HTTP 要求或接收回應所花費的時間超過這個時間，即表示連接逾時。

此批次處理選項極為重要。 根據預設，程式庫 ingresses 事件個別 (單一事件是您傳遞給任何 **IoTHubClient\_LL\_SendEventAsync**)。 如果批次的選項是 **true**, ，程式庫會收集它可以從緩衝區 (最多的 IoT 中心將會接受的最大訊息大小) 的事件數目。  事件批次會在單一 HTTP 呼叫中傳送到 IoT 中樞 (個別事件已統合至 JSON 陣列中)。 啟用批次處理通常會導致效能大幅提升，因為網路來回行程正在減少。 它也會大幅減少頻寬，因為您正利用事件批次傳送一組 HTTP 標頭，而不是針對每個個別事件傳送一組標頭。 除非您有使用其他方式的特定理由，否則通常會想要啟用批次處理。

## 後續步驟

這篇文章描述詳細的行為 **IoTHubClient** 程式庫中找到 **Azure IoT 裝置 SDK c**。 利用此資訊，您應該充分了解功能的 **IoTHubClient** 程式庫。  [下一篇文章](iot-hub-device-sdk-c-serializer.md) 提供類似的詳細資料 **序列化程式** 程式庫。

