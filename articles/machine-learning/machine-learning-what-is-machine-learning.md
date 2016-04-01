<properties
    pageTitle="什麼是 Machine Learning？ | Microsoft Azure"
    description="說明完全受管理之機器學習服務的基本概念，這項雲端技術可讓您建立、操作及銷售解決方案。"
    keywords="what is machine learning,cloud technology,predictive,what is predictive analytics,operationalize"
    services="machine-learning"
    documentationCenter=""
    authors="cjgronlund"
    manager="neerajkh"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2015"
    ms.author="cgronlun;tedway;olgali"/>


# Microsoft Azure Machine Learning 簡介

## 什麼是機器學習？

機器學習服務利用電腦來執行預測模型，從現有的資料學習，以便預測未來的行為、結果和趨勢。

機器學習服務的這些預測可讓應用程式和裝置更聰明。 當您線上購物時，機器學習服務可根據您已經購買的產品，協助推薦其他產品。 當您的信用卡被刷過時，機器學習服務可將該筆交易與交易資料庫進行比對，協助銀行偵測詐騙。

## 什麼是 Microsoft Azure Machine Learning？

Azure Machine Learning 是強大的雲端架構預測性分析服務，可讓您快速地建立預測模型，並將其部署為分析解決方案。

Azure 機器學習不僅提供可建立預測性分析模型的工具，也提供完全受管理的服務，您可以透過這項服務將預測性模型部署為可供取用的 Web 服務。 Azure Machine Learning 提供可在雲端上建立完整預測性分析解決方案的工具：您可以快速地建立、測試、操作及管理預測模型。 您不需要購買任何硬體，或手動管理虛擬機器。

![什麼是機器學習？ Azure Machine Learning 上操作性預測性分析的基本工作流程。](./media/machine-learning-what-is-machine-learning/machine-learning-service-parts-and-workflow.png)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## 什麼是預測性分析？

預測性分析使用各種統計技術 (在本例中為機器學習服務)，來分析收集到的資料或目前資料中的模式或趨勢，以便預測未來的事件。

Azure Machine Learning 是特別強大的預測性分析方式：您可以使用現成的演算法程式庫；在連線到網際網路的電腦上建立模型，而不需要購買額外的設備或基礎結構；以及快速地部署預測解決方案。 您也可以尋找已備妥要使用的範例和解決方案中的 [Microsoft Azure Marketplace](https://datamarket.azure.com/browse?query=machine+learning) 或 [Cortana 分析圖庫](http://gallery.azureml.net/)。

## 在雲端上建置完整的機器學習服務解決方案

Azure Machine Learning 具備在雲端上建立預測性分析解決方案所需的一切功能，從大型演算法程式庫、用於建置模型的 Studio，到可將模型部署為 Web 服務的簡單方法。

### Machine Learning Studio：建立預測模型

建立預測模型中的 [Machine Learning Studio](machine-learning-what-is-ml-studio.md), ，瀏覽器為基礎的工具，拖曳、 卸除，並連接模組。

![什麼是預測性分析：Azure Machine Learning Studio 中的預測性分析實驗範例](./media/machine-learning-what-is-machine-learning/azure-machine-learning-studio-predictive-score-experiment.png)

* 使用大型的程式庫 [機器學習演算法和模組](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7) 要立刻開始您的預測模型的 Machine Learning Studio 中。 從範例實驗、R 和 Python 封裝的程式庫，以及 Xbox 和 Bing 等 Microsoft 公司的一流演算法中進行選擇。 擴充 Studio 模組使用您自己的自訂  [R](machine-learning-r-quickstart.md) 和 [Python](machine-learning-execute-python-scripts.md) 指令碼。
* 在 [Cortana 分析圖庫](machine-learning-gallery-how-to-use-contribute-publish.md), ，您可以開始使用 Azure Machine Learning，並向他人學習社群中。 您可以嘗試其他人所撰寫的實驗、針對實驗提出問題或張貼意見，或者發佈您自己的實驗。 您也可以透過 LinkedIn 和 Twitter 等社交網路分享實驗的連結。  

    ![在 Azure Cortana 分析資源庫中試做預測實驗範本或提供您自己的範本](./media/machine-learning-what-is-machine-learning/machine-learning-cortana-analytics-gallery.png)

### 操作預測性分析解決方案：購買 Web 服務或發佈您自己的服務

* 購買可取用 web 服務的方式從 [Microsoft Azure Marketplace](https://datamarket.azure.com/browse?query=machine+learning), ，例如建議、 文字分析和異常偵測。

* 操作您的預測性分析模型：
    * [部署 Web 服務](machine-learning-publish-a-machine-learning-web-service.md)
    * [透過 API 定型和重新定型模型](machine-learning-retrain-models-programmatically.md)
    * [管理 Web 服務端點](machine-learning-create-endpoint.md)
    * [調整 Web 服務](machine-learning-scaling-endpoints.md)
    * [取用 Web 服務](machine-learning-consume-web-services.md)

## 主要的機器學習服務詞彙和概念
### 資料探索、描述性分析和預測性分析

**資料瀏覽** 是收集大量通常非結構化資料集順序的相關資訊的程序找到特性進行重點分析。 **資料採礦** 是指自動的資料探索。

**描述性分析** 是程序的分析資料集以便彙總發生了什麼事。 大部分商務分析 (例如銷售報表、網路指標和社交網路分析) 都具描述性。

**預測分析** 是建置模型從歷程記錄或目前的資料，以便預測未來結果的程序。


### 監督式和非監督式學習
 **經過指導的學習** 演算法會以加上標籤的資料來訓練-也就是說，資料組成的答案範例。 例如，在用來定型識別信用卡盜用之模型的資料集中，資料點指出標示了已知詐騙收費和有效收費。 大部分的機器學習服務都是監督式。

 **未經指導的學習** 適用於沒有標籤中，資料和目標是要尋找資料的關聯性。 例如，您可能想要尋找具有類似購買習慣的客戶人口統計資料群組。

### 模型定型和評估
機器學習服務模型是您嘗試回答之問題或想要預測之結果的擷取內容。 模型會以現有的資料定型並評估。

#### 從資料定型
在 Azure Machine Learning 中，模型是從處理定型資料和功能模組的演算法模組 (例如計分模組) 建置而來。

在監督式學習中，如果您要定型詐騙偵測模型，您會使用一組標示為詐騙或有效的交易。 您會隨機分割資料集，然後使用一部分來定型模型，另一部分來測試或評估模型。

#### 評估資料
一旦您具有定型模型，請使用其餘測試資料來評估模型。 您會使用已知結果的資料，以便判斷模型的預測是否正確。

### 其他常見的機器學習服務詞彙

* **演算法**︰ 一組獨立的規則用來解決問題，透過資料處理、 計算或自動推論。
* **分類資料**︰ 資料，依照分類進行組織，以及可以分割成群組。 例如，汽車的類別資料集可指定年份、廠牌、車型和價格。
* **分類**︰ 資料點組織成類別目錄的模型為基礎的哪個類別群組為已知的資料集。
* **特性工程設計**︰ 擷取或選取功能的程序相關的資料集，以便增強資料集，並改善結果。 例如，飛機票價資料可透過星期幾和假日來增強。 請參閱 [特徵選取和 Azure Machine Learning 中的設計](machine-learning-feature-selection-and-engineering.md)。
* **模組**︰ 在 Machine Learning Studio 模型中，例如輸入資料模組可輸入及編輯小型資料集的功能項目。 演算法也是 Machine Learning Studio 的一種模組類型。
* **模型**︰ 監督式學習中，模型是機器學習實驗訓練資料集、 演算法模組和功能的模組，例如 [評分模型] 模組所組成的乘積。
* **數值資料**︰ 代表度量 （連續資料） 或計數 （離散資料） 的資料。 也稱為 *量化資料*。
* **資料分割**:，您必須將資料分成範例的方法。 請參閱 [資料分割和取樣](https://msdn.microsoft.com/library/azure/dn905960.aspx) 如需詳細資訊。
* **預測**︰ 預測是一個值或值從機器學習模型的預測。 您也可能會看到「預測的分數」一詞；不過，預測的分數不是模型的最終輸出。 計分之後還會評估模型。
* **迴歸**︰ 模型來預測連續值根據獨立變數，預測汽車的價格，例如根據汽車的年份和廠牌。
* **分數**︰ 產生從定型的分類或迴歸模型的預測的值使用 [評分模型] 模組](https://msdn.microsoft.com/library/azure/dn905995.aspx) Machine Learning Studio 中。 分類模型也會傳回預測值的機率分數。 一旦您已經從模型產生分數，您可以評估模型的精確度使用 [評估模型] 模組](https://msdn.microsoft.com/library/azure/dn905915.aspx)。
* **範例**︰ 資料集的一部分是要代表整體。 您可以隨機取樣，或根據資料集的特定特徵進行取樣。



## 後續步驟
您可以了解基本的預測分析和機器學習使用 [逐步教學課程](machine-learning-create-experiment.md) 和 [以範例建立](machine-learning-sample-experiments.md)。  


<!-- Module References -->
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/


