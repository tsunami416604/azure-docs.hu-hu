<properties
    pageTitle="使用 IoT 中心從裝置上傳檔案 | Microsoft Azure"
    description="請遵循本教學課程以了解如何從使用 Azure IoT 中心搭配 C# 的裝置上傳檔案。"
    services="iot-hub"
    documentationCenter=".net"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# 教學課程：如何使用 IoT 中樞將檔案從裝置上傳到雲端

## 簡介

Azure IoT 中心是一項完全受管理的服務，可讓數百萬個 IoT 裝置和一個應用程式後端進行可靠且安全的雙向通訊。 先前的教學課程 ([Get started with IoT Hub] 和 [Send Cloud-to-Device messages with IoT Hub]) 說明 IoT 中心，以及如何從裝置存取它們，而雲端元件的基本裝置對雲端和雲端-裝置傳訊功能。 [Process Device-to-Cloud messages] 說明可靠地在 Azure blob 儲存體中儲存裝置對雲端訊息的方式。 然而，有些情況下來自裝置的資料不會很容易地對應到相對較小的裝置到雲端訊息。 某些範例是包含圖片、影片、高頻率震動資料範例，或是包含部分前置處理資料形式的大型檔案。 這些檔案通常會使用工具，例如透過批次方式處理 [Azure Data Factory] 或 [Hadoop] 堆疊。 當偏好從裝置上傳檔案來傳送事件時，仍可能使用 IoT 中心安全性與可靠性功能。

本教學課程中所提供的程式碼以 [Send Cloud-to-Device messages with IoT Hub] 示範如何使用定域機組-裝置訊息安全地提供給裝置的 Azure blob URI 可用來上傳檔案，以及如何使用 IoT 中心傳遞通知觸發從您的應用程式後端檔案的處理。 此方法的優點是重複使用 IoT 中樞裝置身分識別以及雲端到裝置訊息的傳遞通知，來通知應用程式後端檔案已經順利上傳。

> [AZURE.NOTE] 此處所使用的相同方法可安全地將裝置從雲端下載檔案。

您可以找到更多有關定域機組對裝置的訊息和 IoT 中心中的安全性 [IoT Hub Developer Guide]。

在本教學課程結尾處，您將會執行兩個 Windows 主控台應用程式：

* **SimulatedDevice**, ，應用程式中建立的修改的版本 [Send Cloud-to-Device messages with IoT Hub], ，這會連結至您的 IoT 中心收到定域機組對裝置訊息，其中包含 Azure blob 的 Uri。 針對每個接收到的雲端到裝置訊息，它會觸發將檔案上傳至指定的 Blob URI。
* **SendCloudToDevice**, ，哪些組建的 Azure blob 的 URI (如所述 [建立並使用與 Blob 服務 SAS](../storage/storage-dotnet-shared-access-signature-part-2.md), 定域機組對裝置訊息中傳送到透過 IoT 中心與模擬裝置，然後接收其傳遞 aknowledgment。

> [AZURE.NOTE] IoT 中心都有支援許多裝置平台和語言 (包括 C、 Java 和 Javascript) 雖然 Azure IoT 裝置 Sdk 的 SDK。 請參閱 [Azure IoT Developer Center] 的逐步指示來連接您的裝置，本教學課程的程式碼，和通常 Azure IoT 中心。 適用於 Java 和 Node 的 Azure IoT 服務 SDK 即將推出。

若要完成本教學課程，您需要下列項目：

+ Microsoft Visual Studio 2015，

+ 使用中的 Azure 帳戶。 <br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用] (http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02 和 amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fiot%2Ftutorials%2Ffile-upload%2F 目標 ="_blank")。


[AZURE.INCLUDE [iot-hub-file-upload-cloud-csharp](../../includes/iot-hub-file-upload-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-file-upload-device-csharp](../../includes/iot-hub-file-upload-device-csharp.md)]

## 執行應用程式

現在您已經準備好執行應用程式。

1.  從在 Visual Studio 中，以滑鼠右鍵按一下您的方案並選取 [ **設定啟始專案**。 選取 **多個啟始專案**, ，然後選取 **啟動** 兩個動作 **SimulatedDevice**, ，和 **SendCloudToDevice** 應用程式。

2.  按下 **F5**, ，而且您會看到所有的應用程式啟動。 選取 **SendCloudToDevice** 視窗並按下按鍵。 您會看到它已上傳檔案時，模擬的裝置會輸出一則訊息， **SendCloudToDevice** 應用程式顯示成功的意見反應回條。 您可以使用 [Azure portal] 或 Visual Studio 伺服器總管來檢查您的儲存體帳戶中的檔案是否存在。

  ![][50]


## 後續步驟

在本教學課程中，您已學到如何運用雲端到裝置訊息來簡化從裝置上傳檔案。 您可以利用下列教學課程繼續探索 IoT 中心功能和案例：

- [Process Device-to-Cloud messages], 示範如何可靠地處理遙測和互動式從裝置傳送的訊息。

有關 IoT 中心的其他資訊：

* [IoT 中心概觀]
* [IoT 中心開發人員指南]
* [IoT 中心指引]
* [支援的裝置平台和語言][Supported devices]
* [Azure IoT 開發人員中心]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png

<!-- Links -->

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md

[Azure portal]: https://portal.azure.com/

[Azure Data Factory]: https://azure.microsoft.com/en-us/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/en-us/documentation/services/hdinsight/

[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
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

