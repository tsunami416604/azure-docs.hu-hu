---
title: Figyeléséről és hibakereséséről mérőszámok az Azure Cosmos DB |} A Microsoft Docs
description: Az Azure Cosmos DB metrikák használatával kapcsolatos gyakori problémák megoldásában, és figyelheti az adatbázis.
keywords: metrics
services: cosmos-db
author: kanshiG
manager: kfile
editor: ''
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: govindk
ms.openlocfilehash: 9b9f72812b1a1f0e30379c32e10d316fcbf71d3b
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37345589"
---
# <a name="monitoring-and-debugging-with-metrics-in-azure-cosmos-db"></a>Figyelés és az Azure Cosmos DB metrikákkal hibakeresés

Az Azure Cosmos DB mérőszámait átviteli sebesség, a storage, a konzisztencia, a rendelkezésre állás és a késés. A [az Azure portal](https://portal.azure.com) összesített nézete metrikák; a részletes metrikákért, mind az ügyfél SDK-t és a [diagnosztikai naplók](./logging.md) érhetők el.

Az új mérőszámok áttekintése, és ismerje meg a keresés forró partíciók az adatbázisban, a következő videó az Azure Friday:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Cosmos-DB-Get-the-Most-Out-of-Provisioned-Throughput/player]
> 

Ez a cikk végigvezeti a gyakori használati esetek és hogyan az Azure Cosmos DB-metrikák elemzéséhez, és ezeket a problémákat hibakeresés használható. Metrikák legyenek gyűjtve 5 percenként, és hét napig megőrződnek.

## <a name="understanding-how-many-requests-are-succeeding-or-causing-errors"></a>Hány kérésnek sikeres, vagy hibát okozó ismertetése

A kezdéshez lépjen a [az Azure portal](https://portal.azure.com) , és keresse meg a **metrikák** panelen. A panelen keresse meg a **kérelmek száma túllépte a kapacitást 1 percenként** diagram. Ez a diagram egy perces által percenként összes kérelmet az állapotkódot alapján szegmentált jeleníti meg. HTTP-állapotkódok kapcsolatos további információkért lásd: [HTTP-állapotkódok az Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

A leggyakoribb állapot hibakód 429-es (korlátozza/szabályozási értékelje), ami azt jelenti, hogy az Azure Cosmos DB-kérelmek meghaladják a kiosztott átviteli sebesség. Ez a leggyakoribb megoldás az, hogy [vertikális felskálázás a RUs](./set-throughput.md) az adott gyűjtemény számára.

![Percenkénti kérések száma](media/use-metrics/metrics-12.png)

## <a name="determining-the-throughput-distribution-across-partitions"></a>Partíciók között az átviteli sebesség terjesztési meghatározása

A partíciókulcsok egy jó számossága kellene elengedhetetlen bármilyen méretezhető alkalmazást. Annak megállapításához, partíciók bontásban particionált gyűjtemény átviteli elosztását, keresse meg a **metrikák paneljén** a a [az Azure portal](https://portal.azure.com). Az a **átviteli** lapon jelenik meg a storage táblázat összefoglalja a **maximálisan felhasznált RU/s egyes fizikai partíciók által** diagram. A következő ábrán az adatok a bal szélső magokon partíció igazolt gyenge terjesztési példáját mutatja be. 

![Egypartíciós jelennek meg (nagy erőforrásigényű) használati du. 3:05:](media/use-metrics/metrics-17.png)

Az egyenetlen átviteli terjesztési okozhat *gyakori elérésű* partíció, amely szabályozott kérelmeinek eredményezhet, és újraparticionálása lehet szükség. Az Azure Cosmos DB particionálási kapcsolatos további információkért lásd: [particionálási és horizontális az Azure Cosmos DB](./partition-data.md).

## <a name="determining-the-storage-distribution-across-partitions"></a>A tároló terjesztési meghatározása a partíciók között

A partíció egy jó számossága kellene elengedhetetlen bármilyen méretezhető alkalmazást. Annak megállapításához, partíciók bontásban particionált gyűjtemény átviteli eloszlása, a metrikák paneljén lépjen az [az Azure portal](https://portal.azure.com). Az átviteli sebesség lapon tárolási táblázat összefoglalja a által maximálisan felhasznált RU/s minden egyes fizikai partíciók diagramon látható. A következő ábra mutatja be a bal szélső magokon partíció igazolt adatok gyenge eloszlása. 

![Gyenge adatelosztás – példa](media/use-metrics/metrics-07.png)

Gyökérok is melyik partíciókulcs a partíció a diagram kattintva van eltorzítják a terjesztési. 

![Partíciós kulccsal van eltorzítják a terjesztés](media/use-metrics/metrics-05.png)

Melyik partíciós kulcs azonosítja a terjesztési okoz a döntés, előfordulhat, hogy után a gyűjtemény újraparticionálni több elosztott partíciós kulccsal. Az Azure Cosmos DB particionálási kapcsolatos további információkért lásd: [particionálási és horizontális az Azure Cosmos DB](./partition-data.md).

## <a name="comparing-data-size-against-index-size"></a>Index mérete alapján adatméret összehasonlítása

Az Azure Cosmos DB a teljes felhasznált tárterület mérete az adatok mérete és a Index mérete együttes használata. Az index mérete általában egy töredékét, az adatok mérete. A metrikák paneljén, az a [az Azure portal](https://portal.azure.com), a tárolás lapon bemutatja az adatok és index alapján tárhelyhasználat áttekintését. Kép (esetleg) azt is megteheti, SDK, annak keresztül egy gyűjteményt, olvassa el a jelenlegi tárolási használata.
```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll")); 
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
``` 
Ha index megtakarítása érdekében szeretné, módosíthatja a [indexelési szabályzat](./indexing-policies.md).

## <a name="debugging-why-queries-are-running-slow"></a>Hibakeresés miért lekérdezések lassan futnak

Azure Cosmos DB SQL API SDK-k, lekérdezés-végrehajtási statisztikák biztosít. 

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

*QueryMetrics* részletesen végrehajtása mennyi ideig egyes összetevői a lekérdezés által tartott. A leggyakoribb kiváltó sokáig futó lekérdezések vizsgálatok (a lekérdezés nem tudta használhatja az indexeket), amely feloldható egy jobb szűrő feltétellel.

## <a name="next-steps"></a>További lépések

Most, hogy bemutattuk, hogyan figyelheti és hibakeresési problémák a metrikák az Azure Portal használatával, érdemes tudhat meg többet az adatbázis-teljesítmény javítása a következő cikkek elolvasásával:

* [Teljesítmény- és mérettesztelés az Azure Cosmos DB tesztelése](performance-testing.md)
* [Az Azure Cosmos DB a teljesítménnyel kapcsolatos tippek](performance-tips.md)
