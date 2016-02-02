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

Azure IoT 中心是一項完全受管理的服務，可讓數百萬個 IoT 裝置和一個應用程式後端進行可靠且安全的雙向通訊。   然而，有些情況下來自裝置的資料不會很容易地對應到相對較小的裝置到雲端訊息。 某些範例是包含圖片、影片、高頻率震動資料範例，或是包含部分前置處理資料形式的大型檔案。  當偏好從裝置上傳檔案來傳送事件時，仍可能使用 IoT 中心安全性與可靠性功能。

此方法的優點是重複使用 IoT 中樞裝置身分識別以及雲端到裝置訊息的傳遞通知，來通知應用程式後端檔案已經順利上傳。
> [AZURE.NOTE] 這裡使用的相同方法可用來讓裝置安全地由雲端下載檔案。



在本教學課程結尾處，您將會執行兩個 Windows 主控台應用程式：

* 針對每個接收到的雲端到裝置訊息，它會觸發將檔案上傳至指定的 Blob URI。
* 

> [AZURE.NOTE] IoT 中心透過 Azure IoT 裝置 SDK 提供許多裝置平台和語言的 SDK 支援 (包括 C、Java 和 Javascript)。  適用於 Java 和 Node 的 Azure IoT 服務 SDK 即將推出。

若要完成本教學課程，您需要下列項目：

+ Microsoft Visual Studio 2015，

+ 使用中的 Azure 帳戶。


[AZURE.INCLUDE [iot-hub-file-upload-cloud-csharp](../../includes/iot-hub-file-upload-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-file-upload-device-csharp](../../includes/iot-hub-file-upload-device-csharp.md)]

## 執行應用程式

現在您已經準備好執行應用程式。

1.  在 Visual Studio 中，以滑鼠右鍵按一下您的解決方案並選取 [設定啟始專案...]****。 選取 [多個啟始專案]****，然後同時針對 **SimulatedDevice** 和 **SendCloudToDevice** App 選取 [啟動]**** 動作。

2.  按下 **F5**，您應該會看到所有的應用程式啟動。 選取 **SendCloudToDevice** 視窗並按下索引鍵。 當模擬裝置已上傳檔案時，您會看到模擬裝置輸出訊息，且 **SendCloudToDevice** App 會顯示成功的意見反應回條。

  ![][50]


## 後續步驟

在本教學課程中，您已學到如何運用雲端到裝置訊息來簡化從裝置上傳檔案。 您可以利用下列教學課程繼續探索 IoT 中心功能和案例：

- 

有關 IoT 中心的其他資訊：

* 
* 
* 
* 
* 






[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png 
[send cloud-to-device messages with iot hub]: iot-hub-csharp-csharp-c2d.md 
[azure portal]: https://portal.azure.com/ 
[azure data factory]: https://azure.microsoft.com/en-us/documentation/services/data-factory/ 
[hadoop]: https://azure.microsoft.com/en-us/documentation/services/hdinsight/ 
[get started with iot hub]: iot-hub-csharp-csharp-getstarted.md 
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

