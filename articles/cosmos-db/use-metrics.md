---
title: A figyelő és hibakeresése az Azure Cosmos DB metrikákkal |} A Microsoft Docs
description: Az Azure Cosmos DB metrikák használatával kapcsolatos gyakori problémák megoldásában, és figyelheti az adatbázis.
keywords: metrics
services: cosmos-db
author: kanshiG
manager: kfile
editor: ''
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: govindk
ms.openlocfilehash: 5e2ff89c45797e8453a3a3e4da4ac7cbe1ee6f16
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852877"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>A figyelő és hibakeresése az Azure Cosmos DB metrikákkal

Az Azure Cosmos DB mérőszámait átviteli sebesség, a storage, a konzisztencia, a rendelkezésre állás és a késés. A [az Azure portal](https://portal.azure.com) ezeket a metrikákat egy összesített nézetét biztosítja. A részletes metrikákért, mind az ügyfél SDK-t és a [diagnosztikai naplók](./logging.md) érhetők el.

Ez a cikk végigvezeti a gyakori használati esetek és hogyan az Azure Cosmos DB-metrikák elemzéséhez, és ezeket a problémákat hibakeresés használható. Metrikák legyenek gyűjtve 5 percenként, és hét napig őrzi meg.

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Megismerheti, hogy hány kérésnek okozza a hibákat vagy sikeres

A kezdéshez lépjen a [az Azure portal](https://portal.azure.com) , és keresse meg a **metrikák** panelen. A panelen keresse meg a **kérelmek száma túllépte a kapacitást 1 percenként** diagram. Ez a diagram egy perces által percenként összes kérelmet az állapotkódot alapján szegmentált jeleníti meg. HTTP-állapotkódok kapcsolatos további információkért lásd: [HTTP-állapotkódok az Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

A leggyakoribb állapot hibakód 429-es (sebessége korlátozza/szabályozás). Ez a hiba azt jelenti, hogy a kérelmek az Azure Cosmos DB több, mint a kiosztott átviteli sebesség. A leggyakoribb megoldás erre a problémára [vertikális felskálázás a RUs](./set-throughput.md) az adott gyűjtemény számára.

![Percenkénti kérések száma](media/use-metrics/metrics-12.png)

## <a name="determine-the-throughput-distribution-across-partitions"></a>Az átviteli sebesség terjesztési határozza meg a partíciók között

A partíciókulcsok egy jó számossága kellene elengedhetetlen bármilyen méretezhető alkalmazást. Annak megállapításához, az átviteli sebesség terjesztési bármely particionált tároló partíciók bontásban, keresse meg a **metrikák paneljén** a a [az Azure portal](https://portal.azure.com). Az a **átviteli** lapon jelenik meg a storage táblázat összefoglalja a **maximálisan felhasznált RU/s egyes fizikai partíciók által** diagram. A következő ábrán az adatok egy alacsony terjesztési példáját mutatja be, ahogy azt a lehetőséget a bal szélen magokon partíció.

![Egypartíciós jelennek meg (nagy erőforrásigényű) használati du. 3:05:](media/use-metrics/metrics-17.png)

Az egyenetlen átviteli terjesztési okozhat *gyakori elérésű* partíció, amely szabályozott kérelmeinek eredményezhet, és újraparticionálása lehet szükség. Az Azure Cosmos DB particionálási kapcsolatos további információkért lásd: [particionálási és horizontális az Azure Cosmos DB](./partition-data.md).

## <a name="determine-the-storage-distribution-across-partitions"></a>Határozza meg a tároló terjesztési partíciók között

A partíció egy jó számossága kellene elengedhetetlen bármilyen méretezhető alkalmazást. Annak megállapításához, minden olyan partíció szerinti bontásban a particionált tároló átviteli eloszlása, a metrikák paneljén lépjen az [az Azure portal](https://portal.azure.com). Az átviteli sebesség lapon tárolási táblázat összefoglalja a által maximálisan felhasznált RU/s minden egyes fizikai partíciók diagramon látható. A következő ábra mutatja be az adatok egy alacsony terjesztési, ahogy azt a lehetőséget a bal szélen magokon partíció.

![Gyenge adatelosztás – példa](media/use-metrics/metrics-07.png)

Gyökérok is melyik partíciókulcs a partíció a diagram kattintva van eltorzítják a terjesztési.

![Partíciós kulccsal van eltorzítják a terjesztés](media/use-metrics/metrics-05.png)

Melyik partíciós kulcs azonosítja a terjesztési okoz a döntés, előfordulhat, hogy után a tároló újraparticionálni több elosztott partíciós kulccsal. Az Azure Cosmos DB particionálási kapcsolatos további információkért lásd: [particionálási és horizontális az Azure Cosmos DB](./partition-data.md).

## <a name="compare-data-size-against-index-size"></a>Hasonlítsa össze az adatok mérete alapján index mérete

Az Azure Cosmos DB a teljes felhasznált tárterület mérete az adatok mérete és a Index mérete együttes használata. Az index mérete általában egy töredékét, az adatok mérete. A metrikák paneljén, az a [az Azure portal](https://portal.azure.com), a tárolás lapon bemutatja az adatok és index alapján tárhelyhasználat áttekintését.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Ha index megtakarítása érdekében szeretné, módosíthatja a [indexelési szabályzat](index-policy.md).

## <a name="debug-why-queries-are-running-slow"></a>Hibakeresési lekérdezések miért a lassan futnak

Azure Cosmos DB SQL API SDK-k, lekérdezés-végrehajtási statisztikák biztosít.

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
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

*QueryMetrics* részletesen végrehajtása mennyi ideig egyes összetevői a lekérdezés által tartott. A leggyakoribb okát vizsgálatok hosszú ideig futó lekérdezések van, ami azt jelenti, a lekérdezés nem tudta használhatja az indexeket. Ez a probléma a hatékonyabb szűrési feltétel lehet orvosolni.

## <a name="next-steps"></a>További lépések

Most már bemutattuk, figyelése és a hibakeresési problémák a metrikák az Azure Portal használatával. További információt a következő cikkek elolvasásával adatbázis-teljesítmény javításához érdemes:

* [Teljesítmény- és mérettesztelés az Azure Cosmos DB tesztelése](performance-testing.md)
* [Az Azure Cosmos DB a teljesítménnyel kapcsolatos tippek](performance-tips.md)
