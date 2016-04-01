<properties 
    pageTitle="Azure 搜尋服務最新更新中的新功能 | Microsoft Azure | 雲端託管搜尋服務" 
    description="描述服務之最新更新內容的 Azure 搜尋服務版本資訊" 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="mblythe" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="11/04/2015" 
    ms.author="heidist"/>

#Azure 搜尋服務的最新更新事項#

Azure 搜尋服務是 Microsoft Azure 上的雲端託管搜尋服務。 它已正式推出，提供 99.9%可用性服務等級協定 (SLA) 的支援設定 [API 2015-02-28 版本](https://msdn.microsoft.com/library/azure/dn798935.aspx)。

##各版本的主要功能以及發行日期

功能會釋放個別或共同發佈各種透過 [REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx), ，[.NET SDK](http://go.microsoft.com/fwlink/?LinkId=528216), ，或在服務儀表板 [Azure 傳統入口網站](https://portal.azure.com)。

.NET 程式庫和 REST API 都有多種版本。 如果我們推出新的功能，舊的 API 仍會保持正常運作。 您可以瀏覽 [搜尋服務版本設定](https://msdn.microsoft.com/library/azure/dn864560.aspx) 若要深入了解我們的版本控制原則。


##Api 版本 2015-02-28 預覽
**發行日期：2015 年 9 月 11 日**

此版本新加入 [Lucene 查詢語法支援](https://msdn.microsoft.com/library/azure/mt589323.aspx) 來處理 [預覽版本的 Azure 搜尋服務 REST API](search-api-2015-02-28-preview.md)。 若要使用新語法，您必須在搜尋文件作業中指定 `queryType`。

此外，這兩個下列功能從預覽版本中，會轉換，而且現在都屬於 MSDN 上的官方 API:
- [自然語言處理器](search-language-support.md)
- 搜尋、建議和查閱查詢中的 POST

##.NET SDK 0.10.0 預覽版本
**發行日期：2015 年 8 月**

這是第二個重複推出的 .NET 用戶端程式庫，Microsoft.Azure.Search.dll。 此版本新增透過 .NET 類別建立、管理和使用索引子的支援。 此外，Azure SQL 索引子開始支援索引地理位置點。

- [索引子類別](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx)
- [資料來源類別](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.datasource.aspx)

##.NET SDK 0.9.6 預覽版本
**發行日期：2015 年 3 月 5日**

此為第一個 Azure 搜尋服務專用的 .NET SDK 公用版本。 此版本包含用戶端程式庫 Microsoft.Azure.Search.dll，並且具有兩個命名空間：

- [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx)
- [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)

不包含：

- [索引子](http://go.microsoft.com/fwlink/p/?LinkId=528173) （此功能不再被排除在 0.10.0-預覽版本）
- [管理 REST API](https://msdn.microsoft.com/library/azure/dn832684.aspx)
- [2015-02-28 預覽](search-api-2015-02-28-Preview.md) 功能 (目前僅供預覽的功能包含 Microsoft 自然語言處理器和 `moreLikeThis`)。

請瀏覽 [如何在.NET 中使用 Azure 搜尋服務](http://go.microsoft.com/fwlink/p/?LinkId=528088) 如需安裝和使用 SDK 的指引。

##Api 版本 2015-02-28 預覽
**發行日期：2015年 4 月 22 日**

- 索引子目前支援 fieldMapping 建構，當實際欄位名稱和外部資料庫及 Azure 搜尋服務索引不同時，會提供欄位指派。 請參閱 [索引子](search-api-indexers-2015-02-28-Preview.md) 的 `2015-02-28-preview` 索引子文件版本...

- 此外，在預覽更新中，索引子現在也支援欄位類型轉換，當原始欄位呈現 JSON 陣列時，您可以在 SQL 資料表中將字串欄位對應到搜尋索引中的字串集合欄位。

**發行日期：2015 年 3 月 5日**

- [Microsoft 自然語言處理器](search-api-2015-02-28-Preview.md) 將支援多個語言和詞幹擴充 Office 和 Bing 支援的所有語言的詞幹分析。

- [moreLikeThis =](search-api-2015-02-28-Preview.md) exclusive 的互斥是搜尋參數， `search=`, ，後者會觸發替代的查詢執行路徑。 `moreLikeThis=` 會透過比較可搜尋的欄位，尋找與指定文件相似的文件，而不是根據輸入的搜尋詞彙對 `search=` 進行全文搜尋。

##Api 版本 2015-02-28
**發行日期：2015 年 3 月 5日**

- [索引子](http://go.microsoft.com/fwlink/p/?LinkID=528210) 是大幅簡化了從 Azure SQL Database、 Azure DocumentDB 以及 Azure Vm 上的 SQL Server 資料來源編製索引的新功能。

- [建議工具](https://msdn.microsoft.com/library/azure/dn798936.aspx) 取代上一個實作 （僅比對的前置詞） 的限制較多的預先輸入查詢支援，藉由新增中置比對的支援。 此實作可以在詞彙的任何地方找到符合項目，並且支援模糊比對。

- [標記提升](http://go.microsoft.com/fwlink/p/?LinkId=528212) 啟用新的評分設定檔案例。 特別的是，此功能會運用保存的資料 (如購物喜好設定)，讓您可以依據個人化資訊提升個別使用者的搜尋結果品質。 

請瀏覽 [Azure 搜尋服務目前已正式推出](http://go.microsoft.com/fwlink/p/?LinkId=528211) Azure 部落格專門討論所有這些功能的服務公告。

##Api 版本 2014-10-20 預覽版本
**發行日期：2014 年 11 月，2015 年 1 月**

- [Lucene 語言分析器](search-api-2014-10-20-preview.md) 加入為 lucene 發布的自訂語言分析器提供多語言支援。 

- 為了建置索引，包括計分設定檔中已導入工具支援 [Azure 傳統入口網站](https://portal.azure.com)。

##Api 版本 2014-07-31 預覽版本
**發行日期：2014 年 8 月 21日**

此版本是 Azure 搜尋服務的公用預覽版本，提供以下核心功能：

- 索引及文件作業專用的 REST API。 在 2015-02-28 版本中，仍保有此 API 版本的大部分內容。 版本的說明文件 `2014-07-31-Preview` 位於 [Azure 搜尋服務 REST API 版本 2014年-07-31](search-api-2014-07-31-preview.md)。

- 調整搜尋結果專用的評分設定檔。 評分設定檔可新增用於運算搜尋分數的準則。 此功能的文件，請參閱 [Azure 搜尋服務評分設定檔 REST API 版本 2014年-07-31](search-api-scoring-profiles-2014-07-31-preview.md)。

- 地理空間支援一開始就已推出，透過 Azure 搜尋服務最早包含的 `Edm.GeographyPoint` 資料類型提供服務。

- 在預覽版本中的佈建 [Azure 傳統入口網站](https://portal.azure.com )。 Azure 搜尋服務是新版入口網站所提供的少數服務中的其中一項服務。

##管理 API 版本 2015-08-19
**發行日期：2015 年 9 月 11 日**

[管理 REST API](https://msdn.microsoft.com/library/azure/dn832684.aspx) 包含下列更新。

- checkNameAvailability 會檢查指定的服務名稱是否已在使用中。
- 複本範圍先前是 1-6，而現在是 1-12。
- SKU 屬性已從屬性包移至服務裝載最上層。
- 建立搜尋服務作業的回應主體已更新，以容納 SKU 設定的重新配置。

##管理 Api 版本 2015-02-28
**發行日期：2015 年 3 月 5日**

[管理 REST API](search-management-api-2014-02-28.md) 屬於正式發行的 Azure 搜尋服務管理 api 的第一個版本。 這個版本與舊版預覽的功能沒有不同。

##管理 api-版本 2014-07-31-預覽
**發行日期：2014 年 10 月**

預覽版本的 [管理 REST API](search-management-api-2014-07-31-preview.md) 起，支援以程式設計方式管理服務。 REST API 服務單獨建自己的版本。


 

