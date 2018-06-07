---
title: Figyelés és az Azure Cosmos DB a metrikák hibakeresés |} Microsoft Docs
description: Az Azure Cosmos Adatbázisba mérőszámok segítségével gyakori problémák hibakeresését, és figyelheti az adatbázis.
keywords: metrics
services: cosmos-db
author: gnot
manager: kfile
editor: ''
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: govindk
ms.openlocfilehash: 49a381efa0603889336f43e409698bbcef44f41f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34615641"
---
# <a name="monitoring-and-debugging-with-metrics-in-azure-cosmos-db"></a>Figyelés és az Azure Cosmos DB a metrikák hibakeresés

Azure Cosmos DB átviteli, a tárolás, a konzisztencia, a rendelkezésre állási és a késleltetés metrikákat biztosít. A [Azure-portálon](https://portal.azure.com) összesített nézete a metrikák; részletesebb metrika, mind az ügyfél SDK és a [diagnosztikai naplók](./logging.md) érhetők el.

Az új mérőszámok áttekintést kaphat, és ismerje meg a keresési működés közbeni partíciók az adatbázisban, tekintse meg a következő Azure péntek videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Cosmos-DB-Get-the-Most-Out-of-Provisioned-Throughput/player]
> 

Ez a cikk végigvezeti a gyakori használati esetek és hogyan Azure Cosmos DB mérőszámok segítségével elemezheti és a problémák hibakeresését. Metrikák 5 percenként összegyűjtött, és hét napig megőrződnek.

## <a name="understanding-how-many-requests-are-succeeding-or-causing-errors"></a>Az ismertetése, hogy hány kérésnek sikeresen lezajlottak vagy okozó hibák

A kezdéshez keressen fel a [Azure-portálon](https://portal.azure.com) , és keresse meg a **metrikák** panelen. A panelen keresse meg a **kérések száma meghaladja a kapacitását / 1 perc** diagram. Ezen a diagramon látható egy perc által perc összes kérelem szegmentált által az állapotkód. További információ a HTTP-állapotkódok: [HTTP-állapotkódok Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

A leggyakoribb állapot hibakód 429 (sávszélesség-szabályozás), ami azt jelenti, hogy az Azure Cosmos Adatbázishoz kérelmek túllépte a létesített átviteli sebesség. Ez a leggyakrabban használt megoldás [növelheti a RUs](./set-throughput.md) az adott gyűjtemény.

![Percenként kérelmek száma](media/use-metrics/metrics-12.png)

## <a name="determining-the-throughput-distribution-across-partitions"></a>Az átviteli sebesség terjesztési közötti partíciók meghatározása

A partíciós kulcsok jó számosságuk rendelkező elengedhetetlen méretezhető alkalmazásokat. Annak megállapításához, a partíciók bontásban particionált gyűjtemény átviteli terjesztése, navigáljon a **metrikák panel** a a [Azure-portálon](https://portal.azure.com). Az a **átviteli** lapon, a tárolási lebontása látható a **RU/mp összes fizikai partíció által felhasznált maximális** diagram. A következő ábra a kihasználtságot partíció bal igazolt adatok gyenge terjesztési mutatja be. 

![Az egypartíciós jelent a nagy mennyiségű használati du. 3:05:](media/use-metrics/metrics-17.png)

Az egyenetlen átviteli terjesztési okozhat *működés közbeni* partíciók, amely szabályozottan halmozott kérelmeket eredményezhet, és úgy lehet szükség. Az Azure Cosmos Adatbázisba partícionálásra vonatkozó további információkért lásd: [partíció és a skála Azure Cosmos DB](./partition-data.md).

## <a name="determining-the-storage-distribution-across-partitions"></a>A tároló terjesztési közötti partíciók meghatározása

A partíció jó számosságuk rendelkező elengedhetetlen méretezhető alkalmazásokat. Annak megállapításához, a partíciók bontásban particionált gyűjtemény átviteli terjesztése, keressen fel a metrikák paneljén a [Azure-portálon](https://portal.azure.com). Az átviteli sebesség lapon tárolási bontásban tartalmazza a Max RU/mp használni minden fizikai partíció diagram jelenik meg. A következő ábra mutatja be az adatok a kihasználtságot partíció bal igazolt gyenge terjesztési. 

![Gyenge adatok terjesztési – példa](media/use-metrics/metrics-07.png)

Gyökérok is mely partíciós kulcs van döntés eloszlását a diagram a partíción kattintva. 

![Partíciós kulcs van döntés a terjesztési](media/use-metrics/metrics-05.png)

Miután mely partíciókulcs azonosítása a terjesztési okoz a döntés, előfordulhat, hogy particionálni a gyűjtemény több elosztott partíciós kulccsal. Az Azure Cosmos Adatbázisba partícionálásra vonatkozó további információkért lásd: [partíció és a skála Azure Cosmos DB](./partition-data.md).

## <a name="comparing-data-size-against-index-size"></a>Adatok mérete alapján index mérete összehasonlítása

Az Azure Cosmos Adatbázisba a teljes felhasznált tárterület az adatok mérete és az Index mérete. Az index mérete általában egy azon részét, az adatok mérete. A metrikák paneljén található a [Azure-portálon](https://portal.azure.com), a tárolás lapon bontásban tartalmazza az adatokat és az index alapján tárhelyhasználatot bővíthető. Kép (lehet, hogy) azt is megteheti, az SDK-ból található keresztül olvassa el a gyűjtemény aktuális tárolási felhasználásának mértéke.
```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll")); 
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
``` 
Ha azt szeretné, index lemezterületet takarítson meg, beállíthatja a [házirend indexelő](./indexing-policies.md).

## <a name="debugging-why-queries-are-running-slow"></a>Ezért lekérdezések lassú futnak hibakeresés

Az SQL API SDK-k Azure Cosmos DB lekérdezés végrehajtási statisztika biztosít. 

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
 “SELECT * FROM c WHERE c.city = ‘Seattle’”, 
 new FeedOptions 
 { 
 PopulateQueryMetrics = true, 
 MaxItemCount = -1, 
 MaxDegreeOfParallelism = -1, 
 EnableCrossPartitionQuery = true 
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id 
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* részleteit mennyi ideig a lekérdezés minden összetevő-tartott a végrehajtása. A leggyakrabban használt alapvető ok hosszú futtatásához lekérdezések vizsgálatok (a lekérdezés nem tudta használni a indexek), amely lehet oldott fel egy jobb szűrési feltételt.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte figyelése és az Azure-portálon a metrikák hibakeresési a hibákat, érdemes lehet tudhat meg többet a következő cikkekben olvasásával adatbázis teljesítményének növelése:

* [Teljesítmény és méretezhetőség, az Azure Cosmos DB tesztelése](performance-testing.md)
* [Teljesítmény tippek az Azure Cosmos DB rendszerhez](performance-tips.md)
