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

Azure IoT 中心是一項完全受管理的服務，可讓數百萬個 IoT 裝置和一個應用程式後端進行可靠且安全的雙向通訊。





第二個模式是*互動式*裝置到雲端訊息的可靠處理。 當它們因為應用程式後端中的一組動作而立即觸發 (相對於送入分析引擎的「資料點」**訊息) 時，裝置到雲端訊息稱為「互動式」**。 例如，由必須觸發在 CRM 系統中插入票證之裝置所發出的警示是*互動式*裝置到雲端訊息，相對於包含溫度範例，屬於*資料點*訊息的遙測訊息。



* 在 Azure Blob 中可靠地儲存*資料點*，且
* 



在本教學課程結尾處，您將會執行三個 Windows 主控台應用程式：

* 
* 
* **ProcessD2cInteractiveMessages**，可將訊息從佇列清除。

> [AZURE.NOTE] IoT 中心透過 Azure IoT 裝置 SDK 提供許多裝置平台和語言的 SDK 支援 (包括 C、Java 和 Javascript)。

> [AZURE.NOTE] 

若要完成本教學課程，您需要下列項目：

+ Microsoft Visual Studio 2015，

+ 使用中的 Azure 帳戶。




[AZURE.INCLUDE [iot-hub-process-d2c-device-csharp](../../includes/iot-hub-process-d2c-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-process-d2c-cloud-csharp](../../includes/iot-hub-process-d2c-cloud-csharp.md)]

## 執行應用程式

現在您已經準備好執行應用程式。

1.  在 Visual Studio 中，以滑鼠右鍵按一下您的解決方案並選取 [設定啟始專案...]****。 選取 [多個啟始專案]****，然後針對**ProcessDeviceToCloudMessages**、**SimulatedDevice** 和 **ProcessD2cInteractiveMessages** App 選取 [啟動]**** 動作。

2.  按下 **F5**，您應該會看到所有的應用程式啟動，且模擬裝置所傳送的每一個互動式訊息都應由互動式訊息處理器進行處理。

  ![][50]

> [AZURE.NOTE] 這是因為達到模擬裝置所傳送之資料的區塊大小限制需要一些時間。 編輯之後，您應該能在您的儲存體容器中看見要建立及更新的 Blob。

## 後續步驟

可以用來實作的類比訊息處理邏輯：

- 

有關 IoT 中心的其他資訊：

* 
* 
* 
* 
* 





[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png 
[azure blobs]: https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-blobs/ 
[azure data factory]: https://azure.microsoft.com/en-us/documentation/services/data-factory/ 
[hadoop]: https://azure.microsoft.com/en-us/documentation/services/hdinsight/ 
[service bus queue]: https://azure.microsoft.com/en-us/documentation/articles/service-bus-dotnet-how-to-use-queues/ 
[eventprocessorhost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx 
[transient fault handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx 
[iot hub guidance - event hubs compatibility]: iot-hub-guidance.md#eventhubcompatible 
[azure storage]: https://azure.microsoft.com/en-us/documentation/services/storage/ 
[azure service bus]: https://azure.microsoft.com/en-us/documentation/services/service-bus/ 
[azure portal]: https://portal.azure.com/ 
[send cloud-to-device messages with iot hub]: iot-hub-csharp-csharp-c2d.md 
[process device-to-cloud messages]: iot-hub-csharp-csharp-process-d2c.md 
[uploading files from devices]: iot-hub-csharp-csharp-file-upload.md 
[iot hub overview]: iot-hub-what-is-iot-hub.md 
[iot hub guidance]: iot-hub-guidance.md 
[iot hub developer guide]: iot-hub-devguide.md 
[iot hub supported devices]: iot-hub-supported-devices.md 
[get started with iot hub]: iot-hub-csharp-csharp-getstarted.md 
[supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md 
[azure iot developer center]: http://www.azure.com/develop/iot 

