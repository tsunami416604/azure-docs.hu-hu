<properties
    pageTitle="使用 IoT 中樞傳送雲端到裝置訊息 | Microsoft Azure"
    description="請遵循本教學課程，以了解如何搭配 C# 使用 Azure IoT 中心傳送雲端到裝置的訊息。"
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


# 教學課程：如何使用 IoT 中樞傳送雲端到裝置訊息

## 簡介

Azure IoT 中心是一項完全受管理的服務，可讓數百萬個 IoT 裝置和一個應用程式後端進行可靠且安全的雙向通訊。





在本教學課程結尾處，您將會執行兩個 Windows 主控台應用程式：

* 
* **SendCloudToDevice**，將雲端到裝置的訊息透過 IoT 中心傳送到模擬的裝置，然後收到其傳遞通知。

> [AZURE.NOTE] IoT 中心透過 Azure IoT 裝置 SDK 提供許多裝置平台和語言的 SDK 支援 (包括 C、Java 和 Javascript)。  適用於 Java 和 Node 的 Azure IoT 服務 SDK 即將推出。

若要完成本教學課程，您需要下列項目：

+ Microsoft Visual Studio 2015，

+ 使用中的 Azure 帳戶。

[AZURE.INCLUDE [iot-hub-c2d-device-csharp](../../includes/iot-hub-c2d-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-c2d-cloud-csharp](../../includes/iot-hub-c2d-cloud-csharp.md)]

## 後續步驟

在本教學課程中，您已了解如何傳送和接收雲端到裝置的訊息。 您可以利用下列教學課程，繼續探索 IoT 中心功能和案例：

- 
- 

有關 IoT 中心的其他資訊：

* 
* 
* 
* 
* 






[get started with iot hub]: iot-hub-csharp-csharp-getstarted.md 
[iot hub developer guide - c2d]: iot-hub-devguide.md#c2d 
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

