<properties 
    pageTitle="DocumentDB 索引編製原則 | Microsoft Azure" 
    description="了解 DocumentDB 中索引的運作方式，以及了解如何設定及變更編製索引原則。 設定在 DocumentDB 中的編製索引原則，以便自動編製索引和追求更高效能。" 
    keywords="how indexing works, automatic indexing, indexing database, documentdb, azure, Microsoft azure"
    services="documentdb" 
    documentationCenter="" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="10/05/2015" 
    ms.author="arramac"/>


# DocumentDB 索引編製原則

雖然許多客戶都很樂意讓 DocumentDB 自動處理 [編製索引的所有層面](documentdb-indexing.md), ，DocumentDB 也支援指定自訂 **索引編製原則** 建立期間的集合。 相較於其他編制索引資料庫平台所提供的次要索引，DocumentDB 中的索引編製原則在彈性和功能上都更為強大，因為後者可讓您設計和自訂索引的圖形，而不會犧牲結構描述的靈活度。 若要了解索引如何在 DocumentDB 內運作，您必須了解透過管理編製索引原則，在索引儲存空間負荷、寫入和查詢的輸送量，以及查詢一致性之間進行細微的取捨。  

在本文中，我們會深入探討 DocumentDB 索引編製原則、自訂索引編製原則的方式，以及相關聯的取捨。 

閱讀本文後，您將能夠回答下列問題：

- DocumentDB 預設如何支援自動編製索引？
- 如何覆寫要在索引編製中包含或排除的屬性？
- 我要如何設定最終更新的索引？
- 如何設定索引編製來執行 Order By 或範圍查詢？
- 如何變更集合的索引編製原則？
- 如何比較不同索引編製原則的儲存空間和效能？

##<a id="CustomizingIndexingPolicy"></a> 自訂集合的索引編製原則

開發人員可以透過覆寫 DocumentDB 集合上的預設索引編製原則，並設定下列各方面，以自訂儲存空間、寫入/查詢效能，以及查詢一致性之間的取捨。

- **包含/排除文件和索引的路徑**。 開發人員可以在集合中插入或取代文件時，選擇要在索引中排除或包含的特定文件。 開發人員也可以選擇要包含或排除特定 JSON 屬性又稱為 在索引中包含的文件編製索引的路徑 (包括萬用字元模式)。
- **設定各種索引類型**。 開發人員也可以針對每個包含的路徑，根據其資料和預期的查詢工作負載以及每個路徑的數值/字串「精確度」，指定它們在集合上所需的索引類型。
- **設定索引更新模式**。 DocumentDB 支援三個索引編製模式，這些模式可以透過 DocumentDB 集合的索引編製原則設定：[一致]、[延遲] 和 [無]。 

下列.NET 程式碼片段示範如何在集合建立期間設定自訂索引原則。 在這裡，我們會以最大精確度及字串和數字的範圍索引來設定原則。 此原則可讓我們對字串執行 Order By 查詢。

    var collection = new DocumentCollection { Id = "myCollection" };
    
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
    
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }
        });

    await client.CreateDocumentCollectionAsync(database.SelfLink, collection);   


>[AZURE.NOTE] 編製索引原則的 JSON 結構描述已變更以支援針對字串範圍索引的 REST API 2015-06-03 版的版本。 .NET SDK 1.2.0 和 Java、 Python 和 Node.js Sdk 1.1.0 支援新的原則結構描述。 較舊的 SDK 使用 REST API 2015-04-08 版，並支援舊版的索引編製原則結構描述。
>
>根據預設，DocumentDB 會使用雜湊索引為文件內的所有字串屬性一致地編製索引，並使用範圍索引為數值屬性編製索引。  

### 資料庫編製索引模式

DocumentDB 支援三個索引編製模式，這些模式可以透過 DocumentDB 集合的索引編製原則設定：[一致]、[延遲] 和 [無]。

**一致**: 如果 DocumentDB 集合的原則指定為 「 一致 」 上指定的 DocumentDB 集合, 的查詢會依照與針對讀數指定相同的一致性層級 (也就是增強式 」、 「 界限-陳舊、 工作階段或 「 最終 」)。 索引會在文件更新 (亦即，在 DocumentDB 集合中插入、取代、更新和刪除文件) 時同步更新。  一致的索引編製支援一致的查詢，但代價可能是減少寫入輸送量。 這指的是減少需要編製索引的唯一路徑以及「一致性層級」的功能。 一致的索引編製模式是針對「快速寫入、立即查詢」工作負載而設計。

**延遲**: 若要允許最大的文件擷取輸送量，可以設定 DocumentDB 集合使用延遲一致性; 也就是說，查詢的最終一致。 索引會在 DocumentDB 集合靜止 (亦即，未充分利用集合的輸送量容量來處理使用者要求) 時，以非同步方式更新。 對於需要文件擷取不受妨礙的「立即擷取、稍後查詢」工作負載，可能適合「延遲」索引編製模式。

**無**: 標示為 「 無 」 索引模式的集合沒有任何與其相關聯的索引。 將索引編製原則設定為「無」時，對於捨棄任何現有的索引具有副作用。

>[AZURE.NOTE] 設定 「 無 」 索引編製原則具有副作用的卸除任何現有的索引。 如果您的存取模式只需要「識別碼」及/或「自我連結」，請使用此選項。

下列範例示範如何搭配使用 .NET SDK 與一致自動編製索引，以在插入所有文件時建立 DocumentDB 集合。

下表顯示根據針對集合設定的索引編製模式 (「一致」和「延遲」)，以及針對查詢要求指定的一致性層級，顯示查詢的一致性。 這適用於使用任何介面 (REST API、SDK)，或從預存程序和觸發程序內進行的查詢。 

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>一致</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>緩慢</strong>
                </p>
            </td>            
        </tr>
        <tr>
            <td valign="top">
                <p>
                    <strong>增強式</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Strong
                </p>
            </td>
            <td valign="top">
                <p>
                    Eventual
                </p>
            </td>            
        </tr>       
        <tr>
            <td valign="top">
                <p>
                    <strong>界限-陳舊</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Bounded Staleness
                </p>
            </td>
            <td valign="top">
                <p>
                    Eventual
                </p>
            </td>            
        </tr>          
        <tr>
            <td valign="top">
                <p>
                    <strong>工作階段</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Session
                </p>
            </td>
            <td valign="top">
                <p>
                    Eventual
                </p>
            </td>            
        </tr>      
        <tr>
            <td valign="top">
                <p>
                    <strong>最終</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Eventual
                </p>
            </td>
            <td valign="top">
                <p>
                    Eventual
                </p>
            </td>            
        </tr>         
    </tbody>
</table>

根據預設，如果使用「無」索引編製模式設定集合，以發出可能需要掃描才能進行查詢的訊號，則所有查詢都會傳回錯誤。 只要在 REST API 中使用 `x-ms-documentdb-enable-scans` 標頭，或使用 .NET SDK 利用 `EnableScanInQuery` 要求選項，仍然可以在沒有範圍索引的情況下執行這些查詢。 例如，使用 ORDER BY 的部分查詢即使使用 `EnableScanInQuery`，也不允許使用「無」。

下表根據索引編製模式 (「一致」、「延遲」和「無」)，顯示指定 EnableScanInQuery 時，查詢的一致性。

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>一致</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>緩慢</strong>
                </p>
            </td>       
            <td valign="top">
                <p>
                    <strong>None</strong>
                </p>
            </td>             
        </tr>
        <tr>
            <td valign="top">
                <p>
                    <strong>增強式</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Strong
                </p>
            </td>
            <td valign="top">
                <p>
                    Eventual
                </p>
            </td>    
            <td valign="top">
                <p>
                    Strong
                </p>
            </td>                
        </tr>       
        <tr>
            <td valign="top">
                <p>
                    <strong>界限-陳舊</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Bounded Staleness
                </p>
            </td>
            <td valign="top">
                <p>
                    Eventual
                </p>
            </td>      
            <td valign="top">
                <p>
                    Bounded Staleness
                </p>
            </td> 
        </tr>          
        <tr>
            <td valign="top">
                <p>
                    <strong>工作階段</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Session
                </p>
            </td>
            <td valign="top">
                <p>
                    Eventual
                </p>
            </td>   
            <td valign="top">
                <p>
                    Session
                </p>
            </td>             
        </tr>      
        <tr>
            <td valign="top">
                <p>
                    <strong>最終</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Eventual
                </p>
            </td>
            <td valign="top">
                <p>
                    Eventual
                </p>
            </td>      
            <td valign="top">
                <p>
                    Eventual
                </p>
            </td>              
        </tr>         
    </tbody>
</table>

下列程式碼範例示範如何搭配使用 .NET SDK 與一致的索引編製，在插入所有文件時建立 DocumentDB 集合。

     // Default collection creates a hash index for all string and numeric    
     // fields. Hash indexes are compact and offer efficient
     // performance for equality queries.
     
     var collection = new DocumentCollection { Id ="defaultCollection" };
     
     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
     
     collection = await client.CreateDocumentCollectionAsync(database.SelfLink, collection);


### 索引路徑

DocumentDB 會將 JSON 文件和索引塑造為樹狀結構，並可讓您調整為樹狀結構中的路徑原則。 您可以在此找到更多詳細資料 [介紹 DocumentDB 索引編製](documentdb-indexing.md)。 您可以選擇編製索引時必須包含或排除文件內的哪些路徑。 針對事先知道查詢模式的情況，這將可改善寫入效能並降低索引儲存。

索引路徑的開頭為根 (/)，且通常結尾嗎? 萬用字元運算子，代表有多個可能值的前置詞。 例如，若要執行 SELECT * FROM Families F WHERE F.familyName ="Andersen"，您必須包含的索引路徑 /familyName/? 在集合的索引原則。

索引路徑也可以使用 * 萬用字元運算子來指定路徑首碼底下的遞迴行為。 例如，使用 /"payload"/* 可將 payload 屬性下的所有項目自索引編製作業中排除。

以下是指定索引路徑的常見模式：

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>Path</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>描述/使用案例</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /*
                </p>
            </td>
            <td valign="top">
                <p>
                    Default path for collection. Recursive and applies to whole document tree.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /prop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Index path required to serve queries like the following (with Hash or Range types respectively):
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop &gt; 5
                </p>
                <p>
                    SELECT * FROM collection c ORDER BY c.prop
                </p>                
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /prop/*
                </p>
            </td>
            <td valign="top">
                <p>
                    Index path for all paths under the specified label. Works with the following queries
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop &gt; 5
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop.nextprop = "value"
                </p>
                <p>
                    SELECT * FROM collection c ORDER BY c.prop
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /props/[]/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Index path required to serve iteration and JOIN queries against arrays of scalars like ["a", "b", "c"]:
                </p>
                <p>
                    SELECT tag FROM tag IN collection.props WHERE tag = "value"
                </p>
                <p>
                    SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /props/[]/subprop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Index path required to serve iteration and JOIN queries against arrays of objects like [{subprop: "a"}, {subprop: "b"}]:
                </p>
                <p>
                    SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"
                </p>
                <p>
                    SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value"
                </p>
            </td>
        </tr>        
        <tr>
            <td valign="top">
                <p>
                    /prop/subprop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Index path required to serve queries (with Hash or Range types respectively):
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop &gt; 5
                </p>
                <p>
                    SELECT * FROM collection c ORDER BY c.prop.subprop
                </p>                
            </td>
        </tr>
    </tbody>
</table>

>[AZURE.NOTE] 設定自訂的索引路徑時，您都必須指定預設的特殊路徑所表示的整份文件樹狀結構的索引編製規則"/ *"。 

下列範例會設定一個採用範圍索引編製且自訂的有效位數值為 20 個位元組：

    var collection = new DocumentCollection { Id = "rangeSinglePathCollection" };    
    
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = 20 } } 
            });

    // Default for everything else
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*" ,
            Indexes = new Collection<Index> {
                new HashIndex(DataType.String) { Precision = 3 }, 
                new RangeIndex(DataType.Number) { Precision = -1 } 
            }
        });
        
    collection = await client.CreateDocumentCollectionAsync(database.SelfLink, pathRange);

### 索引資料類型、類型和精確度

探討如何指定路徑之後，讓我們看看我們可以使用那些選項來設定路徑的編製索引原則。 您可以為每個路徑指定一或多個編製索引的定義：

- 資料型別: **字串**, ，**數目** 或 **點** (可以包含每個資料類型，每個路徑只有一個項目)
- 索引類型: **雜湊** (相等查詢) **範圍** (相等、 範圍或 Order By 查詢) 或 **空間** (空間查詢)。 
- 精確度：數字為 1-8 或 -1 (最大精確度)；字串為 1-100 (最大精確度)

#### 索引類型

DocumentDB 支援每個路徑的雜湊和範圍索引種類 (可針對字串、數字或兩者進行設定)。

- **雜湊** 支援有效率的相等和聯結查詢。 針對大多數使用案例，雜湊索引並不需要比預設值 (3 個位元組) 更高的精確度。
- **範圍** 支援有效率的相等查詢，範圍查詢 (使用 >、 <>、 =、 < =、! =)，和 Order By 查詢。 根據預設，Order By 查詢也需要最大索引精確度 (-1)。

DocumentDB 也支援每個路徑的空間索引類型 (可針對點資料類型加以指定)。 位於指定路徑的值必須是有效的 GeoJSON 點，例如 `{"type": "Point", "coordinates": [0.0, 10.0]}`。

- **空間** 支援有效率空間 (在和距離) 查詢。

以下是支援的索引種類和可用來處理的查詢的範例：

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>索引類型</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>描述/使用案例</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    Hash
                </p>
            </td>
            <td valign="top">
                <p>
                    Hash over /prop/? (or /*) can be used to serve the following queries efficiently:
                      SELECT * FROM collection c WHERE c.prop = "value"
                    Hash over /props/[]/? (or /* or /props/*) can be used to serve the following queries efficiently:
                      SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    Range
                </p>
            </td>
            <td valign="top">
                <p>
                    Range over /prop/? (or /*) can be used to serve the following queries efficiently:
                        SELECT * FROM collection c WHERE c.prop = "value"
                        SELECT * FROM collection c WHERE c.prop > 5
                        SELECT * FROM collection c ORDER BY c.prop
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    Spatial
                </p>
            </td>
            <td valign="top">
                <p>
                    Range over /prop/? (or /*) can be used to serve the following queries efficiently:
                        SELECT * FROM collection c 
                        WHERE ST_DISTANCE(c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) < 40
                        選取 * 從集合 c 其中 ST_WITHIN(c.prop, {"type":"Polygon",...})
                </p>
            </td>
        </tr>        
    </tbody>
</table>

根據預設，如果沒有 (任何精確度的) 範圍索引，以發出可能需要掃描才能進行查詢的訊號，則所有查詢都會傳回錯誤。 只要在 REST API 中使用 x-ms-documentdb-enable-scans 標頭，或使用 .NET SDK 利用 EnableScanInQuery 要求選項，仍然可以在沒有範圍索引的情況下執行範圍查詢。 如果在查詢中有 DocumentDB 可以使用索引據以篩選的其他任何篩選，則將不會傳回任何錯誤。

相同的規則適用於空間查詢。 根據預設，如果沒有空間索引，而且沒有可從索引提供服務的其他篩選，則會針對空間查詢傳回錯誤。 您可以使用 x-ms-documentdb-enable-scan/EnableScanInQuery 將它們執行為掃描。

#### 索引精確度

索引精確度可讓您在索引儲存空間額外負荷和查詢效能之間取捨。 
對於數字，建議使用預設精準度組態 -1 (「最大值」)。 因為數字在 JSON 中為 8 個位元組，相當於 8 個位元組的組態。 挑選較低值的精準度，例如 1-7，表示在某個範圍內的值可對應至相同的索引項目。 因此，您將會減少索引儲存空間，但查詢執行可能必須處理更多文件，並且耗用更多輸送量，也就是要求單位。

索引精準度組態對於字串範圍更實用。 因為字串可以是任意長度，索引精準度的選擇可能會影響字串範圍查詢的效能，並影響所需的索引儲存空間量。 字串範圍索引可以設定為 1-100 或 -1 (「最大值」)。 如果您想要針對字串屬性執行 Order By 查詢，則必須為對應的路徑，將精確度指定為 -1。

空間索引一律使用點的預設索引精準度，且無法被覆寫。 

下列範例示範如何使用 .NET SDK 提高集合中範圍索引的精確度。 請注意，這樣會使用預設路徑 "/*"。

**使用自訂索引精確度建立集合**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };
    
    rangeDefault.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }
        });

    await client.CreateDocumentCollectionAsync(database.SelfLink, rangeDefault);   


> [AZURE.NOTE] 當查詢使用 Order By，但沒有針對所查詢之路徑的最大精確度的範圍索引時，DocumentDB 就會傳回錯誤。 

同樣地，可以從索引編製完全排除路徑。 下一個範例顯示如何 (又稱為排除文件的整個區段 樹狀子目錄) 使用"*"萬用字元。

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    
    collection = await client.CreateDocumentCollectionAsync(database.SelfLink, excluded);


## 開啟和關閉索引編製

您可以選擇是否要讓集合自動編製所有文件的索引。 根據預設，會自動索引所有文件，但您可以選擇將它關閉。 關閉索引編製功能時，便只能透過文件自己的連結或使用識別碼透過查詢來存取文件。

在關閉自動索引編製功能的情況下，您仍然可以選擇性地只將特定的文件新增到索引中。 相反地，您也可以讓自動索引編製功能保持開啟，並選擇性地只排除特定的文件。 當您只需要查詢文件的子集時，索引編製功能開/關組態相當有用。

例如，下列範例顯示如何包含明確使用文件 [DocumentDB.NET SDK](https://github.com/Azure/azure-documentdb-java) 和 [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx) 屬性。

    // If you want to override the default collection behavior to either
    // exclude (or include) a Document from indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(defaultCollection.SelfLink,
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## 修改集合的索引編製原則

DocumentDB 可讓您即時對集合的索引編製原則進行變更。 DocumentDB 集合的索引編製原則如有變更，可能會導致索引圖形變更，包括可以編製索引的路徑、其精確度，以及索引本身的一致性模型。 因此，索引編製原則變更時，需要將舊的索引有效率地轉換成新的索引。 

**線上索引轉換**

![編製索引的運作方式 – DocumentDB 線上索引轉換](media/documentdb-indexing-policies/index-transformations.png)

索引轉換在線上進行，這表示，每個新原則有效率地轉換舊原則編製索引的文件 **不會影響寫入可用性或佈建的輸送量** 的集合。 使用 REST API、SDK 或從預存程序和觸發程序中建立的讀取和寫入作業一致性在索引轉換期間不會受到影響。 也就是說，當您進行索引編製原則變更時，您的 App 效能不會降低，也不需要停機時間。

不過，在索引轉換進行期間，不論索引編製模式設定 (「一致」或「延遲」) 為何，查詢最終會是一致的。 這適用於來自所有介面 (REST API、SDK) 的查詢，以及來自預存程序和觸發程序內的查詢。 索引轉換就像延遲索引編製一樣，是以非同步方式，使用適用於指定複本的備用資源，在複本背景執行。 

索引轉換也會在 **就地** (就地)，亦即，DocumentDB 不會保留兩份索引和交換出以新舊索引。 也就是說，執行索引轉換時，您的集合中不需要也不會耗用其他任何磁碟空間。

當您變更索引編製原則時，套用變更以便從舊索引移到新索引的方式主要取決於索引編製模式設定，而非其他值，例如，包含/排除的路徑、索引種類和精確度。 如果您的舊原則和新原則使用一致的索引編製模式，DocumentDB 會執行線上索引轉換。 您無法在轉換進行時，使用一致的索引編製模式套用另一個索引編製原則變更。

不過，您可以在轉換進行時，移到「延遲」或「無」索引編製模式。 

- 當您移到「延遲」時，所進行的索引原則變更會立即生效，而且 DocumentDB 會開始以非同步方式重新建立索引。 
- 當您移到「無」時，索引會立即失效。 當您想要取消進行中的轉換，並重新開始其他索引編製原則時，移至「無」相當實用。 

如果您使用.NET SDK，您可以開始使用新的索引編製原則變更 **ReplaceDocumentCollectionAsync** 方法，並追蹤進度百分比索引轉換使用 **Readdocumentcollectionasync** 回應屬性 **Indextransformationprogress** 呼叫。 其他 SDK 和 REST API 支援對等屬性和方法，以進行索引編製原則變更。

以下是說明如何將集合的索引編製原則從「一致」索引編製模式修改為「延遲」的程式碼片段。

**將索引編製原則從一致修改為延遲**

    // Switch to lazy indexing.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);


例如，您可以呼叫 ReadDocumentCollectionAsync 以檢查索引轉換的進度，如下所示。

**追蹤索引轉換的進度**

    long smallWaitTimeMilliseconds = 1000;
    long progress = 0;

    while (progress < 100)
    {
        ResourceResponse<DocumentCollection> collectionReadResponse = await     client.ReadDocumentCollectionAsync(collection.SelfLink);
        progress = collectionReadResponse.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromMilliseconds(smallWaitTimeMilliseconds));
    }

您可以移動到「無」索引編製模式，以卸除集合的索引。 如果您想要取消進行中的轉換，並立即開始新的轉換，這可能是實用的操作工具。

**卸除集合的索引**

    // Switch to lazy indexing.
    Console.WriteLine("Dropping index by changing to to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

您什麼時候會對 DocumentDB 集合進行索引編製原則變更？ 以下是最常見的使用案例：

- 在正常操作期間提供一致的結果，但在大量資料匯入期間，改回延遲索引編製
- 開始使用目前 DocumentDB 集合上的新索引編製功能，例如需要空間索引類型的地理空間查詢，或者需要字串範圍索引類型的 Order By/字串範圍查詢
- 手動選取要編製索引的屬性，並在一段時間後變更
- 調整索引編製精確度，以改善查詢效能或減少耗用的儲存空間

>[AZURE.NOTE] 若要修改使用 ReplaceDocumentCollectionAsync 編製索引原則，您需要版本 > = 1.3.0 版.NET SDK
>
> 為使索引轉換順利完成，您必須確定集合上有足夠的可用儲存空間。 如果集合已達到其儲存配額，將會暫停索引轉換。 一旦有可用的儲存空間 (例如您刪除某些文件)，索引轉換會自動繼續。

## 效能微調

DocumentDB API 會提供效能度量 (像是已使用的索引儲存體)，以及每個作業的輸送量成本 (要求單位) 等相關資訊。 這項資訊可以用來比較各種編製索引的原則以及用來微調效能。

若要檢查集合的儲存體配額和使用量，請對集合資源執行 HEAD 或 GET 要求，並檢查 x-ms-request-quota 和 x-ms-request-usage 標頭。 在.NET SDK 中， [Resourceresponse<t>](http://msdn.microsoft.com/library/dn850325.aspx) 和 [Documentsizequota](http://msdn.microsoft.com/library/azure/dn850324.aspx) 屬性在 [ResourceResponse < {1>t<1} >](http://msdn.microsoft.com/library/dn799209.aspx) 包含這些對應值。

     // Measure the document size usage (which includes the index size) against   
     // different policies.        
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(collectionSelfLink);  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


若要測量的索引編製每一個寫入作業 (建立、 更新或刪除)，檢查 x ms-要求費用標頭 (或同等權限 [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) 屬性 [ResourceResponse < {1>t<1} >](http://msdn.microsoft.com/library/dn799209.aspx) .NET SDK 中) 來測量這些作業所耗用的要求單位數。

     // Measure the performance (request units) of writes.     
     ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);              
     Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
     
     // Measure the performance (request units) of queries.    
     IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();                                  
     double totalRequestCharge = 0;
     while (queryable.HasMoreResults)
     {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
        Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
        totalRequestCharge += queryResponse.RequestCharge;
     }
     
     Console.WriteLine("Query consumed {0} request units in total", totalRequestCharge);

## 索引編製原則規格的變更
在 2015 年 7 月 7 日的 REST API 2015-06-03 版引進索引編製原則結構描述中的變更。 SDK 版本中的對應類別具有符合結構描述的新實作。 

在 JSON 規格中實作下列變更：

- 索引編製原則支援字串的範圍索引
- 每個路徑可以有多個索引定義，一個定義用於一種資料類型
- 索引編製精確度針對數字支援 1-8、針對字串支援 1-100 和支援 -1 (最大精確度)
- 路徑區段不需要雙引號來逸出每個路徑。 例如，您可以加入/標題 / 路徑嗎? 而不是 /"title"/?
- 代表「所有路徑」的根路徑可以表示為 /* (除了 / 以外)

如果您有程式碼，會佈建具有以 .NET SDK 1.1.0 版或較舊的版本撰寫的自訂索引編製原則的集合，您必須變更您的應用程式程式碼，處理這些變更以移至 SDK 1.2.0 版。 如果您沒有會設定索引編製原則的程式碼，或打算繼續使用舊的 SDK 版本，則不需要任何變更。

如需實用的比較，以下為使用 REST API 2015-06-03 版及先前的 2015-04-08 版撰寫自訂編製索引原則的範例。

**先前的索引編製原則 JSON**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "IncludedPaths":[
          {
             "IndexType":"Hash",
             "Path":"/",
             "NumericPrecision":7,
             "StringPrecision":3
          }
       ],
       "ExcludedPaths":[
          "/\"nonIndexedContent\"/*"
       ]
    }

**目前的索引編製原則 JSON**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Hash",
                   "dataType":"String",
                   "precision":3
                },
                {
                   "kind":"Hash",
                   "dataType":"Number",
                   "precision":7
                }
             ]
          }
       ],
       "ExcludedPaths":[
          {
             "path":"/nonIndexedContent/*"
          }
       ]
    }

## 後續步驟

請遵循下列連結以取得索引原則管理範例，以及深入了解 DocumentDB 的查詢語言。

1.  [DocumentDB .NET 索引管理程式碼範例](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
2.  [DocumentDB REST API 集合作業](https://msdn.microsoft.com/library/azure/dn782195.aspx)
3.  [使用 DocumentDB SQL 進行查詢](documentdb-sql-query.md)

 


