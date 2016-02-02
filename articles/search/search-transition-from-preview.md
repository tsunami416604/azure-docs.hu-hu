<properties 
    pageTitle="由預覽 api-version=2014* 轉換至 api-version=2015* | Microsoft Azure | 雲端託管搜尋服務" 
    description="了解重大變更，以及要如何將寫在 2014-07-31-Preview 或 2014-10-20-Preview 上的程式碼移轉到 Azure 搜尋服務 (API 版本 = 2015-02-28)。" 
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


# 從預覽 API 版本 = 2014* 轉換為 API 版本 = 2015*

Azure 搜尋服務裝載在雲端上 Microsoft Azure 搜尋服務。 下列指引供客戶在 Azure 搜尋服務的預覽版本建置自訂應用程式，及目前移轉到正式運作的版本所用 (2015-02-28)。

身為預覽客戶，您可能有這些舊的預覽版本之一：

- 2014-07-31-Preview
- 2014-10-20-Preview

既然 Azure 搜尋服務已正式推出，我們鼓勵轉換到較新的版本：2015-02-28 是 Azure 搜尋服務正式運作版本的官方 API 版本。 這個版本記載於 [MSDN](https://msdn.microsoft.com/library/azure/dn798933.aspx)。

我們還推出下一步預覽版本中， [2015年-02-28 預覽](search-api-2015-02-28-preview.md), ，引入還在開發中的功能。 您可以透過在預覽 API 提供意見反應 [Azure 搜尋服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch) 或我們 [意見反應頁面](http://feedback.azure.com/forums/263029-azure-search)。

### 移轉的檢查清單

- 檢查重大變更以判斷您的解決方案是否受到影響。
- 若要將 API 版本直接改 `2015年-02-28` 對於鎖定的版本。 此版本是在 SLA 下。 如果您遇到問題，則可更快解決。
- 建置、部署、測試。 就搜尋行為而言，您仍有 100% 同等的服務，除了重大變更之外。
- 推出產品。
- 請評估新的功能供日後功能採用。 直接改到 2015年-02-28 預覽如果您想要測試磁碟機 Microsoft 自然語言處理器或 `morelikethis`。

## API 版本 = 2015* 的重大變更

此 API 的初始版本包含自動完成或預先輸入建議的功能。 雖然很實用，但這受限制於只能比對前置詞，在搜尋詞彙中的第一個字元搜尋，而無法支援欄位中其他地方的比對。 此實作曾為布林值屬性，稱為 `建議` ，您會設定為 `true` 如果您想要讓比對特定欄位的前置詞。

最初的實作現在已經棄用，改用新 `建議工具` 建構，定義於 [索引](https://msdn.microsoft.com/library/azure/dn798941.aspx) 提供字間和模糊比對的功能。 顧名思義，字間和模糊比對提供更廣泛的比對能力。 字間比對包含前置詞，它仍然會比對開頭的字元，但延伸比對到包含字串的剩餘部分。

我們選擇停用先前的實作 (布林屬性)，代表這在 2015 的任一版本中都完全無法使用，也沒有回溯相容性，以避免客戶建置較新解決方案時意外地採用它。 如果您使用 `2015年-02-28` 或 `2015年-02-28 預覽` 您必須使用新 `建議工具` 建構來啟用預先輸入查詢。

## 編譯現有程式碼為新版本

建議屬性為僅有的重大變更。 如果您未使用這個屬性可以擠 `api 版本` 從 `2014年-07-31-預覽` 或 `2014年-10-20-preview` 與 `2015年-02-28`, ，然後重建並重新部署。 此應用程式會像之前一樣運作。

實作建議的自訂應用程式應執行下列動作：

1. 更新所有 Nuget 封裝。
1. 若要將 api 版本直接改 `2015年-02-28`。 如果您正使用下列程式碼範例，此 API 版本就位在  **AzureSearchHelper** 類別中。
1. 刪除 `建議 = {true | false}` 從 JSON 結構描述定義索引的屬性。
1. 索引底部加入建構 `建議工具` (如所示 [之後](#after) 一節)。
1. 請確認您可以發佈到服務 (您可能需要重新命名索引以避免命名衝突)。
1. 重新建置此解決方案，然後部署到測試環境中。
1. 執行所有測試案例，確保解決方案如預期執行。
1. 推出產品。

從程式碼範例 [codeplex 上的 Adventure Works 範例](https://azuresearchadventureworksdemo.codeplex.com/) 具有原始 `建議` 實作。 您可能會想要使用此範例實際在範例程式碼上進行程式碼移轉。

在下一節中，我們將告訴 [之前](#before) 和 [之後](#after) 建議的實作。 您可以取代 **createcatalogindex ()** 方法中的版本 [之後](#after) 區段，然後建置和部署解決方案以嘗試新功能。

<a name="before"></a>
### 先前

如您所見， `建議` 為每個欄位上設定的布林屬性。 刪除所有 `建議` 屬性。

        private static void CreateCatalogIndex()
        {
            var definition = new 
            {
                Name = "catalog",
                Fields = new[] 
                { 
                    new { Name = "productID",        Type = "Edm.String",         Key = true,  Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "name",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = true,  Facetable = false, Retrievable = true,  Suggestions = true  },
                    new { Name = "productNumber",    Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = true  },
                    new { Name = "color",            Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "standardCost",     Type = "Edm.Double",         Key = false, Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "listPrice",        Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "size",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "weight",           Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "sellStartDate",    Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false, Suggestions = false },
                    new { Name = "sellEndDate",      Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false, Suggestions = false },
                    new { Name = "discontinuedDate", Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "categoryName",     Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = true  },
                    new { Name = "modelName",        Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = true  },
                    new { Name = "description",      Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false }
                }
            };

<a name="after"></a>
### 在

移轉的結構描述定義會省略 `建議` 屬性，並將 `建議工具` 建構底部。

        private static void CreateCatalogIndex()
        {
            var definition = new 
            {
                Name = "catalog",
                Fields = new[] 
                { 
                    new { Name = "productID",        Type = "Edm.String",         Key = true,  Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "name",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = true,  Facetable = false, Retrievable = true },
                    new { Name = "productNumber",    Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "color",            Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "standardCost",     Type = "Edm.Double",         Key = false, Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "listPrice",        Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "size",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "weight",           Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "sellStartDate",    Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false },
                    new { Name = "sellEndDate",      Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false },
                    new { Name = "discontinuedDate", Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "categoryName",     Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "modelName",        Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "description",      Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = false, Retrievable = true }
                },
                suggesters = new[]
                {
                new {
                    name = "sg",
                    searchMode = "analyzingInfixMatching",
                    sourceFields = new []{"name", "productNumber", "categoryName", "modelName", "description"}
                    }
                 }
            };

## 評估新功能和方法

當您已編譯解決方案為新的版本並確認如預期運作後，您可使用下列連結以知悉新的功能。

- [Azure 搜尋服務已正式推出 (部落格文章)](http://go.microsoft.com/fwlink/p/?LinkId=528211)
- [什麼是 Azure 搜尋服務新的最新的更新](search-latest-updates.md)
- [什麼是 Azure 搜尋服務?](search-what-is-azure-search.md)

## 取得說明

API 版本 `2015年-02-28` 是在 SLA 下。 使用支援選項和連結上 [本頁](../support/options/) 支援票證。






