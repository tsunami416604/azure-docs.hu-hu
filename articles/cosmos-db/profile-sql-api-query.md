---
title: SQL-lekérdezés teljesítményének & végrehajtási mérőszámok begyűjtése
description: Ismerje meg, hogyan kérheti be az SQL-lekérdezés-végrehajtási metrikákat és az Azure Cosmos DB-kérelmek profil SQL-lekérdezési teljesítményét.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: 48b9a67de5c870a187ee008bd97265760ca6c341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70998362"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>SQL-lekérdezésvégrehajtási mérőszámok begyűjtése és lekérdezési teljesítmény elemzése a .NET SDK használatával

Ez a cikk bemutatja, hogyan profil sql lekérdezési teljesítmény az Azure Cosmos DB. Ez a profilkészítés `QueryMetrics` a .NET SDK-ból lekért és itt részletezett használatával végezhető el. [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) egy erősen gépelt objektum, amely a háttérlekérdezés végrehajtásával kapcsolatos információkat tartalmaz. Ezeket a mutatókat a [Lekérdezés teljesítményének finomhangolása](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) című cikk részletesebben dokumentálja.

## <a name="set-the-feedoptions-parameter"></a>A FeedOptions paraméter beállítása

A [DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) összes túlterhelése egy választható [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) paramétert tartalmaz. Ez a beállítás teszi lehetővé a lekérdezésvégrehajtásának hangolást és paraméterezését. 

Az SQL-lekérdezés végrehajtási metrikáinak összegyűjtéséhez a [Betáplálási](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) `true`beállítások ban a [PopullateQueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) paramétert kell beállítania. Beállítása `PopulateQueryMetrics` igaz teszi, hogy `FeedResponse` a végrendelet `QueryMetrics`tartalmazza a megfelelő . 

## <a name="get-query-metrics-with-asdocumentquery"></a>Lekérdezési mutatók begyűjtése az AsDocumentQuery() segítségével
A következő kódminta bemutatja, hogyan lehet metrikákat lekérni [az AsDocumentQuery()](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx) metódus használatakor:

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

Az előző szakaszban figyelje meg, hogy az [ExecuteNextAsync](https://msdn.microsoft.com/library/azure/dn850294.aspx) metódustöbb hívást is kezdeményezett. Minden hívás `FeedResponse` olyan objektumot adott `QueryMetrics`vissza, amelynek szótára ; a lekérdezés minden folytatásához. A következő példa bemutatja, hogyan összesítse ezeket `QueryMetrics` a LINQ használatával:

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

## <a name="grouping-query-metrics-by-partition-id"></a>Lekérdezési mutatók csoportosítása partícióazonosító szerint

Csoportosíthatja a `QueryMetrics` partícióazonosító. A partícióazonosító szerint történő csoportosítás lehetővé teszi annak megtekintését, hogy egy adott partíció teljesítményproblémákat okoz-e másokhoz képest. A következő példa bemutatja, hogyan csoportosítsa `QueryMetrics` a LINQ-val:

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

## <a name="linq-on-documentquery"></a>LINQ a DocumentQuery-en

A `FeedResponse` LINQ-lekérdezésből is beszerezheti a `AsDocumentQuery()` következő módszert:

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Drága lekérdezések

Rögzítheti az egyes lekérdezések által felhasznált kérelemegységeket a nagy átviteli sebességű költséges lekérdezések vagy lekérdezések vizsgálatához. A kérésdíj a [RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) tulajdonság használatával `FeedResponse`szerezhető be a alkalmazásban. Ha többet szeretne megtudni arról, hogyan kaphatja be a kérelemdíj az Azure Portalon és a különböző SDK-k, [olvassa el a kérelem egység díja](find-request-unit-charge.md) cikk keresése.

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

## <a name="get-the-query-execution-time"></a>A lekérdezés végrehajtási idejének betöltése

Az ügyféloldali lekérdezés végrehajtásához szükséges idő kiszámításakor győződjön meg arról, hogy csak a metódus hívásához szükséges időt adja meg, `ExecuteNextAsync` a kódbázis más részeinek megjelenítése esetén. Csak ezek a hívások segítenek annak kiszámításában, hogy mennyi ideig tartott a lekérdezés végrehajtása a következő példában látható módon:

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

## <a name="scan-queries-commonly-slow-and-expensive"></a>Lekérdezések bekéselése (általában lassú és költséges)

A vizsgálat lekérdezés olyan lekérdezésre hivatkozik, amelyet nem az index szolgált ki, ami miatt sok dokumentum töltődik be az eredményhalmaz visszaadása előtt.

Az alábbi példa egy beszkavalizált lekérdezésre mutat be:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

A lekérdezés szűrője az UPPER rendszerfunkciót használja, amely nem az indexből jelenik meg. A lekérdezés végrehajtása egy nagy gyűjtemény en a következő lekérdezési metrikákat hozta létre az első folytatáshoz:

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

Vegye figyelembe a következő értékeket a lekérdezési metrikák kimenetéből:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

Ez a lekérdezés 60 951 dokumentumot töltött be, amelyek összesen 399 998 938 bájtot. Ennyi bájt betöltése magas költség- vagy kérelemegység-díjat eredményez. A lekérdezés végrehajtása is hosszú időt vesz igénybe, ami egyértelmű a teljes eltöltött idő tulajdonsággal:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

Ami azt jelenti, hogy a lekérdezés 4,5 másodpercet vett igénybe (és ez csak egy folytatás volt).

A példalekérdezés optimalizálásához kerülje a NAGYBETŰS szűrő használatát. Ehelyett a dokumentumok létrehozásakor vagy `c.description` frissítésekor az értékeket minden nagybetűs karakterbe be kell szúrni. A lekérdezés ekkor a következővé válik: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Ez a lekérdezés most már kiszolgálható az indexből.

A lekérdezésteljesítmény finomhangolásáról a [Lekérdezés teljesítményének finomhangolása](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) című cikkben olvashat bővebben.

## <a name="references"></a><a id="References"></a>Referencia

- [Az Azure Cosmos DB SQL specifikációja](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>További lépések

- [Lekérdezési teljesítmény hangolása](sql-api-query-metrics.md)
- [Indexelés – áttekintés](index-overview.md)
- [Az Azure Cosmos DB .NET-mintái](https://github.com/Azure/azure-cosmos-dotnet-v3)
