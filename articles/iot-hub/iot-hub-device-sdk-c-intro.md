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

 **Azure IoT 裝置 SDK** 是一組設計來簡化事件傳送和接收訊息的程序的程式庫 **Azure IoT 中心** 服務。 在 SDK 中，每個目標為特定的平台的不同變化，但本文將說明 **Azure IoT 裝置 SDK c**。

Azure IoT 裝置 SDK (適用於 C) 是以 ANSI C (C99) 撰寫，以獲得最大可攜性。 如此就很適合在一些平台和裝置上運作 - 尤其是在以將磁碟和記憶體使用量降至最低做為優先考量的情況下。  

有各種 SDK 已經過測試所在的平台 (請參閱 [SDK 文件](https://github.com/Azure/azure-iot-sdks/tree/master/c) 如需詳細資訊)。 雖然本文包含在 Windows 平台上執行之範例程式碼的逐步解說，但請記住，本文所述的程式碼在各種支援的平台上都完全相同。

本文將介紹 Azure IoT 裝置 SDK (適用於 C) 的架構。我們將示範如何初始化裝置程式庫，將事件傳送到 IoT 中樞，以及從 IoT 中樞接收訊息。 本文中的資訊應足以讓您開始使用 SDK，但也提供了可取得程式庫其他相關資訊的指標。

## SDK 架構

您可以找到 **Azure IoT 裝置 SDK c** 下列 GitHub 儲存機制中 ︰

[azure-iot-sdks](https://github.com/Azure/azure-iot-sdks)

最新版本的程式庫可在 **主要** 此儲存機制分支 ︰

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

此儲存機制包含整個系列的 Azure IoT 裝置 SDK。 不過，這篇文章是關於 Azure IoT 裝置 SDK *c* 中可以找到其 **c** 資料夾。

  ![](media/iot-hub-device-sdk-c-intro/02-CFolder.PNG)

SDK 核心實作位於 **常見**, ，**iothub\_client**, ，和 **序列化程式** 儲存機制中的資料夾。  **常見** 資料夾包含在整個程式庫共用的程式碼 (通常您不使用中的程式碼 **常見** 直接資料夾)。 不過， **iothub\_client** 和 **序列化程式** 資料夾包含兩個不同的程式碼中，您將使用的 SDK 層的實作。

-   **IoTHubClient** - **iothub\_client** 資料夾包含實作的 SDK 中的最低 API 層級 ︰ **IoTHubClient** 程式庫。  **IoTHubClient** 程式庫包含 Api 將事件傳送到 IoT 中心，以及接收訊息。 如果您使用此程式庫，就需負責實作訊息序列化，但與 IoT 中樞通訊的其他細節則是由系統為您處理。

-   **序列化程式** - **序列化程式** 資料夾包含實作 **序列化程式** 程式庫。  **序列化程式** 文件庫加入模型化功能所提供的功能之上 **IoTHubClient**。 如果您使用 **序列化程式** 程式庫，開始所定義的模型，指定您想要傳送到 IoT 中心，以及您希望從它所收到的訊息的事件。 定義此模型後，SDK 會提供您一個 API 介面，可讓您輕鬆地處理事件和訊息，而不需操心序列化細節。

查看範例程式碼可以較容易了解這一切。 下列各節將為您逐步解說 SDK 中包含的幾個範例應用程式。 這應可讓您輕鬆了解 SDK 架構層的各種功能以及 API 運作方式的簡介。

## 執行範例之前

您必須先完成兩項工作，才可以在 Azure IoT 裝置 SDK (適用於 C) 中執行範例：準備開發環境和取得裝置認證。  [讀我檔案](https://github.com/Azure/azure-iot-sdks/tree/master/c) 隨附 SDK 提供這兩個工作的指示。 下列各節包含有關這些指示的一些額外評論。

### 準備開發環境

首先，您必須從 GitHub 取得 SDK 的複本，然後建立來源。 您應該取得一份來自來源 **主要** 分支 [GitHub 儲存機制](https://github.com/Azure/azure-iot-sdks)。

當您下載來源的複本時，您必須完成 SDK 文件中所述的步驟 [準備開發環境](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md)。 大部分的工作，您必須執行 Windows 中包含 [準備 Qpid Proton 程式庫](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md#preparing-qpid-proton-libraries-in-windows), ，和 [驗證您的環境](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md#verify-your-environment)。 以下是一些秘訣，可幫助您完成準備指南中所描述的程序：

-   當您建立您 **PROTON\_PATH** 環境變數，使其 **系統** 環境變數，如下所示 ︰

  ![](media/iot-hub-device-sdk-c-intro/07-EnvironmentVariables.PNG)

-   當您安裝 **CMake** 公用程式，選擇選項來加入 **CMake** 到系統路徑的 **所有使用者** (加入 **目前使用者** 另外也適用於):

  ![](media/iot-hub-device-sdk-c-intro/08-CMake.PNG)

-   務必安裝正確的 Python 版本。 通常在 Windows 中，您應該安裝 x86 版本︰

  ![](media/iot-hub-device-sdk-c-intro/09-Python.PNG)

您可以使用安裝程式中的預設選項。

- Python 目錄新增至系統 **路徑** 環境變數。 在 Windows 上，畫面如下所示：
- 
  ![](media/iot-hub-device-sdk-c-intro/16-PythonPath.PNG)

-   在您開啟之前 **VS2015 的開發人員命令提示字元**, ，安裝 Git 命令列工具。 若要安裝這些工具，請完成下列步驟：

    1. 啟動 **Visual Studio 2015** 安裝程式 (或選擇 **Microsoft Visual Studio 2015** 從 **程式和功能** 控制台，然後選取 **變更**)。
    
    2. 請確定 **Git for Windows** 功能的安裝程式中已選取，但您也可以檢查 **Visual Studio 的 GitHub 擴充功能** 提供 IDE 整合的選項 ︰

        ![](media/iot-hub-device-sdk-c-intro/10-GitTools.PNG)

    3. 完成安裝精靈來安裝工具。

    4. 加入 Git 工具 **bin** 目錄新增至系統 **路徑** 環境變數。 在 Windows 上，畫面如下所示：

        ![](media/iot-hub-device-sdk-c-intro/11-GitToolsPath.PNG)

當您執行 **build\_proton.cmd** 從命令提示字元中，指令碼的指令碼從其 GitHub 儲存機制下載 Proton 來源，然後建置 Proton 程式庫。

如果您想要執行 MQTT 範例，請依照指示 [建置 Apache MQTT 庫](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md#building-the-apache-mqtt-library-in-windows) SDK 文件中。 無論如何，您必須執行的最後一件事是執行 **build.cmd**。

完成上述步驟後，您就可以開始編譯範例應用程式。

### 取得裝置認證

現在已設定好您的開發環境，最後一件事就是取得一組裝置認證。  若要讓裝置能夠存取 IoT 中樞，您必須先將該裝置新增至 IoT 中樞裝置登錄。 當您加入您的裝置時，您會取得一組所需的裝置認證，以便裝置能夠連線到 IoT 中樞。 我們將探討下一節中的範例應用程式預期這些認證的形式 **裝置連接字串**。

 [裝置總管](https://github.com/Azure/azure-iot-sdks/tree/master/tools/DeviceExplorer) 工具 （隨附於 Azure IoT 裝置 SDK） 會使用 Azure IoT 服務程式庫 IoT 中心，包括新增的裝置上執行各種函式。 如果您使用 [裝置總管] 來新增裝置，您將會得到對應的連接字串。 您需要此連接字串才能執行範例應用程式。

萬一您不熟悉此程序，下列程序說明如何使用 [裝置總管] 來新增裝置和取得裝置連接字串。

開啟 **[DeviceExplorer.sln](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/DeviceExplorer.sln)** 中 **Visual Studio 2015** ，然後建置方案。 當您執行程式時，您會看到此介面：

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

輸入您 **IoT 中樞連接字串** 中第一個欄位按一下 **更新**。 這可設定此工具，以便與 IoT 中樞通訊。

IoT 中心連接字串設定完成後按一下 [ **管理** ] 索引標籤 ︰

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

您將在其中管理在 IoT 中樞註冊的裝置。

您可以建立一個裝置，即可 **建立** ] 按鈕。 將會顯示一個已預先填入一組金鑰 (主要和次要) 的對話方塊。 您只需要為輸入 **裝置識別碼** 然後按一下 [ **建立**。

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

建立裝置後，就會以所有註冊的裝置 (包括您剛才建立的裝置) 更新 [裝置] 清單。 如果您在新裝置上按一下滑鼠右鍵，您將看到此功能表︰

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

如果您選擇 **複製所選裝置的連線字串** 選項，連接字串至剪貼簿複製到您的裝置。 請保留一份連接字串複本。 在執行後續各節中所述的範例應用程式時，您將會需要它。

完成上述步驟後，您就可以開始執行一些程式碼。 兩個範例在主要原始程式檔頂端都有一個常數，此常數可讓您輸入連接字串。 例如，從對應列 **iothub\_client\_sample\_amqp** 應用程式隨即出現，如下所示。

```
static const char* connectionString = "[device connection string]";
```

如果您想要遵循，請在此輸入您的裝置連接字串，重新編譯解決方案，您將能夠執行範例。

## IoTHubClient

內 **iothub\_client** 資料夾 azure iot sdk 的儲存機制，在沒有 **範例** 包含應用程式的資料夾命名為 **iothub\_client\_sample\_amqp**。

Windows 版本 **iothub\_client\_sample\_ampq** 應用程式包含下列的 Visual Studio 方案 ︰

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-amqp.PNG)

此解決方案內含單一專案： 值得注意的是，此解決方案中安裝了四個 NuGet 套件：

  ![](media/iot-hub-device-sdk-c-intro/17-iothub-client-sample-amqp-githubpackages.PNG)

您永遠需要 **Microsoft.Azure.IoTHub.Common** 封裝，當您使用 SDK。 由於此範例依賴 AMQP，您也必須包含 **Apache.QPID.Proton.AzureIoT** 和 **Microsoft.Azure.IoTHub.AmqpTransport** （有相同的封裝，http） 的封裝。 因為此範例會使用 **IoTHubClient** 程式庫，您也必須包含 **Microsoft.Azure.IoTHub.IoTHubClient** 在我們的解決方案中的套件。

您可以找到範例應用程式中實作 **iothub\_client\_sample\_amqp.c** 原始程式檔 ︰

  ![](media/iot-hub-device-sdk-c-intro/13-iothub_client_sample_amqp_c.PNG)

我們將使用此範例應用程式來引導您完成所需使用 **IoTHubClient** 程式庫。

### 初始化程式庫

您必須先配置 IoT 中樞用戶端控制代碼，才可以開始使用程式庫︰

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

請注意，我們要將我們的裝置連接字串複本傳遞給此函式 (我們從 [裝置總管] 取得的函式)。 我們也會指定我們想要使用的通訊協定。 此範例會使用 AMQP，但 HTTP 也是一個選項。

當您擁有有效 **IOTHUB\_CLIENT\_HANDLE**, ，您可以開始呼叫 Api，以將事件傳送和接收訊息的 IoT 中心。 我們會接著進行探討。

### 傳送事件

您必須完成下列步驟，才能將事件傳送到 IoT 中樞：

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

請注意呼叫 **IoTHubMessage\_Destroy** 函式，當您完成訊息。 您必須進行此呼叫，才能釋放在建立訊息時所配置的資源。

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

請注意，您 **IoTHubMessage\_GetByteArray** 函式可擷取的郵件，在此範例中是一個字串。

### 解除初始化程式庫

當您完成事件傳送和訊息接收時，您可以將 IoT 程式庫解除初始化。 若要這麼做，請發出下列函式呼叫：

```
IoTHubClient_Destroy(iotHubClientHandle);
```

這樣可釋放先前配置的資源 **IoTHubClient\_CreateFromConnectionString** 函式。

如您所見，很容易將事件傳送和接收郵件 **IoTHubClient** 程式庫。 此程式庫會處理與 IoT 中樞進行的通訊細節，包括要使用哪個通訊協定 (從開發人員的觀點來看，這是一個簡單的設定選項)。

 **IoTHubClient** 程式庫也提供精確地控制如何序列化您的裝置傳送到 IoT 中心的事件。 在某些情況下，這是一項優點，但在其他情況下，這是您不想要參與的實作細節。 如果是這樣，您可以考慮使用 **序列化程式** 程式庫，我們將在下一節中說明。

## 序列化程式

在概念上 **序列化程式** 程式庫位於最上層的 **IoTHubClient** SDK 中的程式庫。 它會使用 **IoTHubClient** IoT 中心，但它的基礎通訊的程式庫新增的模型化功能中移除開發人員處理訊息序列化的負擔。 此程式庫的運作方式最好是由範例示範。

內 **序列化程式** azure iot sdk 儲存機制中的資料夾是 **範例** 包含應用程式的資料夾命名為 **simplesample\_amqp**。 Windows 版本的這個範例包含下列 Visual Studio 解決方案：

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_amqp.PNG)

如同先前的範例，此範例也包含數個 NuGet 套件：

  ![](media/iot-hub-device-sdk-c-intro/18-simplesample_amqp-githubpackages.PNG)

我們已了解大部分這些功能先前範例中，但 **Microsoft.Azure.IoTHub.Serializer** 新。 這是必要的當我們使用 **序列化程式** 程式庫。

您可以找到範例應用程式中實作 **simplesample\_amqp.c** 檔案 ︰

  ![](media/iot-hub-device-sdk-c-intro/15-simplesample_amqp_c.PNG)

下列各節將逐步解說此範例的重要部分。

### 初始化程式庫

若要開始使用 **序列化程式** 程式庫，您必須呼叫初始化 Api:

```
serializer_init(NULL);

IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);

ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
```

若要呼叫 **serializer\_init** 函式是一次性的呼叫，用來初始化基礎程式庫。 然後，呼叫 **IoTHubClient\_CreateFromConnectionString** 函式，也就是相同的 API，如下所示 **IoTHubClient** 範例。 此呼叫會設定您的裝置連接字串 (這也可用來選擇您要使用的通訊協定)。 請注意，此範例使用 AMQP 做為傳輸方式，但可能使用過 HTTP。

最後，呼叫 **CREATE\_MODEL\_INSTANCE** 函式。 請注意， **WeatherStation** 是模型的命名空間和 **ContosoAnemometer** 是模型的名稱。 建立模型執行個體後，您便可以使用它來開始傳送事件和接收訊息。 不過，請務必了解模型是什麼。

### 定義模型

中的模型 **序列化程式** 程式庫會定義您的裝置可以將傳送至 IoT 中心與訊息，稱為事件 *動作* 在模組化語言中，它可以接收。 您定義使用中的 C 巨集的模型 **simplesample\_amqp** 範例應用程式 ︰

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

 **BEGIN\_NAMESPACE** 和 **END\_NAMESPACE** 巨集這兩個需要模型做為引數的命名空間。 介於這兩個巨集之間的項目應該就是您的模型的定義和模型所使用的資料結構。

在此範例中，沒有單一模型稱為 **ContosoAnemometer**。 此模型會定義您的裝置可以將傳送至 IoT 中心的兩個事件 ︰ **DeviceId** 和 **WindSpeed**。 它也會定義三項動作 （訊息），可以接收您的裝置 ︰ **TurnFanOn**, ，**TurnFanOff**, ，和 **SetAirResistance**。 每個事件都有類型，而每個動作都有名稱 (以及一組選擇性的參數)。

模型中定義的事件和動作可定義 API 介面，此介面可供您用來將事件傳送到 IoT 中樞，以及回應傳送至裝置的訊息。 最好能透過範例了解相關情況。

### 傳送事件

此模型會定義您可以傳送到 IoT 中樞的事件。 在此範例中，這表示使用定義的兩個事件的其中一個 **WITH_DATA** 巨集。 例如，如果您想要傳送 **WindSpeed** 事件 IoT 中心，有幾個步驟中進行這項工作。 第一個步驟是設定我們要傳送的資料：

```
myWeather->WindSpeed = 15;
```

我們稍早定義的模型可讓我們設定的成員 **結構**。 接著，我們要將想要傳送的事件序列化：

```
unsigned char* destination;
size_t destinationSize;

SERIALIZE(&destination, &destinationSize, myWeather->WindSpeed);
```

此程式碼序列化至緩衝區的事件 (所參考的 **目的地**)。 最後，我們會使用下列程式碼將事件傳送到 IoT 中樞︰

```
sendMessage(iotHubClientHandle, destination, destinationSize);
```

在範例應用程式中，這是將已序列化的事件傳送到 IoT 中樞的協助程式函式：

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
    messageTrackingId++;
}
```

此程式碼是非常類似於我們中看到 **iothub\_client\_sample\_amqp** 應用程式，我們從位元組陣列建立訊息，然後使用 **IoTHubClient\_SendEventAsync** 將它傳送給 IoT 中心。 之後，我們只需釋放我們先前配置的訊息控制代碼和已序列化的資料緩衝區。

最後一個參數的第二個 **IoTHubClient\_SendEventAsync** 成功傳送資料時，會呼叫回呼函式的參考。 以下是回呼函式的範例︰

```
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    int messageTrackingId = (intptr_t)userContextCallback;

    (void)printf("Message Id: %d Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

第二個參數是一個指向使用者內容。我們傳遞至相同的指標 **IoTHubClient\_SendEventAsync**。 在此案例中，此內容是一個簡易計數器，但它可以是您想要的任何東西。

傳送事件就是這麼簡單。 最後只剩下說明如何接收訊息。

### 接收訊息

接收訊息的運作方式類似於訊息的方式在 **IoTHubClient** 程式庫。 首先，您需登錄訊息回呼函式：

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

然後，撰寫在接收訊息時所叫用的回呼函式：

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

此程式碼會重複使用 - 對任何解決方案而言都一樣。 此函式會接收訊息，並負責路由傳送到適當的函式的呼叫 **EXECUTE\_COMMAND**。 此時所呼叫的函式取決於模型中的動作定義。

當您在模型中定義動作時，您必須實作在裝置接收對應的訊息時所呼叫的函式。 例如，如果您的模型定義這項動作：

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

請注意，函式的名稱會與模型中的動作名稱相符，而函式的參數則會與為此動作指定的參數相符。 第一個參數是必要參數，含有我們的模型執行個體的指標。

當裝置收到符合此簽章的訊息時，就會呼叫對應的函式。 因此，除了必須包含的未定案程式碼 **IoTHubMessage**, ，接收訊息時只需定義簡單的函式，針對每個模型中定義的動作。

### 解除初始化程式庫

當您完成資料傳送和訊息接收時，您可以解除初始化 IoT 程式庫：

```
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

上述三個函式均符合先前所述的三個初始化函式。 呼叫這些 API 可確保您釋放先前配置的資源。

## 後續步驟

這篇文章涵蓋使用中的程式庫的基本概念 **Azure IoT 裝置 SDK c**。這提供您足夠的資訊來了解 SDK 中包含什麼、其架構，以及如何開始使用 Windows 範例。 下一篇文章會繼續 SDK 的描述說明 [更多關於 IoTHubClient 程式庫](iot-hub-device-sdk-c-iothubclient.md)。


