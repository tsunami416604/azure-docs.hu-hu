<properties 
    pageTitle="使用 Azure DocumentDB 中的地理空間資料 | Microsoft Azure" 
    description="了解如何使用 Azure DocumentDB 建立和查詢空間物件，以及為其編製索引。" 
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
    ms.date="10/27/2015" 
    ms.author="arramac"/>
    
# 使用 Azure DocumentDB 中的地理空間資料

這篇文章中的地理空間功能介紹了 [Azure DocumentDB](http://azure.microsoft.com/services/documentdb/)。 閱讀本文後，您將能夠回答下列問題：

- 如何儲存 Azure DocumentDB 中的空間資料？
- 如何以 SQL 和 LINQ 查詢 Azure DocumentDB 中的地理空間資料？
- 如何在 DocumentDB 中啟用或停用空間索引編制？

請參閱本 [Github 專案](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Queries.Spatial) 的程式碼範例。

## 空間資料簡介

空間資料可描述空間中物件的位置和形狀。 在大部分的應用程式中，這些會對應至地球上的物件，也就是地理空間資料。 空間資料可以用來代表人、感興趣的地方、城市邊界或湖泊。 常見使用案例通常涉及鄰近性查詢，例如「尋找我目前位置附近的所有咖啡廳」。 

### GeoJSON
DocumentDB 支援索引和查詢，使用具有表示地理空間資料 [GeoJSON 規格](http://geojson.org/geojson-spec.html)。 GeoJSON 資料結構永遠是有效的 JSON 物件，因此可以透過 DocumentDB 來儲存及查詢，無須任何特殊的工具或程式庫。 DocumentDB SDK 提供協助程式類別和方法，讓使用空間資料更容易。 

### 點、Linestring 和多邊形
A **點** 代表空間中的單一位置。 在地理空間資料中，某個點所代表的確切位置可能是雜貨店的街道地址、電話亭、汽車或城市。  點會使用其座標組或經緯度，以 GeoJSON (和 DocumentDB) 來表示。 以下是點的 JSON 範例。

**DocumentDB 中的點**

    {
       "type":"Point",
       "coordinates":[ 31.9, -4.8 ]
    }

>[AZURE.NOTE] GeoJSON 規格指定經度第一個和緯度表示第二個。 如同其他的地圖應用程式，經度和緯度為角度，並以度為表示單位。 經度值是從本初子午線測量，並介於 -180 和 180.0 度之間；緯度值是從赤道測量，並介於 -90.0 和 90.0 度之間。 
>
> DocumentDB 會將座標解譯為依照 WGS-84 參考系統的表示。 請參閱下方詳細的座標參考系統資料。

如同此範例中，包含位置資料的使用者設定檔所示，此資料可以內嵌在 DocumentDB 文件中：

**儲存在 DocumentDB 中含有位置的使用設定檔**

    {
       "id":"documentdb-profile",
       "screen_name":"@DocumentDB",
       "city":"Redmond",
       "topics":[ "NoSQL", "Javascript" ],
       "location":{
          "type":"Point",
          "coordinates":[ 31.9, -4.8 ]
       }
    }

除了點之外，GeoJSON 也支援 Linestring 和多邊形。 **Linestring** 表示一系列的空間和它們連接之直線線段的兩個或多個點。 在地理空間資料中，linestring 通常用來代表高速公路或河流。 A **多邊形** 形成封閉的 LineString 連接的點的界限。 多邊形常用來代表自然構成物，例如湖泊，或代表政治管轄權，例如城市和州省。 以下是 DocumentDB 中多邊形的範例。 

**DocumentDB 中的多邊形**

    {
       "type":"Polygon",
       "coordinates":[
           [ 31.8, -5 ],
           [ 31.8, -4.7 ],
           [ 32, -4.7 ],
           [ 32, -5 ],
           [ 31.8, -5 ]
       ]
    }

>[AZURE.NOTE] GeoJSON 規格，必須為有效的多邊形，最後的座標對所提供應該與第一，若要建立一個封閉的圖形相同。
>
>多邊形內的點必須以逆時針順序指定。 以順時針順序指定多邊形，代表區域內的反轉。

除了點、 LineString 和多邊形，GeoJSON 也會指定如何分組多個地理空間位置，以及如何與地理位置做為建立關聯的任何屬性的表示法 **功能**。 由於這些物件都是有效的 JSON，因此均可在 DocumentDB 中儲存及處理。 不過，DocumentDB 僅支援自動編製點的索引。

### 座標參考系統

由於地球的形狀並不規則，地理空間資料的座標可以許多座標參考系統 (CRS) 來表示，而這些系統各有自己的參考框架和測量單位。 例如「英國國家格網參考系統」對英國而言是非常精確的參考系統，但對其他地區則不是。 

使用中最受歡迎的 CRS 目前是 World Geodetic System  [WGS 84](http://earth-info.nga.mil/GandG/wgs84/)。 GPS 裝置和許多地圖服務，包括 Google 地圖與 Bing Maps API 均是使用 WGS-84。 DocumentDB 僅支援對使用 WGS-84 CRS 的地理空間資料執行索引編製和查詢。 

## 建立具有空間資料的文件
當您建立包含 GeoJSON 值的文件時，值會根據集合的索引編製原則，自動以空間索引進行索引編製。 如果您是以動態類型的語言 (如 Python 或 Node.js) 使用 DocumentDB SDK，則必須建立有效的 GeoJSON。

**以 Node.js 建立具有地理空間資料的文件**

    var userProfileDocument = {
       "name":"documentdb",
       "location":{
          "type":"Point",
          "coordinates":[ -122.12, 47.66 ]
       }
    };

    client.createDocument(collectionLink, userProfileDocument, function (err, created) {
        // additional code within the callback
    });

如果您是使用 .NET (或 Java) SDK，可以在 Microsoft.Azure.Documents.Spatial 命名空間中使用新的點及多邊形類別，藉此將位置資訊內嵌在應用程式物件中。 這些類別可協助將空間資料序列化和還原序列化簡化成 GeoJSON。

**以 .NET 建立具有地理空間資料的文件**

    using Microsoft.Azure.Documents.Spatial;
    
    public class UserProfile
    {
        [JsonProperty("name")]
        public string Name { get; set; }

        [JsonProperty("location")]
        public Point Location { get; set; }
        
        // More properties
    }
    
    await client.CreateDocumentAsync(
        collection.SelfLink, 
        new UserProfile 
        { 
            Name = "documentdb", 
            Location = new Point (-122.12, 47.66) 
        });

如果您沒有經緯度資訊，但有實體地址或位置名稱，如城市或國家/地區，您可以使用像是 Bing Maps REST 服務之類的地理編碼服務，來查閱實際的座標。 深入了解 Bing 地圖的地理編碼 [這裡](https://msdn.microsoft.com/library/ff701713.aspx)。

## 查詢空間類型

既然我們已經探討過如何插入地理空間資料，現在就來看看如何透過 SQL 和 LINQ 使用 DocumentDB 查詢此資料。

### 空間 SQL 內建函數
DocumentDB 支援下列開放地理空間協會 (OGC) 的內建函數，以用於查詢地理空間。 如需有關一組完整的 SQL 語言的內建函式的詳細資訊，請參閱 [查詢 DocumentDB](documentdb-sql-query.md)。

<table>
<tr>
  <td><strong>使用量</strong></td>
  <td><strong>說明</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr、point_expr)</td>
  <td>傳回兩個 GeoJSON 點運算式之間的距離。</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr、polygon_expr)</td>
  <td>傳回布林運算式，指出第一個引數中指定的 GeoJSON 點是否位在第二個引數的 GeoJSON 多邊形內。</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>傳回布林值，指出指定的 GeoJSON 點或多邊形運算式是否有效。</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>如果指定的 GeoJSON 點或多邊形運算式是有效的，就會傳回包含布林值的 JSON 值；但如果是無效的，就會額外加上做為字串值的原因。</td>
</tr>
</table>

空間函數可以用來對空間資料執行鄰近性查詢。 例如，以下查詢使用 ST_DISTANCE 內建函數傳回所有家族文件，且這些文件在 30 公里指定位置內。 

**查詢**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**結果**

    [{
      "id": "WakefieldFamily"
    }]

如果您將空間索引編製包含在索引編製原則中，則「距離查詢」將會透過索引獲得有效利用。 如需空間索引編製的詳細資料，請參閱下面的章節。 如果您沒有指定路徑的空間索引，仍然可以透過指定 `x-ms-documentdb-query-enable-scan` 要求標頭 (且值設定為 "true") 執行空間查詢。 在.NET 中，這可以經由傳遞選擇性 **FeedOptions** 引數來使用查詢 [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) 設為 true。 

ST_WITHIN 可用來檢查點是否在多邊形內。 多邊形常用來表示邊界，例如郵遞區號、州省邊界或自然構成物。 此外，如果您將空間索引編製包含在索引編製原則中，則「距離內」查詢將會透過索引獲得有效利用。 

ST_WITHIN 中的多邊形引數只可以包含單一環狀，也就是多邊形本身不能有漏洞。 檢查 [DocumentDB 限制](documentdb-limits.md) 點 ST_WITHIN 查詢多邊形內所允許的最大數目。

**查詢**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**結果**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE] 類似於如何不相符的型別運作，在 DocumentDB 查詢中，如果是引數格式不正確或無效，則會評估為在指定的位置值 **未定義** 和查詢結果中略過評估文件。 如果您的查詢沒有傳回任何結果，請執行 ST_ISVALIDDETAILED 來偵錯，了解空間類型無效的原因。     

ST_ISVALID 和 ST_ISVALIDDETAILED 可用來檢查空間物件是否有效。 例如，下列查詢以超出範圍的緯度值 (-132.8)，檢查點的有效性。 ST_ISVALID 只會傳回布林值，而 ST_ISVALIDDETAILED 會傳回布林和字串，字串中包含被視為無效的原因。

** 查詢 **

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**結果**

    [{
      "$1": false
    }]

這些函數也可以用來驗證多邊形。 例如，這裡我們使用 ST_ISVALIDDETAILED 驗證不封閉的多邊形。 

**查詢**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**結果**

    [{
       "$1": { 
          "valid": false, 
          "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
        }
    }]
    
### .NET SDK 中的 LINQ 查詢

DocumentDB.NET SDK 也是虛設常式方法 `Distance()` 和 `Within()` 的提供者，供您在 LINQ 運算式中使用。 DocumentDB LINQ 提供者會將這些方法呼叫，轉譯為同等的 SQL 內建函數呼叫 (分別為 ST_DISTANCE 和 ST_WITHIN)。 

以下是 LINQ 查詢的範例，該查詢會使用 LINQ 在 DocumentDB 集合中找出所有文件，而這些文件的「位置」值在指定點的 30 公里半徑內。

**距離的 LINQ 查詢**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(collection.SelfLink)
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

同樣地，以下是尋找所有文件的查詢，這些文件的「位置」均在指定的方塊/多邊形內。 

**範圍內的 LINQ 查詢**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(collection.SelfLink)
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


既然我們已經探討過如何使用 LINQ 和 SQL 查詢文件，現在我們來看一下如何針對空間索引編製設定 DocumentDB。

## 編製索引

如我們所述 [結構描述使用 Azure DocumentDB 索引適用於多種](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) 紙張，我們在設計 DocumentDB 的資料庫引擎是真正無從驗證結構描述，並提供一流的支援 json。 DocumentDB 的寫入最佳化資料庫引擎，現在也可以原生方式了解以 GeoJSON 標準表示的空間資料。

Geometry 是簡單的說，投射到 2D 平面上的測量座標的則漸進式分成使用的儲存格 **quadtree**。 這些資料格會對應至儲存格內的位置為基礎的 1d **希伯特空間填滿曲線**, ，其中保留的點的位置。 此外時位置資料具有索引，它會逐一查看這道程序 **鑲嵌**, ，也就是所有儲存格相交的位置，識別並儲存為 DocumentDB 索引中的索引鍵。 在查詢時，點和多邊形之類的引數也會經過鑲嵌，以擷取相關的格子 ID 範圍，然後用來從索引擷取資料。

如果指定的索引編製原則包含 /* (所有路徑) 的空間索引，則表示集合中可找到的所有點均已編製索引，能進行有效率的空間查詢 (ST_WITHIN 和 ST_DISTANCE)。 空間索引沒有整數位數值，且一律使用預設的整數位數值。

以下 JSON 片段顯示已啟用空間索引的索引編製原則，也就是為文件中可找到的所有 GeoJSON 點編製索引，以用於空間查詢。 如果您要使用 Azure 入口網站修改索引編製原則，可以為索引編製原則指定以下 JSON，藉此啟用集合的空間索引編製。

**集合的索引編製原則 JSON (已啟用空間)**

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
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                }
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

以下是 .NET 的程式碼片段示範，供您了解如何建立集合，同時針對所有包含點的路徑啟用空間索引編製。 

**建立含空間索引編制的集合**

    IndexingPolicy spatialIndexingPolicy = new IndexingPolicy();
    spatialIndexingPolicy.IncludedPaths.Add(new IncludedPath
    {
        Path = "/*",
        Indexes = new System.Collections.ObjectModel.Collection<Index>()
            {
                new RangeIndex(DataType.Number) { Precision = -1 },
                new RangeIndex(DataType.String) { Precision = -1 },
                new SpatialIndex(DataType.Point)
            }
    });

    Console.WriteLine("Creating new collection...");
    collection = await client.CreateDocumentCollectionAsync(dbLink, collectionDefinition);

而以下說明如何修改現有的集合，以利用儲存在文件內任何一個點上的空間索引編制。

**修改含空間索引編制的現有集合**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = spatialIndexingPolicy; 
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(collection.SelfLink);
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [AZURE.NOTE] 如果位置 GeoJSON 文件中的值是格式不正確或不正確，然後它會取得建立索引，空間查詢。 您可以使用 ST_ISVALID 和 ST_ISVALIDDETAILED 驗證位置值。

## 後續步驟
既然您已經學會如何開始使用 DocumentDB 中的地理空間支援，您可以：

- 開始撰寫程式碼與 [Github 上的開放式地理空間.NET 程式碼範例](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Queries.Spatial)
- 取得指針與地理空間查詢在 [DocumentDB 查詢遊樂場](http://www.documentdb.com/sql/demo#geospatial)
- 深入了解 [DocumentDB 查詢](documentdb-sql-query.md)
- 深入了解 [DocumentDB 索引編製原則](documentdb-indexing-policies.md)


