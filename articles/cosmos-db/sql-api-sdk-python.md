---
title: 'Az Azure Cosmos DB: SQL Python API, SDK és -erőforrások'
description: Mindent megtudhat a SQL Python API-t és az SDK kiadási dátum, kivezetési dátum és az Azure Cosmos DB Python SDK minden verziója közötti végzett módosításokat.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: sngun
ms.openlocfilehash: 9a240e15049d1acd7344f3ecf7dbf41ff2c3850d
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55818787"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Az Azure Cosmos DB Python SDK-t az SQL API-hoz: Kibocsátási megjegyzések és erőforrások
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
> * [BulkExecutor – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**SDK letöltése**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API-dokumentáció**|[Python API dokumentációja](https://docs.microsoft.com/python/api/overview/azure/cosmosdb?view=azure-python)|
|**SDK telepítési utasításokat**|[Python SDK telepítési utasításokat](https://github.com/Azure/azure-cosmos-python)|
|**Hozzájárul az SDK-t**|[GitHubon](https://github.com/Azure/azure-cosmos-python)|
|**Első lépések**|[A Python SDK – első lépések](sql-api-python-application.md)|
|**Aktuális támogatott platform**|[Python 2.7-es](https://www.python.org/downloads/) és [Python 3.5.](https://www.python.org/downloads/)|

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="a-name302302"></a><a name="3.0.2"/>3.0.2
* Támogatás hozzáadva a MultiPolygon datatype
* Hibajavítás munkamenetben olvassa el az újrapróbálkozási szabályzat
* Hibajavítás base 64 karakterláncok dekódolás közben helytelen kitöltési hibák

### <a name="a-name301301"></a><a name="3.0.1"/>3.0.1
* Hibajavítás a LocationCache
* Hibajavítás végpont újrapróbálkozási logika
* Rögzített dokumentációja

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0
* Többrégiós írási műveletek támogatása.
* Namespace azure.cosmos módosult.
* Tároló és elem, document_client cosmos_client új nevet kapott: gyűjtemény és dokumentum fogalmakat. 

### <a name="a-name233233"></a><a name="2.3.3"/>2.3.3
* Támogatás hozzáadva a proxy
* Támogatás hozzáadva a módosítási hírcsatorna olvasása
* Támogatás hozzáadva a kvóta fejlécek gyűjtése
* Nagy munkamenet Bugfix jogkivonatok probléma
* Az ReadMedia API Bugfix
* A partíció kulcstartományhoz gyorsítótárban Bugfix

### <a name="a-name232232"></a><a name="2.3.2"/>2.3.2
* Támogatás hozzáadva a alapértelmezett újrapróbálkozások kapcsolati problémák.

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Frissített dokumentáció az Azure Cosmos DB helyett az Azure DocumentDB-referenciáját.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Az SDK-verziója letölthető Azure Cosmos DB Emulatort elérhető legújabb verziója szükséges https://aka.ms/cosmosdb-emulator.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Hibajavítás összesített szótár.
* Hibajavítás a tisztítás perjeleket az erőforrás-hivatkozás.
* Tesztek hozzáadva a Unicode kódolással.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Egy új konzisztenciaszint támogatása az új ConsistentPrefix nevezik.


### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Összesítés lekérdezések (száma, MIN, MAX, SUM és átlagos) támogatása.
* SSL-ellenőrzés letiltása, szemben a Cosmos DB Emulator futtatásakor lehetőség hozzáadva.
* A korlátozás függő kérelmek modul pontosan 2.10.0 kell eltávolítani.
* Süllyesztett minimálisan 2500 RU/s 10,100 RU/s a particionált gyűjtemények átviteli sebességet.
* Támogatás hozzáadva a tárolt eljárás végrehajtása során parancsfájl naplózás engedélyezése.
* REST API-verzió bumped a 2017-01-19' ebben a kiadásban.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* A Szerkesztői módosítások végzett dokumentáció megjegyzéseket.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Támogatás hozzáadva a Python 3.5-ös verzióját.
* Támogatás hozzáadva a kapcsolatkészletezést kérelmek modul használatával.
* Munkamenet-konzisztencia támogatása.
* Támogatás hozzáadva a particionált gyűjtemények lekérdezése felső/ORDERBY.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* A hozzáadott újrapróbálkozási házirend támogatása szabályozott kérelmeinek száma. (Szabályozott kérelmeinek kap egy kérelem aránya túl nagy kivétel, 429-es hibakód). Alapértelmezés szerint az Azure Cosmos DB újrapróbálkozik kilenc alkalommal az egyes kérések 429-es hibakód észlelt, amikor a válaszfejlécben retryAfter idő teljesítésére. Egy rögzített újrapróbálkozási időközt is most már beállíthat a RetryOptions tulajdonság részeként a ConnectionPolicy objektum Ha azt szeretné, figyelmen kívül hagyja a próbálkozások közötti kiszolgáló által visszaadott retryAfter idő. Az Azure Cosmos DB mostantól megvárja, legfeljebb 30 másodpercig az egyes kérelmek szabályozva lett (függetlenül az újrapróbálkozások számát), és visszaadja a választ, a 429-es hibakód. Ezúttal is felülbírálható ConnectionPolicy objektumon a RetryOptions tulajdonságban.
* Cosmos DB mostantól adja vissza x-ms-szabályozás--újrapróbálkozások és x-ms-throttle-retry-wait-time-ms, ismételje meg a válaszfejlécek, a késleltetési jelölésére minden kérésben, száma és a kérelem összesített, az újrapróbálkozások közötti idő.
* Ehelyett bevezetett az adatokhoz hozzáférést biztosító ConnectionPolicy osztály, amely segítségével írhatja felül a RetryOptions tulajdonságának RetryOptions osztály és eltávolította a RetryPolicy osztály és a megfelelő tulajdonság (retry_policy), a document_client osztály közzétéve a alapértelmezett újrapróbálkozási beállításokat.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Többrégiós adatbázisfiókhoz támogatása.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Dokumentumok idő a Live(TTL) funkció támogatása.

### <a name="a-name161161"></a><a name="1.6.1"/>1.6.1
* Hibajavítások kapcsolódó kiszolgálóoldali particionálást, hogy a speciális karakterek a partíciós kulcs elérési útja.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Megvalósított [particionált gyűjtemények](partition-data.md) és [felhasználó által definiált teljesítményszintek](performance-levels.md). 

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Tartomány & ujjlenyomat hozzáadása, amelyek segítik a horizontális skálázás alkalmazásokat, hogy több partíción feloldók particionálásához.

### <a name="a-name142142"></a><a name="1.4.2"/>1.4.2
* Upsert megvalósításához. Új UpsertXXX módszerek Upsert funkció támogatása érdekében adott hozzá.
* Azonosító-alapú útválasztás megvalósítása. Nincs nyilvános API-módosítás, belső összes módosítást.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Támogatja a térinformatikai index.
* Ellenőrzi az összes erőforrás id tulajdonsága. Az erőforrások azonosítóit nem tartalmazhat?, /, #, \, karaktereket vagy záró szóközzel.
* ResourceResponse ad hozzá új fejléc "index átalakítási folyamat".

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Indexelési házirend V2 valósítja meg.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Proxy-kapcsolatot támogat.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Állapot tárolá & kivezetési dátum
A Microsoft biztosít értesítési legalább **12 hónapig** kivonása egy SDK-t kiegyenlítse az a és újabb támogatott verzióra váltás előtt.

Új szolgáltatások és funkciók és optimalizálási lehetőségek csak hozzá az aktuális SDK-hoz, ezért javasoljuk, hogy mindig a legújabb SDK verzióra frissít leghamarabb lehető. 

Cosmos DB-hez a kivont SDK használatával bármilyen kérelmet a rendszer elutasítja a szolgáltatás által.

> [!WARNING]
> Az Azure SQL SDK Pythonhoz készült korábbi összes verziók **1.0.0-s** án visszavontuk **2016. február 29-én**. 
> 
> 

<br/>

| Verzió | Kiadás dátuma | Visszavonás dátuma |
| --- | --- | --- |
| [3.0.2](#3.0.2) |2018. november 15. |--- |
| [3.0.1](#3.0.1) |Oct 04, 2018 |--- |
| [2.3.3](#2.3.3) |2018. Szeptembertől 08. |--- |
| [2.3.2](#2.3.2) |08 2018. május |--- |
| [2.3.1](#2.3.1) |2017. december 21. |--- |
| [2.3.0](#2.3.0) |2017. november 10. |--- |
| [2.2.1](#2.2.1) |2017. szeptember 29 |--- |
| [2.2.0](#2.2.0) |2017. május 10. |--- |
| [2.1.0](#2.1.0) |01, 2017. május |--- |
| [2.0.1](#2.0.1) |2016. október 30. |--- |
| [2.0.0](#2.0.0) |2016. szeptember 29-én |--- |
| [1.9.0](#1.9.0) |2016. július 07. |--- |
| [1.8.0](#1.8.0) |2016. június 14-én |--- |
| [1.7.0](#1.7.0) |2016. április 26. |--- |
| [1.6.1](#1.6.1) |2016. április 08 |--- |
| [1.6.0](#1.6.0) |2016. március 29-én |--- |
| [1.5.0](#1.5.0) |2016. január 03. |--- |
| [1.4.2](#1.4.2) |2015. október 06. |--- |
| 1.4.1 |2015. október 06. |--- |
| [1.2.0](#1.2.0) |2015. augusztus 06. |--- |
| [1.1.0](#1.1.0) |2015. július 09. |--- |
| [1.0.1](#1.0.1) |2015. május 25. |--- |
| [1.0.0](#1.0.0) |2015. április 07. |--- |
| 0.9.4-prelease |2015. január 14-én |2016. február 29-én |
| 0.9.3-prelease |2014. december 09 |2016. február 29-én |
| 0.9.2-prelease |2014. november 25. |2016. február 29-én |
| 0.9.1-prelease |2014. szeptember 23. |2016. február 29-én |
| 0.9.0-prelease |2014. augusztus 21-én |2016. február 29-én |

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
Cosmos DB kapcsolatos további információkért lásd: [a Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapján. 

