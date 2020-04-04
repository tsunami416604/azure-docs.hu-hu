---
title: Az Azure Cosmos DB szolgáltatáskvótái
description: Az Azure Cosmos DB szolgáltatáskvóták és a különböző erőforrástípusok alapértelmezett korlátozásai.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 343e94fe681d398b695e0e24c22544bca470d75a
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652185"
---
# <a name="azure-cosmos-db-service-quotas"></a>Az Azure Cosmos DB szolgáltatáskvótái

Ez a cikk áttekintést nyújt az Azure Cosmos DB különböző erőforrásaihoz ajánlott alapértelmezett kvótákról.

## <a name="storage-and-throughput"></a>Tárolás és átmenő

Miután létrehozott egy Azure Cosmos-fiókot az előfizetése alatt, [adatbázisok, tárolók és elemek létrehozásával](databases-containers-items.md)kezelheti a fiókjában lévő adatokat. Az átviteli értéket tárolószintű vagy adatbázisszintű igényegységek [(RU/s vagy VT)](request-units.md)tekintetében is kiépítheti. Az alábbi táblázat a tároló/adatbázis tárolási és átviteli kapacitáskorlátait sorolja fel.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Maximális felhasználói példány tárolónként ([dedikált átviteli kiépített mód)](databases-containers-items.md#azure-cosmos-containers) | 1 000 000 alapértelmezés szerint. Növelheti az [Azure támogatási jegyének benyújtásával](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) |
| Maximális felhasználói értékek adatbázisonként ([megosztott átviteli kiépített mód)](databases-containers-items.md#azure-cosmos-containers) | 1 000 000 alapértelmezés szerint. Növelheti az [Azure támogatási jegyének benyújtásával](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) |
| Maximális felhasználói csoport (logikai) partíciókulcsonként | 10,000 |
| Maximális tárolás az összes elem között (logikai) partíciókulcsonként| 20 GB |
| Az eltérő (logikai) partíciókulcsok maximális száma | Korlátlan |
| Maximális tárhely tárolónként | Korlátlan |
| Maximális tárhely adatbázisonként | Korlátlan |
| A melléklet maximális mérete fiókonként (a mellékletfunkció értékcsökkenése folyamatban van) | 2 GB |
| 1 GB-onként minimális an-vissza szükséges | 10 RU/s |

> [!NOTE]
> A tároló- vagy átviteli kapacitásmagasabb korlátot igénylő partíciókulcsokat igénylő számítási feladatok kezelésével kapcsolatos gyakorlati tanácsokról a [Szintetikus partíciókulcs létrehozása című](synthetic-partition-keys.md)témakörben olvashat.
>

A Cosmos-tárolónak (vagy megosztott átviteli adatbázisnak) legalább 400 több mint példányszámú átviteli értékével kell rendelkeznie. A tároló növekedésével a minimálistámogatott átviteli szint a következő tényezőktől is függ:

* A tárolóra beállított minimális átviteli teljesítmény a tárolón valaha kiosztott maximális átviteli teljesítménytől függ. Ha például az átviteli átbocsátás 10000 Több rektusra lett növelve, akkor a lehető legalacsonyabb kiosztott átviteli érték 1000 Több mint 1000
* A megosztott átviteli adatbázis minimális átviteli hatása a megosztott átviteli adatbázisban valaha létrehozott tárolók teljes számától is függ, tárolónként 100 több erőforrással mérve. Ha például öt tárolót hozott létre egy megosztott átviteli-adatbázisban, akkor az átviteli sebességgel legalább 500 Több mint erőforrásnak kell lennie.

A tároló vagy adatbázis aktuális és minimális átviteli állása lehívható az Azure Portalról vagy az SDK-kból. További információ: [Átviteli átbocsátás a tárolókon és adatbázisokon.](set-throughput.md) 

> [!NOTE]
> Bizonyos esetekben előfordulhat, hogy csökkentheti az átviteli 10%. Az API segítségével a tárolónkénti pontos minimális rus-ok lehívása.
>

Összefoglalva, itt vannak a minimálisan kiépített VT korlátok. 

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Minimális rus-ok tárolónként[(dedikált átviteli kiépített mód)](databases-containers-items.md#azure-cosmos-containers) | 400 |
| Adatbázisonkénti minimális alternatív/szim-ok ([megosztott átviteli kiépített mód)](databases-containers-items.md#azure-cosmos-containers) | 400 |
| Minimális erőforrás-objektumok tárolónként egy megosztott átviteli adatátus-adatbázison belül | 100 |

Cosmos DB támogatja az átviteli por (RUs) rugalmas méretezése tárolónként vagy adatbázisonként az SDK-kon vagy a portálon keresztül. Minden tároló skálázható szinkron és közvetlenül egy skálán tartományban 10-100-szor, a minimális és maximális értékek között. Ha a kért átviteli érték kívül esik a tartományon, a skálázás aszinkron módon történik. Aszinkron skálázás eltarthat perctől órákig a kért átviteli és adattárolási mérete a tárolóban.  

## <a name="control-plane-operations"></a>Vezérlősík-műveletek

Az [Azure Cosmos-fiókot](how-to-manage-database-account.md) az Azure Portalon, az Azure PowerShellen, az Azure CLI-n és az Azure Resource Manager-sablonokon keresztül építheti ki és kezelheti. Az alábbi táblázat az előfizetésenkénti korlátokat, a fiókot és a műveletek számát sorolja fel.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Adatbázis-fiókok maximális előfizetésenként | alapértelmezés szerint 50. Növelheti az [Azure támogatási jegyének benyújtásával](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|
| A regionális feladatátvételek maximális száma | alapértelmezés szerint 1/óra. Növelheti az [Azure támogatási jegyének benyújtásával](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|

> [!NOTE]
> A regionális feladatátvételek csak egy régiós írási fiókokra vonatkoznak. Többrégiós írási fiókok nem igényelnek, vagy bármilyen korlátozást az írási régió módosítása.

Cosmos DB automatikusan rendszeres időközönként biztonsági mentést készít az adatokról. A biztonsági mentési adatmegőrzési időközökről és az ablakokról az [Online biztonsági mentés és az igény szerinti adat-visszaállítás az Azure Cosmos DB-ben.](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)

## <a name="per-account-limits"></a>Számlánkénti korlátok

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Adatbázisok maximális száma | Korlátlan |
| Megosztott átviteli eresztőhet tartalmazó adatbázisonkénti tárolók maximális száma |25 |
| A tárolók maximális száma adatbázisonként vagy fiókonként dedikált átviteli értékkel  |korlátlan |
| Régiók maximális száma | Nincs korlátozás (minden Azure-régió) |

## <a name="per-container-limits"></a>Konténerenkénti határértékek

Attól függően, hogy melyik API-t használja, az Azure Cosmos-tároló egy gyűjteményt, egy táblát vagy grafikont képviselhet. A tárolók támogatják az [egyedi kulcskorlátozások,](unique-keys.md) [a tárolt eljárások, az eseményindítók és az UDF-ek](stored-procedures-triggers-udfs.md)és az [indexelési házirend](how-to-manage-indexing-policy.md)konfigurációit. Az alábbi táblázat a tárolón belüli konfigurációkra vonatkozó korlátozásokat sorolja fel. 

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Az adatbázis vagy tároló nevének maximális hossza | 255 |
| Maximális tárolt eljárások tárolónként | 100<sup>*</sup>|
| Maximális UDF-ek tárolónként | 25<sup>*</sup>|
| Az indexelési házirendben lévő elérési utak maximális száma| 100<sup>*</sup>|
| Egyedi kulcsok maximális száma tárolónként|10<sup>*</sup>|
| Görbék maximális száma egyedi kulcsmegkötésenként|16<sup>*</sup>|

<sup>*</sup>A tárolónkénti korlátok bármelyikét növelheti az Azure-támogatással való kapcsolatfelvételsel.

## <a name="per-item-limits"></a>Cikkenkénti korlátok

Attól függően, hogy melyik API-t használja, az Azure Cosmos-elem képviselheti egy gyűjteményben lévő dokumentumot, egy tábla sorát vagy egy csomópontot vagy egy grafikon szélét. Az alábbi táblázat a Cosmos DB elemenkénti korlátait mutatja be. 

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Elem maximális mérete | 2 MB (UTF-8 hosszú JSON-ábrázolás) |
| A partíciókulcs értékének maximális hossza | 2048 bájt |
| Az azonosító érték maximális hossza | 1023 bájt |
| Tulajdonságok maximális száma cikkenként | Nincs gyakorlati korlát |
| Maximális beágyazási mélység | Nincs gyakorlati korlát |
| A tulajdonságnév maximális hossza | Nincs gyakorlati korlát |
| A tulajdonság értékének maximális hossza | Nincs gyakorlati korlát |
| A karakterlánc tulajdonságértékének maximális hossza | Nincs gyakorlati korlát |
| A numerikus tulajdonság értékének maximális hossza | IEEE754 dupla pontosságú 64 bites |

Nincsenek korlátozások a cikk hasznos teher, mint a tulajdonságok száma és a beágyazási mélység, kivéve a hossz korlátozások partíciókulcs és id értékek, és a teljes méret korlátozása 2 MB. Előfordulhat, hogy nagy vagy összetett cikkstruktúrákkal rendelkező tárolók indexelési házirendet kell konfigurálnia a RU-felhasználás csökkentése érdekében. Tekintse meg [az elemek modellezése a Cosmos DB](how-to-model-partition-example.md) egy valós példa, és minták nagy elemek kezelésére.

## <a name="per-request-limits"></a>Kérésenkénti korlátok

Az Azure Cosmos DB támogatja a [CRUD és a lekérdezési műveletek](https://docs.microsoft.com/rest/api/cosmos-db/) erőforrások, például tárolók, elemek és adatbázisok ellen. Emellett támogatja a [tranzakciós kötegkérelmeket](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.transactionalbatch) több, azonos partíciókas kulccsal rendelkező elem ellen egy tárolóban.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Egyetlen művelet maximális végrehajtási ideje (például egy tárolt eljárás végrehajtása vagy egyetlen lekérdezési lap lekérése)| 5 másodperc |
| A kérelem maximális mérete (például tárolt eljárás, CRUD)| 2 MB |
| Maximális válaszméret (például oldalszámozott lekérdezés) | 4 MB |
| Tranzakciós kötegben végzett műveletek maximális száma | 100 |

Ha egy művelet, például a lekérdezés eléri a végrehajtási időkorlátot vagy a válasz méretkorlátját, egy eredményoldalt és egy folytatási jogkivonatot ad vissza az ügyfélnek a végrehajtás folytatásához. Nincs gyakorlati korlát az időtartam egy lekérdezés futtatható oldalakon/ folytatások.

A Cosmos DB a HMAC-et használja az engedélyezéshez. Használhatja a főkulcs, vagy egy [erőforrás-jogkivonatok](secure-access-to-data.md) részletes hozzáférés-vezérlés erőforrások, például tárolók, partíciókulcsok vagy elemek részletesen. Az alábbi táblázat a Cosmos DB engedélyezési jogkivonataira vonatkozó korlátokat sorolja fel.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| A fő jogkivonat maximális lejárati ideje | 15 min  |
| Minimális erőforrásjogkivonat lejárati ideje | 10 min  |
| Az erőforrás-jogkivonat lejárati idejének maximális ideje | alapértelmezés szerint 24 óra. Növelheti az [Azure támogatási jegyének benyújtásával](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|
| Maximális óradöntés a tokenhitelesítéshez| 15 min |

Cosmos DB támogatja az írási műveletek végrehajtása során. A szolgáltatás írási műveletenként legfeljebb egy elő- és egy eseményindítót támogat. 

## <a name="autopilot-mode-limits"></a>Robotpilóta mód korlátai

Tekintse meg az [Autopilot](provision-throughput-autopilot.md#autopilot-limits) cikket az átviteli és tárolási korlátok robotpilóta módban.

## <a name="sql-query-limits"></a>SQL-lekérdezési korlátok

A Cosmos DB támogatja az elemek [SQL](how-to-sql-query.md)használatával történő lekérdezését. Az alábbi táblázat a lekérdezési utasítások korlátozásait ismerteti, például a záradékok száma vagy a lekérdezés hossza tekintetében.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Az SQL-lekérdezés maximális hossza| 256 KB |
| Lekérdezésenkénti maximális join-ok| 5<sup>*</sup>|
| Maximális UDF-ek lekérdezésenként| 10<sup>*</sup>|
| Maximális pontszám sokszögenként| 4096 |
| A belefoglalt útvonalak maximális része tárolónként| 500 |
| Konténerenkénti maximális kizárt elérési utak| 500 |
| Összetett index maximális tulajdonságai| 8 |

<sup>*</sup>Ezeket az SQL-lekérdezési korlátokat az Azure-támogatással való kapcsolatfelvételsel növelheti.

## <a name="mongodb-api-specific-limits"></a>MongoDB API-specifikus korlátok

A Cosmos DB támogatja a MongoDB wire protokollt a MongoDB ellen írt alkalmazásokhoz. A támogatott parancsokat és protokollverziókat a [Támogatott MongoDB szolgáltatások és a szintaxis](mongodb-feature-support.md)tartalmazza.

Az alábbi táblázat a MongoDB szolgáltatástámogatására vonatkozó korlátozásokat sorolja fel. Az SQL (core) API-hoz említett egyéb szolgáltatáskorlátok a MongoDB API-ra is vonatkoznak.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Maximális MongoDB lekérdezési memória mérete (Ez a korlátozás csak a 3.2 szerver verzió) | 40 MB |
| A MongoDB-műveletek maximális végrehajtási ideje| 30-as évek |
| A kiszolgálóoldali kapcsolat lezárásának tétlen kapcsolati időtúlidőpontja* | 30 perc |

\*Azt javasoljuk, hogy az ügyfélalkalmazások állítsa az alapjárati kapcsolat időtúltöltését az illesztőprogram-beállításokban 2-3 percre, mert az [Azure LoadBalancer alapértelmezett időtúlszáma 4 perc.](../load-balancer/load-balancer-tcp-idle-timeout.md#tcp-idle-timeout)  Ez az időtúllépésre biztosítja, hogy az automatikus kapcsolatokat ne zárja le az ügyfélgép és az Azure Cosmos DB közötti köztes terheléselosztó.

## <a name="try-cosmos-db-free-limits"></a>Próbálja ki a Cosmos DB Ingyenes korlátait

Az alábbi táblázat a [Try Azure Cosmos DB ingyenes próbaverzióra](https://azure.microsoft.com/try/cosmosdb/) vonatkozó korlátait sorolja fel.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| A vizsgálat időtartama | 30 nap (tetszőleges számú alkalommal megújítható) |
| Maximális tárolók előfizetésenként (SQL, Gremlin, Table API) | 1 |
| Maximális tárolók előfizetésenként (MongoDB API) | 3 |
| Maximális átviteli teljesítmény tárolónként | 5000 |
| Maximális átviteli teljesítmény megosztott átviteli sebességű adatbázisonként | 20000 |
| Maximális teljes tárterület fiókonként | 10 GB |

Próbálja ki a Cosmos DB-t, amely csak az USA középső, észak-európai és délkelet-ázsiai régióiban támogatja a globális elosztást. Az Azure-támogatási jegyek nem hozhatók létre az Azure Cosmos DB-fiókok kipróbálásához. A meglévő támogatási csomagokkal rendelkező előfizetők számára azonban támogatást nyújtanak.

## <a name="free-tier-account-limits"></a>Ingyenes szintfiók-korlátok
Az alábbi táblázat az [Azure Cosmos DB ingyenes rétegfiókok korlátait](optimize-dev-test.md#azure-cosmos-db-free-tier) sorolja fel.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Ingyenes rétegfiókok száma Azure-előfizetésenként | 1 |
| Az ingyenes szintű kedvezmény időtartama | A fiók élettartama. A fiók létrehozása során engedélyeznie kell a fiókot. |
| Maximális RU/s ingyen | 400 RU/s |
| Maximális tárhely ingyen | 5 GB |
| A megosztott átviteli adatátarány-adatbázisok maximális száma | 5 |
| A megosztott átviteli-adatbázisban lévő tárolók maximális száma | 25 <br>Az ingyenes rétegű fiókokban a legfeljebb 25 tárolóval rendelkező megosztott átviteli-adatbázis minimális RU/s-a 400 RU/s. |

  A fentieken kívül a [számlánkénti korlátok](#per-account-limits) az ingyenes rétegszámlákra is vonatkoznak.

## <a name="next-steps"></a>További lépések

Tudjon meg többet a Cosmos DB alapvető fogalmairól [a globális elosztásról](distribute-data-globally.md) és [particionálásról](partitioning-overview.md) és [a kiosztott átviteli ról.](request-units.md)

Az alábbi rövid útmutatókkal könnyedén elkezdheti az Azure Cosmos DB használatát:

* [Bevezetés az Azure Cosmos DB SQL API használatába](create-sql-api-dotnet.md)
* [Ismerkedés az Azure Cosmos DB MongoDB-hoz való API-jával](create-mongodb-nodejs.md)
* [Bevezetés az Azure Cosmos DB Cassandra API használatába](create-cassandra-dotnet.md)
* [Bevezetés az Azure Cosmos DB Gremlin API használatába](create-graph-dotnet.md)
* [Bevezetés az Azure Cosmos DB Table API használatába](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Az Azure Cosmos DB ingyenes kipróbálása](https://azure.microsoft.com/try/cosmosdb/)
