<properties
    pageTitle="開始使用 Azure IoT 中樞 | Microsoft Azure"
    description="請遵循此教學課程以開始搭配 C# 使用 Azure IoT 中心。"
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="12/14/2015"
     ms.author="dobett"/>

# 教學課程：開始使用 Azure IoT 中樞

## 簡介

Azure IoT 中樞是一項完全受管理的服務，可在數百萬個 IoT 裝置和一個解決方案後端之間啟用可靠且安全的雙向通訊。 IoT 專案所面臨的其中一個最大挑戰，就是如何可靠且安全地將裝置連線至解決方案後端。 若要解決這個問題，IoT 中樞：

- 提供可靠的裝置到雲端和雲端到裝置的超大規模傳訊。
- 使用每一裝置的安全性認證和存取控制來啟用安全通訊。
- 包括適用於最受歡迎的語言和平台的裝置程式庫。

本教學課程說明如何：

- 使用 Azure 入口網站來建立 IoT 中樞。
- 在您的 IoT 中樞中建立裝置身分識別。
- 建立模擬裝置，該裝置會傳送遙測到您的雲端後端，並從您的雲端後端接收命令。

在本教學課程結尾處，您將會有三個 Windows 主控台應用程式：

* **CreateDeviceIdentity**, ，它會建立一個裝置身分識別及連接您的模擬的裝置的相關的安全性金鑰。
* **ReadDeviceToCloudMessages**, ，其中顯示模擬的裝置所傳送的遙測。
* **SimulatedDevice**, ，連接到您的 IoT 中心稍早建立的裝置識別身分以及每秒傳送的遙測訊息。

> [AZURE.NOTE] 發行項 [IoT 中心 Sdk][lnk-hub-sdks] 提供各種 Sdk 可讓您建置裝置和您方案的後端上執行兩個應用程式的相關資訊。

若要完成此教學課程，您需要下列項目：

+ Microsoft Visual Studio 2015。

+ 使用中的 Azure 帳戶。 <br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用][lnk-free-trial]。

## 建立 IoT 中樞

您需要為您的模擬裝置建立 IoT 中樞以供連接。 下列步驟顯示如何使用 Azure 入口網站完成這項工作。

1. 登入 [Azure 入口網站][lnk-portal]。

2. 在 Jumpbar 中按一下 [ **新增**, ，然後按一下 [ **物聯網**, ，然後按一下 [ **Azure IoT 中心**。

    ![][1]

3. 在 **IoT 中心** 刀鋒視窗中，選擇 [IoT 中心的設定。

    ![][2]

    * 在 **名稱** 方塊中，輸入您的 IoT 中心的名稱。 如果 **名稱** 有效而且可用，綠色的核取記號會出現在 **名稱** 方塊。
    * 選取 **定價和調整層**。 本教學課程不需要特定層。
    * 在 **資源群組**, 、 建立新的資源群組，或選取現有的 gpo。 如需詳細資訊，請參閱 [使用資源群組來管理您的 Azure 資源][lnk-resource-groups]。
    * 在 **位置**, ，選取要裝載您的 IoT 中心位置。  

4. 當您選擇 IoT 中心設定選項時，按一下 [ **建立**。  Azure 建立您的 IoT 中樞可能需要幾分鐘的時間。 若要檢查狀態，您可以在「開始面板」或 [通知] 面板中監視進度。

    ![][3]

5. 已成功建立 IoT 中心，當開啟新的 IoT 中心分頁，並記下 **Hostname**, ，然後按一下 [ **金鑰** 圖示。

    ![][4]

6. 按一下 [ **iothubowner** 原則，然後複製並記下連接字串中的 **iothubowner** 刀鋒視窗。

    ![][5]

您現在已經建立 IoT 中樞，因此您已具有完成本教學課程的其餘部分所需的主機名稱和連接字串。

[AZURE.INCLUDE [iot-hub-get-started-cloud-csharp](../../includes/iot-hub-get-started-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-csharp](../../includes/iot-hub-get-started-device-csharp.md)]

## 執行應用程式

現在您已經準備好執行應用程式。

1.  在 Visual Studio 中，在 [方案總管中以滑鼠右鍵按一下您的方案，然後按一下 [ **設定啟始專案**。 選取 **多個啟始專案**, ，然後選取 **啟動** 為 **動作** 兩者 **ReadDeviceToCloudMessages** 和 **SimulatedDevice** 專案。

    ![][41]

2.  按下 **F5** 啟動這兩個執行的應用程式。 從主控台輸出 **SimulatedDevice** 應用程式顯示模擬的裝置會傳送給您的 IoT 中心和主控台輸出中的訊息 **ReadDeviceToCloudMessages** 應用程式會顯示您的 IoT 中心接收的訊息。

    ![][42]

## 後續步驟

在本教學課程中，您在入口網站中設定了新的 IoT 中樞，然後在中樞的身分識別登錄中建立了裝置身分識別。 您會將此裝置身分識別用於模擬的裝置，該裝置會將裝置對雲端訊息傳送至中樞，並建立另一個應用程式，以顯示中樞所接收的訊息。 您可以利用下列教學課程繼續探索 IoT 中樞功能和其他 IoT 案例：

- [定域機組對裝置以傳送訊息的 IoT 中心][任務任務連結 c2d-教學課程] 顯示如何將訊息傳送到裝置，並處理所產生的 IoT 中心傳送意見反應。
- [處理序裝置對雲端訊息][任務任務連結的程序-d2c-教學課程] 示範如何可靠地處理遙測和互動式從裝置傳送的訊息。
- [從裝置的檔案上傳][任務任務連結上傳-教學課程] 中描述的模式，以便從裝置的檔案上傳，以便使用的定域機組對裝置的訊息。

您可以在下列文章中進一步了解 IoT 中樞：

* [IoT 中樞概觀][lnk-hub-overview]
* [IoT 中樞開發人員指南][lnk-hub-dev-guide]
* [設計您的 IoT 中樞解決方案][lnk-hub-guidance]
* [支援的裝置平台和語言][lnk-supported-devices]
* [Azure IoT 開發人員中心][lnk-dev-center]

<!-- Images. -->
[1]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub1.png
[2]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub2.png
[3]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub3.png
[4]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub4.png
[5]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub5.png
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png

<!-- Links -->
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-overview]: iot-hub-what-is-iot-hub.md
[lnk-hub-guidance]: iot-hub-guidance.md
[lnk-hub-dev-guide]: iot-hub-devguide.md
[lnk-supported-devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[lnk-dev-center]: http://www.azure.com/develop/iot
[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-resource-groups]: resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
