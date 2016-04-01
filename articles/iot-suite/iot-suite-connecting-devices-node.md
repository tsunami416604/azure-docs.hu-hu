<properties
   pageTitle="使用 Node.js 連接裝置 |Microsoft Azure"
   description="描述如何使用 Node.js 中已寫入的應用程式，將裝置連接至 Azure IoT Suite 預先設定遠端監視方案。"
   services=""
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/10/2015"
   ms.author="dobett"/>


# 將裝置連接至 IoT 套件遠端監視預先設定方案

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## 建置並執行 node.js 範例解決方案

1. 若要複製 *Microsoft Azure IoT Sdk* GitHub 儲存機制並安裝 *Node.js 的 Microsoft Azure IoT 裝置 SDK* 在桌面環境中，請遵循 [準備開發環境][lnk-github-prepare] 指示。

2. 從您的本機複本 [azure iot sdk][lnk-github-repo] 儲存機制，並複製下列兩個節點/裝置範例資料夾檔案資料夾在裝置上 ︰

  - packages.json
  - remote_monitoring.js

3. 開啟 remote_monitoring.js 檔案並尋找下列變數：

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

4. 取代 **[IoT 中心裝置連接字串]** 與您裝置的連接字串。 您可以在遠端監視方案的儀表板中找到 IoT 中樞主機名稱、裝置識別碼和裝置金鑰。 裝置連接字串使用的格式如下：

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

5. 如果您的 IoT 中心主機名稱是 **contoso** 和您的裝置識別碼是 **mydevice**, ，連接字串看起來像這樣 ︰

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

6. 儲存檔案。 在包含這些檔案的資料夾中執行下列命令：

    ```
    npm install
    node remote_monitoring.js
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdks
[lnk-node-installers]: https://nodejs.org/download/
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/doc/devbox_setup.md

