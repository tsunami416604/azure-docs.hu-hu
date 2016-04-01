<properties 
    pageTitle="機器學習範例應用程式：人氣組合 | Microsoft Azure" 
    description="機器學習 Web 服務會執行線上購物車分析，從使用者提供的歷史交易產生人氣組合項目的產品建議。" 
    services="machine-learning" 
    documentationCenter="" 
    authors="CoromT" 
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

# 機器學習範例應用程式：人氣組合


## 重要事項：此服務已被取代

人氣組合提供的功能，這項服務現在已整合至我們更廣泛 [建議 API] (http://gallery.azureml.net/MachineLearningAPI/3574432384684cac9cc766e57729ea4c)。 我們鼓勵您使用建議項目來代替此服務。

##概觀

 [人氣組合 web 服務]( https://datamarket.azure.com/dataset/amla/mba) Machine Learning 中執行線上購物車分析，以產生人氣組合從過去的交易之項目的產品建議。 人氣組合建議可幫助購物者在購買特定項目時，找出目錄中最相關的產品。 以醒目的方式顯示這些建議已證明可以有效提升線上零售商的銷售業績。 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 
  
##開始使用 

訂閱人氣組合 web 服務之後，您可以使用 [購物籃分析服務範例 web 應用程式](https://marketbasket.cloudapp.net/) 來輕鬆地將您的資料上傳至模型，並找出人氣產品組合。 若要使用應用程式或 API，您首先需要 API 金鑰，您可以從取得 [Azure 資料市場帳戶頁面](https://datamarket.azure.com/account)。

##使用 Web 服務 

這項服務包含的 API 可建立人氣組合模型、上傳歷史交易，以及擷取指定產品的最佳排行人氣產品組合。 範例會示範如何使用這些 Api 位於 [Azure-machinelearning-datascience/說明](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Apps/FrequentlyBoughtTogether) GitHub 上的儲存機制。

 


