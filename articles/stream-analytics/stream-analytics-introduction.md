<properties 
    pageTitle="串流分析簡介 | Microsoft Azure" 
    description="了解串流分析，這是可協助您即時分析物聯網 (IoT) 資料流的受管理服務。" 
    keywords="analytics as a service, managed services, stream processing, streaming analytics, what is stream analytics"
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="paulettm" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="12/04/2015" 
    ms.author="jeffstok"/>


# 什麼是串流分析？

Azure 串流分析 (ASA) 是完全受管理、符合成本效益的即時事件處理引擎，可解除鎖定資料的深入見解。 串流分析可讓您輕易地對串流自裝置、感應器、網站、社交媒體、應用程式、基礎結構系統和其他項目的資料設定即時分析計算。

只要在 Azure 入口網站中按幾下，您就可以撰寫串流分析作業，指定串流資料的輸入來源、工作結果的輸出接收，以及利用類似 SQL 的語言表示的資料轉換。 您可以在 Azure 入口網站中監視和調整工作的規模/速度，可從數 KB 調整到一 GB 或每秒處理更多事件。

串流分析運用多年的 Microsoft Research 工作成果為時效性處理作業開發出高度量身訂做的串流引擎，以及這類作業的直覺式規格語言整合功能。

## 串流分析的用途為何？
現今，大量的資料都需要透過網路高速流動。 組織若能即時處理串流資料並採取相關動作，將可大幅改善效率，並在市場上脫穎而出。 各種產業都有應用即時串流分析的狀況：金融服務公司所提供的個人化、即時股票交易分析和警示、即時詐騙偵測、資料及身分識別保護服務、可靠地擷取和分析內嵌在實體物件 (物聯網，又稱 IoT) 中的感應器和傳動器所產生的資料、網頁點選流分析，以及客戶經驗若在某個時間範圍內降低時會發出警示的客戶關係管理 (CRM) 應用程式。 企業正尋求以最具彈性、可靠且符合成本效益的方式執行這類即時事件資料流資料分析，以在高度競爭的現代商務世界中獲致成功。

## 主要功能和優點
-   **方便使用:** 資料流分析支援來說明轉換的簡單、 宣告式查詢模型。 為了最佳化以方便使用，串流分析可使用 SQL 變數，並且為客戶省去處理串流處理系統之技術複雜性的需求。 使用 [資料流分析查詢語言](https://msdn.microsoft.com/library/azure/dn834998.aspx) 在瀏覽器中查詢編輯器中，您更 intelli-了解可協助您快速而輕鬆地實作時間序列的查詢，包括時間為基礎的聯結、 視窗型彙總、 暫時性篩選器，以及其他常見的作業，例如聯結、 彙總、 投影和篩選器自動完成。 此外，對範例資料檔案進行瀏覽器中查詢測試，也能加快反覆開發作業的速度。  

-   **延展性:** 資料流分析是能夠處理高達 1 GB/秒的高事件輸送量。 與整合 [Azure 事件中樞](http://azure.microsoft.com/services/event-hubs/) ，可讓解決方案擷取數百萬來自連線裝置，點選流，每個事件和記錄檔，等等。 為達到此目的，串流分析運用事件中樞資料分割功能，每個資料分割可產生 1 MB/秒。 使用者可將計算分割成查詢定義內的數個邏輯步驟，且每個步驟都能夠進一步分割以增加研展性。  

-   **可靠性、 可重複性及快速復原:** 受管理的服務在雲端中，資料流分析可協助防止資料遺失，並提供業務持續性失敗時透過內建修復功能。 此服務具備內部維護狀態的能力，因此會提供可重複的結果，確保能封存事件並在日後重新套用處理，且一律取得相同的結果。 這項功能可讓客戶及時回頭檢視，並在執行根本原因分析和假設狀況分析等案例時調查計算方式。  

-   **低成本:** 為雲端服務，串流分析已最佳化，可讓使用者更低廉的成本取得並維護即時分析解決方案。 該服務的建置可讓您根據串流單位使用量和系統處理的資料量執行隨用隨付。 使用量衍生自所處理的事件量，以及佈建在叢集內，用來處理個別串流分析工作的計算能力。  

-   **參考資料:** 資料流分析可讓使用者指定及使用參考資料。 這可能是歷史資料，或只是變動頻率較低的非串流資料。 系統可以簡化參考資料的使用，使資料被視為任何其他要與即時擷取的其他事件資料流聯結以執行轉換的傳入事件資料流。  

-   **連線能力:** 資料流分析會直接連線至 Azure 事件中樞資料流擷取和 Azure Blob 服務來擷取資料歷程記錄。 結果可以寫入從資料流分析，對 Azure 儲存體 Blob 或資料表、 Azure SQL DB、 事件中樞、 Azure 服務匯流排主題或佇列，並進一步處理工作流程，用於透過批次分析 Power BI，其中它然後才能視覺化基準， [Azure HDInsight](http://azure.microsoft.com/services/hdinsight/) 或處理一次為一系列的事件。 使用事件中樞時，您可以將多個串流分析與其他資料來源和處理引擎組合在一起，而不會失去計算的串流本質。  

## 取得說明
如需進一步的協助，請嘗試我們 [Azure 串流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## 後續步驟
以上就是串流分析 (物聯網資料串流分析專用的受管理服務) 的簡介。 若要深入了解此服務，請參閱：

- [開始使用 Azure 資料流分析](stream-analytics-get-started.md)
- [調整 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)


