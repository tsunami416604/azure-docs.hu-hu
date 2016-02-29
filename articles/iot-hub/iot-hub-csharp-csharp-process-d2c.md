<properties
    pageTitle="處理 IoT 中心裝置到雲端訊息 | Microsoft Azure"
    description="請依照此教學課程來學習處理 IoT 中心裝置到雲端訊息的有用模式。"
    services="iot-hub"
    documentationCenter=".net"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# 教學課程：如何處理 IoT 中樞裝置到雲端訊息

## 簡介

Azure IoT 中心是一項完全受管理的服務，可讓數百萬個 IoT 裝置和一個應用程式後端進行可靠且安全的雙向通訊。 先前的教學課程 ([Get started with IoT Hub] 和 [Send Cloud-to-Device messages with IoT Hub]) 說明 IoT 中心，以及如何從裝置存取它們，而雲端元件的基本裝置對雲端和雲端-裝置傳訊功能。

本教學課程中所提供的程式碼以 [Get started with IoT Hub] 呈現兩種模式來處理裝置對雲端訊息。

第一個模式是可靠的儲存體裝置對雲端中的訊息 [Azure Blobs]。 實作時，此案例中是很常見 *冷路徑* 分析，其中儲存在 blob 中的資料做為輸入使用分析工具這類驅動 [Azure Data Factory] 或 [Hadoop] 堆疊。

第二個模式是可靠的處理 *互動式* 裝置對雲端的訊息。 裝置對雲端訊息稱為 *互動式* 相對於在應用程式後端中動作的一組立即觸發程序時 *資料點* 送入分析引擎的訊息。 例如，來自已觸發的票證插入 CRM 系統中的裝置的警示是 *互動式* 裝置對雲端的訊息，而不是遙測訊息包含溫度範例即 *資料點* 訊息。

IoT 中心會公開事件中心相容的端點接收裝置對雲端訊息，因為本教學課程使用 [EventProcessorHost] 裝載事件處理器類別中，其中:

* 可靠地儲存 *資料點* Azure Blob 中的訊息和
* 向前 *互動式* 裝置對雲端訊息傳送至 [Service Bus Queue] 立即處理。

[服務匯流排][Service Bus Queue] 是確保互動式訊息的可靠處理的好方法，因為它提供訊息每個檢查點和時間視窗基礎重複資料刪除。

在本教學課程結尾處，您將會執行三個 Windows 主控台應用程式：

* **SimulatedDevice**, ，應用程式中建立的修改的版本 [Get started with IoT Hub], ，哪一個傳送 *資料點* 裝置對雲端訊息每秒，和 *互動式* 裝置對雲端訊息每隔 10 秒。
* **ProcessDeviceToCloudMessages**, ，它會使用 [EventProcessorHost] 可靠地儲存 *資料點* Azure blob 中的訊息，並轉送 *互動式* 訊息至服務匯流排佇列，並
* **ProcessD2cInteractiveMessages**, ，它從佇列中清除佇列中的訊息。

> [AZURE.NOTE] IoT 中心都有支援許多裝置平台和語言 (包括 C、 Java 和 Javascript) 雖然 Azure IoT 裝置 Sdk 的 SDK。 請參閱 [Azure IoT Developer Center] 的逐步指示來連接您的裝置，本教學課程的程式碼，和通常 Azure IoT 中心。

> [AZURE.NOTE] 本教學課程的內容都直接適用於其他方法可以使用事件中心相容的訊息，例如 [Hadoop] Storm 之類的專案。 請參閱 [IoT Hub Guidance - Event Hubs compatibility] 如需詳細資訊。

若要完成本教學課程，您需要下列項目：

+ Microsoft Visual Studio 2015，

+ 使用中的 Azure 帳戶。 <br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用] (http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02 和 amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fiot%2Ftutorials%2Fprocess-d2c%2F 目標 ="_blank")。

是同時也假設知道 [Azure Storage] 和 [Azure Service Bus]。


[AZURE.INCLUDE [iot-hub-process-d2c-device-csharp](../../includes/iot-hub-process-d2c-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-process-d2c-cloud-csharp](../../includes/iot-hub-process-d2c-cloud-csharp.md)]

## 執行應用程式

現在您已經準備好執行應用程式。

1.  從在 Visual Studio 中，以滑鼠右鍵按一下您的方案並選取 [ **設定啟始專案**。 選取 **多個啟始專案**, ，然後選取 **啟動** 動作 **ProcessDeviceToCloudMessages**, ，**SimulatedDevice**, ，和 **ProcessD2cInteractiveMessages** 應用程式。

2.  按下 **F5**, ，而且您會看到所有應用程式啟動，並模擬裝置所傳送的每個互動式訊息應該由互動式訊息處理器。

  ![][50]

> [AZURE.NOTE] 若要查看您正在更新的 blob 檔案，您可能要減少 `MAX_BLOCK_SIZE` 不變的 `StoreEventProcessor` 為較小的項目 (也就是 `1024`). 這是因為達到模擬裝置所傳送之資料的區塊大小限制需要一些時間。 編輯之後，您應該能在您的儲存體容器中看見要建立及更新的 Blob。

## 後續步驟

在本教學課程中，您學到如何可靠地處理 *資料點* 和 *互動式* 裝置對雲端訊息使用 [EventProcessorHost]。 可以用來實作的類比訊息處理邏輯：

- [Uploading files from devices], 描述的模式，以便從裝置的檔案上傳，以便使用的定域機組對裝置的訊息。

有關 IoT 中心的其他資訊：

* [IoT 中心概觀]
* [IoT 中心開發人員指南]
* [IoT 中心指引]
* [支援的裝置平台和語言][Supported devices]
* [Azure IoT 開發人員中心]

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png


<!-- Links -->

[Azure Blobs]: https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-blobs/
[Azure Data Factory]: https://azure.microsoft.com/en-us/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/en-us/documentation/services/hdinsight/
[Service Bus Queue]: https://azure.microsoft.com/en-us/documentation/articles/service-bus-dotnet-how-to-use-queues/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[IoT Hub Guidance - Event Hubs compatibility]: iot-hub-guidance.md#eventhubcompatible

[Azure Storage]: https://azure.microsoft.com/en-us/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/en-us/documentation/services/service-bus/

[Azure portal]: https://portal.azure.com/

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Uploading files from devices]: iot-hub-csharp-csharp-file-upload.md

[IoT Hub Overview]: iot-hub-what-is-iot-hub.md
[IoT Hub Guidance]: iot-hub-guidance.md
[IoT Hub Developer Guide]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Azure IoT Developer Center]: http://www.azure.com/develop/iot

