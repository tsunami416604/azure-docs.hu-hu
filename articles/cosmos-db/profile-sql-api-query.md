---
title: SQL-lekérdezési teljesítmény és a végrehajtási metrikák beolvasása
description: Ismerje meg, hogyan kérheti le az SQL-lekérdezés végrehajtási metrikák és a profil SQL lekérdezési teljesítmény az Azure Cosmos DB-kérelmeket.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: b4017666956d0e01ea19781fb4f1ce2dde15fff5
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481564"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>SQL-lekérdezés végrehajtási metrikák beolvasása és elemzése a lekérdezési teljesítmény .NET SDK használatával

Ez a cikk bemutatja hogyan SQL lekérdezési teljesítmény az Azure Cosmos DB-profil. A profilkészítés végezhető használatával `QueryMetrics` olvassa be a .NET SDK, és itt részleteit. [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) információkkal, a háttér-lekérdezés végrehajtása egy szigorú típusmegadású objektum. Ezek a metrikák részletesen ismertetett a [lekérdezési teljesítmény hangolása](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) cikk.

## <a name="set-the-feedoptions-parameter"></a>Állítsa a FeedOptions paramétert

Az összes túlterheléssel [DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) vesz egy nem kötelező [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) paraméter. Ez a beállítás akkor mit lehetővé teszi, hogy a beállított és paraméteres lekérdezés végrehajtása. 

Az Sql-lekérdezés végrehajtási metrikák gyűjteni, be kell állítani a paraméter [PopulateQueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) a a [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) való `true`. Beállítás `PopulateQueryMetrics` , igaz teszi, hogy a `FeedResponse` fogja tartalmazni a megfelelő `QueryMetrics`. 

## <a name="get-query-metrics-with-asdocumentquery"></a>Lekérdezés AsDocumentQuery() metrikák beolvasása
Az alábbi példakód bemutatja, hogyan használatakor a metrikák beolvasása [AsDocumentQuery()](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx) módszer:

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

Az előző szakaszban láthatja, hogy voltak-e több alkalommal hívnia [ExecuteNextAsync](https://msdn.microsoft.com/library/azure/dn850294.aspx) metódust. Minden egyes hívása adott vissza egy `FeedResponse` , amely rendelkezik a tartalmazó objektum `QueryMetrics`; egy a lekérdezés minden folytatása. Az alábbi példa bemutatja, hogyan összesített ezek `QueryMetrics` LINQ használatával:

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

## <a name="grouping-query-metrics-by-partition-id"></a>A csoportosítás lekérdezés metrikák Partícióazonosító szerint

Csoportosíthatja a `QueryMetrics` által a partíció-azonosító. Partícióazonosító szerinti csoportosítás lehetővé teszi, hogy láthatja, ha egy adott partícióra okozza-e teljesítményproblémákat másokhoz képest. Az alábbi példa bemutatja, hogyan csoportosítsa `QueryMetrics` lehetőségeit a LINQ to:

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

## <a name="linq-on-documentquery"></a>A DocumentQuery LINQ

Is beszerezheti a `FeedResponse` egy LINQ-lekérdezésekre használatával a `AsDocumentQuery()` módszer:

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Drága lekérdezéssel rendelkezik

A kérelemegység egyes lekérdezések által felhasznált vizsgálhatja a drága lekérdezések vagy által felhasználható nagy teljesítményű lekérdezéseket rögzítheti. Megtekintheti a kérelem díja használatával a [RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) tulajdonság `FeedResponse`. Hogyan kérhet a kérelem díja az Azure portal és a különböző SDK-k használatával kapcsolatos további információkért lásd: [keresse meg a kérelem egységek használata után](find-request-unit-charge.md) cikk.

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

## <a name="get-the-query-execution-time"></a>A lekérdezés-végrehajtási idő lekérése

Kiszámításakor egy ügyféloldali lekérdezés végrehajtásához szükséges időt, ellenőrizze, hogy csak az idő meghívásához tartalmazzák a `ExecuteNextAsync` metódust, és nem a kódbázis egyéb részei. Csak ezek a hívások segítséget kiszámításakor, hogy mennyi a lekérdezés végrehajtása tartott a következő példában látható módon:

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

## <a name="scan-queries-commonly-slow-and-expensive"></a>Lekérdezések (gyakran lassú és költséges) vizsgálata

A vizsgálat lekérdezés hivatkozik egy lekérdezést, amely nem volt által kiszolgált az indexet, ami számos a dokumentumok betöltésével az eredményhalmaz visszaküldése előtt.

Az alábbi, például egy vizsgálat lekérdezés:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

A lekérdezési szűrő használja a rendszer függvény felső, amely nem szolgálja ki az indexből. A következő lekérdezés metrikák első folytatására egy nagy-gyűjteményen a lekérdezést végrehajtó állítja elő:

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

Vegye figyelembe a következő értékeket a metrikák lekérdezés kimenete:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

Ez a lekérdezés betöltése 60,951 dokumentumokat, amelyeket 399,998,938 bájt összesíti. Ennyi bájtot betöltése eredményez, magas költségeket vagy a kérelem egység díjszabásának. Hajtsa végre a lekérdezést, amely egyértelmű töltött teljes idő tulajdonság hosszú időt is szükséges:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

Ami azt jelenti, hogy a lekérdezés végrehajtásához 4.5 másodpercig tartott (és csak egy folytatási volt).

Ez a példa lekérdezés optimalizálása érdekében ne felső szűrő használatát. Ehelyett dokumentumok létrehozása és frissítése, a `c.description` értékeket kell beszúrni a nagybetűs karaktereket. A lekérdezés válik: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Ez a lekérdezés már képes kiszolgálni az indexből.

A lekérdezési teljesítmény hangolása kapcsolatos további információkért tekintse meg a [lekérdezési teljesítmény hangolása](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) cikk.

## <a id="References"></a>Hivatkozások

- [Az Azure Cosmos DB SQL-specifikáció](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>További lépések

- [Lekérdezési teljesítmény hangolása](sql-api-query-metrics.md)
- [Az indexelő áttekintése](index-overview.md)
- [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-cosmosdb-dotnet)
