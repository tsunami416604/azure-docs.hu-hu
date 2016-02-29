<properties 
    pageTitle="常見問題集：在 Azure Marketplace 中發佈和使用 Machine Learning 應用程式 | Microsoft Azure" 
    description="常見問題集" 
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
    ms.date="12/08/2015" 
    ms.author="luisca"/> 

#在 Azure Marketplace 中發佈和使用 Machine Learning 應用程式：常見問題集

##從 Marketplace 取用的相關問題


**1.為什麼我輸入 web 服務輸入之後收到下列錯誤訊息:**

**要求產生後端逾時或後端錯誤。 小組正在調查這個問題。 很抱歉造成您的不便。 (500)**

您的輸入參數可能不符合特定 Web 服務所需的格式。 請參閱對應的文件連結，來尋找輸入參數的正確格式以及此 Web 服務的限制。


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**2.如果我複製在 [探索此資料集] 頁面並貼上到另一個瀏覽器視窗中，認證應該看 web 服務 API 連結我用於存取結果，以及如何查看它們?**

您應該使用您的 Marketplace 帳戶作為使用者名稱，並使用主要帳戶金鑰作為密碼。 位於主要帳戶金鑰 **探索此資料集** 頁面下的 web 服務描述 (按一下 **顯示** 按鈕)。 結果可能會顯示在瀏覽器或可供下載，根據哪一個瀏覽器使用。

**3.為什麼我在 [探索此資料集] 頁面上輸入 web 服務輸入之後收到下列錯誤訊息:** 

**處理您的要求時發生未預期的錯誤。 請再試一次。**

您的 web 服務的一個或多個輸入的參數可能超過長度限制使用服務商場上的 web 服務時 **探索此資料集** 頁面。 您可以透過 HTTP POST 方法，使用較長的輸入長度呼叫服務。 如需範例，請參閱 [範例使用 R 在 Machine Learning 上建置的方案，並發佈至 Marketplace](machine-learning-r-csharp-web-service-examples.md)。

**4.為何不會看到 「 API 總管 」 索引標籤 int Azure 傳統入口網站中的存放區中的任何項目?** 

這是 Azure 傳統入口網站 Marketplace 的已知問題。 小組正著手解決這個問題。 


##透過 Azure Machine Learning 在 Marketplace 上發佈的相關問題

**1.為何我的標誌或影像的交易不重新整理我的 web 服務?** 

標誌和影像是在發佈入口網站中快取，新的標誌或影像最多可能需要 10 天才能在入口網站上更新。

**2.為何我在顯示的錯誤訊息的服務商場的 web 服務的 [詳細資料] 索引標籤?**

在連接到 Azure Machine Learning 以取得服務詳細資料時，已知有一個 Marketplace 問題。 小組正著手解決這個問題。

**3.為什麼在 Azure Machine Learning web 服務中的 R 範例程式碼不適用於使用服務商場中的 web 服務?**

直接連接到 Azure Machine Learning Web 服務，相較於透過 Marketplace 連接到這些 Web 服務，有不同的驗證系統。 Marketplace 中的服務是 OData 服務，而且可以使用 GET 或 POST 方法進行呼叫。 

**4.為何我的 Web 服務產品的支援連結無法正確更新我的部分產品？**

支援連結是依照發佈者而非依照產品的全域連結。 

**5.如何使用 Marketplace 中的批次輸入模式來發佈 Web 服務？**

Marketplace Web 服務目前不支援批次輸入模式。

**6.應連絡誰以取得協助，如果我有關於成為資料發行者的問題，或如果我在發佈時有問題?**

請連絡 Azure Marketplace 小組在 <datamarketbd@microsoft.com> 如需詳細資訊。





 

