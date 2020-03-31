---
title: Az Azure Stream Analytics kimenete az Azure SQL Database-be
description: Ismerje meg, hogyan lehet adatokat kimondani az SQL Azure-ba az Azure Stream Analytics szolgáltatásból, és magasabb írási átviteli sebességet érhet el.
author: chetanmsft
ms.author: chetang
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: f68f973882af28d80b3a27bc4591c5ee932404a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443604"
---
# <a name="azure-stream-analytics-output-to-azure-sql-database"></a>Az Azure Stream Analytics kimenete az Azure SQL Database-be

Ez a cikk ismerteti a tippeket, hogy jobb írási átviteli teljesítmény, amikor az Adatok betöltése az SQL Azure Database az Azure Stream Analytics használatával.

Sql kimenet az Azure Stream Analytics támogatja az írást párhuzamosan lehetőségként. Ez a beállítás lehetővé teszi a [teljesen párhuzamos](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) feladat topológiák, ahol több kimeneti partíciók írása a céltáblába párhuzamosan. Ez a beállítás engedélyezése az Azure Stream Analytics azonban nem elegendő a nagyobb átviteli eredmények eléréséhez, mivel ez jelentősen függ az SQL Azure-adatbázis konfigurációjától és a táblasémát. Az indexek, a fürtözési kulcs, az indexkitöltési tényező és a tömörítés kiválasztása hatással van a táblák betöltésének idejére. Ha többet szeretne tudni arról, hogyan optimalizálhatja az SQL Azure-adatbázist a lekérdezési és betöltési teljesítmény javítása érdekében a belső teljesítmények alapján, olvassa el az [SQL-adatbázisok teljesítményére vonatkozó útmutatást.](../sql-database/sql-database-performance-guidance.md) Írások rendezése nem garantált, ha az SQL Azure-adatbázissal párhuzamosan ír.

Íme néhány konfiguráció az egyes szolgáltatásokon belül, amelyek segíthetnek a megoldás általános átviteli teljesítményének javításában.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

- **Particionálás öröklése** – Ez az SQL-kimeneti konfigurációs beállítás lehetővé teszi az előző lekérdezési lépés vagy bemenet particionálási séma öröklését. Ha ez engedélyezve van, a lemezalapú táblába való írás, valamint a [feladathoz teljesen párhuzamos](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) topológiával rendelkező, várhatóan jobb átmenőt fog látni. Ez a particionálás már automatikusan történik sok más [kimenetek](stream-analytics-parallelization.md#partitions-in-sources-and-sinks). A táblázat zárolása (TABLOCK) szintén le van tiltva az ezzel a beállítással készített tömeges lapkák esetében.

> [!NOTE] 
> Ha több mint 8 bemeneti partíciók, a bemeneti particionálási séma öröklése nem megfelelő választás. Ezt a felső korlátot egy egyetlen identitásoszlopot és egy fürtözött indexet tartalmazó táblában figyelték meg. Ebben az esetben fontolja meg [az INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 8 használatát a lekérdezésben, hogy explicit módon adja meg a kimeneti írók számát. A séma és az indexek kiválasztása alapján a megfigyelések eltérőek lehetnek.

- **Kötegelt méret** – AZ SQL kimeneti konfiguráció lehetővé teszi, hogy adja meg a maximális kötegméretet az Azure Stream Analytics SQL-kimenet jellege alapján a céltábla/számítási feladat. A kötegméret az egyes tömeges beszúrási tranzakciókkal küldött rekordok maximális száma. Fürtözött oszlopcentrikus indexekben a [100 K](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) körüli kötegméretek nagyobb párhuzamosítást, minimális naplózást és zárolásoptimalizálást tesznek lehetővé. Lemezalapú táblákban a 10 K (alapértelmezett) vagy alacsonyabb érték optimális lehet a megoldáshoz, mivel a nagyobb kötegméretek zárolási eszkalációt válthatnak ki ömlesztett beszúrások esetén.

- **Bemeneti üzenetek hangolása** – Ha a particionálás és a kötegméret öröklésével optimalizált, az üzenetenkénti bemeneti események számának növelése segít tovább növelni az írási átviteli szintet. A bemeneti üzenetek finomhangolása lehetővé teszi, hogy az Azure Stream Analytics kötegméretek a megadott kötegméretig legyenek, ezáltal javítva az átviteli szintet. Ez az EventHub vagy a Blob bemeneti üzenetek méretének [növelésével](stream-analytics-define-inputs.md) érhető el.

## <a name="sql-azure"></a>SQL Azure

- **Particionált tábla és indexek** – a [particionált](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-2017) SQL-tábla és a particionált indexek a táblában ugyanazzal az oszloptal, mint a partíciókulcs (például PartitionId) jelentősen csökkentheti a versengések között partíciók írási műveletek során. Particionált tábla esetén létre kell hoznia egy [partíciófüggvényt](https://docs.microsoft.com/sql/t-sql/statements/create-partition-function-transact-sql?view=sql-server-2017) és egy [partíciósémát](https://docs.microsoft.com/sql/t-sql/statements/create-partition-scheme-transact-sql?view=sql-server-2017) az ELSŐDLEGES fájlcsoporton. Ez a meglévő adatok rendelkezésre állását is növeli az új adatok betöltése közben. A log IO-korlát a partíciók száma alapján érhető el, amely a termékváltozat frissítésével növelhető.

- **Egyedi kulcssértések elkerülése** – Ha [több kulcsmegsértése figyelmeztető üzenetet](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) kap az Azure Stream Analytics-tevékenységnaplóban, győződjön meg arról, hogy a feladata nem befolyásolja az egyedi megszorítás-megsértések, amelyek valószínűleg a helyreállítási esetek során történnek. Ez elkerülhető az [INDEXEK\_DUP\_KULCS ÁNAK figyelmen kívül hagyása](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) beállítással.

## <a name="azure-data-factory-and-in-memory-tables"></a>Azure Data Factory és a memóriában lévő táblák

- **A memórián belüli tábla mint ideiglenes tábla** – A [memórián belüli táblák](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) lehetővé teszik a nagyon nagy sebességű adatterhelést, de az adatoknak el kell férniük a memóriában. A teljesítménymutatók azt mutatják, hogy a memórián belüli táblából a lemezalapú táblába történő tömeges betöltés körülbelül 10-szer gyorsabb, mint a közvetlen tömeges beszúrás egyetlen író használatával a lemezalapú táblába egy identitásoszlopot és egy fürtözött indexet. A tömeges beszúrási teljesítmény kihasználásához állítson be egy másolási feladatot az [Azure Data Factory használatával,](../data-factory/connector-azure-sql-database.md) amely adatokat másol a memóriában lévő táblából a lemezalapú táblába.

## <a name="avoiding-performance-pitfalls"></a>Teljesítménybuktatók elkerülése
A tömeges beszúrási adatok sokkal gyorsabbak, mint az adatok egyetlen beszúrással történő betöltése, mivel elkerülhető az adatok átvitelének, a beszúrási utasítás elemzésének, az utasítás futtatásának és a tranzakciós rekord kiadásának ismételt többletterhelése. Ehelyett egy hatékonyabb elérési utat a tárolómotor az adatok streameléséhez. Az elérési út beállítási költsége azonban sokkal magasabb, mint egy lemezalapú táblában lévő lapkautasítás. A megtérülési pont általában körülbelül 100 sor, amelyen túl tömeges betöltése szinte mindig hatékonyabb. 

Ha a bejövő események aránya alacsony, könnyen létrehozhat 100 sornál kisebb kötegméreteket, ami a tömeges beszúrást nem hatékonysá teszi, és túl sok lemezterületet használ. A korlátozás megkerüléséhez hajtsa végre az alábbi műveletek egyikét:
* Hozzon létre egy helyett [eseményindító](/sql/t-sql/statements/create-trigger-transact-sql) tegyszerű beszúrást minden sorban.
* Az előző szakaszban leírtak szerint használjon memórián belüli hőmérséklet-táblázatot.

Egy másik ilyen forgatókönyv akkor fordul elő, amikor nem fürtözött oszlopcentrikus indexbe (NCCI) ír, ahol a kisebb tömeges beszúrások túl sok szegmenst hozhatnak létre, ami az index összeomlásához vezethet. Ebben az esetben a javaslat egy fürtözött oszlopcentrikus index használata helyett.

## <a name="summary"></a>Összefoglalás

Összefoglalva, a particionált kimeneti funkció az Azure Stream Analytics SQL-kimenet, a feladat egy particionált párhuzamosítása egy particionált tábla az SQL Azure-ban jelentős átviteli fejlesztéseket. Az Azure Data Factory használata a memórián belüli táblából lemezalapú táblákba történő adatáthelyezés vezényléséhez nagyságrendi átviteli nyereséget biztosíthat. Ha megvalósítható, az üzenetek sűrűségének javítása is fontos tényező lehet a teljes átviteli teljesítmény javításában.
