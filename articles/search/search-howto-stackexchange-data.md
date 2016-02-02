<properties
    pageTitle="如何使用 Azure 搜尋服務搜尋 StackExchange 資料 | Microsoft Azure | 雲端託管搜尋服務"
    description="了解如何使用 Azure 搜尋服務 (Microsoft Azure 上的雲端託管搜尋服務) 執行 REST 搜尋。"
    services="search"
    documentationCenter=""
    authors="liamca"
    manager="pablocas"
    editor=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="11/04/2015"
    ms.author="liamca"/>


# 如何使用 Azure 搜尋服務來搜尋 StackExchange 資料

這篇文章是強調一些可透過完成的核心全文檢索搜尋功能的逐步解說 [Azure 搜尋](https://azure.microsoft.com/services/search/)。 它會利用堆疊 Exchange 所做的資料 [可用](https://archive.org/details/stackexchange) Creative Commons 使用量，以下列 [attribution](http://blog.stackoverflow.com/2009/06/attribution-required/)。

## 開始使用

為了強調某些功能，我已建立可供您使用的 Azure 搜尋服務索引，其中包含從 2015 年 10 月 14 日起來自 Programmer StackExchange 的資料。 注意：我稍後也將示範如何使用此資料建立您自己的 Azure 搜尋服務索引。

讓我們從極為簡單的全文檢索搜尋查詢開始，使用者可能會在其中輸入 "azure" 這個字來尋找任何有關 Azure 的 StackExchange 文章。 嘗試按一下此連結，即可觀看實作示範：

> [http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure](http://fiddle.jshell.net/liamca/gkvfLe6s/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure)

在此範例中，我們只需傳遞 "azure" 這個字做為搜尋參數並顯示傳回的 JSON 格式化結果。 以下是一些您可以嘗試的其他查詢範例。

-   `面向`: 當使用者搜尋的資料集時，能夠篩選的資料是幫助他們瀏覽結果的好方法。 若要這樣做，您通常會從顯示給使用者的一組類別 (facet) 著手。 以下是我們可能想利用的一些 facet 範例：
  - **標記**：許多問題都有與其相關聯的標記，可讓使用者向下鑽研至特定類別
  - **日期**：使用者可能只想查看在特定時間範圍內提問或回答的問題
  - **使用者**: 您可能想要查看或限制特定使用者的結果
在此範例中我們將會搜尋"azure"，但傳回 facet 計數 tagsCollection 和 acceptedAnswerDisplayName 的使用者名稱。

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure%26facet=tagsCollection%26facet=acceptedAnswerDisplayName>

-   `篩選`: 使用者選擇的 facet 之後，您將然後想要執行另一個搜尋，但將篩選以限制 facet 值的結果。 例如，搜尋 "Azure" 但將結果限制為有以遞減順序依 viewCount 排序之 “architecture” 標記的地方：

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure%26$filter=tagsCollection/any(t:+t+eq+'architecture')%26$orderby=viewCount+desc>

-   `拼字錯誤`: 我們新的 (預覽) 支援 [Lucene 查詢運算式](https://msdn.microsoft.com/library/mt589323.aspx) 也可讓您執行一些非常複雜的查詢，例如模糊比對的結果，並限制搜尋特定欄位。 此範例會搜尋標題欄位中的 “visualize” 這個字，但是 ~ 表示模糊比對，這表示也會傳回 visualise 和 visualizing 等結果。

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28&search%3Dtitle%3Avisualise~%26querytype%3Dfull%26searchMode%3Dall%26%24select%3Dtitle>

-   `評分和微調`: [評分設定檔](https://msdn.microsoft.com/library/azure/dn798928.aspx) 會協助您調整搜尋服務所傳回的結果相當有幫助。 事實上，現在我們也可以使用 [Lucene 查詢運算式](https://msdn.microsoft.com/library/mt589323.aspx) 套用計分，用於個別欄位和即時的詞彙。 例如，如果我們想要在標題欄位中搜尋 “visualize” 或 “chart” 等字，還提供更多加權給其中有 “chart” 這個字的項目，我們可以這麼做：

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26search%3Dtitle%3Avisualise+OR+title%3Achart%5E3+%26querytype%3Dfull%26searchMode%3Dall%26%24select%3Dtitle>

  此資料集中有許多其他欄位可用來讓使用者的相關結果增多。 例如，我可以使用：

  - **量級**評分，將其用於 answerCount、commentCount、favoriteCount 和 viewCount 等數值欄位，以增加搜尋結果 (如果搜尋結果的計數恰巧很高)。
  - **有效性**評分，將其用於 creationDate 和 lastActivityDate 等 DateTime 欄位，讓近期建立或使用中的項目增加
  - **欄位權數**，指出是否在問題本文中找到搜尋文字，然而這比在答案中找到時還要相關。

其他您可能想要使用的項目包括：

-   [`建議`] (https://msdn.microsoft.com/library/azure/mt131377.aspx): 當使用者輸入在 [搜尋] 方塊時，會很方便用於自動完成等標題、 標記和使用者名稱的欄位。

-   `建議`: 通常，您需要像 Apache Mahout 工具或 Azure Machine Learning 可協助您建立的建議，讓您顯示類似問題的使用者可能感興趣的檢視，但幸運的是此資料集已經有一些建議。

歡迎隨時使用此 JSFiddle 頁面，嘗試不同類型的查詢。 如果您想要深入了解如何建立此索引，請繼續閱讀。 可自由也送給我直接在我的 twitter 帳戶 [@liamca](https://twitter.com/liamca)。

## 如何建立此 Azure 搜尋服務索引？

Brent 已藉由示範如何將資料預備至 SQL Database，進行了很多困難的工作。 如果您想要自行逐步完成此程序的暫存資料，請瞧瞧他 [教學課程](http://www.brentozar.com/archive/2014/01/how-to-query-the-stackexchange-databases/)。 否則，您可以只運用我從 2015 年 10 月 15 日起以一些資料預先填入的 Azure SQL Database，或者可以嘗試我所建立的 Azure 搜尋服務索引。 詳細資料如下面的「從預備的 Azure SQL Database 匯入資料」一節所述。  
我未超過 Brent 所述的作法就是要在我的 Azure SQL 資料庫，將會使用所建立的檢視 [Azure 搜尋服務索引子](https://msdn.microsoft.com/library/azure/dn946891.aspx) 編目並擷取一組資料表，我想要使用的資料。 以下是此檢視的定義方式。

    CREATE VIEW StackExchangeSearchData as
    SELECT
          PQ.[Id]
          ,PQ.[AcceptedAnswerId]
          ,PQ.[AnswerCount]
          ,PQ.[Body]
          ,PQ.[ClosedDate]
          ,PQ.[CommentCount]
          ,PQ.[CommunityOwnedDate]
          ,PQ.[CreationDate]
          ,PQ.[FavoriteCount]
          ,PQ.[LastActivityDate]
          ,PQ.[LastEditDate]
          ,PQ.[LastEditorDisplayName]
          ,PUQ.DisplayName OwnerDisplayName
          ,PUQ.Reputation OwnerReputation
          ,PQ.[Score]
          ,reverse(REPLACE(LTRIM(REPLACE(reverse(REPLACE(REPLACE (PQ.[Tags], '>' , '],' ), '<' , '[' )), ISNULL(',', '0'), ' ')), ' ', ISNULL(',', '0'))) TagsCollection
          ,PQ.[Title]
          ,PQ.[ViewCount]
          ,PA.[Body] AcceptedAnswerBody
          ,PA.[Score] AcceptedAnswerScore
          ,PUQ.DisplayName AcceptedAnswerDisplayName
          ,PUQ.Reputation AcceptedAnswerReputation
          ,PA.[FavoriteCount] AcceptedAnswerFavoriteCount
          ,PL.[RelatedPostId]
      FROM [StackExchange].[dbo].[Posts] PQ
      LEFT OUTER JOIN [StackExchange].[dbo].[Posts] PA
      and PQ.AcceptedAnswerId = PA.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[PostLinks] PL
      on PQ.Id = PL.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[Users] PUQ
      on PQ.OwnerUserId = PUQ.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[Users] PUA
      on PA.[OwnerUserId] = PUA.Id
      WHERE PQ.PostTypeId = 1

一旦這麼做，您便可以使用 [Azure 傳統入口網站](https://portal.azure.com) 以 「 匯入資料 」，從上述的 Azure SQL 檢視接著會建立 Azure 搜尋索引的結構描述為基礎的檢視中的欄位。 如果您想要使用我所預備的 Azure SQL Database，以下是您可以使用的唯讀連接字串：

    Server=tcp:azs-playground.database.windows.net,1433;Database=StackExchange;User ID=reader@azs-playground;
    Password=EdrERBt3j6mZDP;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;



