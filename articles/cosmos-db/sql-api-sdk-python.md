---
title: 'Azure Cosmos DB: SQL Python API, SDK &-erőforrások'
description: Ismerkedjen meg az SQL Python API-val és az SDK-val, beleértve a kiadási dátumokat, a nyugdíjazási dátumokat és a Azure Cosmos DB Python SDK egyes verzióiban végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 05/11/2020
ms.author: anfeldma
ms.openlocfilehash: ce74a7b02b9c450e07402d96a1aaba2a4b704788
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650714"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Python SDK for SQL API: kibocsátási megjegyzések és erőforrások
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-módosítási hírcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
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
|**Közreműködés az SDK-val**|[GitHub](https://github.com/Azure/azure-cosmos-python)|
|**Bevezetés**|[Ismerkedés a Python SDK-val](sql-api-python-application.md)|
|**Jelenleg támogatott platform**|[Python 2,7](https://www.python.org/downloads/) és [Python 3,5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="302"></a><a name="3.0.2"/>3.0.2
* Többsokszögű adattípus támogatása
* Hibajavítás a munkamenetben – olvasási újrapróbálkozási házirend
* Hiba elhárítása helytelen kitöltési problémák esetén a Base 64 sztring dekódolásakor

### <a name="301"></a><a name="3.0.1"/>3.0.1
* Hibajavítás a LocationCache
* Hibajavítási végpont újrapróbálkozási logikája
* Rögzített dokumentáció

### <a name="300"></a><a name="3.0.0"/>3.0.0
* A többrégiós írások támogatása.
* A névtér az Azure. Cosmos-re változott.
* A tárolóra és elemre átnevezett gyűjtemény-és dokumentum-fogalmak document_client átnevezve cosmos_clientre. 

### <a name="233"></a><a name="2.3.3"/>2.3.3
* Proxy-támogatás hozzáadva
* A módosítási hírcsatorna olvasásának támogatása
* A gyűjteményi kvóta fejlécének támogatása
* Hibajavítás nagyméretű munkamenet-tokenek esetén – probléma
* Hibajavítás a ReadMedia API-hoz
* Hibajavítás a partíciós kulcs tartományának gyorsítótárában

### <a name="232"></a><a name="2.3.2"/>2.3.2
* Az alapértelmezett újrapróbálkozások támogatása a kapcsolódási problémák esetén.

### <a name="231"></a><a name="2.3.1"/>2.3.1
* Az Azure DocumentDB helyett a Azure Cosmos DB referenciára szolgáló dokumentáció frissítve.

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Az SDK-verzióhoz a [Azure Cosmos db Emulator](https://aka.ms/cosmosdb-emulator)legújabb verziójára van szükség.

### <a name="221"></a><a name="2.2.1"/>2.2.1
* Hibajavítás az összesített szótárhoz.
* Hibajavítás az erőforrás-hivatkozás vágási perjeléhez.
* További tesztek a Unicode kódoláshoz.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Egy új, ConsistentPrefix nevű konzisztencia-szint támogatása.


### <a name="210"></a><a name="2.1.0"/>2.1.0
* Összesítő lekérdezések támogatása (DARABSZÁM, MIN., MAX., SUM és AVG).
* Új lehetőség a TLS-ellenőrzés letiltásához a Cosmos DB emulátoron való futtatáskor.
* A függő kérelmek moduljának letiltásával pontosan 2.10.0 kell lennie.
* A 10 100 RU/s és 2500 RU/s közötti particionált gyűjtemények minimális átviteli sebességének csökkentése.
* A parancsfájl-naplózás engedélyezésének támogatása a tárolt eljárás végrehajtása során.
* Ezzel a kiadással a REST API verziója az "2017-01-19" értékre ütközött.

### <a name="201"></a><a name="2.0.1"/>2.0.1
* Szerkesztési módosításokat készített a dokumentációs megjegyzésekben.

### <a name="200"></a><a name="2.0.0"/>2.0.0
* Python 3,5-támogatás hozzáadva.
* A kapcsolatok készletezésének támogatása a kérelmek modul használatával.
* A munkamenet konzisztenciájának támogatása hozzáadva.
* A rendszer hozzáadta a particionált gyűjtemények leggyakoribb/ORDERBY-lekérdezésének támogatását.

### <a name="190"></a><a name="1.9.0"/>1.9.0
* Ismételt újrapróbálkozási szabályzat támogatása a szabályozott kérelmekhez. (A szabályozott kérelmek túl nagy kivételt kapnak, hibakód: 429.) Azure Cosmos DB alapértelmezés szerint a 429-es hibakód miatt a rendszer az egyes kérések esetében kilenc alkalommal újrapróbálkozik, a válasz fejlécében a retryAfter idő tiszteletben tartásával. A ConnectionPolicy objektum RetryOptions tulajdonságának részeként megadható a rögzített újrapróbálkozási időköz, ha figyelmen kívül hagyja az újrapróbálkozások között a kiszolgáló által visszaadott retryAfter időt. Azure Cosmos DB most legfeljebb 30 másodpercig vár minden szabályozott kérelem esetében (az újrapróbálkozások számától függetlenül), és visszaadja a 429-es hibakódú választ. Ez az idő felülbírálható a ConnectionPolicy objektum RetryOptions tulajdonságában is.
* Cosmos DB most visszaadja az x-MS-szabályozás-újrapróbálkozások száma és az x-MS-szabályozás-újrapróbálkozás-WAIT-Time-MS értéket az összes kérelemben, hogy az újrapróbálkozások számának és a kérésnek az újrapróbálkozások között megvárt kumulatív időpontját jelöli.
* Eltávolította a RetryPolicy osztályt és a document_client osztályban elérhető megfelelő tulajdonságot (retry_policy), és ehelyett egy RetryOptions osztályt vezetett be, amely a ConnectionPolicy osztály RetryOptions tulajdonságát használja, amely az alapértelmezett újrapróbálkozások egyes beállításainak felülbírálására használható.

### <a name="180"></a><a name="1.8.0"/>1.8.0
* A többrégiós adatbázis-fiókok támogatása hozzáadva.

### <a name="170"></a><a name="1.7.0"/>1.7.0
* Az élettartam (TTL) szolgáltatás támogatása a dokumentumok számára.

### <a name="161"></a><a name="1.6.1"/>1.6.1
* A kiszolgálóoldali particionálással kapcsolatos hibajavítások speciális karakterek a partíciós kulcs elérési útjában való engedélyezéséhez.

### <a name="160"></a><a name="1.6.0"/>1.6.0
* A [particionált gyűjtemények](partition-data.md) és a [felhasználó által definiált teljesítményszint](performance-levels.md)implementálva. 

### <a name="150"></a><a name="1.5.0"/>1.5.0
* Adja hozzá a kivonatoló &-tartomány partíciójának feloldóit, hogy segítséget nyújtson a több partíción lévő alkalmazások horizontális felskálázásában.

### <a name="142"></a><a name="1.4.2"/>1.4.2
* Upsert implementálása. Új UpsertXXX-metódusok lettek hozzáadva a Upsert szolgáltatás támogatásához.
* Az azonosító alapú útválasztás implementálása. Nincs nyilvános API-változás, az összes változás belső.

### <a name="120"></a><a name="1.2.0"/>1.2.0
* A térinformatikai index támogatása.
* Érvényesíti az összes erőforrás azonosító tulajdonságát. Az erőforrások azonosítói nem tartalmazhatnak szóközt,/, #, \, karakter vagy véget.
* Hozzáadja az új "index átalakítási folyamat" fejlécet a ResourceResponse.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* A v2 indexelési házirendet implementálja.

### <a name="101"></a><a name="1.0.1"/>1.0.1
* Támogatja a proxy-kapcsolatokat.

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Kiadási & nyugdíjazási dátumok
A Microsoft legalább **12 hónappal** korábban értesítést küld az SDK kivonásáról, hogy zökkenőmentes legyen az áttérés egy újabb/támogatott verzióra.

Az új funkciók és funkciók és optimalizálás csak a jelenlegi SDK-hoz adódik hozzá, ezért azt javasoljuk, hogy a lehető leghamarabb frissítsen a legújabb SDK-verzióra. 

A szolgáltatás elutasítja a kivont SDK-val Cosmos DBre irányuló kéréseket.

> [!WARNING]
> Az SQL API- **hoz készült PYTHON** SDK minden verziójának a **2016. február 29-** én kivonult. 
> 
> 

> [!WARNING]
> Az SQL API-hoz készült Python SDK 1. x és 2. x verziója a **2020-es augusztus 30-** án megszűnik. 
> 
> 

<br/>

| Verzió | Kiadás dátuma | Nyugdíjazás dátuma |
| --- | --- | --- |
| [3.0.2](#3.0.2) |November 15., 2018 |--- |
| [3.0.1](#3.0.1) |Oct 04, 2018 |--- |
| [2.3.3](#2.3.3) |Szeptember 08., 2018 |2020. augusztus 30-ig |
| [2.3.2](#2.3.2) |08. május 2018 |2020. augusztus 30-ig |
| [2.3.1](#2.3.1) |December 21., 2017 |2020. augusztus 30-ig |
| [2.3.0](#2.3.0) |November 10., 2017 |2020. augusztus 30-ig |
| [2.2.1](#2.2.1) |Szeptember 29., 2017 |2020. augusztus 30-ig |
| [2.2.0](#2.2.0) |2017. május 10. |2020. augusztus 30-ig |
| [2.1.0](#2.1.0) |Május 01., 2017 |2020. augusztus 30-ig |
| [2.0.1](#2.0.1) |Október 30-ig 2016 |2020. augusztus 30-ig |
| [2.0.0](#2.0.0) |Szeptember 29., 2016 |2020. augusztus 30-ig |
| [1.9.0](#1.9.0) |2016. július 7. |2020. augusztus 30-ig |
| [1.8.0](#1.8.0) |Június 14., 2016 |2020. augusztus 30-ig |
| [1.7.0](#1.7.0) |2016. április 26. |2020. augusztus 30-ig |
| [1.6.1](#1.6.1) |2016. április 08. |2020. augusztus 30-ig |
| [1.6.0](#1.6.0) |Március 29., 2016 |2020. augusztus 30-ig |
| [1.5.0](#1.5.0) |2016. január 03. |2020. augusztus 30-ig |
| [1.4.2](#1.4.2) |Október 06.2015 |2020. augusztus 30-ig |
| 1.4.1 |Október 06.2015 |2020. augusztus 30-ig |
| [1.2.0](#1.2.0) |Augusztus 06, 2015 |2020. augusztus 30-ig |
| [1.1.0](#1.1.0) |Július 9., 2015 |2020. augusztus 30-ig |
| [1.0.1](#1.0.1) |2015. május 25. |2020. augusztus 30-ig |
| [1.0.0](#1.0.0) |2015. április 7. |2020. augusztus 30-ig |
| 0.9.4 – előbérlet |Január 14., 2015 |Február 29., 2016 |
| 0.9.3 – előbérlet |December 9., 2014 |Február 29., 2016 |
| 0.9.2 – előbérlet |November 25., 2014 |Február 29., 2016 |
| 0.9.1 – előbérlet |Szeptember 23., 2014 |Február 29., 2016 |
| 0.9.0 – előbérlet |2014. augusztus 21. |Február 29., 2016 |

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
További információ a Cosmos DBről: [Microsoft Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapja. 

