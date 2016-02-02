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


# Microsoft Azure IoT 裝置 SDK C-深入了解序列化程式

[先文章](iot-hub-device-sdk-c-intro.md) 導入本系列 **Azure IoT 裝置 SDK c**。 下一篇文章所提供的更詳細的描述 [* * IoTHubClient * *](iot-hub-device-sdk-c-iothubclient.md)。 這篇文章提供更詳細的描述的其餘元件完成 SDK 的涵蓋範圍: **序列化程式** 程式庫。

簡介文章說明如何使用 **序列化程式** 傳送事件至和接收訊息的 IoT 中心從程式庫。 在這篇文章擴充討論提供更完整的說明，如何建立具有資料模型的 **序列化程式** 巨集語言。 發行項也包含更多詳細的文件庫將訊息的序列化，並在某些情況下如何控制序列化行為。 我們也將描述您可以修改以判斷您所建立之模型大小的某些參數。

最後，本文會重新審視一些像是訊息和屬性處理的上一個文件中涵蓋的主題。 當我們將了解這些功能的運作相同方式使用 **序列化程式** 程式庫與一樣 **IoTHubClient** 程式庫。

這篇文章中所述的所有項目根據 **序列化程式** SDK 範例。 如果您想要跟著做，請參閱 **simplesample\_amqp** 和 **simplesample\_http** C Azure IoT 裝置 SDK 中包含的應用程式

## 模組化語言

[簡介文章](iot-hub-device-sdk-c-intro.md) 導入本系列 **Azure IoT 裝置 SDK c** 模組化語言提供的範例透過 **simplesample\_amqp** 應用程式:

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

在命名空間內部進行的是模型定義。 在此情況下，是 anemometer 單一模型。 同樣地，模型可以任意命名，但通常此節點稱為裝置或您想要的 IoT 中心與交換的資料類型。

模型包含您可以輸入到 IoT 中樞 (*資料*) 之事件的定義，以及您可以從 IoT 中樞接收的訊息 (*動作*)。 如同您在範例中看到的，事件具有類型和名稱。動作會有一個名稱和選擇性的參數 (各有其類型)。

此範例中並未示範 SDK 支援的其他資料類型。 我們將在稍後討論。
> [AZURE.NOTE] IoT 中心是指裝置傳送給它做為資料 *事件*, ，而模組化語言是指將它稱為 *資料* (使用定義 **WITH_DATA**)。 IoT 中心同樣地，您將傳送至裝置的資料是指 *訊息*, ，而模組化語言是指將它稱為 *動作* (使用定義 **WITH_ACTION**)。 請注意，這些詞彙可以交換使用本文中。

### 支援的資料類型

利用**序列化程式**程式庫建立的模型支援下列資料類型：

| 類型| 說明|
|-------------------------|----------------------------------------|
| double| 雙精確度浮點數|
| int| 32 位元整數|
| float| 單精確度浮點數|
| long| 長整數|
| int8\_t| 8 位元整數|
| int16\_t| 16 位元整數|
| int32\_t| 32 位元整數|
| int64\_t| 64 位元整數|
| 布林| 布林值|
| ascii\_char\_ptr| ASCII 字串|
| EDM\_DATE\_TIME\_OFFSET| 日期時間位移|
| EDM\_GUID| GUID|
| EDM\_BINARY| binary|
| DECLARE\_STRUCT| 複雜資料類型|

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

我們的模型包含 **TestType** 類型的單一資料事件。 **準則** 是複雜類型，其中包含數個成員，共同示範所支援的基本型別 **序列化程式** 模組化語言。

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

基本上，我們將指派值的每個成員 **測試** 結構，然後再呼叫 **SendAsync** 傳送 **測試** 至雲端的資料事件。 **SendAsync** 是將單一資料事件傳送到 IoT 中樞的協助程式函式：

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

請注意，序列化為 JSON，這是產生的格式由 **序列化程式** 程式庫。 也請注意，每個已序列化的 JSON 物件的成員符合的成員 **準則** 我們在我們的模型中定義。 這些值也完全符合所使用的程式碼。 但請注意二進位資料是 base64 編碼: 「 AQID 」 是以 base64 編碼的 {0x01、 0x02、 0x03}。

這個範例示範使用的優點 **序列化程式** 程式庫-它可讓我們傳送至雲端，JSON，而不需要明確地處理我們的應用程式中的序列化。 我們需要擔心的是在模型中設定資料事件的值，然後呼叫簡單的 API 將這些事件傳送至雲端。

利用此資訊，我們可以定義包含支援的資料類型，包括複雜型別 (我們甚至可以包含其他複雜型別中的複雜型別) 的範圍的模型。 不過，他序列化所產生的 JSON 上述範例中會顯示重要的一點。 我們*如何*利用**序列化程式**程式庫傳送資料，可完全決定 JSON 如何形成。 此特點就是我們接下來要討論的內容。

## 進一步了解序列化

上一節強調**序列化程式**程式庫所產生的輸出範例。 本節中，我們將說明程式庫將資料序列化，以及如何控制該行為，使用序列化的 Api。

以進階序列化的討論，我們將使用新控溫器為基礎的模型。 首先，讓我們在我們想要通訊的案例提供一些背景。

我們想要建立可測量氣溫和溼度的控溫器模型。 每一份資料會以不同的方式傳送到 IoT 中心。 根據預設，溫 ingresses 溫度事件一次每 2 分鐘。溼度事件是一次每隔 15 分鐘 ingressed。 Ingressed 任一事件時，它必須包含時間戳記的時間顯示對應的溫度或溼度的測量方式。

提供這種情況下，我們將示範兩種不同的方式建立資料的模型，我們將說明影響模型已序列化的輸出上。

### 模型 1

以下是模型的可支援一個案例的第一個版本:

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

請注意，此模型包含兩個資料事件: **溫度** 和 **溼度**。 不同於先前的範例，每個事件的型別是使用定義的結構 **DECLARE\_STRUCT**。 **TemperatureEvent** 包含溫度測量和時間戳記。**HumidityEvent** 包含溼度測量和時間戳記。 此模型可讓我們以自然的方式模型化上述案例的資料。 當我們將事件傳送至雲端時，我們可能會傳送溫度/時間戳記或溼度/時間戳記組。

我們可以將溫度事件傳送至使用下列程式碼:

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

我們將使用硬式編碼值溫度和溼度的範例程式碼，但假設我們實際上正在擷取這些值由取樣溫上相對應的感應器。

上述程式碼使用先前介紹的協助程式 **GetDateTimeOffset**。 為了將成為清除之後，此程式碼明確分隔序列化，並將事件傳送的工作。 先前的程式碼會將溫度事件序列化至緩衝區。 然後， **sendMessage** 是 helper 函式 (包含在 **simplesample\_amqp**) 會將事件傳送到 IoT 中心:

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

此程式碼是前一節所述之 **SendAsync** 協助程式的子集，所以我們不會在此贅述。

我們執行先前的程式碼以傳送溫度事件時，事件的序列化形式會傳送到 IoT 中樞：

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

我們正在傳送類型溫度 **TemperatureEvent** 與該結構包含 **溫度** 和 **時間** 成員。 這會直接反映在序列化資料中。

同樣地，我們可以傳送溼度事件與這個程式碼:

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

現在，我們要修改模型，使其包含相同的資料，但具有不同的結構。

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

現在讓我們忽略**時間**事件。 忽略該事件之後，以下是要輸入**溫度**的程式碼：

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

And the code for sending the Humidity event appears as follows:
```
溫]-> [溼度 = 45。
如果 (序列化 & 目的地，& destinationSize，溫溼度]-> [= = IOT_AGENT_OK)
{
    sendMessage (iotHubClientHandle、 目的地、 destinationSize);
}
```

This code sends this to IoT Hub:
```
{"Humidity":45}
```

So far there are still no surprises. Now let's change how we use the SERIALIZE macro.

The **SERIALIZE** macro can take multiple data events as arguments. This enables us to serialize the **Temperature** and **Humidity** event together and send them to IoT Hub in one call:
```
如果 (序列化和目的地，& destinationSize，溫]-> [溫度 (溫溼度]-> [) = = IOT_AGENT_OK)
{
    sendMessage (iotHubClientHandle、 目的地、 destinationSize);
}
```

You might guess that the result of this code is that two data events are sent to IoT Hub:

[

{"Temperature":75},

{"Humidity":45}

]

In other words, you might expect that this code is the same as sending **Temperature** and **Humidity** separately. It’s just a convenience to pass both events to **SERIALIZE** in the same call. However, that’s not the case. Instead, the code above sends this single data event to IoT Hub:

{"Temperature":75, "Humidity":45}

This may seem strange because our model defines **Temperature** and **Humidity** as two *separate* events:
```
DECLARE_MODEL (電燈、 溫度調節器
WITH_DATA (int 溫度)，
WITH_DATA (int 溼度)，
WITH_DATA (EDM_DATE_TIME_OFFSET，時間)
);
```

More to the point, we didn’t model these events where **Temperature** and **Humidity** are in the same structure:
```
DECLARE_STRUCT (TemperatureAndHumidityEvent，
int、 溫度、
int、 溼度
);

DECLARE_MODEL (電燈、 溫度調節器
WITH_DATA (TemperatureAndHumidityEvent、 TemperatureAndHumidity)
);
```

If we used this model, it would be easier to understand how **Temperature** and **Humidity** would be sent in the same serialized message. However it may not be clear why it works that way when you pass both data events to **SERIALIZE** using model 2.

This behavior is easier to understand if you know the assumptions that the **serializer** library is making. To make sense of this let’s go back to our model:
```
DECLARE_MODEL (電燈、 溫度調節器
WITH_DATA (int 溫度)，
WITH_DATA (int 溼度)，
WITH_DATA (EDM_DATE_TIME_OFFSET，時間)
);
```

Think of this model in object-oriented terms. In this case we’re modeling a physical device (a thermostat) and that device includes attributes like **Temperature** and **Humidity**.

We can send the entire state of our model with code such as the following:
```
如果 (序列化 & 目的地，& destinationSize，溫]-> [溫度、 控溫器-> 溼度 (溫]-> [時間) = = IOT_AGENT_OK)
{
    sendMessage (iotHubClientHandle、 目的地、 destinationSize);
}
```

Assuming the values of Temperature, Humidity and Time are set, we would see an event like this sent to IoT Hub:
```
{"Temperature": 75，「 溼度 」: 45，「 時間 」: 「 2015年-09-17T18:45:56Z"}
```

Sometimes you may only want to send *some* properties of the model to the cloud (this is especially true if your model contains a large number of data events). It’s useful to send only a subset of data events, such as in our earlier example:
```
{"Temperature": 75，「 時間 」: 「 2015年-09-17T18:45:56Z"}
```

This generates exactly the same serialized event as if we had defined a **TemperatureEvent** with a **Temperature** and **Time** member, just as we did with model 1. In this case we were able to generate exactly the same serialized event by using a different model (model 2) because we called **SERIALIZE** in a different way.

The important point is that if you pass multiple data events to **SERIALIZE,** then it assumes each event is a property in a single JSON object.

The best approach depends on you and how you think about your model. If you’re sending "events" to the cloud and each event contains a defined set of properties, then the first approach makes a lot of sense. In that case you would use **DECLARE\_STRUCT** to define the structure of each event and then include them in your model with the **WITH\_DATA** macro. Then you send each event as we did in the first example above. In this approach you would only pass a single data event to **SERIALIZER**.

If you think about your model in an object-oriented fashion, then the second approach may suit you. In this case, the elements defined using **WITH\_DATA** are the "properties" of your object. You pass whatever subset of events to **SERIALIZE** that you like, depending on how much of your "object’s" state you want to send to the cloud.

Nether approach is right or wrong. Just be aware of how the **serializer** library works, and pick the modeling approach that best fits your needs.

## Message handling

So far this article has only discussed sending events to IoT Hub, and hasn't addressed receiving messages. The reason for this is that what we need to know about receiving messages has largely been covered in an [earlier article](iot-hub-device-sdk-c-intro.md). Recall from that article that you process messages by registering a message callback function:
```
IoTHubClient_SetMessageCallback (iotHubClientHandle，IoTHubMessage，myWeather)
```

You then write the callback function that’s invoked when a message is received:
```
靜態 IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT 結果;
    const 不帶正負號的 char * 緩衝區。
    size_t 大小;
    如果 (IoTHubMessage_GetByteArray (訊息、 & 緩衝區大小)! = IOTHUB_MESSAGE_OK)
    {
        printf (「 無法 IoTHubMessage_GetByteArray\r\n 」)。
        結果 = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*緩衝區就不是零結束*/
        char * temp = malloc (大小 + 1)。
        如果 (temp = = NULL)
        {
            printf (「 無法 malloc\r\n 」)。
            結果 = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy temp、 緩衝區 (大小)。
            temp [大小] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND (userContextCallback，temp)。
            結果 =
                (executeCommandResult = = EXECUTE_COMMAND_ERROR)? IOTHUBMESSAGE_ABANDONED:
                (executeCommandResult = = EXECUTE_COMMAND_SUCCESS)? IOTHUBMESSAGE_ACCEPTED:
                IOTHUBMESSAGE_REJECTED;
            free(temp)
        }
    }
    return result;
}
```

This implementation of **IoTHubMessage** calls the specific function for each action in your model. For example, if your model defines this action:
```
WITH_ACTION (SetAirResistance int、 位置)
```

You must define a function with this signature:
```
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void) 的裝置。
    (void) printf (「 %d.\r\n 來設定空中抵抗位置，」 位置)。
    傳回 EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** is then called when that message is sent to your device.

What we haven't explained yet is what the serialized version of message looks like. In other words, if you want to send a **SetAirResistance** message to your device, what does that look like?

If you're sending a message to a device, you would do so through the Azure IoT service SDK. You still need to know what string to send to invoke a particular action. The general format for sending a message appears as follows:
```
{"Name":""，"Parameters":""}
```

You're sending a serialized JSON object with two properties: **Name** is the name of the action (message) and **Parameters** contains the parameters of that action.

For example, to invoke **SetAirResistance** you can send this message to a device:
```
{"Name":"SetAirResistance"、"Parameters": {「 位置 」: 5}}
```

The action name must exactly match an action defined in your model. The parameter names must match as well. Also note case sensitivity. **Name** and **Parameters** are always uppercase. Make sure to match the case of your action name and parameters in your model. In this example, the action name is "SetAirResistance" and not "setairresistance".

This section described everything you need to know when sending events and receiving messages with the **serializer** library. Before moving on, let's cover some parameters you can configure that control how large your model is.

## Macro configuration

If you’re using the **Serializer** library an important part of the SDK to be aware of is found here:
```
.\\c\\common\\tools\\macro\_utils\_h\_generator。
```

This folder contains a Visual Studio solution called **macro\_utils\_h\_generator.sln**:

  ![](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.PNG)

The program in this solution generates the **macro\_utils.h** file found in the .\\c\\common\\inc directory. There’s a default macro\_utils.h file included with the SDK. This solution allows you to modify some parameters and then recreate the header file based on these parameters.

The two key parameters to be concerned with are **nArithmetic** and **nMacroParameters** which are defined in these two lines found in macro\_utils.tt:
```
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>

```

These values are the default parameters included with the SDK. Each parameter has the following meaning:

-   nMacroParameters – Controls how many parameters you can have in one DECLARE\_MODEL macro definition.

-   nArithmetic – Controls the total number of members allowed in a model.

The reason these parameters are important is because they control how large your model can be. For example, consider this model definition:
```
DECLARE_MODEL (Mymodel>，
WITH_DATA (int，MyData)
);
```

As mentioned previously, **DECLARE\_MODEL** is just a C macro. The name of the model and the **WITH\_DATA** statement (yet another macro) are parameters of **DECLARE\_MODEL**. **nMacroParameters** defines how many parameters can be included in **DECLARE\_MODEL**. Effectively, this defines how many data event and action declarations you can have. As such, with the default limit of 124 this means that you can define a model with a combination of about 60 actions and data events. If you try to exceed this limit, you'll receive compiler errors that look similar to this:

  ![](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.PNG)

The **nArithmetic** parameter is more about the internal workings of the macro language than your application.  It controls the total number of members you can have in your model, including **DECLARE_STRUCT** macros. If you start seeing compiler errors such as this, then you should try increasing **nArithmetic**:

   ![](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.PNG)

If you want to change these parameters, modify the values in the macro\_utils.tt file, recompile the macro\_utils\_h\_generator.sln solution, and run the compiled program. When you do so, a new macro\_utils.h file is generated and placed in the .\\common\\inc directory.

In order to use the new version of macro\_utils.h, remove the **serializer** NuGet package from your solution and in its place include the **serializer** Visual Studio project. This enables your code to compile against the source code of the serializer library. This includes the updated macro\_utils.h. If you want to do this for **simplesample\_amqp**, start by removing the NuGet package for the serializer library from the solution:

   ![](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.PNG)

Then add this project to your Visual Studio solution:

> .\\c\\serializer\\build\\windows\\serializer.vcxproj

When you're done, your solution should look like this:

   ![](media/iot-hub-device-sdk-c-serializer/05-serializer-project.PNG)

Now when you compile your solution, the updated macro\_utils.h is included in your binary.

Note that increasing these values high enough can exceed compiler limits. To this point, the **nMacroParameters** is the main parameter with which to be concerned. The C99 spec specifies that a minimum of 127 parameters are allowed in a macro definition. The Microsoft compiler follows the spec exactly (and has a limit of 127), so you won't be able to increase **nMacroParameters** beyond the default. Other compilers might allow you to do so (for example, the GNU compiler supports a higher limit).

So far we've covered just about everything you need to know about how to write code with the **serializer** library. Before concluding, let's revisit some topics from previous articles that you may be wondering about.

## The lower-level APIs

The sample application on which this article focused is **simplesample\_amqp**. This sample uses the higher level (the non-"LL") APIs to send events and receive messages. If you use these APIs, a background thread runs which takes care of both sending events and receiving messages. However, you can use the lower level (LL) APIs to eliminate this background thread and take explicit control over when you send events or receive messages from the cloud.

As described in a [previous article](iot-hub-device-sdk-c-iothubclient.md), there is a set of functions that consists of the higher level APIs:

-   IoTHubClient\_CreateFromConnectionString

-   IoTHubClient\_SendEventAsync

-   IoTHubClient\_SetMessageCallback

-   IoTHubClient\_Destroy

These APIs are demonstrated in **simplesample\_amqp**.

There is also an analogous set of lower level APIs.

-   IoTHubClient\_LL\_CreateFromConnectionString

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy

Note that the lower level APIs work exactly the same way as described in the previous articles. You can use the first set of APIs if you want a background thread to handle sending events and receiving messages. You use the second set of APIs if you want explicit control over when you send and receive data from IoT Hub. Either set of APIs work equally well with the **serializer** library.

For an example of how the lower level APIs are used with the **serializer** library, see the **simplesample\_http** application.

## Additional topics

A few other topics worth mentioning again are property handling, using alternate device credentials, and configuration options. These are all topics covered in a [previous article](iot-hub-device-sdk-c-iothubclient.md). The main point is that all of these features work in the same way with the **serializer** library as they do with the **IoTHubClient** library. For example, if you want to attach properties to an event from your model, you use **IoTHubMessage\_Properties** and **Map**\_**AddorUpdate**, the same way as described previously:
```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s (propText，sizeof(propText)，"%d"，i)。
Map_AddOrUpdate (propMap，「 SequenceNumber 」，propText);
```

Whether the event was generated from the **serializer** library or created manually using the **IoTHubClient** library does not matter.

For the alternate device credentials, using **IoTHubClient\_LL\_Create** works just as well as **IoTHubClient\_CreateFromConnectionString** for allocating an **IOTHUB\_CLIENT\_HANDLE**.

Finally, if you're using the **serializer** library, you can set configuration options with **IoTHubClient\_LL\_SetOption** just as you did when using the **IoTHubClient** library.

A feature that is unique to the **serializer** library are the initialization APIs. Before you can start working with the library, you must call **serializer\_init**:
```
serializer_init(NULL)
```

This is done just before you call **IoTHubClient\_CreateFromConnectionString**.

Similarly, when you're done working with the library, the last call you’ll make is to **serializer\_deinit**:
```
serializer_deinit()
```

否則，所有上述的其他功能的運作相同 **序列化程式** 程式庫所顯示的一樣 **IoTHubClient** 程式庫。 如需這些主題的詳細資訊，請參閱 [前一篇文章](iot-hub-device-sdk-c-iothubclient.md) 在這一系列。

## 後續步驟

本文說明詳細的獨特性 **序列化程式** 中所包含的程式庫 **Azure IoT 裝置 SDK c**。 透過文中提供的資訊，您應該能充分了解如何使用模型來傳送事件和接收來自 IoT 中樞的訊息。

這也包含如何開發應用程式三篇系列 **Azure IoT 裝置 SDK c**。 這應該是足夠的資訊來協助您開始不僅讓您全面了解 Api 的運作方式。 如需詳細資訊，此處未涵蓋 SDK 中的一些範例。 否則， [SDK 文件](https://github.com/Azure/azure-iot-sdks) 是很好的資源，如需詳細資訊。





