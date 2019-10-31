---
title: Azure Cosmos DB szolgáltatási kvóták
description: Azure Cosmos DB a szolgáltatási kvótákat és az alapértelmezett korlátokat a különböző erőforrástípusok esetében.
author: arramac
ms.author: arramac
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 45cf7b7d9383de467f72769465a3ad382fe9d589
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73064032"
---
# <a name="azure-cosmos-db-service-quotas"></a>Azure Cosmos DB szolgáltatási kvóták

Ez a cikk áttekintést nyújt a Azure Cosmos DB különböző erőforrásaihoz felkínált alapértelmezett kvóták közül.

## <a name="storage-and-throughput"></a>Tárolás és átviteli sebesség

Miután létrehozta az előfizetéséhez tartozó Azure Cosmos-fiókot, a fiókban lévő adatokat [adatbázisok, tárolók és elemek létrehozásával](databases-containers-items.md)kezelheti. Az átviteli sebességet tároló szinten vagy adatbázis-szinten is kiépítheti a [kérési egységek (ru/s vagy RUs)](request-units.md)szempontjából. A következő táblázat felsorolja a tárolók és az adatbázisok tárolási és átviteli korlátait.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Maximális RUs/tároló ([dedikált teljesítményű kiépített mód](databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 alapértelmezés szerint. Megnövelheti [egy Azure-támogatási jegy bejelentésével](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) |
| Maximális RUs/adatbázis ([megosztott teljesítményű kiépített mód](databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 alapértelmezés szerint. Megnövelheti [egy Azure-támogatási jegy bejelentésével](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) |
| Maximális RUs/(logikai) partíciós kulcs | 10,000 |
| Maximális tárterület az összes elem/(logikai) partíciós kulcs között| 10 GB |
| Eltérő (logikai) partíciós kulcsok maximális száma | Korlátlan |
| Tárolók maximális száma | Korlátlan |
| Tárolók maximális száma adatbázis szerint | Korlátlan |
| A mellékletek maximális mérete (a melléklet funkciójának értékcsökkenése) | 2 GB | 

> [!NOTE]
> Ha többet szeretne megtudni a tárolási vagy átviteli sebességre vonatkozó magasabb korlátot igénylő munkaterhelések kezelésével kapcsolatos ajánlott eljárásokról, olvassa el [a szintetikus partíciós kulcs létrehozása](synthetic-partition-keys.md)című témakört.
>

A Cosmos-tárolónak (vagy a megosztott átviteli sebesség adatbázisának) legalább 400 RUs-nek kell lennie. Ahogy a tároló növekszik, a minimálisan támogatott átviteli sebesség az alábbi tényezőktől függ:

* A tárolón megadható minimális átviteli sebesség a tárolón kiépített maximális átviteli sebességtől függ. Ha például az átviteli sebesség a 10000 RUs értékre lett növelve, akkor a lehető legalacsonyabb kiépített átviteli sebesség a következő lesz: 1000 RUs
* A megosztott átviteli sebességű adatbázis minimális átviteli sebessége a megosztott átviteli sebességű adatbázisban korábban létrehozott tárolók teljes számától függ, amelyet a rendszer az 100 RUs/tárolón mért. Ha például öt tárolót hozott létre egy megosztott átviteli sebességű adatbázison belül, az átviteli sebességnek legalább 500 RUs-nek kell lennie

A tárolók vagy adatbázisok aktuális és minimális átviteli sebessége a Azure Portal vagy az SDK-k alapján kérhető le. További információkért lásd: [átviteli sebesség tárolók és adatbázisok](set-throughput.md)számára. 

> [!NOTE]
> Bizonyos esetekben előfordulhat, hogy az átviteli sebesség kevesebb, mint 10%. Az API használatával szerezze be a minimálisan szükséges RUs/tárolót.
>

Összefoglalva: itt vannak a minimálisan kiépített RU-korlátok. 

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Minimális RUs/tároló ([dedikált teljesítményű kiépített mód](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Minimális RUs/adatbázis ([megosztott teljesítményű kiépített mód](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Egy megosztott átviteli sebességű adatbázisban lévő minimális RUs/tároló | 100 |

A Cosmos DB az SDK-k vagy a portál használatával támogatja a tárolók vagy adatbázisok rugalmas skálázását. Az egyes tárolók a minimális és a maximális érték között 10 – 100-szeres skálán belül, szinkronban és azonnal méretezhetők. Ha a kért átviteli sebesség kívül esik a tartományon, a skálázás aszinkron módon történik. Az aszinkron skálázás perctől akár órákig is eltarthat, a tárolóban kért átviteli sebességtől és az adattároló méretétől függően.  

## <a name="control-plane-operations"></a>Vezérlési sík műveletei

Azure Cosmos-fiókját az Azure Portal, az Azure PowerShell, az Azure CLI és a Azure Resource Manager sablonok használatával is [kiépítheti és kezelheti](how-to-manage-database-account.md) . A következő táblázat felsorolja az előfizetések, a fiókok és a műveletek számát.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Adatbázis-fiókok maximális száma (előfizetés) | 50 alapértelmezés szerint. Megnövelheti [egy Azure-támogatási jegy bejelentésével](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)|
| Regionális feladatátvételek maximális száma | Alapértelmezés szerint 1/óra. Megnövelheti [egy Azure-támogatási jegy bejelentésével](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)|

> [!NOTE]
> A regionális feladatátvétel csak az egyetlen régióba írja a fiókokat. A többrégiós írási fiókok nem igénylik vagy nem korlátozzák az írási régió módosítását.

A Cosmos DB rendszeres időközönként automatikusan biztonsági másolatot készít az adatairól. A biztonsági másolatok megőrzési időközökről és a Windowsról a következő témakörben tájékozódhat: [online biztonsági mentés és igény szerinti adatok visszaállítása Azure Cosmos DBban](online-backup-and-restore.md).

## <a name="per-account-limits"></a>Felhasználónkénti korlátok

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Adatbázisok maximális száma | Korlátlan |
| Tárolók maximális száma adatbázison (vagy fiók) | Korlátlan |
| Régiók maximális száma | Nincs korlát (az összes Azure-régió) |

## <a name="per-container-limits"></a>/Tároló korlátok

Attól függően, hogy melyik API-t használja, az Azure Cosmos-tárolók gyűjteményeket, táblákat vagy diagramokat is jelenthetnek. A tárolók támogatják az [egyedi kulcsokra vonatkozó megkötések](unique-keys.md), [tárolt eljárások, eseményindítók és UDF](stored-procedures-triggers-udfs.md)konfigurációját, valamint az [indexelési szabályzatot](how-to-manage-indexing-policy.md). A következő táblázat felsorolja a tárolón belüli konfigurációkra vonatkozó korlátozásokat. 

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Az adatbázis vagy a tároló nevének maximális hossza | 255 |
| Tárolt eljárások maximális száma tárolóban | 100 <sup>*</sup>|
| UDF maximális száma | 25 <sup>*</sup>|
| Elérési utak maximális száma az indexelési házirendben| 100 <sup>*</sup>|
| Egyedi kulcsok maximális száma egy tárolóban|10 <sup>*</sup>|
| Elérési utak maximális száma egyedi kulcs megkötése esetén|16 <sup>*</sup>|

<sup>*</sup> Az Azure támogatási szolgálatával növelheti az egyes tárolók korlátainak bármelyikét.

## <a name="per-item-limits"></a>Cikkenként érvényes korlátok

Attól függően, hogy melyik API-t használja, egy Azure Cosmos-elem a gyűjteményben szereplő dokumentumot, egy tábla egy sorát, vagy egy gráf egyik csomópontját vagy szegélyét jelöli. A következő táblázat a Cosmos DBban található határértékeket mutatja. 

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Elemek maximális mérete | 2 MB (UTF-8 a JSON-ábrázolás hossza) |
| Partíciós kulcs értékének maximális hossza | 2048 bájt |
| Azonosító érték maximális hossza | 1024 bájt |
| Tulajdonságok maximális száma cikkenként | Nincs gyakorlati korlát |
| Maximális beágyazási mélység | Nincs gyakorlati korlát |
| Tulajdonságnév maximális hossza | Nincs gyakorlati korlát |
| Tulajdonságérték maximális hossza | Nincs gyakorlati korlát |
| Karakterlánc-tulajdonságérték maximális hossza | Nincs gyakorlati korlát |
| Numerikus tulajdonság értékének maximális hossza | IEEE754 dupla pontosságú, 64 bites |

Az elemekre nem vonatkoznak korlátozások (például a tulajdonságok száma és a beágyazás mélysége), kivéve a partíciós kulcs és az azonosító értékének korlátozásait, valamint a 2 MB-os teljes méret korlátozását. Előfordulhat, hogy a RU-felhasználás csökkentése érdekében az indexelési házirendet nagy vagy összetett elem-struktúrákkal rendelkező tárolók számára kell konfigurálnia. Tekintse meg a valós példák [modellezési elemeit Cosmos DBban](how-to-model-partition-example.md) , valamint a nagyméretű elemek kezelésére szolgáló mintákat.

## <a name="per-request-limits"></a>Kérelmekre vonatkozó korlátozások

Cosmos DB támogatja a [szifilisz-és lekérdezési műveleteket](https://docs.microsoft.com/rest/api/cosmos-db/) olyan erőforrásokon, mint például a tárolók, elemek és adatbázisok.  

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Egy művelet maximális végrehajtási ideje (például egy tárolt eljárás végrehajtása vagy egy lekérdezési lap beolvasása)| 5 MP |
| Kérelmek maximális mérete (tárolt eljárás, szifilisz)| 2 MB |
| Válasz maximális mérete (például többoldalas lekérdezés) | 4 MB |

Ha egy művelet, például a lekérdezés eléri a végrehajtás időtúllépését vagy a válasz méretkorlát értéket, a rendszer visszaadja az eredmények egy oldalát, valamint egy folytatási tokent az ügyfélnek a végrehajtás folytatásához. Az időtartamra vonatkozóan nincs gyakorlati korlát az egyes oldalakra vagy folytatásokra vonatkozó egyetlen lekérdezés futtatásához.

Cosmos DB a HMAC használja az engedélyezéshez. Használhatja a főkulcsot vagy egy [erőforrás-jogkivonatot](secure-access-to-data.md) a részletes hozzáférés-vezérléshez olyan erőforrásokhoz, mint a tárolók, a partíciós kulcsok vagy az elemek. Az alábbi táblázat a Cosmos DB engedélyezési jogkivonatának korlátait sorolja fel.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Fő jogkivonat lejárati idejének maximális ideje | 15 perc  |
| Erőforrás-jogkivonat minimális lejárati ideje | 10 perc  |
| Erőforrás-jogkivonat maximális lejárati ideje | Alapértelmezés szerint 24 óra. Megnövelheti [egy Azure-támogatási jegy bejelentésével](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)|
| Token-engedélyezés maximális órajele| 15 perc |

Cosmos DB támogatja az eseményindítók végrehajtását az írás során. A szolgáltatás legfeljebb egy trigger előtti és egy trigger utáni műveletet támogat. 

## <a name="sql-query-limits"></a>SQL-lekérdezés korlátai

A Cosmos DB az [SQL](how-to-sql-query.md)használatával támogatja az elemek lekérdezését. Az alábbi táblázat a lekérdezési utasítások korlátozásait mutatja be, például a záradékok vagy a lekérdezés hosszának a számát.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| SQL-lekérdezés maximális hossza| 256 KB <sup>*</sup>|
| Illesztések maximális száma lekérdezés szerint| 5 <sup>*</sup>|
| And maximális száma| 2000 <sup>*</sup>|
| Lekérdezés maximális száma| 2000 <sup>*</sup>|
| UDF maximális száma| 10 <sup>*</sup>|
| Argumentumok maximális száma a kifejezésben| 6000 <sup>*</sup>|
| Maximális pont/sokszög| 4096 <sup>*</sup>|

<sup>*</sup> Az SQL-lekérdezési korlátok bármelyikének növeléséhez vegye fel a kapcsolatot az Azure ügyfélszolgálatával.

## <a name="mongodb-api-specific-limits"></a>MongoDB API-specifikus korlátok

A Cosmos DB a MongoDB-on írt alkalmazások esetében támogatja a MongoDB átviteli protokollt. A támogatott parancsokat és protokoll-verziókat a [támogatott MongoDB-funkciók és-szintaxisban](mongodb-feature-support.md)találja.

A következő táblázat felsorolja a MongoDB funkcióinak támogatására vonatkozó korlátozásokat. Az SQL (Core) API számára említett egyéb szolgáltatási korlátok a MongoDB API-ra is érvényesek.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Maximális MongoDB-lekérdezési memória mérete | 40 MB |
| MongoDB műveletek maximális végrehajtási ideje| 30-as |

## <a name="try-cosmos-db-free-limits"></a>Próbálja ki Cosmos DB szabad korlátokat

A következő táblázat felsorolja az ingyenes próbaverzióra vonatkozó [kipróbálási Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/) korlátozásait.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| A próbaverzió időtartama | 30 nap (tetszőleges számú alkalommal megújítható) |
| Tárolók maximális száma/előfizetés (SQL, Gremlin, Table API) | 1 |
| Tárolók maximális száma (MongoDB API) | 3 |
| Maximális átviteli sebesség/tároló | 5000 |
| Maximális átviteli sebesség egy megosztott átviteli sebességű adatbázisban | 20000 |
| Összes tárterület maximális száma | 10 GB |

Próbálja ki, Cosmos DB támogatja a globális terjesztést csak az USA középső, Észak-és Délkelet-ázsiai régiójában. Nem hozhatók létre Azure-támogatási jegyek az Azure Cosmos DB-fiókok kipróbálásához. Azonban támogatást biztosítanak a meglévő támogatási csomagokkal rendelkező előfizetőknek.

## <a name="next-steps"></a>Következő lépések

További információ a Cosmos DB alapvető fogalmak [globális eloszlásáról](distribute-data-globally.md) , [particionálásáról](partitioning-overview.md) és kiosztott [átviteli sebességéről](request-units.md).

Az alábbi rövid útmutatókkal könnyedén elkezdheti az Azure Cosmos DB használatát:

* [Bevezetés az Azure Cosmos DB SQL API használatába](create-sql-api-dotnet.md)
* [Ismerkedés a Azure Cosmos DB API-MongoDB](create-mongodb-nodejs.md)
* [Bevezetés az Azure Cosmos DB Cassandra API használatába](create-cassandra-dotnet.md)
* [Bevezetés az Azure Cosmos DB Gremlin API használatába](create-graph-dotnet.md)
* [Bevezetés az Azure Cosmos DB Table API használatába](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Az Azure Cosmos DB ingyenes kipróbálása](https://azure.microsoft.com/try/cosmosdb/)
