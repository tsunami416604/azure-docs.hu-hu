---
title: SQL-lekérdezés teljesítményének beolvasása & végrehajtási metrikák
description: Megtudhatja, hogyan kérhet le Azure Cosmos DB kérelmek SQL-lekérdezési végrehajtási metrikáit és profiljának SQL-lekérdezési teljesítményét.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: 48b9a67de5c870a187ee008bd97265760ca6c341
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998362"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>SQL-lekérdezés végrehajtási metrikáinak lekérése és a lekérdezési teljesítmény elemzése a .NET SDK használatával

Ez a cikk bemutatja, hogyan lehet profilt felvenni az SQL-lekérdezés teljesítményére Azure Cosmos DBon. Ez a profilkészítés a .net SDK- `QueryMetrics` ból beolvasott használatával végezhető el, és részletesen itt olvasható. A [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) egy erősen begépelt objektum, amely információkat nyújt a háttérbeli lekérdezés végrehajtásáról. Ezeket a metrikákat a [lekérdezési teljesítmény finomhangolása](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) című cikkben részletesebben dokumentáljuk.

## <a name="set-the-feedoptions-parameter"></a>A FeedOptions paraméter beállítása

A [DocumentClient. CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) összes túlterhelése egy opcionális [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) paramétert is igénybe veheti. Ez a beállítás lehetővé teszi, hogy a lekérdezés-végrehajtás beállítható és paraméteres legyen. 

Az SQL-lekérdezés végrehajtási metrikáinak összegyűjtéséhez be kell állítania a [PopulateQueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) paramétert [](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) a `true`FeedOptions. A `PopulateQueryMetrics` True értékre állításával a `FeedResponse` rendszer a megfelelőt `QueryMetrics`fogja tartalmazni. 

## <a name="get-query-metrics-with-asdocumentquery"></a>Lekérdezési metrikák beolvasása a AsDocumentQuery ()
Az alábbi mintakód bemutatja, hogyan kérhet le mérőszámokat a [AsDocumentQuery ()](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx) metódus használatakor:

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>QueryMetrics összesítése

Az előző szakaszban figyelje meg, hogy a [ExecuteNextAsync](https://msdn.microsoft.com/library/azure/dn850294.aspx) metódusnak több hívása is volt. Minden hívás egy `FeedResponse` olyan objektumot adott vissza, amely a `QueryMetrics`lekérdezés minden folytatásához tartalmaz egy-egy szótárt. Az alábbi példa bemutatja, hogyan összesítheti `QueryMetrics` ezeket a LINQ használatával:

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>Lekérdezési metrikák csoportosítása partíció-azonosító alapján

A `QueryMetrics` partíciót azonosító alapján csoportosíthatja. A Partition ID szerinti csoportosítással megtekintheti, hogy egy adott partíció okoz-e teljesítménnyel kapcsolatos problémákat, amikor másokhoz hasonlít. Az alábbi példa bemutatja, hogyan csoportosíthatók `QueryMetrics` a LINQ:

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>LINQ on DocumentQuery

A következő `AsDocumentQuery()` metódussal is `FeedResponse` kérheti le a LINQ-lekérdezést:

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Költséges lekérdezések

Az egyes lekérdezések által felhasznált kérelmek mennyiségét rögzítheti a nagy átviteli sebességet használó költséges lekérdezések vagy lekérdezések vizsgálatához. A kérések díját a [RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) tulajdonsággal `FeedResponse`kérheti le. Ha többet szeretne megtudni arról, hogyan kérheti le a kérelmek díját a Azure Portal és a különböző SDK-k használatával, tekintse meg [a kérelem egységének megkeresése](find-request-unit-charge.md) című cikket.

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>Lekérdezés végrehajtási idejének lekérése

Az ügyféloldali lekérdezés végrehajtásához szükséges idő kiszámításakor ügyeljen arra, hogy csak a `ExecuteNextAsync` metódus meghívásának idejét és a kód más részeit tartalmazza. Ezek a hívások segítenek kiszámítani, hogy mennyi ideig tartott a lekérdezés végrehajtása a következő példában látható módon:

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>Lekérdezések vizsgálata (általában lassú és költséges)

A vizsgálati lekérdezés olyan lekérdezésre hivatkozik, amely nem az index által szolgált, mert számos dokumentum töltődik be az eredményhalmaz visszaküldése előtt.

Az alábbi példa egy vizsgálati lekérdezést mutat be:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

A lekérdezés szűrője a System (felső) rendszerfunkciót használja, amely nem az indexből van kézbesítve. A lekérdezés egy nagy gyűjteményen való végrehajtása a következő lekérdezési metrikákat hozta létre az első folytatáshoz:

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Jegyezze fel a lekérdezési metrika kimenetének következő értékeit:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

A lekérdezés 60 951 olyan dokumentumot töltött be, amely összesen 399 998 938 bájtot adott meg. A sok bájt betöltése nagy költséggel vagy a kérések egységének díjszabásával jár. A lekérdezés végrehajtása hosszú időt is igénybe vesz, amely a teljes töltött idő tulajdonsággal együtt egyértelmű:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

Azt jelenti, hogy a lekérdezés végrehajtása 4,5 másodperc volt (és ez csak egy folytatás volt).

A példában szereplő lekérdezés optimalizálásához Kerülje a szűrőben lévő felső rész használatát. Ehelyett a dokumentumok létrehozásakor vagy frissítésekor az értékeket az `c.description` összes nagybetűvel kell beszúrni. A lekérdezés ekkor a következőket válik: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Ez a lekérdezés mostantól elérhető az indexből.

A lekérdezési teljesítmény finomhangolásával kapcsolatos további tudnivalókért tekintse meg a [lekérdezési teljesítmény finomhangolása](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) című cikket.

## <a id="References"></a>Hivatkozások

- [Az Azure Cosmos DB SQL-specifikáció](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>További lépések

- [Lekérdezési teljesítmény hangolása](sql-api-query-metrics.md)
- [Indexelés – áttekintés](index-overview.md)
- [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-cosmos-dotnet-v3)
