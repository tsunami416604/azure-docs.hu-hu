---
title: Kimenetek Azure Stream Analytics
description: Ez a cikk a Azure Stream Analytics számára elérhető adatkimeneti beállításokat ismerteti.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: ade5972226a735ce5d093890091536baf02f5eff
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89068983"
---
# <a name="outputs-from-azure-stream-analytics"></a>Kimenetek Azure Stream Analytics

Az Azure Stream Analytics feladatok egy bemenetből, lekérdezésből és kimenetből állnak. Az átalakított adatokat több kimeneti típus is elküldheti. Ez a cikk az Stream Analytics támogatott kimeneteit sorolja fel. Amikor megtervezi a Stream Analytics lekérdezést, a [INTO záradék](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics)használatával tekintse meg a kimenet nevét. A lekérdezéshez több INTO záradékot is használhat feladatokhoz, illetve több kimeneti adatfolyam-feladatokhoz (ha szüksége van rájuk).

Stream Analytics feladatok kimenetének létrehozásához, szerkesztéséhez és teszteléséhez használhatja a [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-output), a [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), a [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), a [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)és a [Visual Studio alkalmazást](stream-analytics-quick-create-vs.md).

Egyes kimeneti típusok támogatják a [particionálást](#partitioning), és a [kimeneti köteg mérete](#output-batch-size) eltérő az átviteli sebesség optimalizálása érdekében. A következő táblázat az egyes kimeneti típusok által támogatott szolgáltatásokat tartalmazza:

| Kimenet típusa | Particionálás | Biztonság | 
|-------------|--------------|----------|
|[Azure Data Lake Storage Gen 1](azure-data-lake-storage-gen1-output.md)|Igen|Azure Active Directory felhasználó </br> MSI|
|[Azure SQL Database](sql-database-output.md)|Igen, engedélyezni kell.|SQL-felhasználó hitelesítése </br> MSI (előzetes verzió)|
|[Azure szinapszis Analytics (előzetes verzió)](azure-synapse-analytics-output.md)|Nem|SQL-felhasználó hitelesítése|
|[BLOB Storage és Azure Data Lake Gen 2](blob-storage-azure-data-lake-gen2-output.md)|Igen|MSI </br> Hozzáférési kulcs|
|[Azure Event Hubs](event-hubs-output.md)|Igen|Hozzáférési kulcs|
|[Power BI](power-bi-output.md)|Nem|Azure Active Directory felhasználó </br> MSI|
|[Azure Table storage](table-storage-output.md)|Igen|Fiókkulcs|
|[Azure Service Bus-üzenetsorok](service-bus-queues-output.md)|Igen|Hozzáférési kulcs|
|[Azure Service Bus témakörök](service-bus-topics-output.md)|Igen|Hozzáférési kulcs|
|[Azure Cosmos DB](azure-cosmos-db-output.md)|Igen|Hozzáférési kulcs|
|[Azure Functions](azure-functions-output.md)|Igen|Hozzáférési kulcs|

## <a name="partitioning"></a>Particionálás

A Stream Analytics a Power BI kivételével minden kimenethez támogatja a partíciókat. A partíciós kulcsokkal és a kimeneti írók számával kapcsolatos további információkért tekintse meg az Önt érdeklő kimeneti típust ismertető cikket. Az összes kimeneti cikk az előző szakaszban van csatolva.  

A kimeneti írók száma a `INTO <partition count>` lekérdezésben (lásd: [into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) záradék használatával vezérelhető, ami hasznos lehet a kívánt feladatok topológiájának megvalósításában. Ha a kimeneti adapter nincs particionálva, az egyik bemeneti partícióban lévő adatok hiánya késlelteti a késői érkezési időtartamot. Ilyen esetekben a kimenet egyetlen író számára lesz egyesítve, ami szűk keresztmetszetet eredményezhet a folyamat során. Ha többet szeretne megtudni a késői beérkezési szabályzatról, tekintse meg az [Azure stream Analytics az események sorrendjét](stream-analytics-out-of-order-and-late-events.md)ismertető szakaszt

## <a name="output-batch-size"></a>Kimeneti köteg mérete

Minden kimenet támogatja a kötegelt feldolgozást, de csak néhány támogatási köteget explicit módon. A Azure Stream Analytics változó méretű kötegeket használ az események feldolgozásához és a kimenetekhez való íráshoz. Általában a Stream Analytics motor nem ír egyszerre egy üzenetet, és batchs-t használ a hatékonysághoz. Ha a bejövő és a kimenő események aránya is magas, Stream Analytics nagyobb kötegeket használ. Ha a kimenő forgalom aránya alacsony, kisebb kötegeket használ a késés alacsonyra állításához.

## <a name="parquet-output-batching-window-properties"></a>A parketta kimenetének batch ablakának tulajdonságai

Azure Resource Manager sablon központi telepítésének vagy a REST APIének használatakor a két batch-ablak tulajdonságai a következők:

1. *timeWindow*

   A maximális várakozási idő tételenként. Az értéknek egy TimeSpan sztringnek kell lennie. Például: "00:02:00" két percre. Ezt követően a köteg a kimenetbe kerül, még akkor is, ha a minimális sorokra vonatkozó követelmény nem teljesül. Az alapértelmezett érték 1 perc, az engedélyezett maximum pedig 2 óra. Ha a blob kimenetének elérésiút-mintázati gyakorisága van, a várakozási idő nem lehet nagyobb, mint a partíció időtartománya.

2. *sizeWindow*

   A kötegek minimális sorainak száma. A parketta esetében minden batch létrehoz egy új fájlt. Az aktuális alapértelmezett érték 2 000 sor, és az engedélyezett maximális érték 10 000 sor.

Ezeket a Batch-ablak tulajdonságokat csak az API 2017-04-01-es verziója támogatja **– előzetes**verzió. Az alábbi példa egy REST API hívás JSON-adattartalmát mutatja be:

```json
"type": "stream",
      "serialization": {
        "type": "Parquet",
        "properties": {}
      },
      "timeWindow": "00:02:00",
      "sizeWindow": "2000",
      "datasource": {
        "type": "Microsoft.Storage/Blob",
        "properties": {
          "storageAccounts" : [
          {
            "accountName": "{accountName}",
            "accountKey": "{accountKey}",
          }
          ],
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
>
> [Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
