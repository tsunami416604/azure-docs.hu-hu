<properties
    pageTitle="適用於 C 的 Azure IoT 裝置 SDK - 序列化程式 | Microsoft Azure"
    description="深入了解在適用於 C 的 Azure IoT 裝置 SDK 中使用序列化程式程式庫"
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

# Microsoft Azure IoT 裝置 SDK (適用於 C) – 深入了解序列化程式

 [先文章](iot-hub-device-sdk-c-intro.md) 中導入這一系列 **Azure IoT 裝置 SDK c**。 下一篇文章所提供的更詳細的描述 [**IoTHubClient**](iot-hub-device-sdk-c-iothubclient.md)。 這篇文章提供更詳細的描述的其餘元件完成 SDK 的涵蓋範圍: **序列化程式** 程式庫。

簡介文章說明如何使用 **序列化程式** 事件，以從傳送和接收訊息的 IoT 中心程式庫。 在這篇文章擴充討論提供更完整的說明，如何建立具有資料模型的 **序列化程式** 巨集語言。 本文也包含更多有關程式庫如何將訊息序列化 (以及在某些情況下，如何控制序列化行為) 的詳細資料。 我們也將描述您可以修改以判斷您所建立之模型大小的某些參數。

最後，本文會回顧先前文章中涵蓋的一些主題，例如訊息和屬性處理。 當我們將了解這些功能的運作相同方式使用 **序列化程式** 程式庫與一樣 **IoTHubClient** 程式庫。

這篇文章中所述的所有項目根據 **序列化程式** SDK 範例。 如果您想要跟著做，請參閱 **simplesample\_amqp** 和 **simplesample\_http** C Azure IoT 裝置 SDK 中包含的應用程式

## 模型化語言

 [簡介文章](iot-hub-device-sdk-c-intro.md) 中導入這一系列 **Azure IoT 裝置 SDK c** 模組化語言提供的範例透過 **simplesample\_amqp** 應用程式:

```
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

如您所見，模型化語言是以 C 巨集為基礎。 您定義開頭一律 **BEGIN\_NAMESPACE** ，而且最後一定與 **END\_NAMESPACE**。 通常會為您的公司命名此命名空間，或如同此範例，為您正在使用的專案命名此命名空間。

在命名空間內部進行的是模型定義。 在此案例中，有一個單一的風速計模型。 同樣地，可以任意命名此模型，但通常會針對您想要與 IoT 中樞交換的裝置或資料類型來命名。  

模型包含定義的事件，您可以輸入至 IoT 中心 ( *資料*) 以及您可以從 IoT 中心接收的訊息 ( *動作*)。 如同您在範例中看到的，事件具有類型和名稱。動作會有一個名稱和選擇性的參數 (各有其類型)。

此範例中並未示範 SDK 支援的其他資料類型。 我們將在稍後討論。

> [AZURE.NOTE] IoT 中心是指裝置傳送給它做為資料 *事件*, ，而模組化語言是指將它稱為 *資料* (使用定義 **WITH_DATA**)。 IoT 中心同樣地，您將傳送至裝置的資料是指 *訊息*, ，而模組化語言是指將它稱為 *動作* (使用定義 **WITH_ACTION**)。 請注意，在本文中可能會交替使用這些詞彙。

### 支援的資料類型

支援下列資料類型中建立的模型 **序列化程式** 程式庫:

| 類型                    | 說明                            |
|-------------------------|----------------------------------------|
| double                  | 雙精確度浮點數 |
| int                     | 32 位元整數                         |
| float                   | 單精確度浮點數 |
| long                    | 長整數                           |
| int8\_t                 | 8 位元整數                          |
| int16\_t                | 16 位元整數                         |
| int32\_t                | 32 位元整數                         |
| int64\_t                | 64 位元整數                         |
| 布林                    | 布林值                                |
| ascii\_char\_ptr        | ASCII 字串                           |
| EDM\_DATE\_TIME\_OFFSET | 日期時間位移                       |
| EDM\_GUID               | GUID                                   |
| EDM\_BINARY             | binary                                 |
| DECLARE\_STRUCT         | 複雜資料類型                      |

我們先從最後一個資料類型開始討論。  **DECLARE\_STRUCT** 可讓您定義複雜資料型別，是基本類型的群組。 這些群組可讓我們定義看起來像這樣的模型：

```
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

我們的模型包含單一資料的事件類型 **準則**。 **準則** 是複雜類型，其中包含數個成員，共同示範所支援的基本型別 **序列化程式** 模組化語言。

使用類似這樣的模型，我們可以撰寫程式碼以將資料傳送到看起來像這樣的 IoT 中樞：

```
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

基本上，我們將指派值的每個成員 **測試** 結構，然後再呼叫 **SendAsync** 傳送 **測試** 至雲端的資料事件。 **SendAsync** 是將單一資料事件傳送至 IoT 中心 helper 函式:

```
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
    unsigned char* destination;
    size_t destinationSize;
    if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
    {
        // null terminate the string
        char* destinationAsString = (char*)malloc(destinationSize + 1);
        if (destinationAsString != NULL)
        {
            memcpy(destinationAsString, destination, destinationSize);
            destinationAsString[destinationSize] = '\0';
            IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
            if (messageHandle != NULL)
            {
                IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

                IoTHubMessage_Destroy(messageHandle);
            }
            free(destinationAsString);
        }
        free(destination);
    }
}
```

此函式序列化指定的資料事件，並將它傳送至使用 IoT 中心 **IoTHubClient\_SendEventAsync**。 這是相同的過往文章中討論的程式碼 (**SendAsync** 封裝到方便的函式的邏輯)。

一個在先前的程式碼中使用其他 helper 函式是 **GetDateTimeOffset**。 此函式會將指定的時間轉換成類型的值 **EDM\_DATE\_TIME\_OFFSET**:

```
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
    struct tm newTime;
    gmtime_s(&newTime, &time);
    EDM_DATE_TIME_OFFSET dateTimeOffset;
    dateTimeOffset.dateTime = newTime;
    dateTimeOffset.fractionalSecond = 0;
    dateTimeOffset.hasFractionalSecond = 0;
    dateTimeOffset.hasTimeZone = 0;
    dateTimeOffset.timeZoneHour = 0;
    dateTimeOffset.timeZoneMinute = 0;
    return dateTimeOffset;
}
```

如果您執行此程式碼，就會傳送下列訊息到 IoT 中樞：

```
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

請注意，序列化為 JSON，這是產生的格式由 **序列化程式** 程式庫。 也請注意，每個已序列化的 JSON 物件的成員符合的成員 **準則** 我們在我們的模型中定義。 值也與程式碼中使用的值完全相符。 不過，請注意，二進位資料是採用 base64 編碼："AQID" 是 {0x01, 0x02, 0x03} 的 base64 編碼。

這個範例示範使用的優點 **序列化程式** 程式庫-它可讓我們傳送至雲端，JSON，而不需要明確地處理我們的應用程式中的序列化。 我們所需操心的只有要在模型中設定資料事件的值，然後呼叫簡單的 API 將這些事件傳送至雲端。

有了這項資訊，我們便可以定義包含支援之資料類型範圍的模型，這些資料類型包括複雜類型 (我們甚至可以包含其他複雜類型內的複雜類型)。 不過，上述範例所產生的序列化 JSON 突顯了一個重點。 *如何* 傳送資料與 **序列化程式** 程式庫可讓您判斷完全如何 JSON 的格式。 此特點就是我們接下來要討論的內容。

## 深入了解序列化

上一節會反白顯示所產生的輸出範例 **序列化程式** 程式庫。 在本節中，我們將說明程式庫如何將資料序列化，以及如何使用序列化 API 來控制該行為。

為了進一步討論序列化，我們將使用以控溫器為基礎的新模型。 首先，讓我們針對所要嘗試處理的案例提供一些背景。

我們想要建立可測量氣溫和溼度的控溫器模型。 每一項資料將會以不同的方式傳送至 IoT 中樞。 根據預設，控溫器會每 2 分鐘輸入溫度事件一次；每 15 分鐘輸入溼度事件一次。 輸入任一事件時，必須包含顯示相對應溫度或溼度之測量時間的時間戳記。

在這個案例中，我們將示範兩種不同的資料模型化方式，並將說明該模型化對序列化輸出的影響。

### 模型 1

以下是支援先前案例的第一版模型：

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

請注意，此模型包含兩個資料事件: **溫度** 和 **溼度**。 不同於先前的範例，每個事件的型別是使用定義的結構 **DECLARE\_STRUCT**。 **TemperatureEvent** 包含溫度度量和時間戳記， **HumidityEvent** 包含溼度度量和時間戳記。 此模型可讓我們以自然的方式模型化上述案例的資料。 當我們將事件傳送至雲端時，我們將會傳送溫度/時間戳記組或溼度/時間戳記組。

我們可以使用類似以下的程式碼將溫度事件傳送至雲端：

```
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

在範例程式碼中，我們將針對溫度和溼度使用硬式編碼值，但請想像成我們實際上是從控溫器上對應的感應器取樣來擷取這些值。

使用上述程式碼 **GetDateTimeOffset** 引進了先前的協助程式。 此程式碼會將序列化與傳送事件的工作明確分隔，原因在稍後會漸趨明朗。 先前的程式碼會將溫度事件以序列化方式傳送至緩衝區。 然後， **sendMessage** 是 helper 函式 (包含在 **simplesample\_amqp**) 會將事件傳送到 IoT 中心:

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

此程式碼是子集 **SendAsync** 上一節中所述，我們將不會經過它一次這裡的協助程式。

我們執行先前的程式碼以傳送溫度事件時，事件的序列化形式會傳送到 IoT 中樞：

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

我們正在傳送類型溫度 **TemperatureEvent** 與該結構包含 **溫度** 和 **時間** 成員。 這會直接反映在序列化資料中。

同樣地，我們可以利用此程式碼來傳送溼度事件：

```
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

傳送到 IoT 中樞的序列化的形式如下所示：

```
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

同樣地，全如預期一般。

您可以使用此模型來想像如何輕鬆地加入其他事件。 定義使用多個結構 **DECLARE\_STRUCT**, ，並將對應的事件包含在模型使用 **WITH\_DATA**。

現在，我們要修改模型，讓它包含相同的資料，但具有不同的結構。

### 模型 2

請考慮上述模型的替代模型：

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

在此情況下，我們已去除 **DECLARE\_STRUCT** 巨集，只會定義資料項目從我們的案例中使用的模組化語言的簡單型別。

只針對目前讓我們忽略 **時間** 事件。 與該離題一下，這裡是輸入的程式碼 **溫度**:

```
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

此程式碼會將下列序列化事件傳送到 IoT 中樞：

```
{"Temperature":75}
```

而傳送 Humidity 事件的程式碼則看起來像這樣：

```
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

此程式碼會將其傳送至 IoT 中樞：

```
{"Humidity":45}
```

到目前為止，仍毫無意外。 現在，讓我們變更 SERIALIZE 巨集的使用方式。

 **序列化** 巨集可做為引數的多個資料事件。 這可讓我們來序列化 **溫度** 和 **溼度** 一起事件並將它們傳送到 IoT 中心一次呼叫中:

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

您或許已經猜到，此程式碼的結果是兩個資料事件都會傳送到 IoT 中樞：

[

{"Temperature":75},

{"Humidity":45}

]

換句話說，您所料，此程式碼會傳送相同 **溫度** 和 **溼度** 分開。 它是只是為了方便起見將這兩個事件 **序列化** 中相同的呼叫。 不過，事實並非如此。 上述程式碼會改為將這個單一資料事件傳送到 IoT 中樞：

{"Temperature":75, "Humidity":45}

這看起來很奇怪，因為我們的模型定義 **溫度** 和 **溼度** 做為兩個 *個別* 事件:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

多點，我們沒有模型這些事件其中 **溫度** 和 **溼度** 位於相同的結構:

```
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

如果我們使用這個模型，則可更容易了解如何 **溫度** 和 **溼度** 要傳送相同的已序列化訊息。 不過它可能無法清除運作如此一來當您將傳遞至這兩個資料事件的原因 **序列化** 使用模型 2。

這個問題很容易了解是否您知道假設 **序列化程式** 正在程式庫。 若要了解這一點，讓我們回到我們的模型：

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

請以物件導向的觀點思考此模型。 在此情況下我們要模型化實體裝置 (溫) 以及該裝置屬性，例如 **溫度** 和 **溼度**。

我們可以利用類似以下的程式碼來傳送模型的整個狀態：

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

假設已設定溫度、溼度和時間的值，我們會看到這樣的事件傳送到 IoT 中樞：

```
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

有時您可能只想傳送 *一些* 到雲端 (特別是如果您的模型包含大量的資料事件) 模型的屬性。 這時，只傳送資料事件的子集會相當有用，就像我們先前的範例一樣：

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

這會產生完全相同的序列化的事件，如同我們已經定義 **TemperatureEvent** 與 **溫度** 和 **時間** 成員，就像我們並未使用模型 1。 在此情況下，我們可以使用不同的模型 (模式 2)，因為我們呼叫產生完全相同的序列化的事件 **序列化** 以不同方式。

重要的一點是，如果您傳遞多個資料事件 **序列化，** 則會假設每個事件可以在單一 JSON 物件的屬性。

最佳方法取決於您及您對模型的思考方式。 如果您要將「事件」傳送至雲端，且每個事件都包含一組已定義的屬性，則第一種方法較為適合。 在此情況下，您會使用 **DECLARE\_STRUCT** 定義每個事件的結構，然後將它們包含在您的模型與 **WITH\_DATA** 巨集。 然後您會依照我們在上述第一個範例中使用的方法來傳送每個事件。 在這種方法只會傳遞至單一資料事件 **序列化程式**。

如果您以物件導向的方式思考模型，則第二種方法可能比較適合您。 在此情況下，項目定義使用 **WITH\_DATA** 是物件的 「 屬性 」。 傳遞至事件的任何子集 **序列化** 要根據您想要傳送至雲端 」 物件的 「 狀態中有多少。

沒有絕對正確或錯誤的方法。 要小心如何 **序列化程式** 程式庫的運作方式，以及挑選最適合您需求模型化方法。

## 訊息處理

到目前為止，本文只討論傳送事件到 IoT 中樞，尚未處理接收訊息。 因為這是我們需要了解接收訊息的主要曾多次討論 [稍早文章](iot-hub-device-sdk-c-intro.md)。 請從那篇文章回憶，您是藉由註冊訊息回呼函式來處理訊息：

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

然後撰寫在接收訊息時所叫用的回呼函式：

```
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

這項實作的 **IoTHubMessage** 呼叫特定函式，針對每個模型中的動作。 例如，如果您的模型定義這項動作：

```
WITH_ACTION(SetAirResistance, int, Position)
```

您必須使用此簽章定義函式：

```
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** 該訊息傳送到您的裝置時，然後呼叫。

不過我們還沒說明序列化版本訊息的外觀。 換句話說，如果您想要傳送 **SetAirResistance** 訊息到您的裝置，該外觀為何?

如果您要傳送訊息給裝置，您會透過 Azure IoT 服務 SDK 進行。 您仍然需要知道要傳送哪個字串才能叫用特殊動作。 傳送訊息的一般格式看起來像這樣：

```
{"Name" : "", "Parameters" : "" }
```

您要傳送序列化的 JSON 物件，具有兩個屬性: **名稱** 動作 (訊息) 的名稱和 **參數** 包含該動作的參數。

例如，若要叫用 **SetAirResistance** 您可以將此訊息傳送到裝置:

```
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

動作名稱必須完全符合您的模型中所定義的動作。 參數名稱也必須相符。 也請注意區分大小寫。 **名稱** 和 **參數** 永遠是大寫。 請務必符合您模型中動作名稱和參數的大小寫。 在此範例中，動作名稱是 "SetAirResistance"，而不是 "setairresistance"。

本章節所述一切知道當傳送事件和接收訊息與 **序列化程式** 程式庫。 在繼續討論之前，讓我們先討論一些您可以設定以控制模型大小的參數。

## 巨集組態

如果您使用 **序列化程式** 程式庫的重要部分，要注意的 SDK 的位置如下:

```
.\\c\\common\\tools\\macro\_utils\_h\_generator.
```

此資料夾包含名為 Visual Studio 解決方案 **macro\_utils\_h\_generator.sln**:

  ![](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.PNG)

此解決方案中的程式會產生 **macro\_utils.h** .\\c\\common\\inc 目錄中找到檔案。 沒有從 SDK 隨附的預設 macro\_utils.h 檔案。 這個解決方案可讓您修改部分參數，然後根據這些參數重新建立標頭檔。

要連接的兩個索引鍵參數 **nArithmetic** 和 **nMacroParameters** macro\_utils.tt 中找到這兩行中定義:

```
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>

```

這些值是 SDK 隨附的預設參數。 每個參數都具有下列意義：

-   nMacroParameters – 控制您可以在一個 DECLARE\_MODEL 巨集定義的參數數目。

-   nArithmetic – 控制模型中允許的成員總數。

這些參數之所以重要，是因為它們控制您模型的大小。 例如，請考慮以此模型定義：

```
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

如前所述， **DECLARE\_MODEL** 只使用 C 巨集。 模型的名稱和 **WITH\_DATA** 陳述式 (但另一個巨集) 是用參數 **DECLARE\_MODEL**。 **nMacroParameters** 定義中可以包含的參數數目 **DECLARE\_MODEL**。 實際上，這定義的是您所能擁有的資料事件和動作宣告數目。 因此，使用預設限制 124 時，表示您可以定義由大約 60 個動作和事件資料所組成的模型。 如果您嘗試超過這個限制，您會收到看起來像這樣的編譯器錯誤：

  ![](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.PNG)

 **NArithmetic** 參數是深入了解巨集語言的內部運作方式與您的應用程式。  它所控制的成員，您可以在模型中，總數包括 **DECLARE_STRUCT** 巨集。 如果您開始看到編譯器錯誤，例如，則您應該嘗試增加 **nArithmetic**:

   ![](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.PNG)

如果您想要變更這些參數，修改 macro\_utils.tt 檔案中的值、 macro\_utils\_h\_generator.sln 方案，請重新編譯並執行已編譯的程式。 當您這樣做時，新的 macro\_utils.h 檔案會產生，並放置於.\\common\\inc 目錄。

若要使用新版本的 macro\_utils.h，移除 **序列化程式** NuGet 封裝，從您的方案，並在其位置包含 **序列化程式** Visual Studio 專案。 這可讓您的程式碼針對序列化程式程式庫的原始程式碼進行編譯。 這包括更新的 macro\_utils.h。 如果您想要執行這項操作 **simplesample\_amqp**, ，開始從方案移除序列化程式庫 NuGet 封裝:

   ![](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.PNG)

然後將此專案新增到您的 Visual Studio 解決方案：

> .\\c\\serializer\\build\\windows\\serializer.vcxproj

完成時，解決方案應該如下所示：

   ![](media/iot-hub-device-sdk-c-serializer/05-serializer-project.PNG)

現在當您編譯您的方案時，更新的 macro\_utils.h 併入您的二進位檔。

請注意，增加這些值到足夠高的數目可能會超出編譯器限制。 到目前為止， **nMacroParameters** 是要考量的主要參數。 C99 規格指定巨集定義中允許最少 127 個參數。 Microsoft 編譯器完全遵循規格 (和長度限制為 127)，因此您無法增加 **nMacroParameters** 超過預設值。 其他編譯器可能會允許您這麼做 (例如 GNU 編譯器支援更高的限制)。

到目前為止我們所討論的幾乎所有一切都想要知道如何撰寫程式碼與 **序列化程式** 程式庫。 在做出結論之前，讓我們先從先前的文章中回顧一些您可能想知道的主題。

## 較低層級的 API

這篇文章著重在其上的範例應用程式 **simplesample\_amqp**。 這個範例使用較高層級 (非 "LL") API 來傳送事件和接收訊息。 如果您使用這些 API，將會執行背景執行緒來處理事件傳送和訊息接收。 不過，您可以使用較低層級 (LL) API 來消除這個背景執行緒，並掌握傳送事件或從雲端接收訊息時的明確控制權。

中所述 [前一篇文章](iot-hub-device-sdk-c-iothubclient.md), ，還有一組函式所組成的較高的層級 Api:

-   IoTHubClient\_CreateFromConnectionString

-   IoTHubClient\_SendEventAsync

-   IoTHubClient\_SetMessageCallback

-   IoTHubClient\_Destroy

這些 Api 示範於 **simplesample\_amqp**。

此外，也有一組類似的較低層級 API。

-   IoTHubClient\_LL\_CreateFromConnectionString

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy

請注意，較低層級 API 的運作方式與先前文章中所述的完全相同。 如果您想要背景執行緒以處理事件傳送和訊息接收，您可以使用第一組 API。 如果您想要掌握與 IoT 中樞之間傳送和接收資料時的明確控制權，您可以使用第二組 API。 一組 Api 工作也能用於 **序列化程式** 程式庫。

如需如何搭配使用較低層級 Api **序列化程式** 程式庫，請參閱 **simplesample\_http** 應用程式。

## 其他主題

值得一提的其他幾個主題包括屬性處理、使用替代裝置認證和組態選項。 這些是所有涵蓋的主題 [前一篇文章](iot-hub-device-sdk-c-iothubclient.md)。 主要的重點是所有這些功能都可使用相同的方式 **序列化程式** 程式庫與一樣 **IoTHubClient** 程式庫。 例如，如果您想要將屬性附加至事件，從您的模型，您使用 **IoTHubMessage\_Properties** 和 **對應**\lang1033\super\f3**AddorUpdate**, 、 先前描述的相同方式:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

是否已產生事件 **序列化程式** 程式庫或建立使用手動 **IoTHubClient** 程式庫並不重要。

對於其他裝置的認證，請使用 **IoTHubClient\_LL\_Create** 也一樣好做為 **IoTHubClient\_CreateFromConnectionString** 配置 **IOTHUB\_CLIENT\_HANDLE**。

最後，如果您使用 **序列化程式** 程式庫，您可以設定組態選項與 **IoTHubClient\_LL\_SetOption** 就像您未使用時 **IoTHubClient** 程式庫。

是唯一的一項功能 **序列化程式** 程式庫會初始化 Api。 您可以開始使用程式庫之前，您必須呼叫 **serializer\_init**:

```
serializer_init(NULL);
```

這是您呼叫之前 **IoTHubClient\_CreateFromConnectionString**。

同樣地，當您完成使用程式庫，您要進行最後一次呼叫是 **serializer\_deinit**:

```
serializer_deinit();
```

否則，所有上述的其他功能的運作相同 **序列化程式** 程式庫所顯示的一樣 **IoTHubClient** 程式庫。 如需這些主題的詳細資訊，請參閱 [前一篇文章](iot-hub-device-sdk-c-iothubclient.md) 本系列。

## 後續步驟

本文說明詳細的獨特性 **序列化程式** 中所包含的程式庫 **Azure IoT 裝置 SDK c**。 透過文中提供的資訊，您應該能充分了解如何使用模型來傳送事件和接收來自 IoT 中樞的訊息。

這也包含如何開發應用程式三篇系列 **Azure IoT 裝置 SDK c**。 這些資訊應該不僅足以讓您入門，還能讓您徹底了解 API 的運作方式。 如需其他資訊，還有一些 SDK 中的範例未涵蓋在本文中。 否則， [SDK 文件](https://github.com/Azure/azure-iot-sdks) 是很好的資源，如需詳細資訊。

