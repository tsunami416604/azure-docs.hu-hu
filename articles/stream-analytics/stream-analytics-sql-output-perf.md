---
title: Az Azure SQL Database Azure Stream Analytics-kimenet
description: Ismerje meg az Azure Stream Analytics ad ki adatokat az SQL Azure és a nagyobb írási teljesítmény arányt érhet el.
services: stream-analytics
author: chetanmsft
ms.author: chetanmsft
manager: katiiceva
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/18/2019
ms.openlocfilehash: 4be73554df0b6bddaafe3910c80c855e127d79f1
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527999"
---
# <a name="azure-stream-analytics-output-to-azure-sql-database"></a>Az Azure SQL Database Azure Stream Analytics-kimenet

Ez a cikk ismerteti a tippek jobb írási átviteli teljesítményt érhet el, amikor adatokat tölt be az Azure Stream Analytics SQL Azure Database-be.

SQL-kimenet az Azure Stream Analytics támogatja a párhuzamos beállítás írása. Ez a beállítás lehetővé teszi, hogy a [teljes párhuzamos](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) topológiákat, ahol több kimeneti partíció írása a céltáblába párhuzamos feladat. Ezzel a beállítással az Azure Stream Analytics azonban nem lehet magasabb szintű termékváltozatot jelentős mértékben függ az SQL Azure adatbázis-konfiguráció és a következő tábla sémáját, eléréséhez elegendő. A kiválasztott indexek, a fürtszolgáltatás key index kitöltési tényező és tömörítés hatással a táblák betöltéséhez szükséges időt. Lekérdezés és a teljesítményt biztosító belső referenciaalapokhoz képest történő betöltése az SQL Azure adatbázis optimalizálása kapcsolatos további információkért lásd: [útmutató az SQL database teljesítményének növeléséhez](../sql-database/sql-database-performance-guidance.md). Az írások rendezése nem garantált, párhuzamos SQL Azure adatbázis írásakor.

Az alábbiakban az egyes szolgáltatásban, amelyekkel javíthatja a megoldás teljes átviteli sebesség bizonyos konfigurációk.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

- **Particionálás öröklése** – Ez az SQL kimeneti konfigurációs beállítás lehetővé teszi, hogy örökli az előző lekérdezés lépés vagy a bemeneti particionálási sémát. A beállítás engedélyezve van, a lemezalapú táblák írása, és a egy [teljes párhuzamos](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) a feladat, topológia várt jobb termékváltozatokat. A particionálás már automatikusan történik, számos más [kimenete](stream-analytics-parallelization.md#partitions-in-sources-and-sinks). Ezzel a beállítással végrehajtott tömeges Beszúrások a tábla zárolása (TABLOCK) is letiltja.

> [!NOTE] 
> Ha több mint 8 bemeneti partíció, örökli a particionálási séma bemeneti adatok nem feltétlenül megfelelő választás. Ez a felső határ figyelhető meg egyetlen identitás oszlopa és a egy fürtözött indexet tartalmazó táblán. Ebben az esetben érdemes lehet [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 8 az lekérdezésekben, explicit módon adja meg a kimeneti írók számát. A séma- és az indexek választás alapján a megfigyelések eltérőek lehetnek.

- **Kötegméret** – SQL kimeneti konfiguráció lehetővé teszi, hogy adja meg a Köteg maximális mérete egy Azure Stream Analytics SQL kimeneti jellegét a céloldali tábla/számítási feladatok alapján. A köteg méretét kötelező minden tömeges küldött rekordok maximális száma helyezze be a tranzakciót. Fürtözött oszlopcentrikus indexek, a batch-méretek körül [100 ezer](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) lehetővé teszik a további ezerszer minimális naplózás és optimalizálásokat zárolását. A lemezalapú táblák 10 ezer (alapértelmezett), vagy alacsonyabb lehet a megoldás optimális magasabb köteg méretek kezdeményezheti a zárolás eszkalációs tömeges Beszúrások során.

- **Bemeneti üzenet hangolása** – Ha Ön már optimalizált használatával particionálás és a batch méret öröklése, egy üzenet partíciónként bemeneti események számának növelésével segít a további leküldése másolatot az írható átviteli sebességet. Bemeneti üzenet hangolása lehetővé teszi, hogy a köteg méretek belül az Azure Stream Analytics, legfeljebb a megadott Batch-méret, ezáltal javul a teljesítmény. Ez használatával érhető el [tömörítési](stream-analytics-define-inputs.md) vagy az EventHub- vagy Blob bemeneti üzenet méretének növelését.

## <a name="sql-azure"></a>SQL Azure

- **Particionált tábla- és az indexek** – használatával egy [particionált](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-2017) SQL-táblát és az azonos a partíciókulccsal (például PartitionId) oszlopot az a tábla particionált indexei jelentősen csökkentheti a versenyt között a partíciók írási műveletek során. A particionált táblára vonatkozóan kell létrehozni egy [partíciós függvényei](https://docs.microsoft.com/sql/t-sql/statements/create-partition-function-transact-sql?view=sql-server-2017) és a egy [partícióséma](https://docs.microsoft.com/sql/t-sql/statements/create-partition-scheme-transact-sql?view=sql-server-2017) az elsődleges fájlcsoportban. Meglévő adatok rendelkezésre állását is ez megnöveli a új adatok betöltése közben. Előfordulhat, hogy eléri a napló i/o-korlát növelhető a Termékváltozat frissítésével partíciók száma alapján.

- **Egyedi kulcs hiba elkerülése érdekében** – Ha [több kulcsok protokollmegsértési figyelmeztető üzenetek](stream-analytics-common-troubleshooting-issues.md#handle-duplicate-records-in-azure-sql-database-output) Azure Stream Analytics tevékenységnaplóban győződjön meg, hogy a feladat egyedi korlátozás megsértése, valószínű, amelyek nem befolyásolják. során helyreállítási esetek. Ez elkerülhető az azzal a [figyelmen kívül HAGYÁSA\_Ismétlődő\_kulcs](stream-analytics-common-troubleshooting-issues.md#handle-duplicate-records-in-azure-sql-database-output) az indexek lehetőséget.

## <a name="azure-data-factory-and-in-memory-tables"></a>Az Azure Data Factory és a táblák

- **Memóriabeli ideiglenes tábla a táblázatban** – [memórián belüli táblák](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) lehetővé teszik a nagyon nagy sebességű adatbetöltést, de az adatokat a memóriában megfelelően kell. Referenciaalapokhoz képest történő megjelenítése tömeges betöltése egy memórián belüli táblából, a lemezalapú táblák 10-szer gyorsabb, mint a közvetlenül tömeges beszúrás, egyetlen író használata a lemezalapú táblához identity oszlopot és a egy fürtözött index. A tömeges beszúrás teljesítményét kihasználva, állítsa be a [használata Azure Data Factory másolási feladat](../data-factory/connector-azure-sql-database.md) , amely adatokat másol a memóriában lévő táblában a lemezalapú táblák.

## <a name="avoiding-performance-pitfalls"></a>Teljesítmény Alkalmazásmegoldásokra elkerülése
Adatok beszúrása tömeges sokkal gyorsabb, mint az egyszeres beszúrást hajt végre az adatok betöltése, mert az ismétlődő elkerülhető az adatátvitel, az insert utasítás elemzés, az utasítás futtatása és kiállító tranzakció rekord járó többletterhelést. Ehelyett egy hatékonyabb elérési utat használjuk, a storage-motor az adatok továbbításához. Ezt az elérési utat a telepítő költsége azonban sokkal magasabb, mint a lemezalapú táblák egy insert utasítás. A megtérülési pont mérete általában körülbelül 100 sor, túli tömeges betöltési szinte mindig további hatékony. 

Ha a bejövő események aránya alacsony, könnyedén létrehozható köteg méretek alacsonyabb, mint 100 sor, amely lehetővé teszi a tömeges beszúrás nem elég hatékony és túl sok lemezterületet használ. A probléma megoldásához, az alábbi műveletek egyikét teheti:
* Hozzon létre egy INSTEAD OF [eseményindító](/sql/t-sql/statements/create-trigger-transact-sql) egyszerű insert használata minden egyes sorára.
* Használjon egy memóriabeli ideiglenes táblára, az előző szakaszban leírtak szerint.

Egy másik ilyen forgatókönyv esetén a írása (NCCI), nem fürtözött oszlopcentrikus indexbe, kisebb tömeges Beszúrások hozható létre, amelyek az index lefagyhat túl sok szegmensek. Ebben az esetben a javaslatot, hogy a fürtözött Oszlopcentrikus index használja helyette.

## <a name="summary"></a>Összegzés

Összefoglalva, az SQL-kimenet, az Azure Stream Analytics a particionált kimeneti szolgáltatásával a feladat egy particionált tábla az SQL Azure igazított ezerszer kell lehetővé teszik, jelentős átviteli fejlesztései. Azure Data Factory felhasználásával a lemezalapú táblák be egy memórián belüli táblából adatáthelyezés vezénylésre biztosíthat nagyságrendű átvitelisebesség-növekedések. Ha lehetséges, üzenet sűrűségű javítására is tényező teljes átviteli sebesség növelése.
