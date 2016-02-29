<properties
   pageTitle="在 Azure 搜尋服務中建立多種語言的文件索引 | Microsoft Azure | 雲端託管搜尋服務"
   description=" Azure 搜尋服務支援 56 種語言，運用來自 Lucene 的語言分析器和來自 Microsoft 的自然語言處理技術。"
   services="search"
   documentationCenter=""
   authors="yahnoosh"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="10/19/2015"
   ms.author="jlembicz"/>

# 在 Azure 搜尋服務中建立多種語言的文件索引
> [AZURE.SELECTOR]
- [入口網站](search-language-support.md)
- [REST](https://msdn.microsoft.com/library/azure/dn879793.aspx)
- [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)

揭開語言分析器的強大功能，就如同在索引定義中的可搜尋欄位上設定一個屬性一樣簡單。 您現在可以在入口網站中執行這個步驟。

以下是 Azure 搜尋服務的 Azure 傳統入口網站刀鋒視窗螢幕擷取畫面，可供使用者定義索引結構描述。 在這個刀鋒視窗中，使用者可以建立所有欄位並設定各欄位的分析器屬性。

> [AZURE.NOTE] 您只能設定語言分析器期間欄位定義中，建立新的索引，從一開始時，或將新欄位加入至現有的索引。 確保您建立欄位時完全指定所有的屬性 (包括分析器)。 一旦定義欄位，您將無法編輯屬性或變更分析器類型。

1. 登入 [Azure 傳統入口網站](https://portal.azure.com) 並開啟您的搜尋服務的 [服務] 分頁。
2. 按一下 [ **新增索引** 頂端的 [服務儀表板啟動新的索引，或開啟現有的索引，在新的欄位，您將加入至現有的索引上設定分析器。
3. [欄位] 刀鋒視窗隨即出現，顯示可供您定義索引結構描述的選項，包括用於選擇語言分析器的 [分析器] 索引標籤。
4. 在欄位中，先是欄位定義中提供的名稱、 選擇資料類型，以及設定屬性，以將欄位標記為全文檢索搜尋，可以在搜尋結果中，可用在 facet 導覽結構中，擷取排序等等。 
5. 之後再繼續下一個欄位中，開啟 **分析器** ] 索引標籤。 
6. 捲動以尋找您要定義的欄位。 
7. 如果您沒有將此欄位標示為搜尋，請立即按一下此核取方塊將其標示為 [可搜尋]。
8. 按一下 [分析器] 區域以顯示可用的分析器清單。
9. 選擇您要使用的分析器。

![][1]
*若要選取分析器，請按一下 [欄位] 分頁上的 [分析] 索引標籤*

![][2]
*選取其中一個支援的分析器，針對每個欄位*

根據預設，所有可搜尋的欄位使用 [標準 Lucene 分析器](http://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) 其中具有語言中立性。 若要檢視支援的分析器的完整清單，請參閱 [Azure 搜尋服務中的語言支援](https://msdn.microsoft.com/library/azure/dn879793.aspx)。

一旦針對某個欄位選取語言分析器，它將用於該欄位的每個索引和搜尋要求。 使用不同的分析器針對多個欄位發出查詢時，查詢將由每個欄位的右分析器獨立處理。

許多 Web 和行動應用程式使用不同的語言來服務世界各地的使用者。 有可能藉由為每種支援語言建立一個欄位，以定義這類案例的索引。

![][3]
*索引定義，以支援每種語言的描述欄位*

如果已知發出查詢的代理程式的語言，範圍的搜尋要求特定的欄位，使用 **searchFields** 查詢參數。 下列查詢只會針對波蘭文描述發出：

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2015-02-28`

有時候不知道發出查詢之代理程式的語言，在此情況下，可以針對所有欄位同時發出查詢。 如有需要您可以使用定義結果以特定語言喜好設定 [評分設定檔](https://msdn.microsoft.com/library/azure/dn798928.aspx)。 在下面範例中，相對於波蘭文和法文的相符項目，在英文描述中找到的相符項目會有較高的評分：

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2015-02-28`

如果您的.NET 開發人員，請注意，您可以設定使用的語言分析器 [Azure 搜尋服務.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Search/0.13.0-preview)。 最新版本包含對 Microsoft 語言分析器的支援。

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png

