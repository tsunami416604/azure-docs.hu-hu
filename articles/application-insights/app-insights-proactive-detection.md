<properties 
    pageTitle="Application Insights：主動式偵測" 
    description="Application Insights 會深入分析您的 App 遙測，並且警告您有潛在的問題。" 
    services="application-insights" 
    documentationCenter="windows"
    authors="antonfrMSFT" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/17/2015" 
    ms.author="awills"/>

#  Application Insights：主動式偵測

*Application Insights 目前僅供預覽。*


Application Insights 會深入分析您的 App 遙測，並且可以警告您有潛在的效能問題。 您可能是因為經由電子郵件收到我們的其中一項主動式警示才會閱讀本文。

## 什麼是主動式偵測？

主動式偵測使用機器學習服務和資料採礦演算法來偵測影響應用程式效能的異常模式。 主動式偵測會自動分析 Application Insights 所收集的效能遙測。 它會傳送關於應用程式的任何效能異常的電子郵件。 您不需設定任何臨界值規則。 主動偵測通知整合在一起以便快速分級和診斷問題的應用程式的見解分析功能。 

主動式偵測目前為預覽狀態，並非可供所有 Application Insights 使用者使用。 如果您想要試用這個功能，請連絡 AppInsightsML@microsoft.com，並與您將它設定好，我們會合作。

## 關於主動式警示

* *我為什麼會收到這封電子郵件？*
 * 主動式偵測分析您的應用程式傳送至 Application Insights 的遙測後，偵測到您的應用程式中有效能問題。
* *收到通知代表一定是有問題嗎？*
 * 編號 它是只建議對某些事您可能想要查看更多密切。 
* *我該怎麼辦？*
 * [查看顯示的資料](#responding-to-an-alert)。 使用計量瀏覽器來檢閱一段時間的效能，並向下鑽研其他計量。 使用搜尋來篩選出可協助您識別根本原因的特定事件。 
* *所以你們會看到我的資料嗎？*
 * 編號 服務是完全自動的。 只有您會收到通知。 您的資料是 [私用](app-insights-data-retention-privacy.md)。


## 偵測程序

* *偵測哪些種類的異常？*
 * 由您自行檢查會很耗時的模式。 例如，某種位置、時間與平台組合的效能不佳。
* *你們會分析 Application Insights 收集的所有資料嗎？*
 * 目前尚未。 我們目前會分析要求回應時間和頁面載入時間。 其他計量的分析功能即將推出。 
* *我可以建立自己的異常偵測規則嗎？*
 * 尚未提供。 但是您可以：
 * [設定提醒](app-insights-alerts.md) ，告訴您當公制交叉臨界值。)
 * [匯出 telemetry](app-insights-export-telemetry.md) 至 [資料庫](app-insights-code-sample-export-sql-stream-analytics.md) 或 [到 PowerBI](app-insights-export-power-bi.md) 或 [其他](app-insights-code-sample-export-telemetry-sql-database.md) 工具，其中您可以分析它自己。
* *執行分析的頻率為何？*
 * 我們每天都會根據前一天的遙測執行分析。
* * 會跟著這個取代 [公制警示](app-insights-alerts.md)?
 * 編號  我們不認可偵測每一個您可以考慮異常的行為。

## 如何調查主動式偵測所引發的問題

您可以從電子郵件或從異常清單開啟異常報告。

![](./media/app-insights-proactive-detection/03.png)


* **當** 顯示偵測到問題的時間。
* **什麼** 說明
 * 偵測到的問題；
 * 我們發現的事件集的特性顯示了問題行為。
* 表格會比較效能差的事件集和所有其他事件的平均行為。

按下連結以開啟 [計量瀏覽器]，搜尋相關報告、篩選緩慢執行的事件集的時間和屬性。

修改時間範圍和篩選器可探索遙測。

## 如何改善效能？

您將從自己的經驗得知，對網站使用者而言，回應緩慢和失敗是最大挫折之一。 因此，請務必解決問題。

### 分級

首先，這很重要嗎？ 如果頁面的載入速度一直很慢，但是只有 1% 的網站台使用者必須查看該網頁，您或許有更重要的事項需要考慮。 另一方面，如果只有 1% 的使用者開啟該網頁，但它每次都擲回例外狀況，這可能就是值得調查的問題。

使用電子郵件中的影響敘述作為一般指南，但請留意該敘述並不是全部的詳情。 蒐集其他證據進行確認。

請考慮這個問題的參數。 如果是地理位置相關，設定 [可用性測試](app-insights-monitor-web-app-availability.md) 包括該區域: 只需中可能有網路問題該區域。 

### 診斷頁面載入緩慢 

問題出在哪裡？ 伺服器是否回應太慢、頁面是否很長，或瀏覽器必須執行很多工作才能顯示頁面？

開啟 [瀏覽器] 計量刀鋒視窗。  [區隔的瀏覽器頁面載入時間顯示](app-insights-javascript.md#explore-your-data) 顯示即將時間。 

* 如果 **傳送要求時間** 是高，該伺服器的回應緩慢，或是要求具有大量資料的某篇文章。 查看 [效能度量資訊](app-insights-web-monitor-performance.md#metrics) 來調查回應時間。 
* 設定 [相依性追蹤](app-insights-dependencies.md) 緩慢是否因為外部服務或您的資料庫。
* 如果 **接收回應** ，尤其是，網頁和其相依組件-JavaScript，CSS，等等 (但不是會以非同步方式載入的資料) 的影像很長。 設定 [可用性測試](app-insights-monitor-web-app-availability.md), ，而且一定要設定載入相依組件] 選項。 當您取得一些結果時，請開啟結果的詳細資料並將它展開，以查看不同檔案的載入時間。
* 高 **用戶端處理時間** 建議指令碼執行速度變慢。 如果原因不明顯，請考慮加入一些時間計時程式碼並在 trackMetric 呼叫中傳送時間。

### 改善慢速網頁

Web 上有改善您的伺服器回應和頁面載入時間的完整建議，因此我們不會嘗試這次重複說明。 以下是您可能已知道的一些祕訣，這只是為提醒您：

* 由大型檔案造成的緩慢載入：以非同步方式載入指令碼和其他組件。 使用指令碼統合。 將主頁面分成可個別載入其資料的 Widget。 不要對長資料表傳送純舊式 HTML：使用指令碼要求 JSON 或其他壓縮格式的資料，然後就地填滿資料表。 有一些絕佳的架構可協助進行這一切。 (當然，也必須承擔大型指令碼)。
* 降低伺服器相依性：考慮您的元件的地理位置。 比方說，如果您使用 Azure，請確定 Web 伺服器和資料庫位於相同的區域中。 查詢是否會擷取超過所需的資訊？ 快取或批次處理是否有所幫助？
* 容量問題：查看回應時間和要求計數的伺服器計量。 如果回應時間尖峰與要求計數尖峰不成比例，有可能是您的伺服器已被過度使用。 


## 通知電子郵件

* *我必須訂閱這項服務才能收到通知嗎？*
 * 編號 我們傀儡定期 surveys 中所有的應用程式了解使用者的資料，並傳送通知，如果它偵測到問題。
* *我是否可以取消訂閱或改為傳送通知給我的同事？*
 * 按一下警示或電子郵件中的取消訂閱連結。 
 
    有的人目前正在傳送 [應用程式了解資源的 「 寫入 」 權限](app-insights-resources-roles-access-control.md)。
* *我不想要被這些訊息淹沒。*
 * 它們被限制為一天一則訊息。 您不會重複收到任何訊息。
* *如果我沒有做任何動作，會收到提醒嗎？*
 * 不會，每個問題您只會收到一次訊息。
* *我遺失了電子郵件。 哪裡可以找到通知在入口網站?*
 * 在 [應用程式了解應用程式的概觀，按一下 [ **主動偵測** 並排顯示。 


## 相關文章

* [偵測、分級、診斷](app-insights-detect-triage-diagnose.md)
* [設定計量警示](app-insights-alerts.md)
* [計量瀏覽器](app-insights-metrics-explorer.md)
* [搜尋總管](app-insights-diagnostic-search.md)
 
