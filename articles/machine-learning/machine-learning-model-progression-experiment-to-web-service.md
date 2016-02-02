<properties
    pageTitle="機器學習服務模型如何從實驗進展為實際運作的 Web 服務 | Microsoft Azure"
    description="機制的概觀，說明 Azure Machine Learning 模型如何從開發實驗進展為實際運作的 Web 服務。"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/10/2015"
    ms.author="garye"/>



# 機器學習服務模型如何從實驗進展為實際運作的 Web 服務















>[AZURE.NOTE] 

Studio。















## 開發及部署預測性 Web 服務




![](media\machine-learning-model-progression-experiment-to-web-service\model-stages-from-experiment-to-web-service.png)

*圖 1 - 一般預測性分析模型的階段*

### 訓練實驗









機器


### 預測性實驗








會透過下列步驟為您完成這項轉換：

-   


-   排除與計分無關的任何多餘模組

-   加入最終的 Web 服務將使用的輸入和輸出連接埠






在此轉換程序中不會捨棄訓練實驗。





>[AZURE.NOTE] 



如需




### Web 服務




如需







| | | |
| ------------------- | --------------- | ---------------------- |
| Studio 中的實驗畫布| **前往 Web 服務**| Studio 中的 Web 服務組態|
| Studio 中的 Web 服務組態| **檢視最新版本**| Studio 中的實驗畫布|
| Studio 中的 Web 服務組態| **管理端點...**| Azure 傳統入口網站中的端點管理|
| Azure 傳統入口網站中的端點管理| **在 Studio 中編輯**| Studio 中的實驗畫布|

![](media\machine-learning-model-progression-experiment-to-web-service\connections-between-experiment-and-web-service.png)

*圖 2 - 實驗和 Web 服務之間的連接*

## 非一般的情況：建立非預測性 Web 服務







逐一查看實驗並滿足於該實驗之後：

1.  


2.  按一下 [執行]****

3.  按一下 [部署 Web 服務]****




## Web 服務按鈕





**實驗包含預測性模型**




| **實驗的類型**| **按鈕**| **作用**|
| -------------------- | -------- | -------------- |
| 實驗開發中| **設定 Web 服務**| 提供兩個選項|
| &nbsp;| - **預測性 Web 服務**| 將實驗轉換成訓練實驗和評分實驗。|
| &nbsp;| - **重新定型 Web 服務**| 將實驗轉換成重新訓練實驗 (請參閱以下的「更新」一節)|
| 訓練實驗| **設定 Web 服務**| 提供兩個選項|
| &nbsp;| - **更新預測性實驗**| 以您對訓練實驗所做的變更更新相關聯的預測性實驗|
| &nbsp;| - **重新定型 Web 服務**| 將訓練實驗轉換成重新訓練實驗 (請參閱以下的「更新」一節)|
| &nbsp;| -*或*- **部署 Web 服務**| 如果您已為部署設定訓練實驗，則會將其部署為 Web 服務|
| 預測性實驗| **部署 Web 服務**| 將預測實驗部署為 Web 服務|






| **實驗的類型**| **按鈕**| **作用**|
| -------------------- | -------- | -------------- |
| 實驗開發中| **設定 Web 服務**| 準備實驗供部署為 Web 服務|
| 實驗已備妥供部署| **部署 Web 服務**| 將實驗部署為 Web 服務，開啟 Web 服務組態頁面|

## 使用您的 Web 服務




視您需要更新的項目而定：










以下是範例: 假設您做預測分析傳回整個
輸入資料與預測結果的資料列。
想要只傳回結果的 web 服務。 因此，您可以加入
**專案資料行** 模組在預測實驗中前,
輸出連接埠，以排除結果以外的資料行。 當您
按一下 [ **部署 Web 服務** 同樣地，更新 web 服務。

**您想要使用新資料重新定型模型**

如果您想要保留您的機器學習模型，但您想要
訓練新資料，有兩個選擇:

1.  **Web 服務執行時重新定型模型** -如果您想要
    若要重新訓練您的模型在預測 web 服務正在執行，
    您可以藉由進行一些修改訓練
    實驗，讓它 *** 重新訓練實驗 ***, ，您就可以
    將其部署為 *** web * 服務的重新訓練**。 如需有關指示
    若要這樣做，請參閱 [重新定型機器學習服務模型
    programmatically](machine-learning-retrain-models-programmatically.md)。

2.  * * 回到原始定型實驗，並使用不同
    訓練資料來開發您模型 * *-您做預測分析
    連結至 web 服務，但不是訓練實驗
    直接連接以這種方式。 如果您修改原始的訓練
    實驗並按一下 [ **設定 Web 服務**, ，它會建立 *新*
    預測實驗，部署時，會建立 *新*
    web 服務。 它不只是更新原始的 Web 服務。

    因此如果您要修改的訓練實驗，請開啟它，然後按一下
    **另存新檔** 製作複本。 這將會保留原始
    定型實驗、 做預測分析，以及 web 服務。 您可以
    現在建立新的 web 服務與您的變更。 一旦開發了
    您可以再決定是否要停止上一個新的 web 服務
    web 服務，或讓它與新並存執行。

**您想要定型不同的模型**

如果您想要變更
原始預測實驗中，例如選取不同的
機器學習演算法，嘗試不同的訓練方法等。，
然後您必須遵循上述的第二個程序
重新訓練您的模型: 開啟訓練實驗中，按一下 **另存新檔**
若要製作複本，再啟動邁向新的開發程式
模型建立預測的實驗中，與部署網站
服務。 這會建立新的 web 服務不相關的原始
一個-您可以決定哪一個，或兩者來繼續執行。

## 進階閱讀

如需這個程序的詳細資訊，請參閱下列文章：

-   轉換 [轉換 Machine Learning 訓練實驗-
    實驗來預測 experiment](machine-learning-convert-training-experiment-to-scoring-experiment.md)

-   部署 web 服務的 [部署 Azure Machine Learning web
    service](machine-learning-publish-a-machine-learning-web-service.md)

-   重新定型模型-[重新定型機器學習服務模型
    programmatically](machine-learning-retrain-models-programmatically.md)

如需整個程序的範例，請參閱：

-   [機器學習教學課程: 在 Azure 中建立您的第一個實驗
    機器學習服務
    Studio](machine-learning-create-experiment.md)

-   [逐步解說: 開發預測分析解決方案的信用額度
    在 Azure 機器中的風險評估
    Learning](machine-learning-walkthrough-develop-predictive-solution.md)





