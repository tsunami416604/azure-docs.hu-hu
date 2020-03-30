---
title: Az Azure Cosmos DB metrikák figyelése és hibakeresése
description: Az Azure Cosmos DB metrikáival hibakeresést okozhat a gyakori problémák, és figyelheti az adatbázist.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: b65bc6097d4841c79a68d4313ac7a3f89f6d1dbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065935"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Az Azure Cosmos DB metrikák figyelése és hibakeresése

Az Azure Cosmos DB az átviteli sebességhez, a tároláshoz, a konzisztenciához, a rendelkezésre álláshoz és a késéshez nyújt metrikákat. Az Azure Portal ezen metrikák összesített nézetét nyújtja. Az Azure Cosmos DB-metrikákat az Azure Monitor API-ból is megtekintheti. Ha többet szeretne megtudni arról, hogyan tekintheti meg a metrikákat az Azure Monitorból, tekintse meg a [Metrikák begyűjtése](cosmos-db-azure-monitor-metrics.md) az Azure Monitorból cikket. 

Ez a cikk bemutatja a gyakori használati eseteket, és azt, hogy az Azure Cosmos DB-metrikák hogyan használhatók a problémák elemzésére és hibakeresésére. A mérőszámok öt percenként kerülnek összegyűjtésre, és hét napig tárolják.

## <a name="view-metrics-from-azure-portal"></a>Metrikák megtekintése az Azure Portalról

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg a **Metrikák** ablaktáblát. Alapértelmezés szerint a metrikák ablaktábla az Azure Cosmos-fiók összes adatbázisának tárolási, index- és kérési egységek metrikákat jeleníti meg. Ezeket a metrikákat adatbázisonként, tárolónként vagy régiónként szűrheti. A metrikák at is szűrheti egy adott időpontban. Az átviteli, tárolási, rendelkezésre állási, késési és konzisztencia-metrikák további részletek külön lapokon találhatók. 

   ![Cosmos DB teljesítménymetrikák az Azure Portalon](./media/use-metrics/performance-metrics.png)

A **metrikák** ablaktáblán a következő mérőszámok érhetők el: 

* **Átviteli metrikák** – Ez a metrika a felhasznált vagy sikertelen kérelmek számát mutatja (429 válaszkód), mert a tároló számára kiosztott átviteli sebesség vagy tárolókapacitás túllépte.

* **Tárolási metrikák** – Ez a metrika az adatok és az indexhasználat méretét mutatja.

* **Rendelkezésre állási metrikák** – Ez a metrika a sikeres kérelmek százalékos arányát mutatja az óránkénti összes kérelemhez viszonyítva. A sikerességi arányt az Azure Cosmos DB SLA-k határozzák meg.

* **Késésmetrikái** – Ez a metrika az Azure Cosmos DB által megfigyelt olvasási és írási késést mutatja abban a régióban, ahol a fiók működik. A georeplikált fiók régiói közötti késést vizualizálhatja. Ez a metrika nem képviseli a végpontok között kérelem késése.

* **Konzisztenciametriák** – Ez a metrika azt mutatja be, hogy a végleges a konzisztencia a választott konzisztenciamodell. Többrégiós fiókok esetén ez a mérőszám a kiválasztott régiók replikációs késését is mutatja.

* **Rendszermetrikák** – Ez a metrika azt mutatja, hogy a fő partíció hány metaadat-kérelmet szolgál ki. Ez is segít a szabályozott kérelmek azonosításában.

Az alábbi szakaszok ismertetik a gyakori forgatókönyveket, ahol használhatja az Azure Cosmos DB metrikák. 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>A sikeres vagy hibát okozó kérelmek száma

Első lépésekhez nyissa meg az [Azure Portalt,](https://portal.azure.com) és keresse meg a **Metrikák** panelt. A panelen keresse meg a **A kérelmek száma meghaladja a kapacitást 1 perces diagramonként. Ez a diagram az állapotkód szerint szegmentált összes kérelem percről percre történő ábrázolását mutatja. A HTTP-állapotkódokról az [Azure Cosmos DB HTTP-állapotkódjai](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb)című témakörben talál további információt.

A leggyakoribb hibaállapot-kód a 429 (sebességkorlátozás/szabályozás). Ez a hiba azt jelenti, hogy az Azure Cosmos DB-nek érkező kérelmek több, mint a kiosztott átviteli. A probléma leggyakoribb megoldása az adott gyűjtemény [rt-s kódja.](./set-throughput.md)

![Kérelmek száma percenként](media/use-metrics/metrics-12.png)

## <a name="determine-the-throughput-distribution-across-partitions"></a>A partíciók közötti átviteli terjesztés meghatározása

A partíciókulcsok jó számossága minden méretezhető alkalmazáshoz elengedhetetlen. Bármely particionált tároló partíciók szerinti átviteli teljesítményeloszlásának meghatározásához keresse meg a **Metrikák panelt** az [Azure Portalon.](https://portal.azure.com) Az **átviteli sebesség** lapon a tárolási bontás az **egyes fizikai partíciódiagramok által felhasznált RU/másodperc ben** jelenik meg. Az alábbi ábra az adatok gyenge eloszlását mutatja be a bal szélen lévő ferde partíció szerint.

![Egyetlen partíció, amely 15:05-kor látja a nagy használatot](media/use-metrics/metrics-17.png)

Az egyenetlen átviteli terjesztési okozhat *a rendszer függőben* lévő partíciókat, ami szabályozott kérelmeket eredményezhet, és újraparticionálást igényelhet. Az Azure Cosmos DB particionálásáról további információt a [Partíció és méretezés az Azure Cosmos DB-ben című témakörben talál.](./partition-data.md)

## <a name="determine-the-storage-distribution-across-partitions"></a>A partíciók közötti tárolási eloszlás meghatározása

A partíció jó számossága minden méretezhető alkalmazáshoz elengedhetetlen. Bármely particionált tároló partíciók szerinti tárolási eloszlásának meghatározásához menjen a Metrikák panelhez az [Azure Portalon.](https://portal.azure.com) A Tárolás lapon a tárolási bontás a legfelső partícióbillentyűk diagram által felhasznált Adatok + Index tárolóban jelenik meg. Az alábbi ábra az adattárolás gyenge eloszlását mutatja, ahogy azt a bal szélen lévő ferde partíció mutatja.

![Példa gyenge adatterjesztésre](media/use-metrics/metrics-07.png)

A diagram partíciójára kattintva megszakíthatja, hogy melyik partíciókulcs dönti el a terjesztést.

![A partíciókulcs elferdíti a terjesztést](media/use-metrics/metrics-05.png)

Miután azonosította, hogy melyik partíciókulcs okozza a ferdítést a disztribúcióban, előfordulhat, hogy újra kell particionálnia a tárolót egy elosztottabb partíciós kulccsal. Az Azure Cosmos DB particionálásáról további információt a [Partíció és méretezés az Azure Cosmos DB-ben című témakörben talál.](./partition-data.md)

## <a name="compare-data-size-against-index-size"></a>Az adatok méretének összehasonlítása az indexmérettel

Az Azure Cosmos DB-ben a teljes felhasznált tárterület az adatméret és az index méretének kombinációja. Az index mérete általában az adatméret töredéke. Az [Azure Portal](https://portal.azure.com)Metrikák panelen a Storage lap bemutatja a tárolási felhasználás adatok és index alapján történő bontását.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Ha helyet szeretne őrizni az indextérben, módosíthatja az [indexelési házirendet.](index-policy.md)

## <a name="debug-why-queries-are-running-slow"></a>Hibakeresés, hogy miért lassúak a lekérdezések

Az SQL API SDK-k, az Azure Cosmos DB lekérdezés-végrehajtási statisztikákat biztosít.

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

*QueryMetrics* részletesen ismerteti, hogy a lekérdezés egyes összetevői nek végrehajtása mennyi ideig tartott. A hosszú ideig futó lekérdezések leggyakoribb kiváltó oka a beolvasás, ami azt jelenti, hogy a lekérdezés nem tudta kihasználni az indexeket. Ez a probléma jobb szűrőfeltétellel oldható meg.

## <a name="next-steps"></a>További lépések

Most már megtanulta, hogyan figyelheti és debug problémák az Azure Portalon megadott metrikák használatával. Az alábbi cikkek elolvasásával többet is megtudhat az adatbázis teljesítményének javításáról:

* Ha többet szeretne megtudni arról, hogyan tekintheti meg a metrikákat az Azure Monitorból, tekintse meg a [Metrikák begyűjtése](cosmos-db-azure-monitor-metrics.md) az Azure Monitorból cikket. 
* [Teljesítmény- és méretezési tesztelés az Azure Cosmos DB-vel](performance-testing.md)
* [Az Azure Cosmos DB teljesítményével kapcsolatos tippek](performance-tips.md)
