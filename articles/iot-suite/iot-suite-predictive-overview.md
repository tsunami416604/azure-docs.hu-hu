<properties
 pageTitle="預先設定的預防性維護解決方案 |Microsoft Azure"
 description="預先設定之 Azure IoT 預防性維護解決方案的說明。"
 services=""
 documentationCenter=""
 authors="stevehob"
 manager="timlt"
 editor=""/>

<tags
 ms.service="na"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="12/01/2015"
 ms.author="stevehob"/>


# 預先設定的預防性維護解決方案概觀

*預測維護* 預先設定的解決方案是其中一個 [預先設定解決方案 ][lnk_preconfigured_solutions] 隨附 [Microsoft Azure IoT 套件 ][lnk_iot_suite]。 此解決方案整合一個建立的預測模型即時裝置遙測收集 [Azure Machine Learning ][lnk_machine_learning]。


有了 Azure IoT 套件，企業可以又快又方便地連接和監視資產，並即時分析資料。 預先設定的預防性維護解決方案會利用該資料及豐富的儀表板與視覺效果，為企業提供新的資訊，以提升其效率及增加收益來源。

## 案例

Fabrikam 是區域性航空公司，而以優惠的價格為客戶提供優良的服務，是該公司努力的目標。 維護問題是造成航班延誤的原因之一，而引擎維護又是其中最為棘手的項目。 因為必須嚴防飛行期間發生引擎故障，所以 Fabrikam 不僅會定期檢查其引擎，而且會恪遵所安排的維護計畫表。 但因為飛機引擎的問題不一， 所以有一些引擎維護工作並非必要。 但嚴重者若在執行維護工作之前發生問題，可能會造成飛機停飛。 此種延誤將會造成嚴重的損失，而飛機所在地點若正好缺少適當的技術人員或備品零件，將更為嚴重。

Fabrikam 飛機的引擎由各種感應器進行檢測，而這些感應器會監視飛行期間的引擎狀況。 Fabrikam 使用 Azure IoT 套件收集飛行期間所收集的感應器資料。 經過多年累積引擎運作資料與失敗資料之後，Fabrikam 的資料科學家製做出了一個模型，可以預測飛機引擎的剩餘使用壽命 (RUL)。 他們從四個引擎感應器的資料中，找出了資料之間的相互關聯性，而其中一個引擎更潛藏了最終會導致引擎故障的問題。 Fabrikam 現在除了繼續執行定期檢查來確保安全之外，還會在每次飛行後，將飛行期間從引擎收集而來的遙測資料，套用到這些模型，以計算每具引擎的 RUL。 Fabrikam 現在已可以預測未來的失敗點，並預先規劃維護及維修計劃。

因為可以預測必要維護時機，讓 Fabrikam 可以最佳化各項作業，進而降低成本。 維護專員與排班專員一起合作，依據飛機在特定停駐點停機的時間，規劃出維護的時間，以確保飛機能有足夠的停飛時間，不會造成排程中斷。 Fabrikam 可以據此安排技術人員，讓飛機無須浪費時間等待，就可獲得有效率的維修。 庫存控制管理員因為會收到維護計畫，所以可以據此最佳化其訂單程序與備用零件庫存。 這一切不僅讓 Fabrikam 可以將飛機停飛的時間降至最低，也降低了整體營運成本，同時確保了乘客與機組員的安全。

## 預防性維護解決方案的建置方式

若要了解如何 [Azure IoT 套件 ][lnk_iot_suite] 提供功能的客戶需要瞭解到預測維護，請檢閱這 [資訊圖 ][lnk_infographic]。

為示範這些功能如何運用透過 IoT 套件服務收集而來的裝置遙測資料，以及如何透過 Web 應用程式將這些資料呈現給使用者，此解決方案會利用現有的 Azure 機器學習範本。 Microsoft 已建置 [迴歸模型 ][lnk_regression_model] 並發佈完整的範本資料<sup>\[1\]</sup>, ，和逐步指引。

Azure IoT 預先設定的預防性維護解決方案會利用此範本所建立的迴歸模型 (此模型會部署到您的 Azure 訂用帳戶，並透過發行的 API 加以公開)。 此解決方案包含了代表 4 具 (全部共 100 具) 引擎之測試資料與 4 個 (全部共 21 個) 感應器之資料流的交集，並由定型的模型提供精確的結果。

*\[1\] A.Saxena< 和 K Goebel (2008)。 「 Turbofan 引擎降低模擬資料集 」、 NASA 名稱 Prognostics 資料儲存機制 (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/)、 NASA 名稱參考資料中心、 Moffett 欄位，CA*

## 後續步驟

若要深入了解 Azure IoT 可預測性維護案例的方式，閱讀 [物聯網 ][lnk_capture_value]。

採取 [逐步解說 ][lnk-predictive-walkthrough] 預測維護的預先設定的解決方案。


[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md 
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md 
[lnk_iot_suite]: iot-suite-overview.md 
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/ 
[lnk_infographic]: https://www.microsoft.com/en-us/server-cloud/predictivemaintenance/Index.html 
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3 
[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF 

