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

Azure IoT 中心是一項完全受管理的服務，可讓數百萬個 IoT 裝置和一個應用程式後端進行可靠且安全的雙向通訊。  [Get started with IoT Hub] 教學課程示範如何建立 IoT 中心、 佈建裝置身分識別，以及程式碼模擬的裝置傳送至雲端裝置訊息。

本教學課程以 [Get started with IoT Hub] 並示範如何將裝置定域機組訊息傳送至同一個裝置，如何要求傳遞通知 (*意見*) 從 IoT 中心，並接收來自應用程式定域機組的後端。

您可以找到更多有關定域機組對裝置中的訊息 [IoT 中心開發人員指南][IoT Hub Developer Guide - C2D]。

在本教學課程結尾處，您將會執行兩個 Windows 主控台應用程式：

* **SimulatedDevice**, ，應用程式中建立的修改的版本 [Get started with IoT Hub], ，這會連線至您的 IoT 中心及接收定域機組對裝置的訊息。
* **SendCloudToDevice**, ，它將透過 IoT 中心與模擬裝置定域機組對裝置訊息傳送，但收到其傳遞 aknowledgment。

> [AZURE.NOTE] IoT 中心都有支援許多裝置平台和語言 （包括 C、 Java 和 Javascript） 雖然 Azure IoT 裝置 Sdk 的 SDK。 請參閱 [Azure IoT Developer Center] 的逐步指示來連接您的裝置，本教學課程的程式碼，和通常 Azure IoT 中心。 適用於 Java 和 Node 的 Azure IoT 服務 SDK 即將推出。

若要完成本教學課程，您需要下列項目：

+ Microsoft Visual Studio 2015，

+ 使用中的 Azure 帳戶。 <br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用] (http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02 和 amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fiot%2Ftutorials%2Fc2d%2F 目標 ="_blank")。

[AZURE.INCLUDE [iot-hub-c2d-device-csharp](../../includes/iot-hub-c2d-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-c2d-cloud-csharp](../../includes/iot-hub-c2d-cloud-csharp.md)]

## 後續步驟

在本教學課程中，您已了解如何傳送和接收雲端到裝置的訊息。 您可以利用下列教學課程，繼續探索 IoT 中心功能和案例：

- [Process Device-to-Cloud messages], 示範如何可靠地處理遙測和互動式從裝置傳送的訊息。
- [Uploading files from devices], 描述的模式，以便從裝置的檔案上傳，以便使用的定域機組對裝置的訊息。

有關 IoT 中心的其他資訊：

* [IoT 中心概觀]
* [IoT 中心開發人員指南]
* [IoT 中心指引]
* [支援的裝置平台和語言][Supported devices]
* [Azure IoT 開發人員中心]

<!-- Images. -->

<!-- Links -->

[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

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


