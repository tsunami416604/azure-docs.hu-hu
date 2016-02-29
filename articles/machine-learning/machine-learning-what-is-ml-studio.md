<properties 
    pageTitle="什麼是 Azure Machine Learning Studio？ | Microsoft Azure"
    description="從已準備就緒可供使用之演算法與模組的程式庫快速建置模型的拖放工具 - Azure ML Studio 概觀。"
    keywords="azure machine learning,azure ml, ml studio"
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
    ms.date="10/13/2015"
    ms.author="garye"/>

# 什麼是 Azure Machine Learning Studio？

Microsoft Azure Machine Learning Studio 是共同作業式的拖放工具，您可以用來依據您的資料建置、測試及部署預測分析解決方案。 Machine Learning Studio 會以 Web 服務方式發佈模型，讓自訂應用程式或 BI 工具 (例如 Excel) 都能夠很容易地使用。

Machine Learning Studio 讓資料科學、預測分析、雲端資源和您的資料齊聚一堂！

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Machine Learning Studio 互動式工作區

若要開發預測分析模型，您通常會使用一或多個來源的資料，透過各種資料操作和統計函數來轉換和分析該資料，然後產生一組結果。 開發此類模型是一種反覆式過程。 隨著您修改各種函數及其參數，結果會不斷收斂，直到您對已訓練的有效模型感到滿意為止。

**Azure Machine Learning Studio** 可讓您輕鬆地建置、 測試和反覆運算預測分析模型互動式的視覺化工作區。 您拖放 ***資料集*** 和分析 ***模組*** 到互動式 ***畫布***, ，連接在一起表單 ***實驗***, ，而您 ***執行*** Machine Learning Studio 中。 若要反覆調整模型設計，您 ***編輯*** 實驗 ***儲存*** 的複本，如果需要的話，再執行一次。 當您準備好時，您可以將轉換您 ***訓練實驗*** 至 ***做預測分析***, ，然後 ***發行*** 為 ***web 服務*** ，讓其他人可以存取您的模型。

>[AZURE.TIP] 若要下載及列印圖表，以提供 Machine Learning Studio 功能的概觀，請參閱 [Azure Machine Learning Studio 功能的概觀圖](machine-learning-studio-overview-diagram.md)。

不需要設計程式，只要在視覺上連接資料集和模組，即可建構預測分析模型。

![Azure ML Studio 圖表：建立實驗、讀取許多來源的資料、寫入評分的資料及寫入模型。][ml-studio-overview]

## 開始使用 Machine Learning Studio

當您第一次進入 [Machine Learning Studio](https://studio.azureml.net) 看 **首頁** 頁面。 您可以從這裡檢視文件、影片、網路研討會，以及尋找其他重要資源。

有三個索引標籤的上方: **首頁** (在您啟動)， **Studio**, ，和 **圖庫**。

### Studio

按一下 [ **Studio** ] 索引標籤，您將使用您的 Microsoft 帳戶或工作或學校帳戶登入要求。 登入之後，您會在左邊看到下列索引標籤：

- **實驗** -執行和儲存為草稿的實驗已建立
- **WEB 服務** -Web 服務部署從您的實驗
- **筆記本** -您已建立的 Jupyter notebook
- **資料集** -您已上傳至 Studio 的資料集
- **定型模型** -您已受過訓練實驗，並儲存在 Studio 中的模型
- **設定** -設定可讓您設定帳戶和資源的集合。

### 資源庫

按一下 [ **圖庫** ] 索引標籤，您會來到 Cortana 分析的組件庫。 [資源庫] 可以讓資料科學家和開發人員社群在此分享使用 Cortana 分析套件的元件建立的解決方案。

如需組件庫的詳細資訊，請參閱 [共用及探索 Cortana 分析的組件庫中的方案](machine-learning-gallery-how-to-use-contribute-publish.md)。

## 實驗的元件

實驗由資料集組成，資料集提供資料給分析模組，將模組連接起就能建構預測分析模型。 明確地說，有效的實驗有三個特性：

- 實驗至少有一個資料集和一個模組
- 資料集只能連接到模組
- 模組可以連接到資料集或其他模組
- 模組的所有輸入埠必須有資料流程的某些連線
- 必須設定每個模組的所有必要參數

您可以從頭建立實驗，或者使用現有的範例實驗做為範本。 如需詳細資訊，請參閱 [使用範例實驗來建立新的實驗](machine-learning-sample-experiments.md)。

如需建立簡易實驗的範例，請參閱 [Azure Machine Learning Studio 中建立簡易實驗](machine-learning-create-experiment.md)。

建立預測分析解決方案的更完整逐步解說，請參閱 [開發預測方案使用 Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)。

### 資料集

資料集是指已上傳至 Machine Learning Studio 而可供模型化程序中使用的資料。 Machine Learning Studio 隨附許多範例資料集供您實驗，您可以在需要時上傳更多資料集。 以下是隨附資料集的一些例子：

- **各種汽車的油耗資料** -每加侖英里 (MPG) 值，以數磁柱、 馬力等來識別。
- **乳癌資料** -乳癌診斷資料。
- **森林火災資料** -葡萄牙西北部的樹系引發大小。

當您建置實驗時，可以從畫布左邊的資料集清單選擇。

如需 Machine Learning Studio 中包含的範例資料集的清單，請參閱 [Azure Machine Learning Studio 中使用範例資料集](machine-learning-use-sample-datasets.md)。

### 模組

模組是指您在資料上可執行的演算法。 Machine Learning Studio 有許多模組，從資料輸入函數到訓練、評分和驗證程序都有。 以下是隨附模組的一些例子：

- [轉換成 ARFF][轉換-至-arff]-將.NET 序列化資料集轉換為屬性關聯檔案格式 (ARFF)。
- [基本統計資料][基本-統計資料]-計算基本統計資料，例如平均值、 標準差等。
- [線性迴歸][線性-迴歸]-建立線上梯度下降線性迴歸模型。
- [評分模型][評分模型-] 給訓練的分類或迴歸模型。

當您建置實驗時，可以從畫布左邊的模組清單選擇。  

模組可能有一組參數可用來設定模組的內部演算法。 當您選取在畫布上的模組時，模組的參數會顯示在 **屬性** 畫布右邊的窗格。 您可以在此窗格中修改參數來調整模型。

某些說明瀏覽可用的機器學習演算法的大型程式庫，請參閱 [如何選擇演算法為 Microsoft Azure Machine Learning](machine-learning-algorithm-choice.md)。

## 部署預測性分析 Web 服務

當您的預測性分析模型準備好時，可以從 Machine Learning Studio 將它部署為 Web 服務。 如需有關此程序的詳細資訊，請參閱 [部署 Azure Machine Learning web 服務](machine-learning-publish-a-machine-learning-web-service.md)。

[ml-studio-overview]:./media/machine-learning-what-is-ml-studio/azure-ml-studio-diagram.jpg

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/

