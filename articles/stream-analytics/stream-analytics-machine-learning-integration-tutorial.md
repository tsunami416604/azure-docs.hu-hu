<properties 
    pageTitle="教學課程：Azure 串流分析和 Azure Machine Learning 整合 | Microsoft Azure" 
    description="如何在串流分析作業中利用 UDF 和機器學習服務"
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72" 
    manager="paulettm" 
    editor="cgronlun"
/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="12/14/2015" 
    ms.author="jeffstok"
/> 

# 教學課程：串流分析和機器學習服務整合簡介 #

本教學課程旨在協助您透過機器學習服務整合，快速設定簡單的串流分析作業。 我們將利用 Cortana 分析資源庫的情緒分析機器學習服務模型，來分析串流文字資料並即時判斷情緒分數。 本教學課程很適合用來了解下列各種案例，例如對串流 twitter 資料進行即時情緒分析、由支援人員分析客戶聊天記錄、論壇/部落格/影片上的評論，以及其他許多即時預測評分案例。
  
本教學課程提供附有文字的範例 CSV 檔 (如下圖 1 所示) 做為 Azure Blob 存放區的輸入。 作業時會將情緒分析模型做為使用者定義的函式 (UDF) 套用到 Blob 存放區中的範例文字資料。 最終結果則會放置在相同 Azure Blob 存放區中的另一個 CSV 檔中。 下圖 2 提供此設定的圖表。 如需更真實的案例，可將此 Blob 存放區的輸入換成 Azure 事件中樞輸入內的串流 twitter 資料。 此外 [Power BI](https://powerbi.microsoft.com/) 無法建立即時彙總的情感視覺效果。 本文日後重新編寫時將會包含這類擴充功能。

圖 1：  

![串流分析機器學習服務教學課程圖 1](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

圖 2：    

![串流分析機器學習服務教學課程圖 2](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

## 必要條件

本文的先決條件如下：

1.  有效的 Azure 訂用帳戶
2.  內附資料的 CSV 檔。 [圖 2 中的一項提供 [github](https://github.com/jeffstokes72/azure-stream-analytics-repository/blob/master/sampleinputs.csv) 下載，或您可能會建立您自己。 本教學課程在編寫時是假設您會使用可供下載的 CSV 檔。

概括而言，我們將會執行下列步驟：

1.  將 CSV 輸入檔案上傳至 Blob 儲存體
2.  將 Cortana 分析資源庫的情緒分析模型加入到機器學習服務工作區
3.  將此模型部署為 Azure Machine Learning 工作區中的 Web 服務
4.  建立串流分析作業，以函式形式呼叫此 Web 服務來判斷所輸入文字的情緒。
5.  啟動串流分析作業並觀察輸出 


## 將 CSV 輸入檔上傳至 Blob 儲存體

在此步驟中，您可以使用包括簡介中所指定的檔案在內的任何 CSV 檔。 若要上傳檔案， [Azure 儲存體總管](http://storageexplorer.com/) 或者可能使用 Visual Studio，以及自訂程式碼。 本教學課程所提供的是適用於 Visual Studio 的範例。

1.  展開 [Azure]，並以滑鼠右鍵按一下 **儲存體**。 選擇 **附加外部儲存體** ，並提供 **帳戶名稱** 和 **帳戶金鑰**。  

    ![串流分析機器學習服務教學課程伺服器總管](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)  

2.  展開剛連接的儲存體，並選擇 **Create Blob Container** ，並提供邏輯名稱。 對建立好的容器按兩下以檢視其內容 (此時會是空的)。  

    ![串流分析機器學習服務教學課程建立 Blob](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)  

3.  按一下 [上傳 CSV 檔案 **上傳 Blob** 圖示，然後選擇 [ **檔案從本機磁碟**。  

## 新增 Cortana 分析資源庫中的情緒分析模型

1.  下載 [預測情感分析模型](https://gallery.cortanaanalytics.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) Cortana 分析圖庫中。  
2.  按一下 [ **開啟** Studio 中 ︰  

    ![串流分析機器學習服務教學課程開啟機器學習服務 Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3.  登入以前往工作區。 選擇最適合您所在地的位置。
4.  現在按一下 **執行** Studio 底部  
5.  一旦順利執行，按一下 **部署 Web 服務**。
6.  現在情緒分析模型已可供使用。 若要驗證，請按一下 [ **測試** ] 按鈕，並提供文字輸入，例如"I love Microsoft"和測試會傳回類似的結果，如下所示 ︰

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`  

![串流分析機器學習服務教學課程分析資料](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)  

按一下 [ **Excel 2010 或更早版本** 活頁簿連結以取得 API 金鑰和 URL，您稍後設定資料流分析工作。 (如果要利用另一個 Azure 帳戶之工作區中的機器學習服務模型，才必須執行此步驟。 本教學課程假設這正是我們為了解決此案例而必須執行的動作)。  

![串流分析機器學習服務教學課程分析實驗](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-experiement.png)  

記下所下載 Excel 檔中的 Web 服務 URL 和存取金鑰，如下所示：  

![串流分析機器學習服務教學課程快速概覽](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## 建立使用機器學習服務模型的串流分析作業

1.  瀏覽至 [Azure 管理入口網站](https://manage.windowsazure.com)。  
2.  按一下 [ **新**, ，**Data Services**, ，**串流分析** 和 **快速建立**。 提供 **工作名稱**, 適當 **區域** 工作，然後選擇 **區域監視儲存體帳戶**。    
3.  一旦建立工作時，瀏覽至 **輸入** ] 索引標籤上，按一下 [ **加入輸入**。  

    ![串流分析機器學習服務教學課程資料新增機器學習服務輸入](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)  

4.  第一頁上 **加入輸入** 精靈] 中選取 **資料流** 並按下一步。 在第二個頁面上選取 **Blob 儲存體** 作為輸入，然後按一下 **下一步**。  
5.  在 **Blob 儲存體設定** 精靈頁面會提供儲存體帳戶 blob 容器時的資料已上傳先前定義的名稱。 按一下 [ **下一步**。 選擇 **CSV** 為 **事件序列化格式**。 接受其餘的預設值 **序列化設定**。 按一下 [ **確定**。  
6.  瀏覽至 **輸出** ] 索引標籤上，按一下 [ **新增輸出**。  

    ![串流分析機器學習服務教學課程新增輸出](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)  

7.  選擇 **Blob 儲存體** ，並提供相同的參數，除了容器。  **輸入** 已設定為從其中名為"test"的容器讀取 **CSV** 上傳檔案。 如 **輸出**, ，將 「 testoutput 」。 容器名稱必須不同，並請確認此容器存在。     
8.  按一下 [ **下一步** 設定輸出的 **序列化設定**。 為輸入，以選擇 **CSV** 按一下 **確定** ] 按鈕。
9.  瀏覽至 **函式** ] 索引標籤上，按一下 [ **加入機器學習函式**。  

    ![串流分析機器學習服務教學課程新增機器學習服務函式](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)  

10. 在 **機器學習 Web 服務設定** 頁面上，尋找 Machine Learning 工作區、 web 服務和預設端點。 在本教學課程中，如果您知道 URL 而且擁有金鑰，請手動套用設定，以便熟悉工作區 Web 服務的設定方式。 提供端點 **URL** 和 **API 金鑰**。 然後按一下 [ **確定**。    

    ![串流分析機器學習服務教學課程機器學習服務 Web 服務](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)    

11. 瀏覽至 **查詢** ] 索引標籤上，修改查詢，如下所示 ︰  

```
    WITH subquery AS (  
        SELECT text, sentiment(text) as result from input  
    )  
      
    Select text, result.[Score]  
    Into output  
    From subquery  
```

12. 按一下 [ **儲存** 儲存查詢。    

## 啟動串流分析作業並觀察輸出

1.  按一下 [ **啟動** 底端的工作。 
2.  在 **開始查詢] 對話方塊**, ，選擇 [ **自訂時間** ，然後選取 [當 CSV 已上傳至 Blob 儲存體之前的時間。 按一下 [ **確定**。  

    ![串流分析機器學習服務教學課程自訂時間](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)  

3.  使用上傳 CSV 檔時所使用的工具瀏覽至 Blob 儲存體。 本教學課程使用 Visual Studio。
4.  在作業啟動後經過幾分鐘的時間，就會建立輸出容器並於其中上傳 CSV 檔。  
5.  按兩下該檔案將會開啟預設的 CSV 編輯器，並且應該會顯示如下內容：  

    ![串流分析機器學習服務教學課程 csv 檢視](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

## 結論

本教學課程建立了串流分析作業，此作業會讀取串流文字資料，並對其套用即時情緒分析。 您不必擔心麻煩的情緒分析模型建立工作，就能完成全部的工作。 這是 Cortana 分析套件的其中一項優點。

您也能觀察 Azure 機器語言函式的相關度量。  按一下 [ **監視** ] 索引標籤。 此時會出現三個函式的相關度量。  
  
- 「函式要求」指出機器學習服務 Web 服務的要求數目。  
- 「函式事件」指出要求中的事件數目：依預設，每個 ML Web 服務的要求可包含多達 1000 個事件。  
- 「失敗的函式要求」指出機器學習服務 Web 服務的失敗要求數目。  

    ![串流分析機器學習服務教學課程 ml 監視檢視](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)  


