<properties
    pageTitle="將資料匯入 Machine Learning Studio | Microsoft Azure"
    description="如何從各種資料來源將資料匯入 Azure Machine Learning Studio。 了解支援的資料類型和資料格式。"
    keywords="import data,data format,data types,data sources,training data"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2015"
    ms.author="garye;bradsev" />


# 從各種資料來源將訓練資料匯入 Azure Machine Learning Studio

## 簡介

若要在 Machine Learning Studio 中使用您自己的資料來開發和訓練預測性分析方案，您可以： 

- 將資料從上傳 **本機檔案** 事先從硬碟來建立工作區中的資料集模組。  
- 從一種存取資料 **線上資料來源** 您的實驗執行時使用 [讀取器] [讀取器] 模組。 
- 使用資料，從另一個 Azure 機器學習實驗儲存為 **資料集**。 

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

每個選項都在上方選單的其中一個主題裡說明。 這些主題會示範如何從各種資料來源匯入資料以用於 Machine Learning Studio。 

> [AZURE.NOTE] 有提供可用於此用途的 Machine Learning Studio 中的許多範例資料集。 如需這些資訊，請參閱 [Azure Machine Learning Studio 中使用範例資料集](machine-learning-use-sample-datasets.md))。

這個簡介主題也會示範如何備妥資料以在 Machine Learning Studio 中使用，並描述支援的資料格式和資料類型。 

> [AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## 備妥資料以在 Azure Machine Learning Studio 中使用
Machine Learning Studio 是專為與矩形或表格式資料搭配使用而設計，例如分隔的文字資料，或資料庫的結構化資料，雖然在某些情況下可能會使用非矩形資料。

如果您的資料相對乾淨是最佳狀況。 也就是說，在您將資料上傳至您的實驗之前，您要關注如不具引號的字串的問題。

但是，Machine Learning Studio 中有模組可讓您在實驗中進行一些資料操作。 依據您使用的機器學習演算法，您可能需要決定如何處理資料結構問題，例如遺漏值和疏鬆資料，有模組可以協助處理。 查看 **資料轉換** 執行這些功能的模組的模組調色盤的區段。

您可以在實驗的任何位置，檢視或下載模組產生的資料，方法是以滑鼠右鍵按一下輸出連接埠。 視模組而定，可能會提供不同的下載選項，或者可以在 Machine Learning Studio 中的網頁瀏覽器檢視資料。

## 支援的資料格式和資料類型

視您用來匯入資料的機制及其來源而定，您可以將一些資料類型匯入您的實驗：

- 純文字 (.txt)
- 逗號分隔值 (CSV)，具有標頭 (.csv) 或不具標頭 (.nh.csv)
- 定位鍵分隔值 (TSV)，具有標頭 (.tsv) 或不具標頭 (.nh.tsv)
- Hive 資料表
- SQL 資料庫資料表
- OData 值
- SVMLight 資料 (.svmlight) (請參閱 [SVMLight 定義](http://svmlight.joachims.org/) 格式資訊)
- 屬性關係檔案格式 (ARFF) 資料 (.arff) (請參閱 [ARFF 定義](http://weka.wikispaces.com/ARFF) 格式資訊)
- Zip 檔案 (.zip)
- R 物件或工作區檔案 (.RData)

如果您以 ARFF 格式匯入內含中繼資料的資料，Machine Learning Studio 會使用此中繼資料定義每個資料行的標題和資料類型。

如果您以 TSV 或 CSV 格式匯入不包含此中繼資料的資料，Machine Learning Studio 會透過取樣資料來推斷每個資料行的資料類型。 如果資料也沒有資料行標題，Machine Learning Studio 會提供預設名稱。

您可以明確指定，或變更標題，使用 [中繼資料編輯器] [中繼資料編輯器] 的資料行資料類型。

下列 **資料型別** Machine Learning Studio 可辨識:

- String
- Integer
- 兩倍
- Boolean
- DateTime
- TimeSpan

Machine Learning Studio 使用名為的內部資料類型 ***資料表*** 模組之間傳遞資料。 您可以明確地將使用 [轉換成資料集] 的 [轉換-dataset] 模組的資料表格格式將資料轉換。

接受「資料表格」以外格式的任何模組，會在將資料傳遞至下一個模組之前，無訊息地將資料轉換為「資料表格」。

您可以視需要使用其他轉換模組，將「資料表格」格式轉換回 CSV、TSV、ARFF 或 SVMLight 格式。
查看 **資料格式轉換** 執行這些功能的模組的模組調色盤的區段。



<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

