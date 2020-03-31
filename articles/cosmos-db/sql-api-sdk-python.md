---
title: 'Azure Cosmos DB: SQL Python API, SDK & erőforrások'
description: Tudjon meg mindent az SQL Python API-ról és az SDK-ról, beleértve a kiadási dátumokat, a megszüntetési dátumokat és az Azure Cosmos DB Python SDK egyes verziói között végrehajtott módosításokat.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: sngun
ms.openlocfilehash: 6bc636b751d12bdb576e54f26536ac0045839229
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70137338"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Python SDK SQL API-hoz: Kibocsátási megjegyzések és erőforrások
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET módosítási hírcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Aszinkron Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [Többi](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Tömeges végrehajtó - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**SDK letöltése**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API-dokumentáció**|[Python API referenciadokumentációja](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**SDK telepítési utasítások**|[Python SDK telepítési utasítások](https://github.com/Azure/azure-cosmos-python)|
|**Hozzájárulás az SDK-hoz**|[Github](https://github.com/Azure/azure-cosmos-python)|
|**Első lépések**|[A Python SDK – első lépések](sql-api-python-application.md)|
|**Jelenlegi támogatott platform**|[Python 2.7](https://www.python.org/downloads/) és [Python 3.5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="302"></a><a name="3.0.2"/>3.0.2
* A MultiPolygon adattípus támogatása hozzáadva
* Hibajavítás a munkamenet olvasási újraházirendjében
* Hibajavítás a helytelen kitöltési problémákmiatt, miközben 64 karakterláncot dekódolnak

### <a name="301"></a><a name="3.0.1"/>3.0.1
* Hibajavítás a LocationCache-ben
* Hibajavítás végpont újrapróbálkozási logikája
* Rögzített dokumentáció

### <a name="300"></a><a name="3.0.0"/>3.0.0
* Többrégiós írások támogatása.
* A Névtér azure.cosmos névtérre változott.
* Tárolóra és elemre document_client átnevezett gyűjtési és dokumentumfogalmak cosmos_client névre. 

### <a name="233"></a><a name="2.3.3"/>2.3.3
* Hozzáadott támogatása proxy
* További támogatás a változáshír csatorna olvasásához
* A gyűjteménykvóta-fejlécek támogatása hozzáadva
* Hibajavítás a nagy munkamenet-tokenek esetén
* Hibajavítás a ReadMedia API-hoz
* Hibajavítás a partíciókulcs-tartomány gyorsítótárában

### <a name="232"></a><a name="2.3.2"/>2.3.2
* A csatlakozási problémák alapértelmezett újrapróbálkozásai támogatással bővültek.

### <a name="231"></a><a name="2.3.1"/>2.3.1
* Frissített dokumentáció az Azure Cosmos DB-re való hivatkozáshoz az Azure DocumentDB helyett.

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Ez az SDK-verzió az Azure Cosmos DB Emulator legújabb verzióját igényli, amely letölthető a alkalmazásból. https://aka.ms/cosmosdb-emulator

### <a name="221"></a><a name="2.2.1"/>2.2.1
* Hibajavítás az összesített szótárhoz.
* Hibajavítás az erőforrás-kapcsolatban a vágások vágásához.
* Unicode kódolási tesztek hozzáadva.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* A Konzisztens előtag nevű új konzisztenciaszint támogatása hozzáadva.


### <a name="210"></a><a name="2.1.0"/>2.1.0
* Az összesítési lekérdezések támogatása hozzáadva (DARAB, MIN, MAX, SZUM és AVG).
* Hozzáadott egy lehetőséget az SSL-ellenőrzés letiltására, amikor cosmos DB emulátor ellen fut.
* Eltávolította a korlátozás függő kérelmek modul pontosan 2.10.0.
* A particionált gyűjtemények minimális átviteli hatása 10 100 RU/s-ról 2500 RU/s-ra csökkent.
* A parancsfájlnaplózás engedélyezésének támogatása a tárolt eljárás végrehajtása során.
* Rest API-verzió a következő kiadással "2017-01-19" lesz.

### <a name="201"></a><a name="2.0.1"/>2.0.1
* Szerkesztői módosításokat végzett a dokumentációs megjegyzéseken.

### <a name="200"></a><a name="2.0.0"/>2.0.0
* A Python 3.5 támogatása hozzáadva.
* A kérelmek modul használatával támogatja a kapcsolatkészletezést.
* A munkamenet konzisztenciájának támogatása hozzáadva.
* A particionált gyűjtemények TOP/ORDERBY lekérdezéseinek támogatása hozzáadva.

### <a name="190"></a><a name="1.9.0"/>1.9.0
* Újrapróbálkozási szabályzat támogatása a szabályozott kérelmekhez hozzáadva. (A szabályozott kérelmek túl nagy kivételt kapnak, hibakód: 429.) Alapértelmezés szerint az Azure Cosmos DB újrapróbálkozik kilenc alkalommal minden kérelmet, ha hibakód 429-es hibakód merül fel, tiszteletben tartva az újrapróbálkozási idő után a válasz fejlécében. A rögzített újrapróbálkozási időmost már beállítható a ConnectionPolicy objektum Újrapróbálkozási tulajdonságának részeként, ha figyelmen kívül szeretné hagyni a kiszolgáló által az újrapróbálkozások között visszaadott újrapróbálkozási időt. Az Azure Cosmos DB most antól legfeljebb 30 másodpercet vár minden egyes szabályozás alatt álló kérelemre (az újrapróbálkozások számától függetlenül), és a 429-es hibakóddal adja vissza a választ. Ez az idő a ConnectionPolicy objektum ÚjrapróbálkozásI beállítások tulajdonságában is felülbírálható.
* A Cosmos DB most antól x-ms-throttle-retry-count és x-ms-throttle-retry-wait-time-ms a válaszfejlécek minden kérelemben a szabályozás újrapróbálkozások számát és a kérelem által az újrapróbálkozások között várakozó összesített időt adja vissza.
* Eltávolította a RetryPolicy osztályt és a document_client osztályban elérhetőnek megfelelő tulajdonságot (retry_policy), és ehelyett bevezetett egy RetryOptions osztályt, amely a RetryOptions tulajdonságot a ConnectionPolicy osztályban, amely a alapértelmezett újrapróbálkozási beállításokat.

### <a name="180"></a><a name="1.8.0"/>1.8.0
* Hozzáadva a többrégiós adatbázisfiókok támogatása.

### <a name="170"></a><a name="1.7.0"/>1.7.0
* Hozzáadva a Time To Live(TTL) funkció támogatása a dokumentumokhoz.

### <a name="161"></a><a name="1.6.1"/>1.6.1
* Hibajavítások kapcsolatos szerver oldali particionálás, hogy különleges karaktereket partíció kulcs elérési útját.

### <a name="160"></a><a name="1.6.0"/>1.6.0
* [Particionált gyűjtemények](partition-data.md) és [a felhasználó által definiált teljesítményszintek.](performance-levels.md) 

### <a name="150"></a><a name="1.5.0"/>1.5.0
* Kivonatoló & tartomány partíciófeloldók, hogy segítse az alkalmazások több partíción átnyúló skálázás.

### <a name="142"></a><a name="1.4.2"/>1.4.2
* Valósítsa meg upsert. Új UpsertXXX módszerek hozzá, hogy támogassa Upsert funkció.
* Azonosítóalapú útválasztás megvalósítása. Nincs nyilvános API-módosítás, minden belső módosítás.

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Támogatja a térinformatikai indexet.
* Az összes erőforrás azonosítótulajdonságának ellenőrzése. Az erőforrások azonosítói nem tartalmazhatnak ?, /, #, karaktereket, \, és szóközökkel végződhetnek.
* Új fejléc "index átalakítási folyamat" a ResourceResponse.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* V2 indexelési házirendet valósít meg.

### <a name="101"></a><a name="1.0.1"/>1.0.1
* Támogatja a proxykapcsolatot.

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>A megszüntetési & kiadása
A Microsoft legalább **12 hónappal** az SDK kicsomagolása előtt értesítést küld az újabb/támogatott verzióra való áttérés zökkenőmentessé.

Az új funkciók és funkciók és optimalizálások csak az aktuális SDK-hoz kerülnek hozzáadásra, ezért javasoljuk, hogy mindig a lehető leghamarabb frissítsen a legújabb SDK-verzióra. 

A Cosmos DB-nek a rendszer egy kivisszavonult SDK-t használó kérését a szolgáltatás elutasítja.

> [!WARNING]
> **2016. február 29-én**a Python SDK **1.0.0-s** verzió előtt az SQL API-hoz készült összes verziója kivisszavonult. 
> 
> 

> [!WARNING]
> **2020. augusztus 30-án**a Python SDK for SQL API 1.x és 2.x összes verziója megszűnik. 
> 
> 

<br/>

| Verzió | Megjelenési dátum | Nyugdíjazás dátuma |
| --- | --- | --- |
| [3.0.2](#3.0.2) |2018. november 15. |--- |
| [3.0.1](#3.0.1) |2018. október 04. |--- |
| [2.3.3](#2.3.3) |2018. szeptember 08. |2020. augusztus 30. |
| [2.3.2](#2.3.2) |2018. május 08.May 08, 2018 |2020. augusztus 30. |
| [2.3.1](#2.3.1) |2017. december 21., 2017 |2020. augusztus 30. |
| [2.3.0](#2.3.0) |2017. november 10., 2017. |2020. augusztus 30. |
| [2.2.1](#2.2.1) |2017. szeptember 29. |2020. augusztus 30. |
| [2.2.0](#2.2.0) |2017. május 10. |2020. augusztus 30. |
| [2.1.0](#2.1.0) |2017. május 01.May 01, 2017 |2020. augusztus 30. |
| [2.0.1](#2.0.1) |2016. október 30. |2020. augusztus 30. |
| [2.0.0](#2.0.0) |2016. szeptember 29. |2020. augusztus 30. |
| [1.9.0](#1.9.0) |2016. július 07. |2020. augusztus 30. |
| [1.8.0](#1.8.0) |2016. június 14. |2020. augusztus 30. |
| [1.7.0](#1.7.0) |2016. április 26. |2020. augusztus 30. |
| [1.6.1](#1.6.1) |2016. április 08. |2020. augusztus 30. |
| [1.6.0](#1.6.0) |2016. március 29. |2020. augusztus 30. |
| [1.5.0](#1.5.0) |2016. január 03. |2020. augusztus 30. |
| [1.4.2](#1.4.2) |2015. augusztus 26. |2020. augusztus 30. |
| 1.4.1 |2015. augusztus 26. |2020. augusztus 30. |
| [1.2.0](#1.2.0) |2015. augusztus 06. |2020. augusztus 30. |
| [1.1.0](#1.1.0) |2015. július 09. |2020. augusztus 30. |
| [1.0.1](#1.0.1) |2015. május 25. |2020. augusztus 30. |
| [1.0.0](#1.0.0) |2015. augusztus 25. |2020. augusztus 30. |
| 0.9.4-prelease |2015. augusztus 25. |2016. február 29. |
| 0.9.3-prelease |2014. december 09. |2016. február 29. |
| 0.9.2-prelease |2014. augusztus 25. |2016. február 29. |
| 0.9.1-prelease |2014. szeptember 23. |2016. február 29. |
| 0.9.0-prelease |2014. augusztus 21. |2016. február 29. |

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
A Cosmos DB szolgáltatásról a [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatáslapján olvashat bővebben. 

