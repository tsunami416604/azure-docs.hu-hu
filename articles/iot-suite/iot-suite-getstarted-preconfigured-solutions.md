<properties
    pageTitle="開始使用預先設定解決方案 | Microsoft Azure"
    description="遵循此教學課程學習如何部署 Azure IoT Suite 預先設定解決方案。"
    services=""
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="12/03/2015"
     ms.author="dobett"/>

# 教學課程：IoT 預先設定解決方案入門

## 簡介

Azure IoT 套件 [預先設定的解決方案][lnk-preconfigured-solutions] 結合多個 Azure IoT 服務來實作常見的 IoT 商務案例的端對端解決方案。

本教學課程會示範如何佈建 *遠端監視* 預先設定的解決方案。 其中也逐步說遠端監視預先設定的解決方案的基本功能。

若要完成本教學課程，您需要作用中的 Azure 訂用帳戶。

> [AZURE.NOTE]  如果您沒有帳戶，您可以建立免費試用帳戶，只需要幾分鐘的時間。 如需詳細資料，請參閱 [Azure 免費試用][lnk_free_trial]。

## 佈建遠端監視預先設定的解決方案

1.  登入 [azureiotsuite.com][lnk-azureiotsuite] 使用您的 Azure 帳戶認證，並按一下 [ **+** 來建立新的方案。

2.  按一下 [ **選取** 上 **遠端監視** 並排顯示。

3.  輸入 **方案名稱** 的遠端監視預先設定的解決方案。

4.  選取 **區域** 和 **訂閱** 您想要用來佈建方案。

5.  按一下 [ **建立方案** 開始佈建程序。 這通常需要數分鐘的執行時間。

## 檢視遠端監視解決方案儀表板

1.  當佈建已完成，並指出您預先設定的解決方案磚 **準備**, ，按一下 [ **啟動** 開啟新索引標籤中的遠端監視解決方案入口網站。

    ![][img-launch-solution]

2.  根據預設，方案入口網站會顯示 *方案儀表板*。 您可以使用左側功能表選取其他檢視。

    ![][img-dashboard]

## 檢視解決方案裝置清單

1.  按一下 [ **裝置** 左側功能表中要顯示 *裝置清單* 針對此解決方案。

    ![][img-devicelist]

2.  您可以看到佈建程序所建立的四個模擬裝置。

3.  按一下裝置清單中的裝置，以檢視裝置詳細資料。

    ![][img-devicedetails]

## 傳送命令至裝置

1.  按一下 [ **命令** 所選裝置的裝置詳細資料窗格中。

    ![][img-devicecommands]

2.  選取 **PingDevice** 命令清單。

3.  按一下 [ **傳送命令**。

4.  您可以在命令歷程記錄中看見命令的狀態。

    ![][img-pingcommand]

## 新增模擬裝置

1.  瀏覽回到裝置清單。

2.  按一下 [ **+ 新增裝置** 左上角來新增新的裝置中。

    ![][img-adddevice]

3.  按一下 [ **新增** 上 **模擬裝置** 並排顯示。

    ![][img-addnew]

4.  選取 **讓我定義自己的裝置識別碼**, ，輸入唯一的裝置識別碼名稱，例如 **mydevice_01**。

5.  按一下 [ **建立**。

    ![][img-definedevice]

6. 在步驟 3 的 **新增模擬的裝置** 按一下 **完成** 以返回 [裝置] 清單。

7.  您可以檢視您的裝置 **執行** 在裝置清單中。

    ![][img-runningnew]

## 檢視和編輯解決方案規則

1.  返回方案儀表板和檢視 **警示歷程記錄** 並排顯示。

    ![][img-alarmhistory]

2.  此規則 **AlarmTemp** 觸發這些警示。

3.  按一下 [ **規則** 在左側功能表中檢視此解決方案的規則。

    ![][img-rules]

4.  預先設定的解決方案會佈建兩個規則。

5.  按一下 [ **溫度** 規則清單，以檢視規則內容中的規則。

6.  按一下 [ **編輯** 規則屬性] 窗格中。

    ![][img-displayrule]

7.  變更 **閾值** 為 30，並保留所有其他屬性相同。

8.  按一下 [ **儲存及檢視規則**。

    ![][img-editrule]

9.  返回 **警示歷程記錄** 資料表中 **方案儀表板** 並觀察此行為所產生的更新規則變更。

    ![][img-newhistory]

## 後續步驟

您已經建置一個可運作的預先設定解決方案，接下來可以進行下列案例：

-   [自訂預先設定解決方案指南][lnk-customize]
-   [預先設定的預防性維護解決方案概觀][lnk-predictive]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-alarmhistory]: media/iot-suite-getstarted-preconfigured-solutions/alarmhistory.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-displayrule]: media/iot-suite-getstarted-preconfigured-solutions/displayrule.png
[img-editrule]: media/iot-suite-getstarted-preconfigured-solutions/editrule.png
[img-newhistory]: media/iot-suite-getstarted-preconfigured-solutions/newhistory.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-predictive]: iot-suite-predictive-overview.md


