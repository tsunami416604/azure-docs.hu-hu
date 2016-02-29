<properties 
    pageTitle="在串流分析中設定查詢的警示 | Microsoft Azure" 
    description="了解串流分析警示" 
    keywords="set up alerts"
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


# 設定 Azure 串流分析工作的警示

## 簡介：監視頁面

您可以設定會在計量達到您指定的條件時觸發的警示。

例如，「 是否在最近 15 分鐘的輸出事件 < 100，將電子郵件通知傳送到電子郵件識別碼: xyz@company.com 」。

規則可以透過入口網站中，度量上設定，或者也可以設定 [以程式設計方式](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) 作業記錄檔資料。

## 透過 Azure 入口網站設定警示

在 Azure 管理入口網站中設定警示的方式有兩種：  

1.   **監視** 串流分析工作] 索引標籤  
2.  管理服務中的作業記錄檔  

## 透過入口網站中工作的 [監視] 索引標籤來設定警示

1.  在 [監視] 索引標籤中，選取的度量，然後按一下 **新增規則** 按鈕底部的 [儀表板，並設定您的規則。  

    ![儀表板](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)  

2.  定義此警示的名稱和說明  

    ![建立規則](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)  

3.  輸入臨界值、警示評估視窗與警示的動作  

    ![定義狀況](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)  

## 透過作業記錄檔來設定警示

1.  移至 **警示** ] 索引標籤中的管理服務中 [Azure 入口網站](https://manage.windowsazure.com)。  
2.  按一下 [ **新增規則**  

    ![準則](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)  

3.  定義此警示的名稱和說明。 選取「串流分析」做為服務類型，並選取工作名稱以做為服務名稱。  

    ![定義警示](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)  

## 在 Azure Preview 入口網站中設定警示 ##

在 Azure 預覽入口網站中，瀏覽您感興趣的警示的資料流分析工作並按一下 [ **監視** 一節。  在 **度量** 刀鋒視窗隨即開啟，並按一下 [ **新增警示** 命令。

  ![Azure Preview 入口網站設定](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

您可以為您的警示規則命名，並選擇將會顯示在電子郵件通知的描述。

當您選取 [計量] 時，您將會為計量選擇條件和閾值標準。

  ![Azure Preview 入口網站選取度量](./media/stream-analytics-set-up-alerts/07-stream-analytics-set-up-alerts.png)  

如需在 Azure Preview 入口網站中設定警示的詳細資訊，請參閱 [接收警示通知](./azure-portal/insights-receive-alert-notifications.md)。  

## 取得說明
如需進一步的協助，請嘗試我們 [Azure 串流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## 後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流分析](stream-analytics-get-started.md)
- [調整 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

