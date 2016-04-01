<properties 
    pageTitle="檢視事件和稽核記錄檔" 
    description="了解如何查看發生在 Azure 訂用帳戶中的所有事件。" 
    authors="HaniKN-MSFT" 
    manager="kamrani" 
    editor="" 
    services="azure-portal" 
    documentationCenter="na"/>

<tags 
    ms.service="azure-portal" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/28/2015" 
    ms.author="hanikn"/>

# 檢視事件和稽核記錄檔

Azure 資源管理員會完全稽核在 Azure 資源上執行的所有作業，從建立和刪除到授與或撤銷存取權。 您可以瀏覽 Azure 入口網站，這些記錄檔，而且您也可以使用 [REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx) 或 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) 以程式設計方式存取完整的事件集。

## 瀏覽影響您 Azure 訂用帳戶的事件

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 按一下 [ **瀏覽** ，然後選取 **稽核記錄檔**。  
    ![瀏覽中樞](./media/insights-debugging-with-events/Insights_Browse.png)
3. 這將會開啟一個刀鋒視窗，顯示過去 7 天影響任何訂用帳戶的所有事件。 頂端是圖表，顯示資料的層級下, 面是記錄檔的完整清單 ︰
    ![所有事件](./media/insights-debugging-with-events/Insights_AllEvents.png)

>[AZURE.NOTE] 您只能在 Azure 入口網站檢視給定訂閱 500 個最新的事件。 

4. 您可以按一下任何記錄項目，以查看其構成事件。 例如，當您在資源群組中部署項目時，可能會建立或修改許多不同的資源。 在每個項目中，您可以看到：
    *  **層級** 事件-例如，它可以只是可追蹤 (**資訊**)，或發生錯誤，您必須知道 (**錯誤**)。 
    *  **狀態** -最終狀態通常是 **成功** 或 **失敗**, ，但也可能是 **已接受** 若是長時間執行的作業。
    * *當* 發生事件。
    * *誰* 如果有人執行作業。 並非所有作業都都由使用者，因此它們不會有，由後端服務都執行一些 **呼叫端**。
    *  **相互關聯識別碼** 的事件-這是這組作業的唯一識別碼。

5. 您可以從這裡移至 [詳細資料] 刀鋒視窗，以查看事件的細節。
   
    ![資源群組](./media/insights-debugging-with-events/Insights_EventDetails.png)

    如 **失敗** 事件，此頁面通常包括 **子狀態** 和 **屬性** 區段，其中包含用於進行偵錯的詳細資料。

## 篩選特定記錄檔

若要查看套用至特定的實體，或特定類型的事件，您可以篩選 [稽核記錄檔] 刀鋒視窗按一下 **篩選** 命令。 您也使用 [篩選] 刀鋒視窗來變更 **時段** 的稽核記錄] 刀鋒視窗。

在您按一下此命令後，便會隨即開啟新的刀鋒視窗：

![篩選器](./media/insights-debugging-with-events/Insights_EventFilter.png)

篩選器的類型共有四種：

1. 依訂用帳戶
2. 由 **資源群組**
3. 由 **資源類型**
4. 依特定 **資源** -此時，您必須超過的完整 *資源識別碼* 您感興趣的資源

此外，您也可以依據執行事件的人員，依據事件的層級來篩選事件。

當您完成後，選擇您想要看到，請按一下 **更新** 刀鋒視窗底部的按鈕。

## 監視影響特定資源的事件

1. 按一下 [ **瀏覽** 來尋找您感興趣的資源。 您也可以查看所有的整個記錄 **資源群組**。
2. 在資源的刀鋒視窗中，向下捲動直到您找到 **事件** 並排顯示。  
    ![事件] 磚](./media/insights-debugging-with-events/Insights_EventsTile.png)
3. 按一下該磚，以查看經篩選後僅顯示所選資源的事件。 您可以使用 **篩選** 命令來變更時間範圍，或套用更具體的篩選器。

## 後續步驟

* [接收警示通知](insights-receive-alert-notifications.md) 每當事件發生時。
* [監視服務計量](insights-how-to-customize-monitoring.md) 並確認您的服務可用且可回應。
* [追蹤服務健康狀況](insights-service-health.md) 找出 Azure 何時遭遇效能降低或服務中斷。  

