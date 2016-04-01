<properties 
    pageTitle="如何在串流分析中啟動串流工作 | Microsoft Azure" 
    description="如何在 Azure 串流分析中執行串流工作 | 學習路徑區段。"
    keywords="streaming jobs"
    documentationCenter=""
    services="stream-analytics"
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

# 如何在 Azure 串流分析中執行串流工作

當已指定工作輸入、查詢及輸出時，即可開始串流分析工作。

開始您的工作：

1.  在 Azure 入口網站中，工作儀表板中，按一下 [ **啟動** 頁面的底部。

    ![開始工作按鈕](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)  

    在 Azure 預覽入口網站中，按一下 [ **啟動** 作業頁面頂端。

    ![Azure Preview 入口網站的開始工作按鈕](./media/stream-analytics-run-a-job/4-stream-analytics-run-a-job.png)  

2.  指定 **開始輸出** 值，以判斷當此工作會開始產生輸出。 針對先前尚未啟動的工作預設值是 **工作開始時間**, ，這表示工作將會立即開始處理資料。 您也可以指定 **自訂** （適用於使用歷程記錄資料） 過去或未來 （延遲處理，直到未來的時間） 時間。 當工作具有先前啟動和停止，此選項時 **上次停止時間** 可用才能從上次輸出時間繼續進行工作並避免資料遺失。  

    ![啟動串流工作的時間](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)  

    ![Azure Preview 入口網站開始串流工作的時間](./media/stream-analytics-run-a-job/5-stream-analytics-run-a-job.png)  

3.  確認選擇。 工作狀態會變更為 *起始* 且會很快就將 *執行* 一旦啟動工作。 您可以監視進度 **啟動** 中的作業 **通知中樞**:

    ![串流工作進度](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)  

    ![Azure Preview 入口網站串流工作進度](./media/stream-analytics-run-a-job/6-stream-analytics-run-a-job.png)  

## 取得說明
如需進一步的協助，請嘗試我們 [Azure 串流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## 後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流分析](stream-analytics-get-started.md)
- [調整 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)


