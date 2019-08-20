---
title: 'Az Azure Cosmos DB: SQL Python API, SDK & erőforrások'
description: Ismerkedjen meg az SQL Python API-val és az SDK-val, beleértve a kiadási dátumokat, a nyugdíjazási dátumokat és a Azure Cosmos DB Python SDK egyes verzióiban végrehajtott módosításokat.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: sngun
ms.openlocfilehash: ed90c22fa8c5b94567a9886ca71c9b35fbb103f0
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624629"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Python SDK az SQL API-hoz: Kibocsátási megjegyzések és erőforrások
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-módosítási hírcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Aszinkron Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Tömeges végrehajtó – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**SDK letöltése**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API-dokumentáció**|[Python API-referenciák dokumentációja](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**SDK telepítési utasítások**|[A Python SDK telepítési utasításai](https://github.com/Azure/azure-cosmos-python)|
|**Hozzájárul az SDK-t**|[GitHubon](https://github.com/Azure/azure-cosmos-python)|
|**Első lépések**|[Ismerkedés a Python SDK-val](sql-api-python-application.md)|
|**Jelenleg támogatott platform**|[Python 2,7](https://www.python.org/downloads/) és [Python 3,5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="a-name302302"></a><a name="3.0.2"/>3.0.2
* Többsokszögű adattípus támogatása
* Hibajavítás a munkamenetben – olvasási újrapróbálkozási házirend
* Hiba elhárítása helytelen kitöltési problémák esetén a Base 64 sztring dekódolásakor

### <a name="a-name301301"></a><a name="3.0.1"/>3.0.1
* Hibajavítás a LocationCache
* Hibajavítási végpont újrapróbálkozási logikája
* Rögzített dokumentáció

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0
* A többrégiós írások támogatása.
* A névtér az Azure. Cosmos-re változott.
* A tárolóra és elemre átnevezett gyűjtemény-és dokumentum-fogalmakat a document_client átnevezi a cosmos_client névre. 

### <a name="a-name233233"></a><a name="2.3.3"/>2.3.3
* Proxy-támogatás hozzáadva
* A módosítási hírcsatorna olvasásának támogatása
* A gyűjteményi kvóta fejlécének támogatása
* Hibajavítás nagyméretű munkamenet-tokenek esetén – probléma
* Hibajavítás a ReadMedia API-hoz
* Hibajavítás a partíciós kulcs tartományának gyorsítótárában

### <a name="a-name232232"></a><a name="2.3.2"/>2.3.2
* Az alapértelmezett újrapróbálkozások támogatása a kapcsolódási problémák esetén.

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Az Azure DocumentDB helyett a Azure Cosmos DB referenciára szolgáló dokumentáció frissítve.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Az SDK-verziója letölthető Azure Cosmos DB Emulatort elérhető legújabb verziója szükséges https://aka.ms/cosmosdb-emulator.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Hibajavítás az összesített szótárhoz.
* Hibajavítás az erőforrás-hivatkozás vágási perjeléhez.
* További tesztek a Unicode kódoláshoz.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Egy új konzisztenciaszint támogatása az új ConsistentPrefix nevezik.


### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Összesítés lekérdezések (száma, MIN, MAX, SUM és átlagos) támogatása.
* Új lehetőség az SSL-ellenőrzés letiltásához Cosmos DB emulátoron való futtatáskor.
* A függő kérelmek moduljának letiltásával pontosan 2.10.0 kell lennie.
* Süllyesztett minimálisan 2500 RU/s 10,100 RU/s a particionált gyűjtemények átviteli sebességet.
* A parancsfájl-naplózás engedélyezésének támogatása a tárolt eljárás végrehajtása során.
* Ezzel a kiadással a REST API verziója az "2017-01-19" értékre ütközött.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Szerkesztési módosításokat készített a dokumentációs megjegyzésekben.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Python 3,5-támogatás hozzáadva.
* A kapcsolatok készletezésének támogatása a kérelmek modul használatával.
* A munkamenet konzisztenciájának támogatása hozzáadva.
* A rendszer hozzáadta a particionált gyűjtemények leggyakoribb/ORDERBY-lekérdezésének támogatását.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Ismételt újrapróbálkozási szabályzat támogatása a szabályozott kérelmekhez. (A szabályozott kérelmek túl nagy kivételt kapnak, hibakód: 429.) Azure Cosmos DB alapértelmezés szerint a 429-es hibakód miatt a rendszer az egyes kérések esetében kilenc alkalommal újrapróbálkozik, a válasz fejlécében a retryAfter idő tiszteletben tartásával. A ConnectionPolicy objektum RetryOptions tulajdonságának részeként megadható a rögzített újrapróbálkozási időköz, ha figyelmen kívül hagyja az újrapróbálkozások között a kiszolgáló által visszaadott retryAfter időt. Azure Cosmos DB most legfeljebb 30 másodpercig vár minden szabályozott kérelem esetében (az újrapróbálkozások számától függetlenül), és visszaadja a 429-es hibakódú választ. Ez az idő felülbírálható a ConnectionPolicy objektum RetryOptions tulajdonságában is.
* Cosmos DB most visszaadja az x-MS-szabályozás-újrapróbálkozások száma és az x-MS-szabályozás-újrapróbálkozás-WAIT-Time-MS értéket az összes kérelemben, hogy az újrapróbálkozások számának és a kérésnek az újrapróbálkozások között megvárt kumulatív időpontját jelöli.
* Eltávolította a RetryPolicy osztályt és a document_client osztályon elérhető megfelelő tulajdonságot (retry_policy), és ehelyett egy RetryOptions osztályt vezetett be, amely a RetryOptions osztály ConnectionPolicy tulajdonságát írja le, amely a az újrapróbálkozások alapértelmezett beállításai.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Többrégiós adatbázisfiókhoz támogatása.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Az élettartam (TTL) szolgáltatás támogatása a dokumentumok számára.

### <a name="a-name161161"></a><a name="1.6.1"/>1.6.1
* A kiszolgálóoldali particionálással kapcsolatos hibajavítások speciális karakterek a partíciós kulcs elérési útjában való engedélyezéséhez.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Megvalósított [particionált gyűjtemények](partition-data.md) és [felhasználó által definiált teljesítményszintek](performance-levels.md). 

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Tartomány & ujjlenyomat hozzáadása, amelyek segítik a horizontális skálázás alkalmazásokat, hogy több partíción feloldók particionálásához.

### <a name="a-name142142"></a><a name="1.4.2"/>1.4.2
* Upsert megvalósításához. Új UpsertXXX-metódusok lettek hozzáadva a Upsert szolgáltatás támogatásához.
* Azonosító-alapú útválasztás megvalósítása. Nincs nyilvános API-módosítás, belső összes módosítást.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* A térinformatikai index támogatása.
* Ellenőrzi az összes erőforrás id tulajdonsága. Az erőforrások azonosítóit nem tartalmazhat?, /, #, \, karaktereket vagy záró szóközzel.
* ResourceResponse ad hozzá új fejléc "index átalakítási folyamat".

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* A v2 indexelési házirendet implementálja.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Támogatja a proxy-kapcsolatokat.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Kiadási & nyugdíjazási dátumok
A Microsoft legalább **12 hónappal** korábban értesítést küld az SDK kivonásáról, hogy zökkenőmentes legyen az áttérés egy újabb/támogatott verzióra.

Az új funkciók és funkciók és optimalizálás csak a jelenlegi SDK-hoz adódik hozzá, ezért azt javasoljuk, hogy a lehető leghamarabb frissítsen a legújabb SDK-verzióra. 

A szolgáltatás elutasítja a kivont SDK-val Cosmos DBre irányuló kéréseket.

> [!WARNING]
> Az Azure SQL SDK for Python minden verziója, a **1.0.0** verzió előtt **, 2016. február 29-** én megszűnt. 
> 
> 

<br/>

| Version | Kiadás dátuma | Visszavonás dátuma |
| --- | --- | --- |
| [3.0.2](#3.0.2) |November 15., 2018 |--- |
| [3.0.1](#3.0.1) |Oct 04, 2018 |--- |
| [2.3.3](#2.3.3) |Szeptember 08., 2018 |--- |
| [2.3.2](#2.3.2) |08. május 2018 |--- |
| [2.3.1](#2.3.1) |December 21., 2017 |--- |
| [2.3.0](#2.3.0) |2017. november 10. |--- |
| [2.2.1](#2.2.1) |Szeptember 29., 2017 |--- |
| [2.2.0](#2.2.0) |2017. május 10. |--- |
| [2.1.0](#2.1.0) |Május 01., 2017 |--- |
| [2.0.1](#2.0.1) |2016. október 30. |--- |
| [2.0.0](#2.0.0) |Szeptember 29., 2016 |--- |
| [1.9.0](#1.9.0) |2016. július 7. |--- |
| [1.8.0](#1.8.0) |2016. június 14-én |--- |
| [1.7.0](#1.7.0) |Április 26., 2016 |--- |
| [1.6.1](#1.6.1) |2016. április 08. |--- |
| [1.6.0](#1.6.0) |2016. március 29-én |--- |
| [1.5.0](#1.5.0) |2016. január 03. |--- |
| [1.4.2](#1.4.2) |Október 06.2015 |--- |
| 1.4.1 |Október 06.2015 |--- |
| [1.2.0](#1.2.0) |Augusztus 06, 2015 |--- |
| [1.1.0](#1.1.0) |2015. július 09. |--- |
| [1.0.1](#1.0.1) |2015. május 25. |--- |
| [1.0.0](#1.0.0) |2015. április 07. |--- |
| 0.9.4-prelease |Január 14., 2015 |2016. február 29-én |
| 0.9.3-prelease |December 9., 2014 |2016. február 29-én |
| 0.9.2-prelease |November 25., 2014 |2016. február 29-én |
| 0.9.1-prelease |Szeptember 23., 2014 |2016. február 29-én |
| 0.9.0-prelease |2014. augusztus 21. |2016. február 29-én |

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
Cosmos DB kapcsolatos további információkért lásd: [a Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapján. 

