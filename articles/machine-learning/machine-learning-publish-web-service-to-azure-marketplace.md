<properties 
    pageTitle="將 Machine Learning Web 服務發佈至 Azure Marketplace | Microsoft Azure" 
    description="如何將 Azure Machine Learning Web 服務發佈至 Azure Marketplace" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="paulettm" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/01/2015" 
    ms.author="bharaths"/>

# 將 Azure Machine Learning Web 服務發佈至 Azure Marketplace 

Azure Marketplace 可讓您發行 Azure Machine Learning Web 服務，作為供外部客戶付費或免費使用的服務。 本文章將提供該程序的概觀，以及入門使用的指引連結。 透過此程序，您將可讓您的 Web 服務成為可供其他開發人員運用在其應用程式中的服務。


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## 發行程序概觀 

以下是將 Azure Machine Learning Web 服務發行至 Azure Marketplace 的步驟：

1. 建立及發佈機器學習服務要求-回應服務 (RRS)
2. 將服務部署至實際執行環境中，並取得 API 金鑰與 OData 端點資訊。
3. 使用已發佈的 web 服務的 URL，發行至 [Azure Marketplace (資料市場)](https://publish.windowsazure.com/workspace/) 
4. 您的產品在提交之後必須經過審閱和核准，才可供客戶購買。 發行程序可能需要數個工作天。 

## 逐步解說
###步驟 1：建立及發佈機器學習服務要求-回應服務 (RRS)###
 如果您有不這樣做，請看看這 [逐步](machine-learning-walkthrough-5-publish-web-service.md)。

###步驟 2：將服務部署至實際執行環境中，並取得 API 金鑰與 OData 端點資訊###
1. 從 [Azure 傳統入口網站](http://manage.windowsazure.com), ，請選取 **MACHINE LEARNING** 選項從左側的導覽列中，並選取您的工作區。 

2. 按一下 [ **WEB 服務** 索引標籤，然後選取您想要發佈至 marketplace 的 web 服務。

    ![Azure Marketplace][workspace]

3. 選取您想要 Marketplace 取用的端點。 如果您尚未建立任何額外的端點，您可以選取 **預設** 端點。

4. 當您按一下在端點上時，您將能夠看到 **API 金鑰**。 在稍後的步驟 3 中您將需要此資訊，因此建立一個複本。

    ![Azure Marketplace][apikey]

5. 按一下 [ **要求/回應** 方法，此時我們不支援發佈批次執行服務至 marketplace。 這將帶您前往進入要求/回應方法的 API 說明頁面。

6. 複製 **OData 端點位址**, ，您將需要此資訊在稍後的步驟 3 中。

    ![Azure Marketplace][odata]




將服務部署到實際執行環境。



###步驟 3：使用已發行之 Web 服務的 URL，發行至 Azure Marketplace (資料市場)###

1.  瀏覽至 [Azure Marketplace (資料市場)](http://datamarket.azure.com/home) 
2.  按一下 [ **發行** 在頁面頂端的連結。 這樣會帶您到 [Microsoft Azure 發佈入口網站](https://publish.windowsazure.com)
3.  按一下 [ **發行者** 區段，以註冊為發行者。
4.  在建立新的優惠，選取 **Data Services**, ，然後按一下 [ **建立新的資料服務**。 
 
    ![Azure Marketplace][image1]

    <br />


5.  在 **計劃** 提供您的產品，包括價格方案的相關資訊。 決定您要提供免費還是付費服務。 若要收費，請提供付款資訊，例如您的銀行和稅務資訊。

6.  在 **行銷** 提供您提供服務，例如標題和描述，提供相關資訊。

7.  在 **定價** 您可以設定特定國家/地區，您計劃的價格，或讓系統項目 「 自動 」 您的產品。

8. 在 **資料服務** 索引標籤上，按一下 [ **Web 服務** 為 **資料來源**。

    ![Azure Marketplace][image2]

9.  從 Azure 傳統入口網站取得 Web 服務 URL 和 API 金鑰，如以上的步驟 2 所述。

10. 在 Marketplace 資料服務設定] 對話方塊中，貼上 OData 端點位址貼入 **服務 URL** 文字方塊。

11. 如 **驗證**, ，選擇 [ **標頭** 為 **驗證配置**。

    - 輸入 「 授權 」 **標頭名稱**。
    - 如 **標頭值**, 中輸入"Bearer"(不含引號)，按一下 **空間** 列，，然後貼上 API 金鑰。
    - 選取 **This Service is OData** 核取方塊。
    - 按一下 [ **測試連接** 來測試連線。

12. 在 **類別**, ，確保 **Machine Learning** 已選取。

13. 當您完成輸入提供服務，所有的中繼資料上按一下 [ **發行**, ，然後 **發送至預備環境**。 此時，將通知您必須先修正的任何其餘問題。

14. 確定完成所有未解決的問題後，請按一下 **要求核准以推入至生產環境**。 發行程序可能需要數個工作天。 


[image1]:./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]:./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
[workspace]:./media/machine-learning-publish-web-service-to-azure-marketplace/selectworkspace.png
[apikey]:./media/machine-learning-publish-web-service-to-azure-marketplace/apikey.png
[odata]:./media/machine-learning-publish-web-service-to-azure-marketplace/odata.png
 

