<properties
   pageTitle="在 Windows 上使用 C 連接裝置 | Microsoft Azure"
   description="描述如何在 Windows 上使用已寫入 C 的應用程式，將裝置連接至 Azure IoT Suite 預先設定遠端監視方案。"
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

## 在 Windows 上建置並執行範例 C 的解決方案

1. 若要複製 *Microsoft Azure IoT Sdk* GitHub 儲存機制並安裝 *C 的 Microsoft Azure IoT 裝置 SDK* 在 Windows 桌面環境中，請遵循 [設定 Windows 開發環境 ][lnk-setup-windows] 指示。

2. 在 Visual Studio 2015 中，開啟 **remote_monitoring.sln** 解決方案 **c\\serializer\\samples\\remote_monitoring\\windows** 儲存機制的本機複本中的資料夾。

3. 在**方案總管**中，在 **remote_monitoring** 專案中，開啟 **remote_monitoring.c** 檔案。

4. 在檔案中，找到下列程式碼：

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

5. 從遠端監視方案的儀表板將 [裝置識別碼]**** 和 [裝置金鑰]**** 取代為裝置的值。

6. 使用儀表板中的 **IoT 中樞主機名稱**取代 **[IoTHub 名稱]** 和 **[IoTHub 後置詞，也就是 azure-devices.net**]。 例如您的 **IoT 中樞主機名稱** 為 **contoso.azure-devices.net**，請用 **contoso** 取代 [IoT中樞名稱] ****，再用 **azure-devices.net** 取代 [IoT 中樞後置詞，也就是 azure-devices.net]****，如下所示：

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

7. 在**方案總管**中，以滑鼠右鍵按一下 **remote_monitoring** 專案，按一下 [偵錯]****，然後按一下 [開始新執行個體]**** 來建置和執行範例。 當應用程式將範例遙測傳送到 IoT 中樞時，主控台會顯示訊息。

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[lnk-setup-windows]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#windows 

