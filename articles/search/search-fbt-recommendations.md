<properties
   pageTitle="Azure 搜尋 "Frequently Bought Together" recommendations | Microsoft Azure | Apache Mahout | Azure Machine Learning"
   description="Azure 搜尋服務應用程式使用 Apache Mahout 或 Azure Machine Learning 中加入氣、 新的您，或者也購買了建議的範例程式碼"
   services="search"
   documentationCenter=""
   authors="liamca"
   manager="pablocas"
   editor=""
   tags="machine learning"/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="11/12/2015"
   ms.author="liamca"/>

# Azure Search「人氣組合」建議

如果您已在線上購買，您可能會注意到有提供額外商品或服務供您考量的「人氣組合」、「購買此項目的客戶也購買了」或甚至是「您適用的新項目」清單。 一般而言，此資料是線上零售商根據許多客戶的購買記錄或只是您專屬的客戶喜好設定資料編譯而成。 能夠透過搜尋提供相關的消費者導向建議，對商用應用程式和提供多媒體內容或資訊的網站來說，都是一項強大的附加功能。

在 Azure Search 應用程式中，您可以撰寫一些額外的程式碼來導入資料和用來識別資料中模式和關係的服務，然後將其轉換成與您使用者相關的建議，藉此方式實作建議。 

[這個範例會示範如何](https://github.com/liamca/azure-search-recommendations), ，並使用 [Apache Mahout]() 建議引擎。

在本文的其餘部分，我將引導您使用與影片出租相關的範例資料，完成新增「人氣出租組合」建議的範例程式碼。

![1](./media/search-fbt-recommendations/product_recommendations.png)

## 什麼是建議？

*建議* 是一種技術，從目錄中呈現更多項目根據現有的搜尋用來建議項目，並改進轉換的使用者活動 （例如網頁記錄）。  

建議引擎通常定型使用過去的客戶活動，或藉由直接從數位存放區中收集資料。 在此範例中，我使用 Apache Mahout 編譯建議資料。

一般建議如下 ︰ 
- 經常 Bought 在一起 ︰ 這購買的客戶也購買了，
- 客戶對項目的建議 ︰ 客戶也購買了，像是

## 建立 Azure Search 索引

- 開啟 AzureSearchMovieRecommendations.sln 解決方案，然後將 ImportAzureSearchIndexData 設定為「預設專案」。  
- 開啟 ImportAzureSearchIndexData 內的 Program.cs，然後將 SearchServiceName 和 SearchApiKey 更改成指向您的 Azure Search 服務。
- 從 http://grouplens.org/datasets/hetrec-2011/ 下載 hetrec2011-movielens-2k.zip，並將檔案 Movies.dat 與 user_ratedmovies.dat 複製到 \ImportAzureSearchIndexData\data
- 執行專案來建立索引並載入影片資料。 
- 最後，應用程式將會執行測試搜尋。

## 建立一個簡單的 HTML 應用程式來搜尋影片

已完成的 JavaScript web 應用程式可讓您查詢 Azure Search 索引，位於 ︰ 
\WebSite\starter-template-complete

如果您想要從頭逐步示範，可以在這裡找到原始 CSS:
\WebSite\starter-template

開啟 \WebSite\starter-template-complete 內的 search.js 檔案，然後以您的 Azure Search 服務詳細資料更新 apiKey 和 azureSearchService。

您應該能夠在 Chrome 之類的瀏覽器中開啟此檔案，以在現在透過在搜尋方塊中輸入來檢視影片。

## 使用 Mahout 來執行建立建議的命令

- 將 data\movie_usage.txt 檔案上傳至「Azure Blob 儲存體」。 
- 建立 HDInsight 執行個體 (啟用「遠端桌面」)，並透過「遠端桌面」(可從「Azure 傳統入口網站」使用) 連線到電腦。
- 從 HDInsight 電腦，開啟「Hadoop 命令列」。
- 切換至 c:\apps\dist 底下的 Mahout bin 目錄。 地雷看起來像這樣，但您可以取得最新版的 Mahout
    C:\apps\dist\mahout-1.0.0.2.3.3.0-2992\bin
- 執行下列命令列，其中以您的「Azure 儲存體」詳細資料 (您放置 movie_usage.txt 檔案的位置) 取代 [CONTAINER] 和 [STORAGEACT]：

    mahout itemsimilarity -s SIMILARITY_COSINE --input "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/movie_usage.txt" --output "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/output/" --tempDir "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/temp" -m 5

這應該需要好幾分鐘才能完成，但是儲存體容器時，應該包含下列檔案，其中包含您的電影推薦 ︰
/movies/output/part-r-00000

此檔案有 3 個資料行：[有片的項目識別碼]、[此影片之相關建議的項目識別碼]、[相似度百分比]

## 將資料從 Mahout 匯入至 Azure Search

建立 Azure Search 索引的程式也建立了一個稱為 `Recommendations` 的欄位，此欄位屬於「集合」類型 (像是一組以逗號分隔的字串)。 我們會將在上一個步驟中建立的資料與此 Azure Search 索引合併。  

- 從 Visual Studio 解決方案 AzureSearchMovieRecommendations.sln，開啟 MahoutOutputLoader 內的 Program.cs。
- 以您的 Azure Search 服務詳細資料更新 SearchServiceName 和 SearchApiKey。
- 以您儲存 Mahout 產品建議檔時所針對的「Azure 儲存體」帳戶詳細資料更新 StorageApiKey 和 StorageAccountName。
- 執行應用程式來合併資料。
 
## 將建議視覺化
此時，您應該能夠回到 Web 應用程式並按一下任何影片來查看建議。

如果您想要看看當您按一下此影像時傳回建議的方式，請開啟 Search.js 並查看 openMovieDetails() 函式。

## 功勞

GroupLens (http://grouplens.org/datasets/hetrec-2011/) 所提供的資料

此頁面，此資料的授權的詳細資訊，請參閱 ︰ http://files.grouplens.org/datasets/hetrec2011/hetrec2011-movielens-readme.txt



