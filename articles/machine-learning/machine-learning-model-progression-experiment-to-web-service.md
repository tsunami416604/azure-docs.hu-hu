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

 ***實驗*** 是可讓 Azure Machine Learning Studio 中的畫布
若要以互動方式開發，您執行、 測試及逐一查看您在建立
預測分析模型。 可用的各種不同的模組，
您可用來將資料送回您的實驗、 操作資料，
使用機器學習演算法定型模型，評分模型
評估結果，並輸出最終的值。

一旦您滿意您的實驗，您可以將其部署為
***Azure web 服務*** ，這樣使用者就可以將新的資料傳送和接收上一步
結果。

在這篇文章中，我們將提供如何機制的概觀您
機器學習模型會從開發實驗
運作的 web 服務。

>[AZURE.NOTE] 有其他方式來開發和部署機器學習
模型中，但這篇文章著重於如何使用 Machine Learning
Studio。 如需如何建立與預測 web 服務的討論
R，請參閱部落格文章 [建置與部署預測 Web 應用程式使用 RStudio
與 Azure
ML](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx)。

雖然 Azure Machine Learning Studio 是主要是要協助您
開發和部署 *預測分析模型*, ，就可以使用
Studio 來開發並不包含預測的實驗
分析模型。 例如，實驗可能只是輸入的資料，
管理，並接著將結果輸出。 就像預測
分析實驗中，您可以部署為此非預測實驗
web 服務，但它的較簡單的程序因為無法實驗
訓練或評分機器學習模型。 雖然這不是
Studio 的一般用途，我們會將它包含在下面討論使
我們可以給 Studio 的運作方式的完整說明。

## 開發及部署預測性 Web 服務

以下是典型的解決方案遵循您在開發階段，
部署使用 Machine Learning Studio:

![](media\machine-learning-model-progression-experiment-to-web-service\model-stages-from-experiment-to-web-service.png)

*圖 1 - 一般預測性分析模型的階段*

### 訓練實驗

 ***訓練實驗*** 是初始實驗機器中的畫布
Learning Studio。 訓練實驗的目的是要讓您
將放在開發、 測試、 重複，與最終訓練機器學習
模型。 您可以更定型多個模型同時為您尋找
最好的解決方案，但是完成之後，您將選取您要體驗
單一定型的模型並消除從實驗的其餘部分。 針對
範例的開發預測分析實驗中，請參閱 [開發
在 Azure 中的信用風險評估預測分析解決方案
機器
學習](machine-learning-walkthrough-develop-predictive-solution.md)。

### 預測性實驗

一旦已定型的模型在訓練實驗中，按一下
**設定 Web 服務** 在 Machine Learning Studio 和 Studio 會
若要將訓練實驗轉換程序
***預測實驗***。 預測實驗的目的是
使用定型的模型為新的資料，其目標是最終評分
成為運作為 Azure web 服務。

會透過下列步驟為您完成這項轉換：

-   轉換的一組用於定型成單一模組的模組
    並將它儲存為定型模型

-   排除與計分無關的任何多餘模組

-   加入最終的 Web 服務將使用的輸入和輸出連接埠

可能有更多的變更，您想要取得您預測
準備好部署為 web 服務的實驗。 例如，如果您想要
您可以加入 web 服務來輸出結果的子集，
篩選模組的輸出連接埠之前。

在此轉換程序中不會捨棄訓練實驗。
完成程序時您會在 Studio 中擁有兩個索引標籤 ︰ 一個用於
訓練實驗，一個用於預測的實驗。 如此一來，
部署您的 web 服務之前，您可以變更的訓練
請實驗並重建做預測分析。 您可以儲存一份或
若要啟動實驗另一行的訓練實驗。

>[AZURE.NOTE] 當您按一下 **設定 Web 服務** 啟動自動
若要將訓練實驗轉換預測的實驗中，程序
這很適合大部分情況。 但是，如果是將訓練實驗
複雜 （例如，您有多個路徑，如您所加入的訓練
在一起），您可能想要手動執行這項轉換。 如需
此轉換程序的運作方式的詳細資訊，請參閱 [轉換為機器
預測學習訓練實驗
experiment](machine-learning-convert-training-experiment-to-scoring-experiment.md)。

### Web 服務

一旦您滿意您做預測分析都已就緒，您
按一下 [ **部署 Web 服務** 來實作您的模型，部署
它為 ***Azure web 服務***。 使用者現在可以將資料傳送至您的模型使用
web 服務 REST API，並收到傳回的結果。 如需
了解如何這麼做，請參閱 [如何使用 Azure Machine
學習已從機器學習服務部署的 web 服務
實驗](machine-learning-consume-web-services.md)。

您一次部署 web 服務、 做預測分析和 web
服務保持連線，以及您可以移回往來兩者之間 ︰

|***從這個頁面...***|***按一下此選項...***|***若要開啟此頁面...***|
| ------------------- | --------------- | ---------------------- |
|Studio 中的實驗畫布|**前往 Web 服務**|Studio 中的 Web 服務組態|
|Studio 中的 Web 服務組態|**檢視最新版本**|Studio 中的實驗畫布|
|Studio 中的 Web 服務組態|**管理端點...**|Azure 傳統入口網站中的端點管理|
|Azure 傳統入口網站中的端點管理|**在 Studio 中編輯**|Studio 中的實驗畫布|

![](media\machine-learning-model-progression-experiment-to-web-service\connections-between-experiment-and-web-service.png)

*圖 2 - 實驗和 Web 服務之間的連接*

## 非一般的情況：建立非預測性 Web 服務

如果您的實驗並訓練預測分析模型，則您
不需要建立訓練實驗和計分實驗-沒有
只有一個實驗中，而且您可以將它部署為 web 服務
(Machine Learning Studio 會偵測是否要包含您的實驗
預測模型來分析您所使用的模組）。

逐一查看實驗並滿足於該實驗之後：

1.  按一下 [ **設定 Web 服務** -輸入和輸出加入節點
    自動

2.  按一下 [ **執行**

3.  按一下 [ **部署 Web 服務**

現在部署您的 web 服務，以及您可以存取與管理它只是
像是預測 web 服務。

## Web 服務按鈕

在機器學習 Studio 中，web 服務按鈕- **總 Web 設定
服務** 和 **部署 Web 服務** -變更名稱和函式
根據位置就會在開發程序。

**實驗包含預測性模型**

如果實驗定型和評分預測模型，則 web
服務的按鈕可執行這些函式 ︰

|**實驗的類型**|**按鈕**|**作用**|
| -------------------- | -------- | -------------- |
|實驗開發中|**設定 Web 服務**|提供兩個選項|
|&nbsp;|- **預測 Web 服務**|將實驗轉換成訓練實驗和評分實驗。|
|&nbsp;|- **重新定型 Web 服務**|將實驗轉換成重新訓練實驗 (請參閱以下的「更新」一節)|
|訓練實驗|**設定 Web 服務**|提供兩個選項|
|&nbsp;|- **更新做預測分析**|以您對訓練實驗所做的變更更新相關聯的預測性實驗|
|&nbsp;|- **重新定型 Web 服務**|將訓練實驗轉換成重新訓練實驗 (請參閱以下的「更新」一節)|
|&nbsp;|-*或*- **部署 Web 服務**|如果您已為部署設定訓練實驗，則會將其部署為 Web 服務|
|預測性實驗|**部署 Web 服務**|將預測實驗部署為 Web 服務|

**實驗並 *不* 包含預測模型**

如果實驗不訓練和評分預測模型，然後在
web 服務按鈕會簡單許多 ︰

|**實驗的類型**|**按鈕**|**作用**|
| -------------------- | -------- | -------------- |
|實驗開發中|**設定 Web 服務**|準備實驗供部署為 Web 服務|
|實驗已備妥供部署|**部署 Web 服務**|將實驗部署為 Web 服務，開啟 Web 服務組態頁面|

## 使用您的 Web 服務

既然您已部署為 web 服務，您的實驗要是您
必須將其更新嗎？

視您需要更新的項目而定：

**您想要變更的輸入或輸出，或您想要修改了
web 服務來管理資料**

如果您不只變更模型，但只變更如何 web
服務處理資料時，您可以編輯做預測分析，然後
按一下 [ **部署 Web 服務** 一次。 Web 服務將會停止，
將部署更新過的預測實驗，並將 web 服務
再次啟動。

以下是範例 ︰ 假設您做預測分析傳回整個
輸入資料與預測結果的資料列。 您可能會決定您
想要只傳回結果的 web 服務。 因此，您可以加入
**專案資料行** 模組在預測實驗中前,
輸出連接埠，以排除結果以外的資料行。 當您
按一下 [ **部署 Web 服務** 同樣地，更新 web 服務。

**您想要使用新資料重新定型模型**

如果您想要保留您的機器學習模型，但您想要
訓練新資料，有兩個選擇 ︰

1.  **Web 服務執行時重新定型模型** -如果您想要
    若要重新訓練您的模型在預測 web 服務正在執行，
    您可以藉由進行一些修改訓練
    實驗，讓它 ***重新訓練實驗***, ，您就可以
    將其部署為 ***重新訓練 web* 服務**。 如需有關指示
    若要這樣做，請參閱 [重新定型機器學習服務模型
    以程式設計方式](machine-learning-retrain-models-programmatically.md)。

2.  **請回到原始定型實驗，並使用不同
    訓練資料來開發您的模型** -您做預測分析
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
重新訓練您的模型 ︰ 開啟訓練實驗中，按一下 **另存新檔**
若要製作複本，再啟動邁向新的開發程式
模型建立預測的實驗中，與部署網站
服務。 這會建立新的 web 服務不相關的原始
一個-您可以決定哪一個，或兩者來繼續執行。

## 進階閱讀

如需這個程序的詳細資訊，請參閱下列文章：

-   實驗-轉換 [轉換 Machine Learning 訓練
    要做預測分析實驗](machine-learning-convert-training-experiment-to-scoring-experiment.md)

-   部署 web 服務- [部署 Azure Machine Learning web
    服務](machine-learning-publish-a-machine-learning-web-service.md)

-   重新定型模型- [重新定型機器學習服務模型
    以程式設計的方式](machine-learning-retrain-models-programmatically.md)

如需整個程序的範例，請參閱：

-   [機器學習教學課程 ︰ 在 Azure 中建立您的第一個實驗
    機器學習服務
    Studio](machine-learning-create-experiment.md)

-   [逐步解說 ︰ 開發預測分析解決方案的信用額度
    在 Azure 機器中的風險評估
    學習](machine-learning-walkthrough-develop-predictive-solution.md)


