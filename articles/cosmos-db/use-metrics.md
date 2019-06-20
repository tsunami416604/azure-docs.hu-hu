---
title: A figyelő és hibakeresése az Azure Cosmos DB metrikákkal
description: Az Azure Cosmos DB metrikák használatával kapcsolatos gyakori problémák megoldásában, és figyelheti az adatbázis.
ms.service: cosmos-db
author: kanshiG
ms.author: sngun
ms.topic: conceptual
ms.date: 06/18/2019
ms.reviewer: sngun
ms.openlocfilehash: ef457fe8c21bc7e62f910a78913069df32bea1a3
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275684"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>A figyelő és hibakeresése az Azure Cosmos DB metrikákkal

Az Azure Cosmos DB mérőszámait átviteli sebesség, a storage, a konzisztencia, a rendelkezésre állás és a késés. Az Azure Portalon ezeket a metrikákat egy összesített nézetét biztosítja. Azure Cosmos DB-mérőszámok az Azure Monitor API is megtekintheti. Az Azure monitor mérőszámainak megtekintése kapcsolatos további információkért tekintse meg a [az Azure Monitor metrikáinak beolvasása](cosmos-db-azure-monitor-metrics.md) cikk. 

Ez a cikk végigvezeti a gyakori használati esetek és hogyan az Azure Cosmos DB-metrikák elemzéséhez, és ezeket a problémákat hibakeresés használható. Metrikák legyenek gyűjtve 5 percenként, és hét napig őrzi meg.

## <a name="view-metrics-from-azure-portal"></a>Metrikák megtekintése az Azure Portalról

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg a **metrikák** ablaktáblán. Alapértelmezés szerint a metrikák panelen látható a tárhely, index, kérelem egységek metrikáit az Azure Cosmos-fiókjában található összes adatbázishoz. Ezek a metrikák, adatbázis, tárolót vagy régió szerint szűrheti. A metrikákat egy megadott idő részletesség, szűrheti is. További részleteket az átviteli sebesség, storage, rendelkezésre állási, késés és konzisztencia metrikák külön lapon találhatók. 

   ![A cosmos DB teljesítmény-mérőszámok az Azure Portalon](./media/use-metrics/performance-metrics.png)

A következő metrikák érhetők el a **metrikák** panelen: 

* **Átviteli sebességre vonatkozó mérőszámok** – Ez a metrika felhasznált, vagy sikertelen volt (429 válaszkód) kérések számát jeleníti meg, mert túllépte a tároló kiosztott átviteli sebesség vagy tárolókapacitás.

* **Storage-mérőszámok** – Ez a mérőszám a használati adatok és indexek mérete jeleníti meg.

* **Rendelkezésre állási metrikák** – Ez a mérőszám a sikeres kérelmek százalékos megjeleníti az összes kérelmet az óránként. Az Azure Cosmos DB SLA-k sikerességi arányát határozza meg.

* **Késés metrikák** – Ez a metrika az olvasási és írási késés, a régiót, ahol a fiók üzemel az Azure Cosmos DB által megfigyelt jeleníti meg. Egy georeplikált fiók régiók közötti késés jelenítheti meg. Ez a mérőszám a teljes körű késleltetése nem képviselik.

* **Konzisztencia-metrikák** – Ez a mérőszám azt mutatja, hogyan végleges konzisztencia modell úgy dönt, a konzisztencia. Többrégiós-fiókok esetében ez a metrika is megjeleníti a replikációs késés kiválasztott régiók között.

* **Rendszermérőszámokat** – Ez a metrika jeleníti meg, hány metaadat-kérelmek szolgáltatja a fő partíció. Azt is segít azonosítani a szabályozott kérelmeinek száma.

Az alábbi szakaszok ismertetik a gyakori forgatókönyveket, melyekben használhatja az Azure Cosmos DB-metrikák. 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Megismerheti, hogy hány kérésnek okozza a hibákat vagy sikeres

A kezdéshez lépjen a [az Azure portal](https://portal.azure.com) , és keresse meg a **metrikák** panelen. A panelen keresse meg a ** kérelmek száma túllépte a kapacitást egy 1 perces diagramban. Ez a diagram egy perces által percenként összes kérelmet az állapotkódot alapján szegmentált jeleníti meg. HTTP-állapotkódok kapcsolatos további információkért lásd: [HTTP-állapotkódok az Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

A leggyakoribb állapot hibakód 429-es (sebessége korlátozza/szabályozás). Ez a hiba azt jelenti, hogy a kérelmek az Azure Cosmos DB több, mint a kiosztott átviteli sebesség. A leggyakoribb megoldás erre a problémára [vertikális felskálázás a RUs](./set-throughput.md) az adott gyűjtemény számára.

![Percenkénti kérések száma](media/use-metrics/metrics-12.png)

## <a name="determine-the-throughput-distribution-across-partitions"></a>Az átviteli sebesség terjesztési határozza meg a partíciók között

A partíciókulcsok egy jó számossága kellene elengedhetetlen bármilyen méretezhető alkalmazást. Annak megállapításához, az átviteli sebesség terjesztési bármely particionált tároló partíciók bontásban, keresse meg a **metrikák paneljén** a a [az Azure portal](https://portal.azure.com). Az a **átviteli** lapon jelenik meg a storage táblázat összefoglalja a **maximálisan felhasznált RU/s egyes fizikai partíciók által** diagram. A következő ábrán az adatok egy alacsony terjesztési példáját mutatja be, ahogy azt a lehetőséget a bal szélen magokon partíció.

![Egypartíciós jelennek meg (nagy erőforrásigényű) használati du. 3:05:](media/use-metrics/metrics-17.png)

Az egyenetlen átviteli terjesztési okozhat *gyakori elérésű* partíció, amely szabályozott kérelmeinek eredményezhet, és újraparticionálása lehet szükség. Az Azure Cosmos DB particionálási kapcsolatos további információkért lásd: [particionálási és horizontális az Azure Cosmos DB](./partition-data.md).

## <a name="determine-the-storage-distribution-across-partitions"></a>Határozza meg a tároló terjesztési partíciók között

A partíció egy jó számossága kellene elengedhetetlen bármilyen méretezhető alkalmazást. Annak megállapításához, a tároló terjesztési bármely partíciók bontásban particionált tároló, a metrikák paneljén lépjen az [az Azure portal](https://portal.azure.com). A tárolás fülön a storage bontás jelenik meg az adatok + felső partíciós kulcsok diagram által felhasznált tárterület Index. A következő ábra mutatja be, az adattárolás, ahogy azt a lehetőséget a bal szélen magokon partíció a gyenge eloszlása.

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

* Az Azure monitor mérőszámainak megtekintése kapcsolatos további információkért tekintse meg a [az Azure Monitor metrikáinak beolvasása](cosmos-db-azure-monitor-metrics.md) cikk. 
* [Teljesítmény- és mérettesztelés az Azure Cosmos DB tesztelése](performance-testing.md)
* [Az Azure Cosmos DB a teljesítménnyel kapcsolatos tippek](performance-tips.md)
