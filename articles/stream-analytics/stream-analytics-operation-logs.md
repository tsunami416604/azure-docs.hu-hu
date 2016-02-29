<properties 
    pageTitle="利用資料流分析中的作業和服務記錄檔 | Microsoft Azure" 
    description="如何使用串流分析作業記錄檔" 
    keywords="service logs"
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

# 了解串流分析服務及作業記錄檔

所有 Azure 服務都會將作業記錄訊息提供給使用者，以記錄與管理作業有關的詳細資料。 在 Azure 串流分析中，這項資訊可用於偵錯用途，例如檢視工作狀態、工作進度與失敗訊息，以從一開始處理到輸出都可隨著時間追蹤作業進度。

## 在 Azure 管理入口網站中尋找作業記錄檔

作業記錄檔可用兩種方式來存取：  

- 串流分析工作的儀表板  
- Azure 入口網站中的管理服務  

## 串流分析工作的儀表板

串流分析工作相對應的記錄檔連結會顯示在該工作的 [儀表板] 索引標籤上。 按一下該連結時，其會以能夠顯示該特定工作最新記錄檔的方式來設定篩選器。

  ![選取管理服務記錄檔](./media/stream-analytics-operation-logs/01-stream-analytics-operation-logs.png)  

## 管理服務

若要以手動方式瀏覽至串流分析的作業記錄檔以及 Azure 入口網站中的其他服務：

1.  按一下 [ **管理服務** 中 [Azure 入口網站](https://manage.windowsazure.com)。
2.  選取 **資料流分析** 的 **類型** 和作業的名稱 **服務名稱**。  

  ![選取串流分析](./media/stream-analytics-operation-logs/02-stream-analytics-operation-logs.png)  

## 在 Azure Preview 入口網站中尋找稽核記錄檔 ##

若要尋找您在 Azure 預覽入口網站中的資料流分析工作的操作記錄檔，請按一下 [ **瀏覽** ，然後選取 **稽核記錄檔**。

  ![Azure Preview 入口網站選取串流分析](./media/stream-analytics-operation-logs/06-stream-analytics-operation-logs.png)  

這會開啟刀鋒視窗，顯示訂用帳戶所有資源在過去 7 天中的事件。  您可以查看指定型別或時間範圍的事件，即可篩選 **篩選** 命令。

  ![Azure Preview 入口網站選取串流分析](./media/stream-analytics-operation-logs/07-stream-analytics-operation-logs.png)  

## 取得記錄檔詳細資料

您可以依照時間範圍和狀態來篩選，以檢視工作的記錄檔。

在 Azure 管理入口網站中，按一下 **詳細資料** 來檢視有關所選事件的其他詳細資料視窗底部的按鈕。 

  ![選取詳細資料](./media/stream-analytics-operation-logs/03-stream-analytics-operation-logs.png)  

在 Azure Preview 入口網站中，按一下記錄項目即可查看其中的詳細事件。

  ![Azure Preview 入口網站選取詳細資料](./media/stream-analytics-operation-logs/08-stream-analytics-operation-logs.png)  

在這裡，您可以開啟 **詳細** 刀鋒視窗上的事件即可。

  ![Azure Preview 入口網站選取詳細資料](./media/stream-analytics-operation-logs/09-stream-analytics-operation-logs.png)  

## 偵錯失敗的工作

在 Azure 管理入口網站中，按一下搜尋圖示並鍵入 ‘failed’。 這樣可以找出所有包含失敗項目的記錄檔。 

  ![針對失敗的工作進行偵錯](./media/stream-analytics-operation-logs/04-stream-analytics-operation-logs.png)  

在 Azure 預覽入口網站中，您可以篩選檢視的訊息層級 **重大** 事件。

  ![Azure Preview 入口網站偵錯](./media/stream-analytics-operation-logs/10-stream-analytics-operation-logs.png)  

您可以選取其中一個失敗，然後按一下 [ **詳細資料** 如需有關此錯誤。  某些錯誤訊息也會提供降低此問題之風險的方法。 

  ![Operation Details](./media/stream-analytics-operation-logs/05-stream-analytics-operation-logs.png)  

如果您需要連絡 [支援](http://azure.microsoft.com/support/options/) 或提供資訊給透過團隊 [MSDN 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics), ，請特別注意作業詳細資料， **相互關聯識別碼**。 

## 取得說明
如需進一步的協助，請嘗試我們 [Azure 串流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## 後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流分析](stream-analytics-get-started.md)
- [調整 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

