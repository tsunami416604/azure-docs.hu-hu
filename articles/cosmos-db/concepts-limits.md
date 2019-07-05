---
title: Az Azure Cosmos DB korlátai
description: Ez a cikk ismerteti az Azure Cosmos DB korlátok.
author: arramac
ms.author: arramac
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2019
ms.openlocfilehash: 28eb7c6a11f71fa87835bcfe78e635753965bac3
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561235"
---
# <a name="limits-in-azure-cosmos-db"></a>Az Azure Cosmos DB korlátai

Ez a cikk az Azure Cosmos DB szolgáltatásban a korlátok áttekintést nyújt.

## <a name="storage-and-throughput"></a>Tárolási és átviteli sebesség

Miután létrehozott egy Azure Cosmos-fiók az előfizetéshez tartozó, adatai is kezelhetők a fiókjában található [létrehozása az adatbázisok, tárolók és elemek](databases-containers-items.md). Is kioszthatja az átviteli sebességet egy tároló-szintű vagy egy adatbázisszintű, hogy [kérelemegységek (RU/mp vagy fenntartott)](request-units.md). Az alábbi táblázat a tárolás és a egy tároló, illetve az adatbázis átviteli vonatkozó korlátokat.

| Resource | Alapértelmezett korlát |
| --- | --- |
| Maximális RUs tárolónként ([dedikált átviteli sebesség kiosztott mód](databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 alapesetben. Megnövelheti azt [bejelentés az Azure támogatási jegy](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) , vagy vegye fel velünk a kapcsolatot [kérje meg a Cosmos DB](mailto:askcosmosdb@microsoft.com) |
| Az adatbázisonkénti maximális kérelemegység ([megosztott átviteli sebesség kiosztott mód](databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 alapesetben. Megnövelheti azt [bejelentés az Azure támogatási jegy](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) , vagy vegye fel velünk a kapcsolatot [kérje meg a Cosmos DB](mailto:askcosmosdb@microsoft.com) |
| Maximális kérelemegység (logikai) partíciókulcson | 10,000 |
| Minden elem (logikai) partíciókulcson maximális tárolás| 10 GB |
| Eltérő partíciókulcsok (logikai) maximális száma | Korlátlan |
| Maximális tárterület tároló | Korlátlan |
| Maximális tárterület adatbázisonként | Korlátlan |

> [!NOTE]
> Ajánlott eljárások, amelyekre szükség magasabb korlátok tárterület vagy átviteli sebesség partíciókulcsok rendelkező számítási feladatok kezeléséhez, lásd: [gyakori elérésű Partíciókulcsok tervezése](synthetic-partition-keys.md)
>

Cosmos-tároló (vagy megosztott átviteli adatbázis) rendelkeznie kell egy minimum átviteli sebesség 400 kérelemegységet. A tároló növekedésével a minimális támogatott átviteli is függ a következő tényezőket:

* A maximális létesített átviteli sebesség minden eddiginél a tárolón. A szolgáltatás támogatja egy tárolót a 10 %-a kiépített maximális emelő teljesítményét. Például ha az átviteli sebességet 10000 kérelemegység-ra nőtt, majd a legkisebb lehetséges kiosztott átviteli sebesség lenne 1000 kérelemegység
* Egy megosztott átviteli adatbázisban, minden eddiginél létrehozott tárolók száma tárolónként 100 kérelemegység mérni. Például ha öt tárolók egy megosztott átviteli adatbázisban létrehozott, majd az átviteli sebességet kell lennie legalább 500 kérelemegység

Az aktuális és a minimális átviteli sebességet egy tároló vagy egy adatbázist az Azure portal vagy az SDK-k lekérhetők. További információkért lásd: [tárolók és adatbázisok kiépítése átviteli sebességet](set-throughput.md). Összefoglalva az alábbiakban a minimális kiosztott RU-korlátokat. 

| Resource | Alapértelmezett korlát |
| --- | --- |
| Minimális RUs tárolónként ([dedikált átviteli sebesség kiosztott mód](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Az adatbázisonkénti minimális kérelemegység ([megosztott átviteli sebesség kiosztott mód](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Egy megosztott átviteli adatbázison belül tárolónként minimális kérelemegység | 100 |

A cosmos DB támogatja a rugalmas méretezhetőség az átviteli sebesség (RU) tároló vagy az adatbázis az SDK-k vagy a portálon keresztül. A tárolók egy méretezési csoport tartományba 10 – 100-szor, minimális és maximális értékek közötti szinkron módon történik, és azonnal méretezhető. Ha a kért átviteli sebesség értéke kívül esik a tartományon, aszinkron módon történik méretezés. Aszinkron skálázás órát is igénybe perc függően a kért átviteli sebesség és a tárolóban lévő adatok tárolási mérete.  

## <a name="control-plane-operations"></a>Vezérlési síkjával végzett műveletek

Is [kialakításához és kezeléséhez az Azure Cosmos-fiók](how-to-manage-database-account.md) az Azure Portalon, az Azure PowerShell, az Azure parancssori felület és Azure Resource Manager-sablonok használatával. Az alábbi táblázat felsorolja az előfizetés, a fiók és a műveletek száma vonatkozó korlátok.

| Resource | Alapértelmezett korlát |
| --- | --- |
| Előfizetésenként legfeljebb adatbázisfiókhoz | Alapértelmezés szerint 50. Megnövelheti azt [bejelentés az Azure támogatási jegy](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) , vagy vegye fel velünk a kapcsolatot [kérje meg a Cosmos DB](mailto:askcosmosdb@microsoft.com)|
| Régiónkénti feladatátvétel maximális száma | Alapértelmezés szerint 1/óra. Megnövelheti azt [bejelentés az Azure támogatási jegy](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) , vagy vegye fel velünk a kapcsolatot [kérje meg a Cosmos DB](mailto:askcosmosdb@microsoft.com)|

> [!NOTE]
> Régiónkénti feladatátvétel csak írási egyrégiós vonatkoznak. Többrégiós írási fiókok nem igényelnek és nem rendelkezik az írási régió módosítása korlátozott.

A cosmos DB az adatok biztonsági másolatait a rendszeres időközönként automatikusan vesz igénybe. További információ a biztonsági másolatok megőrzési időszakok és a windows rendszeren: [Online biztonsági mentés és igény szerinti adatok visszaállítása az Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-container-limits"></a>Tároló korlátok

Attól függően, melyik API-t használ az Azure Cosmos-tárolókat képviselő gyűjteményt, tábla, vagy graph. Tárolók támogatják a konfigurációk [egyedi kulcsra vonatkozó megkötések](unique-keys.md), [tárolt eljárások, eseményindítók és felhasználói függvények](stored-procedures-triggers-udfs.md), és [indexelési szabályzat](how-to-manage-indexing-policy.md). A következő táblázat felsorolja az adott tárolóban lévő konfigurációkra korlátok. 

| Resource | Alapértelmezett korlát |
| --- | --- |
| Adatbázis vagy a tároló neve | 255 |
| Maximális tárolt eljárások tárolónként | 100 <sup>*</sup>|
| Maximális UDF-EK tárolónként | 25 <sup>*</sup>|
| Indexelési szabályzat útvonalak maximális száma| 100 <sup>*</sup>|
| Maximális száma tárolónként egyedi kulcsok|10 <sup>*</sup>|
| Egyedi kulcsmegkötés / elérési út maximális száma|16 <sup>*</sup>|

<sup>*</sup> Azure-támogatás igénybevételénél, vagy vegye fel velünk a kapcsolatot növelheti tároló korlátok bármelyikét [kérje meg a Cosmos DB](mailto:askcosmosdb@microsoft.com).

## <a name="per-item-limits"></a>Konfigurációelem korlátok

Attól függően, melyik API-t használ egy Azure Cosmos-elemet egy gyűjtemény, tábla, vagy egy csomópontot egy sort egy dokumentumot vagy edge grafikon hozhat létre. Az alábbi táblázat elemenként korlátait Cosmos DB-ben. 

| Resource | Alapértelmezett korlát |
| --- | --- |
| Egy elem maximális mérete | 2 MB (JSON-reprezentációval hossza UTF-8) |
| Partíciókulcs-értékkel maximális hossza | 2048 bájt |
| Azonosító érték maximális hossza | 1024 bájt |
| Elemenként tulajdonságok maximális száma | Gyakorlati korlátlan |
| Maximális beágyazás mélységére | Gyakorlati korlátlan |
| Tulajdonság neve | Gyakorlati korlátlan |
| Tulajdonság értékének maximális hossza | Gyakorlati korlátlan |
| Tulajdonság értéke karakterlánc maximális hossza | Gyakorlati korlátlan |
| Numerikus tulajdonság értékének maximális hossza | IEEE754 kétszeres pontosságú 64 bites |

Nem vonatkoznak korlátozások a cikk is észleltünk adattartalmakat. például tulajdonságait és a beágyazási róluk, kivéve a mezőhossz-korlátozásai a partíciókulcs és azonosító értéket, és a teljes számát a korlátozása, 2 MB méretű. Előfordulhat, hogy nagy vagy összetett elem struktúrák fogyasztott csökkentése érdekében a tárolók indexelési szabályzat konfigurálása. Lásd: [elemek Cosmos DB-ben a modellezési](how-to-model-partition-example.md) egy való életből vett például, és minták nagy elemek kezelésére.

## <a name="per-request-limits"></a>Kérelem korlátok

A cosmos DB támogatja a [CRUD-MŰVELETEKKEL és lekérdezési műveleteket](https://docs.microsoft.com/rest/api/cosmos-db/) -erőforrásokon, például adatbázisok, tárolók és elemek.  

| Resource | Alapértelmezett korlát |
| --- | --- |
| Maximális végrehajtási ideje az egyetlen művelettel (például egy tárolt eljárás végrehajtása vagy egy egyetlen lekérdezési lap)| 5 mp |
| Kérelem maximális mérete (CRUD-MŰVELETEKKEL, a tárolt eljárás)| 2 MB |
| A válasz maximális mérete (például többoldalas lekérdezések) | 4 MB |

Egyszer egy művelet, például a végrehajtás időtúllépés vagy a válasz maximális mérete eléri a lekérdezés, akkor adja vissza egy oldal eredmények és a egy folytatási tokent, az ügyfél folytathatja a végrehajtási. Nincs egyetlen lekérdezést is futtathatja lapok/continuations időtartamát gyakorlati korlátozva.

A cosmos DB HMAC-val használ a hitelesítéshez használ. Is használhatja, vagy egy főkulcsot, vagy egy [erőforrás-jogkivonatokat](secure-access-to-data.md) olyan erőforrásokhoz, mint a tárolók részletes hozzáférés-vezérlést, particionálja a kulcsok vagy elemek. Az alábbi táblázat a Cosmos DB-ben engedélyezési jogkivonatok vonatkozó korlátok.

| Resource | Alapértelmezett korlát |
| --- | --- |
| Maximális fő jogkivonat lejárati ideje | 15 perc  |
| Minimális erőforrás-jogkivonat lejárati ideje | 10 perc  |
| Maximális erőforrás-jogkivonat lejárati ideje | Alapértelmezés szerint 24 h. Megnövelheti azt [bejelentés az Azure támogatási jegy](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) , vagy vegye fel velünk a kapcsolatot [kérje meg a Cosmos DB](mailto:askcosmosdb@microsoft.com)|
| Engedélyezési jogkivonat torzulása maximális órája| 15 perc |

A cosmos DB írási műveletek során eseményindító végrehajtása támogatja. A szolgáltatás támogatja a legfeljebb egy üzem előtti eseményindítót és a egy utáni trigger írási művelet. 

## <a name="sql-query-limits"></a>SQL-lekérdezési korlátok

A cosmos DB támogatja az elemekről lekérdezése [SQL](how-to-sql-query.md). A következő táblázat ismerteti a korlátozások a lekérdezés utasítások, például szolgáló szerződéses klauzulák, vagy a lekérdezési hossza száma tekintetében.

| Resource | Alapértelmezett korlát |
| --- | --- |
| SQL-lekérdezés maximális hossza| 256 KB <sup>*</sup>|
| Lekérdezésenként legfeljebb illesztések| 5 <sup>*</sup>|
| Lekérdezésenként legfeljebb equal| 2000 <sup>*</sup>|
| Lekérdezésenként legfeljebb ORs| 2000 <sup>*</sup>|
| Lekérdezésenként legfeljebb UDF-EK| 10 <sup>*</sup>|
| Maximális száma a függvényhívásokban szereplő argumentumokra kifejezés| 6000 <sup>*</sup>|
| Sokszög maximális pontok| 4096 <sup>*</sup>|

<sup>*</sup> Azure-támogatás igénybevételénél, vagy vegye fel velünk a kapcsolatot növelheti az SQL-lekérdezési korlátok bármelyikét [kérje meg a Cosmos DB](mailto:askcosmosdb@microsoft.com).

## <a name="mongodb-api-specific-limits"></a>MongoDB API-specifikus korlátok

A cosmos DB támogatja a MongoDB átviteli protokoll ellen a mongodb-hez írt alkalmazások esetén. Keresse meg a támogatott parancsok és a protokoll-verziók [támogatott MongoDB funkcióihoz és szintaxisához](mongodb-feature-support.md).

Az alábbi táblázat a MongoDB-funkciók támogatása jellemző korlátokat. Egyéb szolgáltatási korlátai már említettük, az SQL-(mag) API a MongoDB API-t is vonatkozik.

| Resource | Alapértelmezett korlát |
| --- | --- |
| Maximális MongoDB lekérdezés memória mérete | 40 MB |
| MongoDB-műveletek maximális végrehajtási ideje| 30S |

## <a name="try-cosmos-db-free-limits"></a>Korlátok Cosmos DB ingyenes kipróbálása

A következő táblázat felsorolja a vonatkozó korlátozásokat az [próbálja ki ingyenesen az Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) próbaverziós.

| Resource | Alapértelmezett korlát |
| --- | --- |
| A próbaidőszak alatt | 30 nap (lehet újítani tetszőleges számú alkalommal) |
| A tárolók száma előfizetésenként (SQL, a Gremlin Table API) maximális száma | 1 |
| Tárolók maximális száma előfizetésenként (MongoDB API-val) | 3 |
| Tároló egységenkénti maximális adatátviteli sebesség | 5000 |
| Megosztott átviteli adatbázisonkénti maximális átviteli sebesség | 20000 |
| Fiókonkénti maximális összes tárhely | 10 GB |

Próbálja ki a Cosmos DB támogatja a globális terjesztés csak az USA középső RÉGIÓJA, Észak-Európa és Délkelet-ázsiai régióban. Próbálja ki az Azure Cosmos DB-fiókok nem hozható létre az Azure támogatási jegyeket. Azonban támogatást is magukban a meglévő támogatási csomagokról-előfizetőknek.

## <a name="next-steps"></a>További lépések

Tudjon meg többet a Cosmos DB alapvető fogalmak [globális terjesztés](distribute-data-globally.md) és [particionálás](partitioning-overview.md) és [kiosztott átviteli sebesség](request-units.md).

Az alábbi rövid útmutatókkal könnyedén elkezdheti az Azure Cosmos DB használatát:

* [Bevezetés az Azure Cosmos DB SQL API használatába](create-sql-api-dotnet.md)
* [Ismerkedés az Azure Cosmos DB API a mongodb-hez](create-mongodb-nodejs.md)
* [Bevezetés az Azure Cosmos DB Cassandra API használatába](create-cassandra-dotnet.md)
* [Bevezetés az Azure Cosmos DB Gremlin API használatába](create-graph-dotnet.md)
* [Bevezetés az Azure Cosmos DB Table API használatába](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Az Azure Cosmos DB ingyenes kipróbálása](https://azure.microsoft.com/try/cosmosdb/)
