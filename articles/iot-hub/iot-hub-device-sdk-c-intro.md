<properties
    pageTitle="使用適用於 C 的 Azure IoT 裝置 SDK | Microsoft Azure"
    description="深入了解並開始使用 Azure IoT 裝置 SDK (適用於 C) 中的範例程式碼。"
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


# Azure IoT 裝置 SDK (適用於 C) 簡介

「Azure IoT 裝置 SDK」****是一組程式庫，設計用來簡化從 **Azure IoT 中樞** 服務傳送事件和接收訊息的程序。 在 SDK 中，每個目標為特定的平台的不同變化，但本文將說明 **Azure IoT 裝置 SDK c**。

Azure IoT 裝置 SDK (適用於 C) 是以 ANSI C (C99) 撰寫，以獲得最大可攜性。 這很適合的平台和裝置數目上運作，特別是在最小化磁碟和記憶體耗用量是優先考量。

有各種 SDK 已經過測試所在的平台 (請參閱 [SDK 文件](https://github.com/Azure/azure-iot-sdks/tree/master/c) 如需詳細資訊)。 雖然這篇文章包含 Windows 平台上執行的範例程式碼的逐步解說，請記住，本文中所述的程式碼範圍的支援平台上完全相同。

本文將介紹 Azure IoT 裝置 SDK (適用於 C) 的架構。我們將示範如何初始化裝置程式庫，將事件傳送到 IoT 中樞，以及從 IoT 中樞接收訊息。 這篇文章中的資訊應該足以開始使用 SDK，但也提供程式庫的其他資訊的指標。

## SDK 架構

您可以在下列 GitHub 儲存機制中找到 **Azure IoT 裝置 SDK (適用於 C)**︰

[azure iot sdk](https://github.com/Azure/azure-iot-sdks)

在此儲存機制的 **master** 分支中可找到最新版的程式庫：

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

此儲存機制包含整個系列的 Azure IoT 裝置 SDK。 不過，這篇文章是關於 Azure IoT 裝置 SDK *c* 中可以找到其 **c** 資料夾。

  ![](media/iot-hub-device-sdk-c-intro/02-CFolder.PNG)

SDK 核心實作位於 **常見**, ，**iothub\_client**, ，和 **序列化程式** 儲存機制中的資料夾。 **common** 資料夾包含所有程式庫使用的共用程式碼 (通常您不會直接使用 **common** 資料夾中的程式碼)。 不過， **iothub\_client** 和 **序列化程式** 資料夾包含兩個不同的程式碼中，您將使用的 SDK 層的實作。

-   **IoTHubClient** - **iothub\_client** 資料夾包含實作的 SDK 中的最低 API 層級: **IoTHubClient** 程式庫。 **IoTHubClient** 程式庫包含用於將事件傳送到 IoT 中樞以及從 IoT 中樞接收訊息的 API。 如果您使用此程式庫，您必須負責實作訊息序列化，但會為您處理 IoT 中心與通訊的其他詳細資料。

-   **serializer** - **serializer** 資料夾包含 **serializer** 程式庫的實作。 **serializer** 程式庫在 **IoTHubClient** 所提供的功能之上加入模型化功能。 如果您使用 **serializer** 程式庫，您首先會定義一個模型，以指定您要傳送至 IoT 中樞的事件以及您期望從 IoT 中樞收到的訊息。 一旦定義此模型之後，SDK 會提供您 API 介面，可讓您輕鬆地使用事件和訊息，而不必擔心序列化詳細資料。

所有這些都是更容易了解藉由查看範例程式碼。 下列各節將為您逐步解說 SDK 中包含的幾個範例應用程式。 這應可讓您輕鬆了解 SDK 架構層的各種功能以及 API 運作方式的簡介。

## 執行範例之前

您必須先完成兩項工作，才可以在 Azure IoT 裝置 SDK (適用於 C) 中執行範例：準備開發環境和取得裝置認證。  [讀我檔案](https://github.com/Azure/azure-iot-sdks/tree/master/c) 隨附 SDK 提供這兩個工作的指示。 下列各節包含一些額外議題上的指示。

### 準備開發環境

首先，您必須從 GitHub 取得 SDK 的複本，然後建立來源。 您應該取得一份來自來源 **主要** 分支 [GitHub 儲存機制](https://github.com/Azure/azure-iot-sdks)。

當您下載來源的複本時，您必須完成 SDK 文件中所述的步驟 [準備開發環境](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md)。 大部分的工作，您必須執行 Windows 中包含 [準備 Qpid Proton 程式庫](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md#preparing-qpid-proton-libraries-in-windows), ，和 [驗證您的環境](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md#verify-your-environment)。 以下是一些秘訣可協助您完成準備指南所述的程序:

-   當您建立您 **PROTON\_PATH** 環境變數，使其 **系統** 環境變數，如下所示:

  ![](media/iot-hub-device-sdk-c-intro/07-EnvironmentVariables.PNG)

-   當您安裝 **CMake** 公用程式時，請選擇將 **CMake** 新增至**所有使用者**之系統 PATH 的選項 (新增至**目前使用者**也可行)︰

  ![](media/iot-hub-device-sdk-c-intro/08-CMake.PNG)

-   務必安裝正確的 Python 版本。 通常在 Windows 中，您應該安裝 x86 版本︰

  ![](media/iot-hub-device-sdk-c-intro/09-Python.PNG)

您可以使用安裝程式中的預設選項。

-Python 目錄新增至系統 **路徑** 環境變數。在 Windows 上，畫面如下所示：
-----------------------------------------------

  ![](media/iot-hub-device-sdk-c-intro/16-PythonPath.PNG)

-   在您開啟之前 **VS2015 的開發人員命令提示字元**, ，安裝 Git 命令列工具。 若要安裝這些工具，請完成下列步驟：

    1. 啟動 **Visual Studio 2015** 安裝程式 (或從 [程式和功能]**** 控制台選擇 **Microsoft Visual Studio 2015**，然後選取 [變更]****)。

    2. 請確定 **Git for Windows** 功能的安裝程式中已選取，但您也可以檢查 **Visual Studio 的 GitHub 擴充功能** 提供 IDE 整合的選項:

        ![](media/iot-hub-device-sdk-c-intro/10-GitTools.PNG)

    3. 完成安裝精靈來安裝工具。

    4. 將 Git 工具 **bin** 目錄新增至系統 **PATH** 環境變數。 在 Windows 上，畫面如下所示：

        ![](media/iot-hub-device-sdk-c-intro/11-GitToolsPath.PNG)

當您執行 **build\_proton.cmd** 從命令提示字元中，指令碼的指令碼從其 GitHub 儲存機制下載 Proton 來源，然後建置 Proton 程式庫。

如果您想要執行 MQTT 範例，請依照指示 [建置 Apache MQTT 庫](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md#building-the-apache-mqtt-library-in-windows) SDK 文件中。 無論如何，最後您都必須要執行 **build.cmd**。

完成上述步驟後，您就可以開始編譯範例應用程式。

### 取得裝置認證

現在已設定好您的開發環境，最後一件事就是取得一組裝置認證。 針對要能夠存取 IoT 中心的裝置，您必須先新增裝置到 IoT 中心裝置登錄。 當您加入您的裝置時，您會取得一組所需的裝置認證，以便裝置能夠連線到 IoT 中樞。 我們在下一節中看到的範例應用程式預期這些認證的形式為**裝置連接字串**。

[裝置總管](https://github.com/Azure/azure-iot-sdks/tree/master/tools/DeviceExplorer) 工具 (隨附於 Azure IoT 裝置 SDK) 會使用 Azure IoT 服務程式庫 IoT 中心，包括新增的裝置上執行各種函式。 如果您使用裝置檔案總管] 來新增裝置時，您會取得對應的連接字串。 您需要此連接字串才能執行範例應用程式。

萬一您不熟悉此程序，下列程序說明如何使用 [裝置總管] 來新增裝置和取得裝置連接字串。

開啟 * *[DeviceExplorer.sln](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/DeviceExplorer.sln)* * 在 **Visual Studio 2015** ，然後建置方案。 當您執行程式時，您會看到此介面：

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

輸入您 **IoT 中樞連接字串** 中第一個欄位按一下 **更新**。 這可設定此工具，以便與 IoT 中樞通訊。

IoT 中心連接字串設定完成後按一下 **管理** ] 索引標籤:

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

您將在其中管理在 IoT 中樞註冊的裝置。

按一下 [建立]**** 按鈕即可建立裝置。 對話方塊會顯示一組預先填入的金鑰 (主要和次要)。 您只需要為輸入 **裝置識別碼** 然後按一下 [ **建立**。

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

一旦建立裝置時，[裝置] 清單會更新所有已註冊的裝置，包括您剛建立一個。 如果您以滑鼠右鍵按一下新的裝置，您會看到此功能表:

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

如果您選擇 **複製所選裝置的連線字串** 選項，連接字串至剪貼簿複製到您的裝置。 保留的連接字串的複本。 執行下一節中所述的範例應用程式時，您將需要它。

完成上述步驟後，您就可以開始執行一些程式碼。 這兩個樣本的常數，頂端的主要原始程式檔，可讓您輸入的連接字串。 例如，從對應列 **iothub\_client\_sample\_amqp** 應用程式隨即出現，如下所示。

```
static const char* connectionString = "[device connection string]";
```

如果您想要遵循，請在此輸入您的裝置連接字串，重新編譯解決方案，您將能夠執行範例。

## IoTHubClient

內 **iothub\_client** 資料夾 azure iot sdk 的儲存機制，在沒有 **範例** 包含應用程式的資料夾命名為 **iothub\_client\_sample\_amqp**。

Windows 版本 **iothub\_client\_sample\_ampq** 應用程式包含下列的 Visual Studio 方案:

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-amqp.PNG)

此解決方案內含單一專案： 值得注意的是，有四個安裝在此解決方案的 NuGet 封裝:

  ![](media/iot-hub-device-sdk-c-intro/17-iothub-client-sample-amqp-githubpackages.PNG)

您在使用 SDK 時一律需要 **Microsoft.Azure.IoTHub.Common** 套件。 由於此範例依賴 AMQP，您也必須包含 **Apache.QPID.Proton.AzureIoT** 和 **Microsoft.Azure.IoTHub.AmqpTransport** (有相同的封裝，http) 的封裝。



  ![](media/iot-hub-device-sdk-c-intro/13-iothub_client_sample_amqp_c.PNG)

我們將使用此範例應用程式來逐步解說使用 **IoTHubClient** 程式庫時所需的項目。

### 初始化程式庫

您必須先配置 IoT 中樞用戶端控制代碼，才可以開始使用程式庫︰

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

我們也會指定我們想要使用的通訊協定。 此範例會使用 AMQP，但 HTTP 也是一個選項。

我們會接著進行探討。

### 傳送事件

將事件傳送到 IoT 中心時，需要您完成下列步驟:

首先，建立一則訊息：

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_Over_AMQP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText);
```

接著，傳送此訊息：

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

每次傳送訊息時，您會指定傳送資料時所叫用的回呼函式的參考：

```
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %d with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```



### 接收訊息

接收訊息是非同步作業。 首先，您會登錄在裝置接收訊息時所要叫用的回呼：

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

最後一個參數是您所要項目的無效指標。 在範例中，這是一個整數的指標，但可能是更複雜的資料結構的指標。 這可讓回呼函式與此函式的呼叫者以共用狀態運作。

當裝置接收訊息時，會叫用登錄的回呼函式：

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) == IOTHUB_MESSAGE_OK)
    {
        (void)printf("Received Message [%d] with Data: <<<%.*s>>> & Size=%d\r\n", *counter, (int)size, buffer, (int)size);
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```



### 解除初始化程式庫



```
IoTHubClient_Destroy(iotHubClientHandle);
```

This frees up the resources previously allocated by the **IoTHubClient\_CreateFromConnectionString** function.

As you can see, it’s easy to send events and receive messages with the **IoTHubClient** library. The library handles the details of communicating with IoT Hub, including which protocol to use (from the perspective of the developer, this is a simple configuration option).

The **IoTHubClient** library also provides precise control over how to serialize the events your device sends to IoT Hub. In some cases this is an advantage, but in other cases this is an implementation detail with which you don’t want to be concerned. If that's the case, you might consider using the **serializer** library, which we'll describe in the next section.

## Serializer

Conceptually the **serializer** library sits on top of the **IoTHubClient** library in the SDK. It uses the **IoTHubClient** library for the underlying communication with IoT Hub, but it adds modeling capabilities that remove the burden of dealing with message serialization from the developer. How this library works is best demonstrated by an example.

Within the **serializer** folder in the azure-iot-sdks repository is a **samples** folder that contains an application called **simplesample\_amqp**. The Windows version of this sample includes the following Visual Studio solution:

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_amqp.PNG)

As with the previous sample, this one includes several NuGet packages:

  ![](media/iot-hub-device-sdk-c-intro/18-simplesample_amqp-githubpackages.PNG)

We've seen most of these in the previous sample, but **Microsoft.Azure.IoTHub.Serializer** is new. This is required when we use the **serializer** library.

You can find the implementation of the sample application in the **simplesample\_amqp.c** file:

  ![](media/iot-hub-device-sdk-c-intro/15-simplesample_amqp_c.PNG)

The following sections walk you through the key parts of this sample.

### Initializing the library

To start working with the **serializer** library, you must call the initialization APIs:
```





```

The call to the **serializer\_init** function is a one-time call and is used to initialize the underlying library. Then, you call the **IoTHubClient\_CreateFromConnectionString** function, which is the same API as in the **IoTHubClient** sample. This call sets your device connection string (this is also where you choose the protocol you want to use). Note that this sample uses AMQP as the transport, but could have used HTTP.

Finally, call the **CREATE\_MODEL\_INSTANCE** function. Note that **WeatherStation** is the namespace of the model and **ContosoAnemometer** is the name of the model. Once the model instance is created, you can use it to start sending events and receiving messages. However, it's important to understand what a model is.

### Defining the model

A model in the **serializer** library defines the events that your device can send to IoT Hub and the messages, called *actions* in the modeling language, which it can receive. You define a model using a set of C macros as in the **simplesample\_amqp** sample application:
```








);


```

The **BEGIN\_NAMESPACE** and **END\_NAMESPACE** macros both take the namespace of the model as an argument. It’s expected that anything between these macros is the definition of your model(s) and the data structures that the models use.

In this example, there is a single model called **ContosoAnemometer**. This model defines two events that your device can send to IoT Hub: **DeviceId** and **WindSpeed**. It also defines three actions (messages) that your device can receive: **TurnFanOn**, **TurnFanOff**, and **SetAirResistance**. Each event has a type, and each action has a name (and optionally a set of parameters).

The events and actions defined in the model define an API surface that you can use to send events to IoT Hub, as well as respond to messages being sent to the device. This is best understood through an example.

### Sending events

The model defines the events that you can send to IoT Hub. In this example, that means one of the two events defined using the **WITH_DATA** macro. For example, if you want to send a **WindSpeed** event to an IoT hub, there are a few steps involved in making this happen. The first is to set the data we want to send:
```

```

The model we defined earlier allows us to do this by setting a member of a **struct**. Next, we serialize the event we want to send:
```




```

This code serializes the event to a buffer (referenced by **destination**). Finally, we’ll send the event to IoT hub with this code:
```

```

This is a helper function in the sample application that sends our serialized event to IoT Hub:
```

{
    
    
    
    {
        
        {
            
        }
        else
        {
            
        }

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
    messageTrackingId++;

}
```

This code is very similar to what we saw in the **iothub\_client\_sample\_amqp** application, in which we created a message from a byte array and then used **IoTHubClient\_SendEventAsync** to send it to IoT Hub. After that we just have to free the message handle and serialized data buffer we allocated earlier.

The second to last parameter of **IoTHubClient\_SendEventAsync** is a reference to a callback function that’s called when the data is successfully sent. Here's an example of a callback function:
```

{
    

    (void)printf("Message Id: %d Received.\r\n", messageTrackingId);
    
    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));

}
```

The second parameter is a pointer to user context; the same pointer we passed to **IoTHubClient\_SendEventAsync**. In this case the context is a simple counter, but it can be anything you want.

That's all there is to sending events. The only thing left to cover is how to receive messages.

### Receiving messages

Receiving a message works similarly to the way messages work in the **IoTHubClient** library. First, you register a message callback function:
```

```

Then, you write the callback function that's invoked when a message is received:
```

{
    
    
    size_t 大小;
    
    {
        
        
    }
    else
    {
        
        
        
        {
            
            
        }
        else
        {
            
            
            
            
                
                
                
            
        }
    }
    return result;
}
```

This code is boilerplate -- it's the same for any solution. This function receives the message and takes care of routing it to the appropriate function through the call to **EXECUTE\_COMMAND**. The function called at this point depends on the definition of the actions in our model.

When you define an action in your model, you're required to implement a function that's called when your device receives the corresponding message. For example, if your model defines this action:
```

```

You must define a function with this signature:
```

{
    
    
    
}
```

Note that the name of the function matches the name of the action in the model and that the parameters of the function match the parameters specified for the action. The first parameter is always required and contains a pointer to the instance of our model.

When the device receives a message that matches this signature, the corresponding function is called. Therefore, aside from having to include the boilerplate code from **IoTHubMessage**, receiving messages is just a matter of defining a simple function for each action defined in your model.

### Uninitializing the library

When you’re done sending data and receiving messages, you can uninitialize the IoT library:
```
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_Destroy(iotHubClientHandle);

}

```

上述三個函式均符合先前所述的三個初始化函式。 呼叫這些 API 可確保您釋放先前配置的資源。

## 後續步驟







