<properties 
    pageTitle="在 Cortana 分析程序中進行資料取樣" 
    description="如何在不同的儲存體環境中瀏覽資料。" 
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
    ms.date="10/20/2015" 
    ms.author="fashah;garye;bradsev" /> 

#<a name="heading"></a>在 Cortana 分析程序中進行資料取樣

這 **功能表** 主題連結，說明如何從不同的儲存體環境中取樣資料。 此工作是 Cortana 分析程序 (CAP) 中的一個步驟。

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

## 簡介

本文件說明為儲存於 Azure blob 儲存體中的資料進行取樣，您可以利用程式設計方式下載它，然後使用 Python 程式碼範例來對它進行取樣。 執行此動作的步驟如下：

本文件涵蓋如何對在 Cortana 分析程序中通常使用三個位置所儲存的資料進行取樣：

- **Azure blob 容器資料** 取樣方式以程式設計方式下載它，然後對它的 Python 程式碼範例進行取樣。
- **SQL Server 資料** 使用 SQL 和 Python 程式設計語言進行取樣。 
- **Hive 資料表資料** 取樣使用 Hive 查詢。


