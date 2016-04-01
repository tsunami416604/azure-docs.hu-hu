<properties
   pageTitle="在 Logic Apps 中使用 Azure 機器學習服務連接器 | Microsoft Azure App Service"
   description="如何建立並設定 Azure 機器學習服務連接器，並在 Azure App Service 的邏輯應用程式中使用它"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="11/11/2015"
   ms.author="jehollan"/>
   
# 概觀
適用於 Logic Apps 的 Azure ML 連接器可讓您針對批次評分 (批次執行服務) 和重新定型呼叫 Azure ML API。 結合這些功能與邏輯應用程式觸發程序，可用來排程批次作業以及設定排定的模型重新定型。

 ![][1]
 
## 開始使用 Azure 機器學習服務連接器，並將它加入您的邏輯應用程式
若要開始，在 Azure ML Studio 中建立實驗，然後設定和部署 Web 服務。 您便可以使用 API URL 和在批次強徵說明頁面上找到的 BES Post URL 的索引鍵。 ([更多資訊](https://github.com/Azure/azure-content/blob/master/articles/machine-learning/machine-learning-walkthrough-5-publish-web-service.md))

若要使用連接器執行 BES 作業，請將 Azure ML 連接器加入邏輯應用程式。 然後輸入所需的資訊 (詳細說明請參閱下文)。
若要安裝 Retraining、 新增第二個 Azure ML 連接器和提供的輸入的參數 (請參閱 [這裡](machine-learning-retrain-models-programmatically.md) 如需有關設定進行重新訓練的模型)。

## 執行 Azure ML 批次執行作業
Azure ML 連接器提供下列四個選項來執行批次執行 (BES) 作業 ︰
1.  批次作業執行與輸入和輸出 ︰ 實驗有 web 服務輸入和輸出模組
2.  批次作業沒有輸入和輸出 ︰ 實驗並沒有 web 服務輸入或輸出模組 （例如使用讀取器和寫入器的模組）
3.  批次作業的唯一輸入 ︰ 實驗有 web 服務輸入的模組，但是沒有 web 服務輸出模組 （例如使用寫入器模組）
4.  批次作業具有唯一的輸出 ︰ 實驗有任何 web 服務輸入] 模組，但有 web 服務輸出模組 （例如使用讀取器模組）
請注意 BES 是非同步的要求，而可能需花的時間才能完成，視資料大小和模型的複雜度而定。 當作業完成時，連接器會傳回輸出結果。

### 執行批次執行：有輸入和輸出
Studio 實驗有 web 服務輸入和輸出模組，如果您要提供的 blob 儲存體帳戶和位置資訊 ([了](machine-learning-consume-web-services.md))。 此外，包含全域 （web 服務） 參數，如果在您的實驗中設定 ([了](machine-learning-web-service-parameters.md))。

![][2]

按一下省略符號可顯示和隱藏 [全域參數] 欄位。 這可讓您用逗號分隔的欄位和值清單提供一個或多個全域 (Web 服務) 參數。

![][3]

BES 作業的其他變化，例如沒有 Web 服務輸入或輸出的作業，也可透過連接器使用。

## 設定重新定型

使用 [設定重新定型] 動作設定一次性或排程的 ML 模型重新定型。 
結合連接器建立的批次執行作業，您可以完成定型和更新 Web 服務模型的步驟。 在此工作流程中，您會使用連接器兩次。 
1.  第一個連接器用來執行 BES 作業，重新定型您的模型並傳回輸出。 此執行的輸出將會有新模型的 URL (.ilearner)。 它可以是選擇性的 – 如果您已在實驗中設定它 – 傳回評估模組的輸出的 URL，是 csv 檔案。
在下一個步驟中，您可以使用評估模組輸出中的資料，來決定是否要取代 Web 服務中的模型 (例如，如果準確度 > 0.85)。
1.  第二個連接器用來設定重新定型。 它會使用從第一個連接器的輸出參數 （選擇性） 檢查有更新模型條件與 web 服務更新為使用新定型的模型。
  * 例如，您可以存取新定型的模型的 URL 之 BES 工作輸出輸入 `@{body('besconnector').Results.output2.FullURL}` 重新培訓模型 URL] 欄位中。 這是假設在訓練實驗中的 web 服務輸出稱為 output2。
  * 資源的名稱，用於儲存的定型模型的完整名稱做預測分析例如 MyTrainedModel [定型模型]
  * 評估結果金鑰] 欄位中，您可以輸入任何參數 （如果您已加入它），訓練實驗的 [評估] 模組的輸出中傳回。 您可以在 Azure ML Studio 中將訓練實驗中評估模組的結果視覺化程現，即可查看可用參數清單。 在分類實驗中，這些會包含準確度、精確度、回收、F 分數、AUC、平均記錄遺失和定型記錄遺失。

![][4]
 
### 排定的重新訓練
 
使用邏輯應用程式觸發程序，您可以設定連接器執行排程。 這可以定期在新資料到達時重新定型模型。 BES 作業會重新定型模型，重新定型完成後，重新定型動作會更新模型。
 
![][5]
 
## 連接器的輸出 
 
**BES**︰ 之後的批次工作可順利完成時，連接器的輸出會有以下的每個 web 服務輸出資訊。
 
 ![][6]
 
請注意，如果您並未包含 Web 服務輸出 (例如，您在 Studio 中使用寫入器模組寫入實驗的資料庫)，則不會得到這些輸出。

**重新訓練**︰ 順利完成之後重新定型，輸出就會有下列資訊。

![][7]

## 摘要

使用適用於 Logic Apps 的 Azure ML 連接器，您可以執行批次評分和重新定型作業，並按隨選或排定的週期性排程執行。 兩個動作會自動組合，可以評分資料，以及重新定型、評估及更新 Web 服務的模型，而不需要撰寫任何程式碼。

 <!--Image references-->
[1]: ./media/app-service-logic-connector-azureml/img1.png
[2]: ./media/app-service-logic-connector-azureml/img2.png
[3]: ./media/app-service-logic-connector-azureml/img3.png
[4]: ./media/app-service-logic-connector-azureml/img4.png
[5]: ./media/app-service-logic-connector-azureml/img5.png
[6]: ./media/app-service-logic-connector-azureml/img6.png
[7]: ./media/app-service-logic-connector-azureml/img7.png


