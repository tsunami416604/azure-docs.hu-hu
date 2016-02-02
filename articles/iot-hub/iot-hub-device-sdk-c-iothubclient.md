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

[先文章](iot-hub-device-sdk-c-intro.md) 導入本系列 **C 的 Microsoft Azure IoT 裝置 SDK**。 該文章已說明 SDK 中有兩個架構層。 在基底的是 **IoTHubClient** 程式庫，可直接管理與 IoT 中樞的通訊。 還有**序列化程式**庫，此程式庫建置於其頂部以提供序列化服務。 在本文中，我們將提供 **IoTHubClient** 程式庫的其他詳細資料。

前一篇文章描述如何使用 **IoTHubClient** 程式庫傳送事件到 IoT 中樞及接收訊息。 本文將向您介紹**較低層級 API**，以說明如何更精確地管理傳送和接收資料的「時機」**來擴大討論範圍。 我們也將說明如何使用 **IoTHubClient** 程式庫中的屬性處理功能，將屬性附加到事件 (並從訊息將其擷取)。 最後，我們將提供其他說明，以不同的方式來處理從 IoT 中樞接收的訊息。

本文藉由涵蓋數個其他主題，包括裝置認證的詳細資料以及如何透過組態選項變更 **IoTHubClient** 的行為，來進行總結。

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

However there are companion functions to each of these APIs:

-   IoTHubClient\_LL\_CreateFromConnectionString

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy


These functions all include “LL” in the API name. Other than that, the parameters of each of these functions are identical to their non-LL counterparts. However, the behavior of these functions is different in one important way.

When you call **IoTHubClient\_CreateFromConnectionString**, the underlying libraries create a new thread that runs in the background. This thread sends events to, and receives messages from, IoT Hub. No such thread is created when working with the "LL" APIs. The creation of the background thread is a convenience to the developer. You don’t have to worry about explicitly sending events and receiving messages from IoT Hub -- it happens automatically in the background. In constrast, the "LL" APIs give you explicit control over communication with IoT Hub, if you need it.

To understand this better, let’s look at an example:

When you call **IoTHubClient\_SendEventAsync**, what you're actually doing is putting the event in a buffer. The background thread created when you call **IoTHubClient\_CreateFromConnectionString** continually monitors this buffer and sends any data that it contains to IoT Hub. This happens in the background at the same time that the main thread is performing other work.

Similarly, when you register a callback function for messages using **IoTHubClient\_SetMessageCallback**, you're instructing the SDK to have the background thread invoke the callback function when a message is received, independent of the main thread.

The "LL" APIs don’t create a background thread. Instead, a new API must be called to explicitly send and receive data from IoT Hub. This is demonstrated in the following example.

The **iothub\_client\_sample\_http** application that’s included in the SDK demonstrates the lower-level APIs. In that sample, we send events to IoT Hub with code such as the the following:
```
EVENT_INSTANCE 訊息;
sprintf_s (msgText，sizeof(msgText)、 」 出現 Message_ %d_from_iothubclient_ll_over_http"i)。
message.messageHandle = IoTHubMessage_CreateFromByteArray ((const 不帶正負號 char *) msgText，strlen(msgText));

IoTHubClient_LL_SendEventAsync (iotHubClientHandle，message.messageHandle，SendConfirmationCallback，與訊息)
```

The first three lines create the message, and the last line sends the event. However, as mentioned previously, "sending" the event means that the data is simply placed in a buffer. Nothing is transmitted on the network when we call **IoTHubClient\_LL\_SendEventAsync**. In order to actually ingress the data to IoT Hub, you must call **IoTHubClient\_LL\_DoWork**, as in this example:
```
時 (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle)
    ThreadAPI_Sleep(1000)
}
```

This code (from the **iothub\_client\_sample\_http** application) repeatedly calls **IoTHubClient\_LL\_DoWork**. Each time **IoTHubClient\_LL\_DoWork** is called, it sends some events from the buffer to IoT Hub and it retrieves a queued message being sent to the device. The latter case means that if we registered a callback function for messages, then the callback is invoked (assuming any messages are queued up). We would have registered such a callback function with code such as the following:
```
IoTHubClient_LL_SetMessageCallback (iotHubClientHandle，ReceiveMessageCallback，& receiveContext)
```

The reason that **IoTHubClient\_LL\_DoWork** is often called in a loop is that each time it’s called, it sends *some* buffered events to IoT Hub and retrieves *the next* message queued up for the device. Each call isn’t guaranteed to send all buffered events or to retrieve all queued messages. If you want to send all events in the buffer and then continue on with other processing you can replace this loop with code such as the following:
```
IOTHUB_CLIENT_STATUS 狀態。

雖然 ((IoTHubClient_LL_GetSendStatus iotHubClientHandle，& 狀態 = = IOTHUB_CLIENT_OK) (& s) (& s) (狀態 = = IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle)
    ThreadAPI_Sleep(1000)
}
```

This code calls **IoTHubClient\_LL\_DoWork** until all events in the buffer have been sent to IoT Hub. Note that this does not also imply that all queued messages have been received. Part of the reason for this is that checking for "all" messages isn’t as deterministic an action. What happens if you retrieve "all" of the messages, but then another one is sent to the device immediately after? A better way to deal with that is with a programmed timeout. For example, the message callback function could reset a timer every time it’s invoked. You can then write logic to continue processing if, for example, no messages have been received in the last *X* seconds.

When you’re finished ingressing events and receiving messages, be sure to call the corresponding function to clean up resources.
```
IoTHubClient_LL_Destroy(iotHubClientHandle)
```

Basically there’s only one set of APIs to send and receive data with a background thread and another set of APIs that does the same thing without the background thread. A lot of developers may prefer the non-LL APIs, but the lower level APIs are useful when the developer wants explicit control over network transmissions. For example, some devices collect data over time and only ingress events at specified intervals (for example, once an hour or once a day). The lower-level APIs give you the ability to explicitly control when you send and receive data from IoT Hub. Others will simply prefer the simplicity that the lower level APIs provide. Everything happens on the main thread rather than some work happening in the background.

Whichever model you choose, be sure to be consistent in which APIs you use. If you start by calling **IoTHubClient\_LL\_CreateFromConnectionString**, be sure you only use the corresponding lower level APIs for any follow-up work:

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy

-   IoTHubClient\_LL\_DoWork

The opposite is true as well. If you start with **IoTHubClient\_CreateFromConnectionString**, then use the non-LL APIs for any additional processing.

In the Azure IoT device SDK for C, see the **iothub\_client\_sample\_http** application for a complete example of the lower level APIs. The **iothub\_client\_sample\_amqp** application can be referenced for a full example of the non-LL APIs.

## Property handling

So far when we've described sending data, we've been referring to the body of the message. For example, consider this code:
```
EVENT_INSTANCE 訊息;
sprintf_s msgText (sizeof(msgText)，"Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray ((const 不帶正負號 char *) msgText，strlen(msgText));
IoTHubClient_LL_SendEventAsync (iotHubClientHandle，message.messageHandle，SendConfirmationCallback，與訊息)
```

This example sends a message to IoT Hub with the text "Hello World." However, IoT Hub also allows properties to be attached to each message. Properties are name/value pairs that can be attached to the message. For example, we can modify the previous code to attach a property to the message:
```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s (propText，sizeof(propText)，"%d"，i)。
Map_AddOrUpdate (propMap，「 SequenceNumber 」，propText);
```

We start by calling **IoTHubMessage\_Properties** and passing it the handle of our message. What we get back is a **MAP\_HANDLE** reference that enables us to start adding properties. The latter is accomplished by calling **Map\_AddOrUpdate**, which takes a reference to a MAP\_HANDLE, the property name, and the property value. With this API we can add as many properties as we like.

When the event is read from **Event Hub**, the receiver can enumerate the properties and retrieve their corresponding values. For example, in .NET this would be accomplished by accessing the [Properties collection on the EventData object](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

In the previous example, we’re attaching properties to an event that we send to IoT Hub. Properties can also be attached to messages received from IoT Hub. If we want to retrieve properties from a message, we can use code such as the following in our message callback function:
```
靜態 IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    。 。 。

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

The call to **IoTHubMessage\_Properties** returns the **MAP\_HANDLE** reference. We then pass that reference to **Map\_GetInternals** to obtain a reference to an array of the name/value pairs (as well as a count of the properties). At that point it's a simple matter of enumerating the properties to get to the values we want.

You don't have to use properties in your application. However, if you need to set them on events or retrieve them from messages, the **IoTHubClient** library makes it easy.

## Message handling

As stated previously, when messages arrive from IoT Hub the **IoTHubClient** library responds by invoking a registered callback function. There is a return parameter of this function that deserves some additional explanation. Here’s an excerpt of the callback function in the **iothub\_client\_sample\_http** sample application:
```
靜態 IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    。 。 。
    傳回 IOTHUBMESSAGE_ACCEPTED;
}
```

Note that the return type is **IOTHUBMESSAGE\_DISPOSITION\_RESULT** and in this particular case we return **IOTHUBMESSAGE\_ACCEPTED**. There are other values we can return from this function that change how the **IoTHubClient** library reacts to the message callback. Here are the options.

-   **IOTHUBMESSAGE\_ACCEPTED** – The message has been processed successfully. The **IoTHubClient** library will not invoke the callback function again with the same message.

-   **IOTHUBMESSAGE\_REJECTED** – The message was not processed and there is no desire to do so in the future. The **IoTHubClient** library should not invoke the callback function again with the same message.

-   **IOTHUBMESSAGE\_ABANDONED** – The message was not processed successfully, but the **IoTHubClient** library should invoke the callback function again with the same message.

For the first two return codes, the **IoTHubClient** library sends a message to IoT Hub indicating that the message should be deleted from the device queue and not delivered again. The net effect is the same (the message is deleted from the device queue), but whether the message was accepted or rejected is still recorded.  Recording this distinction is useful to senders of the message who can listen for feedback and find out if a device has accepted or rejected a particular message.

In the last case a message is also sent to IoT Hub, but it indicates that the message should be redelivered. Typically you’ll abandon a message if you encounter some error but want to try to process the message again. In contrast, rejecting a message is appropriate when you encounter an unrecoverable error (or if you simply decide you don’t want to process the message).

In any case, be aware of the different return codes so that you can elicit the behavior you want from the **IoTHubClient** library.

## Alternate device credentials

As explained previously, the first thing to do when working with the **IoTHubClient** library is to obtain a **IOTHUB\_CLIENT\_HANDLE** with a call such as the following:
```
IOTHUB_CLIENT_HANDLE; iotHubClientHandle
iotHubClientHandle = IoTHubClient_CreateFromConnectionString (connectionString AMQP_Protocol);
```

The arguments to **IoTHubClient\_CreateFromConnectionString** are the connection string of our device and a parameter that indicates the protocol we use to communicate with IoT Hub. The connection string has a format that appears as follows:
```
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

There are four pieces of information in this string: IoT Hub name, IoT Hub suffix, device ID, and shared access key. You obtain the fully qualified domain name (FQDN) of an IoT hub when you create your IoT hub instance in the Azure portal — this gives you the IoT hub name (the first part of the FQDN) and the IoT hub suffix (the rest of the FQDN). You get the Device ID and the Shared Access Key when you register your device with IoT Hub (as described in the [previous article](iot-hub-device-sdk-c-intro.md)).

**IoTHubClient\_CreateFromConnectionString** gives you one way to initialize the library. If you prefer, you can create a new **IOTHUB\_CLIENT\_HANDLE** by using these individual parameters rather than the connection string. This is achieved with the following code:
```
IOTHUB_CLIENT_CONFIG; iotHubClientConfig
iotHubClientConfig.iotHubName ="";
iotHubClientConfig.deviceId ="";
iotHubClientConfig.deviceKey ="";
iotHubClientConfig.iotHubSuffix ="";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create (& iotHubClientConfig);
```

This accomplishes the same thing as **IoTHubClient\_CreateFromConnectionString**.

It may seem obvious that you would want to use **IoTHubClient\_CreateFromConnectionString** rather than this more verbose method of initialization. Keep in mind, however, that when you register a device in IoT Hub what you get is a device ID and device key (not a connection string). The **Device Manager** SDK tool introduced in the [previous article](iot-hub-device-sdk-c-intro.md) uses libraries in the **Azure IoT service SDK** to create the connection string from the device ID, device key, and IoT Hub host name. So calling **IoTHubClient\_LL\_Create** may be preferable because it saves you the step of generating a connection string. Use whichever method is convenient.

## Configuration options

So far everything described about the way the **IoTHubClient** library works reflects its default behavior. However, there are a few options that you can set to change how the library works. This is accomplished by leveraging the **IoTHubClient\_LL\_SetOption** API. Consider this example:
```
不帶正負號的 int 逾時 = 30000。
IoTHubClient_LL_SetOption (iotHubClientHandle、 「 逾時 」 及逾時)。
```

有一些常用的選項：

-   **SetBatching** (bool) - 如果為 **true**，則傳送到 IoT 中樞的資料會以批次傳送。 如果為 **false**，就表示訊息會個別傳送。 預設值為 **false**。

-   **Timeout** (unsigned int) - 這個值會以毫秒為單位表示。 如果傳送 HTTP 要求或接收回應所花費的時間超過這個時間，即表示連接逾時。

此批次處理選項極為重要。 根據預設，程式庫 ingresses 事件個別 (單一事件是您傳遞給任何 **IoTHubClient\_LL\_SendEventAsync**)。 如果批次處理選項為 **true**，程式庫會盡可能從緩衝區收集事件 (上限為 IoT 中樞將接受的最大訊息大小)。 事件批次會在單一 HTTP 呼叫中傳送到 IoT 中樞 (個別事件已統合至 JSON 陣列中)。 啟用批次處理通常會導致效能大幅提升，因為網路來回行程正在減少。 它也會大幅減少頻寬，因為您正利用事件批次傳送一組 HTTP 標頭，而不是針對每個個別事件傳送一組標頭。 除非您有使用其他方式的特定理由，否則通常會想要啟用批次處理。

## 後續步驟

本文深入探討**適用於 C 的 Azure IoT 裝置 SDK** 中發現之 **IoTHubClient** 程式庫的行為。 利用這項資訊，您就可以充分了解 **IoTHubClient** 程式庫的功能。  [下一篇文章](iot-hub-device-sdk-c-serializer.md) 提供類似的詳細資料 **序列化程式** 程式庫。





