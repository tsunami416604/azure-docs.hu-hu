<properties 
    pageTitle="Cortana 分析程序為何?  | Microsoft Azure" 
    description="Cortana 分析程序 (CAP) 是一種系統化的資料科學方法，可建置運用進分析的智慧型應用程式。" 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev"
    manager="paulettm" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/23/2015" 
    ms.author="bradsev;gopitk" /> 


# 什麼是 Cortana 分析程序 (CAP)？

Cortana 分析程序 (CAP) 是一種系統化的資料科學方法，可列出一系列運用進階分析來建置智慧型應用程式的步驟。 CAP 步驟提供 **指引** 如何定義問題、 分析相關的資料、 建立和評估預測模型，然後再部署這些模型在智慧型應用程式。 

以下是在步驟 **Cortana 分析程序**:  

![CAP 工作流程](./media/machine-learning-data-science-the-cortana-analytics-process/CAP-workflow.png)

此程序 **反覆**: 了解新的和現有的或在模型中的調整逐漸成形，以及需要修改先前在順序中完成的步驟。 現有的組織開發和專案計劃程序 **輕鬆地調整** 使用端點定義的步驟順序。 

處理序中的步驟所訂位和連結中 [CAP 學習途徑](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) 和以下所述。  

## 準備步驟 

## P1. 規劃分析專案 

定義您的商業目標和問題，是展開分析專案的第一步。 在所指定的 **商務需求**。 此步驟的主要目標，是要識別分析需要哪些主要商業變數 (例如銷售預測，或訂單屬詐騙性質的機率) 進行預測以達成這些需求。 額外的規劃然後是通常重要深入的了解 **資料來源** 從分析的觀點來應付專案的目標。 舉例來說，我們常會發現現有的系統必須收集並記錄其他類型的資料，才能解決問題並達成專案目標。 如需指引，請參閱 [Cortana 分析程序中規劃您的環境](machine-learning-data-science-plan-your-environment.md) 和 [的 Azure Machine Learning 中進階分析案例](machine-learning-data-science-plan-sample-scenarios.md)。  

## P2. 設定分析環境 

Cortana 分析程序的分析環境包含數個元件： 

- **資料工作區** 預備資料的分析和模型，其中 
-  **處理基礎結構** 前置處理、 瀏覽，和將資料模型化
-  **執行階段基礎結構** 化分析模型和執行智慧型用戶端應用程式，使用模型。  

必須設定的分析基礎結構通常是環境中不屬於核心作業系統的部分。 但它通常會運用來自企業內多個系統的資料，以及來自公司外部的資料。 分析基礎結構可以純雲端架構或內部部署設定，或是兩者的混合。 如需選項，請參閱 [設定 Cortana 分析程序中使用的資料科學環境](machine-learning-data-science-environment-setup.md)。

## 分析步驟：  

## 1.將資料嵌入分析環境中 

第一個步驟是從各種來源 (企業內部或外部) 將相關資料導入預計用來處理資料的分析環境中。  **格式** 的資料來源，可能會有所不同目的地所需的格式。 因此，嵌入工具也可能必須完成某些資料轉換。 如需選項，請參閱 [資料載入至儲存體環境以供分析](machine-learning-data-science-ingest-data.md)

除了初始擷取資料時，許多智慧型應用程式都必須定期重新整理資料，持續學習程序的一部分。 這可以經由設定 **資料管線** 或工作流程。 這構成了程序中反覆執行的部分，包括會重新建置和重新評估部署解決方案的智慧型應用程式所使用的分析模型。 例如，請參閱 [將資料從內部部署 SQL server 移至 Azure Data Factory 與 SQL Azure](machine-learning-data-science-move-sql-azure-adf.md)。


## 2.瀏覽和前置處理資料 

下一個步驟是調查，以取得更深入了解資料及其 **摘要統計資料** , ，關聯性，並使用這類技術 **視覺化**。 這也是其中的問題 **資料品質** 及完整性，例如遺漏值、 資料類型不相符和不一致的資料關聯的處理。 必須先使用前置處理轉換來清理原始資料，才能進一步執行分析和模型化。 如需說明，請參閱 [工作以準備資料增強機器學習](machine-learning-data-science-prepare-data.md)。


## 3.開發特性 

資料科學家，扮演網域專家必須識別的功能所擷取的資料集的主要屬性，最適合用來預測計劃期間所識別重要商務變數。 這些新特性可以衍生自現有資料，或可能需要收集其他資料。 這個程序稱為 **特性工程設計** 建置有效的預測分析系統的主要步驟的其中一個。 要執行此步驟，必須有創意地結合網域知識和獲取自資料瀏覽步驟的深入資訊。 如需指引，請參閱 [特性工程設計 Cortana 分析程序中的](machine-learning-data-science-create-features.md)。


## 4.建立預測模型 

資料科學家會建立分析模型，使用已清理和特性化的資料來預測規劃步驟中定義的商業需求所識別的重要變數。 機器學習系統支援多個 **模型演算法** ，適用於各種情況。 如需指引，請參閱 [如何選擇演算法為 Microsoft Azure Machine Learning](machine-learning-algorithm-choice,md)。

資料科學家必須選擇最適合其預測工作的模型，而來自多個模型的結果也經常必須結合以取得最佳結果。 模型化的輸入資料通常隨機分成三個部分：

- 訓練資料集、 
- 驗證資料集 
- 測試資料集 

使用建立模型 **訓練資料集**。 執行模型，並測量預測錯誤，以選取最佳模型 (與微調的參數) 的組合 **驗證資料集**。 最後 **測試資料集** 用來評估所選的模型上獨立的資料不是用來訓練或驗證模型的效能。  如需程序，請參閱 [如何評估 Azure Machine Learning 中的模型效能](machine-learning-evaluate-model-performance.md)。


## 5.部署和取用模型 

一旦我們擁有一組執行的模型，可以是 **運作** 取用其他應用程式。 根據商務需求，進行了預測處於 **即時** 或 **批次** 為基礎。 若要能運作，模型必須以公開 **開啟 API 介面** ，輕鬆地取用從各種應用程式這類線上網站、 試算表、 儀表板或各種商務和後端應用程式。 請參閱 [部署 Azure Machine Learning web 服務](machine-learning-publish-a-machine-learning-web-service.md)。

## 摘要和後續步驟

 [Cortana 分析程序](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) 會模型化為一系列的反覆執行的步驟， **提供指引** 建置智慧型的應用程式中使用進階的分析所需的工作。 每個步驟也會提供詳細資訊，說明如何使用各種不同的 Microsoft 技術來完成工作。 

雖然 CAP 並未規定特定類型的 **文件** 成品，最好記錄的資料探索、 模型和評估結果，並將儲存相關的程式碼分析可以逐一查看時所需。 這也可讓您在處理牽涉到類似資料和預測工作的其他應用程式時，能夠重複使用分析工作。

完整的端對端逐步解說會示範的程序中的所有步驟 **特定案例** 也會提供。 請參閱 [作用中的 Cortana 分析程序: 使用 SQL Server](machine-learning-data-science-process-sql-walkthrough.md) 和 [作用中的 Cortana 分析程序: 使用 HDInsight Hadoop 叢集](machine-learning-data-science-process-hive-walkthrough.md)。

 

