<properties
   pageTitle="在 Linux 上使用 C 連接裝置| Microsoft Azure"
   description="描述如何在 Linux 上使用已寫入 C 的應用程式，將裝置連接至 Azure IoT Suite 預先設定遠端監視方案。"
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

## 在 Linux 上建置並執行範例 C 的解決方案

1. 若要複製 *Microsoft Azure IoT Sdk* GitHub 儲存機制並安裝 *C 的 Microsoft Azure IoT 裝置 SDK* 在 Linux 桌面環境中，請遵循 [設定 Linux 開發環境][lnk-setup-linux] 指示。

2. 開啟檔案 **c/serializer/samples/remote_monitoring/remote_monitoring.c** 在文字編輯器中。

3. 在檔案中，找到下列程式碼：

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

4. 取代 **[裝置識別碼]** 和 **[裝置 Key]** 以您的裝置遠端監視方案儀表板中的值。

5. 使用 **IoT 中心 Hostname** 儀表板中取代 **IoTHub 姓名** 和 **[IoTHub 後置字元，也就是 azure devices.net]**。 例如，如果您 **IoT 中心主機名稱** 是 **contoso.azure devices.net**, ，取代 **IoTHub 姓名** 與 **contoso** 並取代 **[IoTHub 後置字元，也就是 azure devices.net]** 與 **azure devices.net** ，如下所示:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

6. 儲存您的變更並建置範例。 您可以建立您的範例執行 build.sh 指令碼 **c/build_all linux** 目錄。 組建指令碼會建立 **cmake** 資料夾來儲存已編譯的範例程式。

7. 執行 **cmake/序列化程式/範例/remote_monitoring/remote_monitoring** 範例應用程式。

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-setup-linux]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#linux


