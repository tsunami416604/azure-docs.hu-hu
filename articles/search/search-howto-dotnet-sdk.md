<properties
   pageTitle="如何由 .NET 應用程式使用 Azure 搜尋服務 | Microsoft Azure | 雲端託管搜尋服務"
   description="如何從 .NET 應用程式使用 Azure 搜尋服務"
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="10/07/2015"
   ms.author="brjohnst"/>


# 如何從 .NET 應用程式使用 Azure 搜尋服務

這篇文章會逐步指引您學會如何使用 [Azure 搜尋服務.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)。 您可以透過 .NET SDK，利用 Azure 搜尋服務在應用程式中實作豐富的搜尋經驗。

## Azure 搜尋服務 SDK 中有哪些內容

SDK 包含用戶端程式庫 `Microsoft.Azure.Search`。 該程式庫可讓您管理索引、資料來源及索引子，以及更新和管理文件，還可以執行查詢，而且一律不需要處理 HTTP 和 JSON 的細節。

用戶端程式庫定義的類別，如 `索引`, ，`欄位`, ，和 `文件`, ，以及作業，例如 `Indexes.Create` 和 `Documents.Search` 上 `SearchServiceClient` 和 `SearchIndexClient` 類別。 這些類別可編成以下命名空間：

- [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx)
- [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)

目前的 Azure 搜尋服務 .NET SDK 版本是發行前版本。 如果您想要提供意見反應對我們併入第一個穩定版本中，請造訪我們 [意見反應頁面](http://feedback.azure.com/forums/263029-azure-search)。

.NET SDK 支援版本 `2015年-02-28` Azure 搜尋服務 REST api，記載於 [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx)。 此版本現在包含對 Microsoft 語言分析的支援。 較新的功能是 *不* 在此版本，例如支援 `moreLikeThis` 搜尋參數，都在 [預覽](search-api-2015-02-28-preview.md) 還提供在 SDK 中。 您可以查看 [搜尋服務版本設定](https://msdn.microsoft.com/library/azure/dn864560.aspx) 或 [Azure 搜尋服務的最新版本更新](search-latest-updates.md) 的任一功能的狀態更新。

此 SDK 中其他不支援的功能包括：

  - [管理作業](https://msdn.microsoft.com/library/azure/dn832684.aspx)。 管理作業包括佈建 Azure Search 服務和管理 API 金鑰。 未來會以個別的 Azure 搜尋服務 .NET Management SDK 支援這些作業。

## SDK 的需求

1. Visual Studio 2013 或更新版本。

2. 擁有 Azure Search 服務。 為了使用 SDK，您需要為服務命名，還需要一或多個 API 金鑰。 [入口網站中建立服務](search-create-service-portal.md) 協助您完成下列步驟。

3. 下載 Azure 搜尋服務.NET SDK [nuget](http://www.nuget.org/packages/Microsoft.Azure.Search) Visual Studio 中使用 [管理 NuGet 封裝]。 只要搜尋封裝名稱 `Microsoft.Azure.Search` NuGet.org 上。 務必選取 [包含發行前版本 (Include Prerelease)]，確保 SDK 的發行前版本會出現在搜尋結果中。

Azure 搜尋服務 .NET SDK 支援以 .NET Framework 4.0 或更新版為目標的應用程式，以及以 Windows 8.1 和 Windows Phone 8.1 為目標的 Windows 市集應用程式。 不支援 Silverlight。

## 主要情節

您必須在搜尋應用程式中執行一些作業。 本教學課程中涵蓋了這些主要情節：

- 建立索引
- 透過文件填入索引
- 使用全文檢索搜尋和篩選來搜尋文件

這些情節的說明都隨附範例程式碼， 歡迎在您的應用程式中使用這些程式碼片段。

### 概觀

我們將探索的範例應用程式，會建立名為 "hotels" 的新索引，並透過一些文件填入索引，然後執行一些搜尋查詢。 以下是主要程式，該程式顯示了整體流程：

    // This sample shows how to delete, create, upload documents and query an index
    static void Main(string[] args)
    {
        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";
    
        string apiKey = "Put your API admin key here."
    
        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
    
        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);
    
        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);
    
        SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
    
        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);
    
        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");
    
        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");
    
        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();
    }

我們會逐步說明， 首先我們需要建立新 `SearchServiceClient`。 此物件可讓您管理索引。 若要建構此物件，必須提供 Azure Search 服務名稱和系統管理 API 金鑰。

        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";
    
        string apiKey = "Put your API admin key here."
    
        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

> [AZURE.NOTE] 如果您提供不正確的金鑰 (例如，查詢金鑰系統管理金鑰卻需要)， `SearchServiceClient` 將會擲回 `CloudException` 與錯誤訊息 「 禁止 」 第一次您呼叫作業方法，例如 `Indexes.Create`。 如果遇到此情況，請按兩下我們的 API 金鑰。

以下幾行程式碼會呼叫建立名為 "hotels" 索引的方法，如果索引已存在，請加以刪除。 稍後我們會逐項執行這些方法。

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);
    
        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

接著必須填入索引。 若要這樣做，我們需要 `SearchIndexClient`。 有兩種方式取得: 建構該項目，或藉由呼叫 `Indexes.GetClient` 上 `SearchServiceClient`。 為方便起見，我們使用後者。

        SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

> [AZURE.NOTE] 在一般搜尋應用程式中，索引管理和填入是由搜尋查詢的個別元件處理。 `Indexes.GetClient` 可以很輕易填入索引，因為它可以節省提供其他的麻煩 `SearchCredentials`。 其作法是您用來建立系統管理金鑰傳遞 `SearchServiceClient` 新 `SearchIndexClient`。 不過，在執行查詢的應用程式的一部分，最好是建立 `SearchIndexClient` 直接如此一來您可以傳遞查詢金鑰，而不是系統管理金鑰。 這不但符合最低權限的準則，也可以讓您的應用程式更安全。 您可以深入了解系統管理金鑰和查詢金鑰 [這裡](https://msdn.microsoft.com/library/azure/dn798935.aspx)。

既然我們已經有 `SearchIndexClient`, ，我們可以開始填入索引。 這要使用另一個方法來執行，稍後我們會逐項說明。

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

最後，我們會執行一些搜尋查詢，以顯示結果，然後再次使用 `SearchIndexClient`:

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");
    
        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");
    
        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();

如果使用有效的服務名稱和 API 金鑰執行此應用程式，則輸出應該會看起來如下：

    Deleting index...
    
    Creating index...
    
    Uploading documents...
    
    Searching documents 'fancy wifi'...
    
    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]
    
    Filter documents with category 'Luxury'...
    
    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury        Tags: []
    Complete.  Press any key to end application...

本文結尾會提供完整的應用程式原始程式碼。

接下來，我們要仔細看看每個呼叫的方法 `Main`。

### 建立索引

在建立之後 `SearchServiceClient`, ，接下來 `Main` 沒有是刪除"hotels"索引，如果已經存在。 刪除方法如下：

    private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("hotels"))
        {
            serviceClient.Indexes.Delete("hotels");
        }
    }

這個方法會使用給定 `SearchServiceClient` 若要查看是否有索引，如果是的話就刪除它。
> [AZURE.NOTE] 為簡單起見，本文的範例程式碼使用 Azure 搜尋服務 .NET SDK 的同步方法。 我們建議您在應用程式中使用非同步方法，讓應用程式保有可擴充性且回應靈敏。 例如，在上述方法，您可以使用 `ExistsAsync` 和 `DeleteAsync` 而不是 `Exists` 和 `刪除`。

下一步] `Main` 會呼叫這個方法建立新的"hotels"索引:

    private static void CreateHotelsIndex(SearchServiceClient serviceClient)
    {
        var definition = new Index()
        {
            Name = "hotels",
            Fields = new[]
            {
                new Field("hotelId", DataType.String)                       { IsKey = true },
                new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
            }
        };
    
        serviceClient.Indexes.Create(definition);
    }

這個方法會建立新 `索引` 物件的清單 `欄位` 定義新索引的結構描述的物件。 每個欄位均有一個名稱、資料類型和一些屬性，以用於定義欄位的搜尋行為。 除了欄位之外，您還可以新增評分設定檔、建議工具或 CORS 選項到 Index (基於簡化目的，範例已省略這些選項)。 您可以找到更多資訊 Index 物件以及其組成部分 SDK 參考上 [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.index_members.aspx), 、 以及如下所示 [Azure 搜尋服務 REST API 參考](https://msdn.microsoft.com/library/azure/dn798935.aspx)。

### 填入索引

下一步 `Main` 是填入新建立的索引。 執行方法如下：

    private static void UploadDocuments(SearchIndexClient indexClient)
    {
        var documents =
            new Hotel[]
            {
                new Hotel()
                { 
                    HotelId = "1058-441", 
                    HotelName = "Fancy Stay", 
                    BaseRate = 199.0, 
                    Category = "Luxury", 
                    Tags = new[] { "pool", "view", "concierge" }, 
                    ParkingIncluded = false, 
                    LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
                    Rating = 5, 
                    Location = GeographyPoint.Create(47.678581, -122.131577)
                },
                new Hotel()
                { 
                    HotelId = "666-437", 
                    HotelName = "Roach Motel",
                    BaseRate = 79.99,
                    Category = "Budget",
                    Tags = new[] { "motel", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                    Rating = 1,
                    Location = GeographyPoint.Create(49.678581, -122.131577)
                },
                new Hotel() 
                { 
                    HotelId = "970-501", 
                    HotelName = "Econo-Stay",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "pool", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(46.678581, -122.131577)
                },
                new Hotel()
                { 
                    HotelId = "956-532", 
                    HotelName = "Express Rooms",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "wifi", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(48.678581, -122.131577)
                },
                new Hotel() 
                { 
                    HotelId = "566-518", 
                    HotelName = "Surprisingly Expensive Suites",
                    BaseRate = 279.99,
                    Category = "Luxury",
                    ParkingIncluded = false
                }
            };
    
        try
        {
            indexClient.Documents.Index(IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc))));
        }
        catch (IndexBatchException e)
        {
            // Sometimes when your Search service is under load, indexing will fail for some of the documents in
            // the batch. Depending on your application, you can take compensating actions like delaying and
            // retrying. For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
        }
    
        // Wait a while for indexing to complete.
        Thread.Sleep(2000);
    }

此方法分四個部分。 第一個建立的陣列 `旅館` 將做為我們上傳至索引的輸入資料的物件。 為簡單起見，此資料採硬式編碼。 在您的應用程式中，資料可能會來自像是 SQL 資料庫的外部資料來源。

第二個部分會建立 `IndexAction` 每個 `旅館`, ，然後在新一起群組那些 `IndexBatch`。 批次然後上傳至 Azure 搜尋服務索引 `Documents.Index` 方法。
> [AZURE.NOTE] 在此範例中，我們只上傳文件。 如果您想要變更合併至現有的文件或刪除文件，您可以建立 `IndexAction` 對應 `IndexActionType`。 我們不需要指定 `IndexActionType` 在此範例中因為預設值是 `上載`。

此方法的第三部分是擷取區塊，該區塊會為編制索引處理重要錯誤情況。 如果您的 Azure 搜尋服務失敗的批次中的文件的一些索引 `IndexBatchException` 所擲回 `Documents.Index`。 如果您在服務負載過重時編制文件的索引，就會發生此情況。 **我們強烈建議您在程式碼中明確處理此情況。**您可以延遲，然後重新嘗試將失敗的文件編制索引，或像範例一樣加以記錄並繼續，或是根據您應用程式的資料一致性需求執行其他操作。

最後，方法會延遲兩秒。 您的 Azure 搜尋服務中會發生非同步索引編製，因此範例應用程式必須稍待一會，才能確定文件已準備好可供搜尋。 通常只有在示範、測試和範例應用程式中，才需要這類延遲。

#### .NET SDK 如何處理文件

您可能想知道如何 Azure 搜尋服務.NET SDK 能夠上載像是使用者定義類別的執行個體 `旅館` 索引。 為了回答這個問題，讓我們看看 `旅館` 類別:

    [SerializePropertyNamesAsCamelCase]
    public class Hotel
    {
        public string HotelId { get; set; }
    
        public string HotelName { get; set; }
    
        public double? BaseRate { get; set; }
    
        public string Category { get; set; }
    
        public string[] Tags { get; set; }
    
        public bool? ParkingIncluded { get; set; }
    
        public DateTimeOffset? LastRenovationDate { get; set; }
    
        public int? Rating { get; set; }
    
        public GeographyPoint Location { get; set; }
    
        public override string ToString()
        {
            return String.Format(
                "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                HotelId,
                HotelName,
                Category,
                (Tags != null) ? String.Join(", ", Tags) : String.Empty);
        }
    }

首先要注意是每個公用屬性的 `旅館` 對應的欄位在索引定義中，但有一項重要的差異: 每個欄位的名稱以小寫字母 (「 駝峰 」)，啟動時，每個公用屬性名稱 `旅館` 開頭的小寫字母 (「 巴斯卡 」)。 這在執行資料繫結、而目標結構描述在應用程式開發人員控制範圍之外的 .NET 應用程式中很常見。 與其違反.NET 命名方針，使屬性名稱為駝峰式，您可以告訴 SDK 將屬性名稱對應至駝峰式自動 `[SerializePropertyNamesAsCamelCase]` 屬性。

第二個要注意的是 `旅館` 類別為公用屬性的資料類型。 這些屬性的.NET 型別對應到其對等的欄位類型索引定義中。 例如， `類別` 字串屬性會對應至 `類別` 欄位的型別 `Edm.String`。 也有類似的型別對應之間 `bool?` 和 `Edm.Boolean`, ，`DateTimeOffset?` 和 `Edm.DateTimeOffset`, 等等。型別對應的特定規則已一起記載 `Documents.Get` 方法 [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx)。
> [AZURE.NOTE] 當設計您自己的模型類別對應至 Azure Search 索引，請務必宣告實值型別的屬性，例如 `bool` 和 `int` 可為 null (例如， `bool?` 而不是 `bool`)。 這是必要的，因為 Azure 搜尋中所有的基本欄位類型可以是 Null。 如果您使用非 null 的型別，就會非預期的結果時編製索引的預設值，例如 `0` 和 `false`。 具體而言，這類預設值將會在索引期間轉換成 Null。 在未來的 SDK  版本中，使用不可為 Null 的類型將反而導致例外狀況。

這讓使用您的類別做為文件可雙向有效；您也可以擷取搜尋結果，然後讓 SDK 將結果自動還原序列化為您選擇的類型，我們會在下一節中看到這部分。
> [AZURE.NOTE] Azure 搜尋服務.NET SDK 也支援使用動態類型文件 `文件` 類別，也就是索引鍵/值對應的欄位值的欄位名稱。 當您在設計階段卻不知道索引的結構描述時，這很實用，否則要繫結到特定模型類別會很麻煩。 所有的方法在 SDK 中處理文件具有多載，可使用 `文件` 類別，以及使用泛型型別參數的強型別多載。 本教學課程的範例程式碼只使用了後者。 您可以深入了解 `文件` 類別 [這裡](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx)。

### 搜尋索引中的文件

此範例應用程式的最後一個步驟是搜尋索引中的一些文件。 執行方法如下：

    private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
    {
        // Execute search based on search text and optional filter
        var sp = new SearchParameters();
    
        if (!String.IsNullOrEmpty(filter))
        {
            sp.Filter = filter;
        }
    
        DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
        foreach (SearchResult<Hotel> result in response)
        {
            Console.WriteLine(result.Document);
        }
    }

首先，這個方法會建立新 `SearchParameters` 物件。 該物件用於指定查詢的其他選項，例如排序、篩選、分頁及 Facet。 在此範例中，我們只設定 `篩選` 屬性。

下一步是實際執行搜尋查詢， 這是使用 `Documents.Search` 方法。 在此方法中，我們傳遞搜尋文字做為字串，並再加上先前建立的搜尋參數。 我們也指定 `旅館` 的型別參數為 `Documents.Search`, ，藉此告訴 SDK 將搜尋結果中的文件還原序列化型別的物件 `旅館`。

最後，此方法會反覆查詢搜尋結果中的所有相符項，然後將每個文件列印至主控台。

我們來進一步了解如何呼叫此方法：

    SearchDocuments(indexClient, searchText: "fancy wifi");
    
    SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

在第一個呼叫中，我們要尋找含有 "fancy" 或 "wifi" 查詢字詞的所有文件。 在第二個呼叫中，搜尋字詞設為 "*"，這表示「全部尋找」。 您可以找到有關搜尋查詢運算式語法的詳細資訊 [這裡](https://msdn.microsoft.com/library/azure/dn798920.aspx)。

第二個呼叫使用 OData `$filter` 運算式 `類別 eq 'Luxury'`。 這會限制為只傳回文件搜尋其中 `類別` 欄位完全符合"Luxury"字串。 您可以深入了解 Azure 搜尋服務支援的 OData 語法 [這裡](https://msdn.microsoft.com/library/azure/dn798921.aspx)。

現在您已經知道這兩個呼叫的用途，應該可以輕易了解為什麼它們的輸出會看起來如下：

    Searching documents 'fancy wifi'...
    
    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]
    
    Filter documents with category 'Luxury'...
    
    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury        Tags: []

第一個搜尋結果傳回兩個文件。 第一個具有 「 新潮 」 在名稱中，而第二個則具有"wifi" `標記` 欄位。 第二個搜尋傳回兩個文件，它們剛好是索引中的有唯一文件 `類別` 欄位設為"Luxury"。

此步驟已完成本教學課程，但別就此結束。 **後續步驟**會提供可深入了解 Azure 搜尋服務的其他資源。

## 後續步驟

- 加深知識 [影片和其他範例和教學課程](search-video-demo-tutorial-list.md)。
- 了解這個版本的 Azure 搜尋服務 SDK 的功能: [Azure 搜尋服務概觀](https://msdn.microsoft.com/library/azure/dn798933.aspx)
- 檢閱 [命名慣例](https://msdn.microsoft.com/library/azure/dn857353.aspx) ，了解命名各種物件的規則。
- 檢閱 [支援的資料類型](https://msdn.microsoft.com/library/azure/dn798938.aspx) Azure 搜尋服務中。


## 範例應用程式的原始程式碼

以下是本逐步解說課程中所使用的完整範例應用程式原始程式碼。 請注意，如果想要建置並執行範例，您必須在 Program.cs 中，以您的值取代服務名稱和 API 金鑰預留位置。

Program.cs：

    using System;
    using System.Configuration;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;
    
    namespace AzureSearch.NETSDKSample
    {
        class Program
        {
            // This sample shows how to delete, create, upload documents and query an index
            static void Main(string[] args)
            {
                // Put your search service name here. This is the hostname portion of your service URL.
                // For example, if your service URL is https://myservice.search.windows.net, then your
                // service name is myservice.
                string searchServiceName = "myservice";
    
                string apiKey = "Put your API admin key here."
    
                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
    
                Console.WriteLine("{0}", "Deleting index...\n");
                DeleteHotelsIndexIfExists(serviceClient);
    
                Console.WriteLine("{0}", "Creating index...\n");
                CreateHotelsIndex(serviceClient);
    
                SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
    
                Console.WriteLine("{0}", "Uploading documents...\n");
                UploadDocuments(indexClient);
    
                Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
                SearchDocuments(indexClient, searchText: "fancy wifi");
    
                Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
                SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");
    
                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }
    
            private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists("hotels"))
                {
                    serviceClient.Indexes.Delete("hotels");
                }
            }
    
            private static void CreateHotelsIndex(SearchServiceClient serviceClient)
            {
                var definition = new Index()
                {
                    Name = "hotels",
                    Fields = new[]
                    {
                        new Field("hotelId", DataType.String)                       { IsKey = true },
                        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
                    }
                };
    
                serviceClient.Indexes.Create(definition);
            }
    
            private static void UploadDocuments(SearchIndexClient indexClient)
            {
                var documents =
                    new Hotel[]
                    {
                        new Hotel()
                        { 
                            HotelId = "1058-441", 
                            HotelName = "Fancy Stay", 
                            BaseRate = 199.0, 
                            Category = "Luxury", 
                            Tags = new[] { "pool", "view", "concierge" }, 
                            ParkingIncluded = false, 
                            LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
                            Rating = 5, 
                            Location = GeographyPoint.Create(47.678581, -122.131577)
                        },
                        new Hotel()
                        { 
                            HotelId = "666-437", 
                            HotelName = "Roach Motel",
                            BaseRate = 79.99,
                            Category = "Budget",
                            Tags = new[] { "motel", "budget" },
                            ParkingIncluded = true,
                            LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                            Rating = 1,
                            Location = GeographyPoint.Create(49.678581, -122.131577)
                        },
                        new Hotel() 
                        { 
                            HotelId = "970-501", 
                            HotelName = "Econo-Stay",
                            BaseRate = 129.99,
                            Category = "Budget",
                            Tags = new[] { "pool", "budget" },
                            ParkingIncluded = true,
                            LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                            Rating = 4,
                            Location = GeographyPoint.Create(46.678581, -122.131577)
                        },
                        new Hotel()
                        { 
                            HotelId = "956-532", 
                            HotelName = "Express Rooms",
                            BaseRate = 129.99,
                            Category = "Budget",
                            Tags = new[] { "wifi", "budget" },
                            ParkingIncluded = true,
                            LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                            Rating = 4,
                            Location = GeographyPoint.Create(48.678581, -122.131577)
                        },
                        new Hotel() 
                    {
                            HotelId = "566-518", 
                            HotelName = "Surprisingly Expensive Suites",
                            BaseRate = 279.99,
                            Category = "Luxury",
                            ParkingIncluded = false
                        }
                    };
    
                try
                {
                    indexClient.Documents.Index(IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc))));
                }
                catch (IndexBatchException e)
                {
                    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
                    // the batch. Depending on your application, you can take compensating actions like delaying and
                    // retrying. For this simple demo, we just log the failed document keys and continue.
                    Console.WriteLine(
                        "Failed to index some of the documents: {0}",
                        String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
                }
    
                // Wait a while for indexing to complete.
                Thread.Sleep(2000);
            }
    
            private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
            {
                // Execute search based on search text and optional filter
                var sp = new SearchParameters();
    
                if (!String.IsNullOrEmpty(filter))
                {
                    sp.Filter = filter;
                }
    
                DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
                foreach (SearchResult<Hotel> result in response)
                {
                    Console.WriteLine(result.Document);
                }
            }
        }
    }

Hotel.cs：

    using System;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;
    
    namespace AzureSearch.NETSDKSample
    {
        [SerializePropertyNamesAsCamelCase]
        public class Hotel
        {
            public string HotelId { get; set; }
    
            public string HotelName { get; set; }
    
            public double? BaseRate { get; set; }
    
            public string Category { get; set; }
    
            public string[] Tags { get; set; }
    
            public bool? ParkingIncluded { get; set; }
    
            public DateTimeOffset? LastRenovationDate { get; set; }
    
            public int? Rating { get; set; }
    
            public GeographyPoint Location { get; set; }
    
            public override string ToString()
            {
                return String.Format(
                    "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                    HotelId,
                    HotelName,
                    Category,
                    (Tags != null) ? String.Join(", ", Tags) : String.Empty);
            }
        }
    }



