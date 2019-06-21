---
title: Leképezés adatfolyam teljesítmény és finomhangolási útmutató az Azure Data Factoryban |} A Microsoft Docs
description: További információ az adatfolyamok leképezés használata esetén az Azure Data Factoryban adatfolyamok teljesítményét befolyásoló legfontosabb tényezők.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: bbbc2bc5c47821469ecf15a27195b1bf0c12e6e5
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190631"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Leképezési adatok folyamatok teljesítmény és finomhangolás – útmutató

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Az Azure Data Factory leképezési adatfolyamok adja meg egy böngészőben kódmentes felületen megtervezésére, üzembe helyezése és összehangolására adatátalakítások ipari méretekben.

> [!NOTE]
> Ha nem ismeri a ADF-leképezés adatfolyamok általánosságban, lásd: [az adatok elkezdenek beérkezni áttekintése](concepts-data-flow-overview.md) Ez a cikk elolvasása előtt.
>

> [!NOTE]
> Amikor tervezésekor és tesztelése a ADF felhasználói felületről adatfolyamok, ügyeljen arra, hogy kapcsolja be a hibakeresési kapcsolót, hogy a data folyamatok hajthat végre valós idejű ízelítőt kapott egy fürt várakozás nélkül.
>

![Hibakeresési gomb](media/data-flow/debugb1.png "hibakeresése")

## <a name="monitor-data-flow-performance"></a>Data flow teljesítményének monitorozása

Tervezése a társítási adatok elkezdenek beérkezni a böngészőben, miközben test jednotky minden egyes átalakítás kattintva választhat az adatok előzetes lapon az egyes átalakítási beállítások alsó ablaktábláján. A következő lépéssel, figyelembe kell venni, hogy a folyamattervezőben az adatokat a folyamat-végpont tesztelése. Adatok folyamat végrehajtása tevékenység hozzáadása és az adatfolyam teljesítményének tesztelése a hibakeresési gomb használatával. A folyamat ablak alsó ablaktábláján az "actions" eyeglass ikon jelenik meg:

![Adatfolyam-figyelő](media/data-flow/mon002.png "adatfolyam figyelő 2")

Kattintson erre az ikonra a végrehajtási terv és a későbbi teljesítményprofilját a adatfolyama jeleníti meg. Ezen információk használatával az adatfolyama elleni különböző méretű adatforrások teljesítményének. Vegye figyelembe, hogy az általános teljesítmény számítások a fürt feladat végrehajtási telepítési idő 1 perc akkor feltételezheti, és ha az alapértelmezett Azure integrációs modult használ, előfordulhat, hogy hozzá kell, valamint a fürt léptetéses felfelé időt 5 perc.

![Data Flow figyelési](media/data-flow/mon003.png "adatfolyam figyelő 3")

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Az Azure SQL Database és az Azure SQL Data warehouse-bA optimalizálása

![Forrás-rész](media/data-flow/sourcepart3.png "rész forrás")

### <a name="partition-your-source-data"></a>Az adatforrás-adatok particionálása

* Nyissa meg "Optimalizálása", és válassza a "Forrás". Egy adott tábla oszlop vagy egy típus beállítása a lekérdezésben.
* Ha "oszlop" lehetőséget választja, majd válassza ki a partíciós oszlopa.
* A kapcsolatok maximális számát is, állítsa be az Azure SQL DB-hez. Magasabb a beállítás az adatbázis kapcsolatait párhuzamos szerezhet próbálhatja ki. Bizonyos esetekben azonban csak korlátozott számú kapcsolatok gyorsabb működés eredményezhet.
* A forrás adatbázistáblák nem lehet particionálni kell.
* Beállítása egy lekérdezést a forrás-átalakítást, amely megfelel az adatbázis-táblázat a particionálási séma lehetővé teszi a forrás-adatbázismotor partíció eltávolítási kihasználhatja.
* Ha a forrás már nincs particionálva, ADF továbbra is a Spark átalakítási környezetben a forrás átalakításában szerepel a kiválasztott kulcs alapján adatparticionálási fogja használni.

### <a name="set-batch-size-and-query-on-source"></a>Köteg mérete és a lekérdezések a forráson

![Forrás](media/data-flow/source4.png "forrás")

* Köteg mérete beállítás utasítja az ADF adatok tárolása a memóriában helyett soronként-csoportok. Egy választható beállítás, és előfordulhat, hogy elfogynak a számítási csomópontokon, ha azok nem megfelelően mérete.
* Egy lekérdezési beállítás sorok jobb forrásban szűrését, mielőtt még vizsgálatát az adatfolyam-feldolgozási, így gyorsabb kezdeti adatgyűjtés is engedélyezheti.
* Ha egy lekérdezést használ, az Azure SQL DB, azaz READ UNCOMMITTED adhat hozzá opcionális lekérdezési javaslatok

### <a name="set-isolation-level-on-source-transformation-settings-for-sql-datasets"></a>Átalakítási beállítások forrás SQL-adatkészletek esetében a elkülönítési szint beállítása

* Olvassa el a nem véglegesített biztosít gyorsabb lekérdezés eredményeit az adatforrás-átalakítás

![Elkülönítési szintet](media/data-flow/isolationlevel.png "elkülönítési szint")

### <a name="set-sink-batch-size"></a>Állítsa be a fogadó kötegmérete

![Fogadó](media/data-flow/sink4.png "fogadó")

* Annak érdekében, hogy az adatfolyamok sor soronként feldolgozása, állítsa be a "Batch"mérete a fogadó Azure SQL DB beállításaiban. Ez jelzi az ADF folyamat adatbázisba írja az megadott mérete alapján, és kötegekben.

### <a name="set-partitioning-options-on-your-sink"></a>Állítsa be a beállítások a fogadón particionálás

* Akkor is, ha nem rendelkezik a cél Azure SQL Database tábláiban particionált adatokkal, nyissa meg az optimalizálás lapot, és állítsa be a particionálást.
* Nagyon gyakran egyszerűen mondanunk ADF ciklikus időszeletelés helyett kényszerítése minden kapcsolat egyetlen partícióról csomópont/sokkal gyorsabb Adatbetöltési eredményez a végrehajtási Spark-fürtökön futó particionálás használata.

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>Az Azure integrációs modul számítási motor méretének növelése

![Új integrációs modul](media/data-flow/ir-new.png "új integrációs modul")

* Növelje a magok számát, amely növeli a csomópontok számát, és nyújt további feldolgozási teljesítmény, lekérdezéséhez és az Azure SQL DB-hez.
* Próbálja ki a "Compute-optimalizált" és "Memóriahasználatra optimalizált" további erőforrásokat a számítási csomópontok vonatkozó beállításokat.

### <a name="unit-test-and-performance-test-with-debug"></a>Test jednotky és hibakeresési a teljesítményteszt

* Ha egység tesztelési adatfolyam-gyűjteményre, a "Data Flow Debug" gomb beállítása ON.
* Az adatfolyam-tervezőben belül az átalakítások a Adatelőnézet lap segítségével az Adatátalakítási logikát eredményeinek megtekintése.
* Az adatáramlás a folyamattervezőben helyez el egy adatfolyam-tevékenységet a folyamat tervezési egységtesztet vászonalapú, és a "Debug" gomb segítségével tesztelheti.
* Hibakeresési módban tesztelése egy élő fűtéssel fürt környezetre, várjon, amíg egy just-in-time-fürt léptetéses mentése nélkül fog működni.

### <a name="disable-indexes-on-write"></a>Tiltsa le az indexeket írás
* Az ADF folyamat tárolt eljárási tevékenység előtt az adatfolyam-tevékenység, amely letiltja a célként megadott táblákhoz, a fogadó az éppen írt indexei használja.
* Az adatfolyam tevékenység után adjon hozzá egy másik tárolt eljárás tevékenység, amely ezeket az indexeket engedélyezve van.

### <a name="increase-the-size-of-your-azure-sql-db"></a>Az Azure SQL Database méretének növelése
* Ütemezhet egy átméretezése a forrás és fogadó Azure SQL Database a folyamatban, hogy az átviteli sebesség növelése és a minimalizálása érdekében, ha dtu-k Azure szabályozás korlátozza a futtatása előtt.
* A folyamat-végrehajtás befejezése után az adatbázisokat, térjen vissza a normál futtatási díjszabás méretezheti.

## <a name="optimizing-for-azure-sql-data-warehouse"></a>Az Azure SQL Data warehouse optimalizálása

### <a name="use-staging-to-load-data-in-bulk-via-polybase"></a>A polybase tömeges adatok betöltése az átmeneti segítségével

* Annak érdekében, hogy az adatfolyamok sor soronként feldolgozása, megadva a "Tesztelés" lehetőséget az fogadó között, így a ADF kihasználhatják a Polybase elkerülése érdekében a sor-sor beszúrása a DW-be. Ez fel fog szólítania a polybase szolgáltatást akkor használja, hogy az adatok tömeges tölthetők az ADF.
* Az adatokat a folyamat tevékenységet végrehajtása esetén egy folyamatot, az átmeneti tárolás engedélyezve van, válassza ki az átmeneti adatok tömeges betöltése a Blob tároló helyét kell.

### <a name="increase-the-size-of-your-azure-sql-dw"></a>Az Azure SQL DW méretének növelése

* Ütemezhet egy átméretezése a forrás és fogadó Azure SQL dw-vel, a folyamatban, hogy az átviteli sebesség növelése és a minimalizálása érdekében az Azure szabályozás, ha DWU korlátok futtatása előtt.

* A folyamat-végrehajtás befejezése után az adatbázisokat, térjen vissza a normál futtatási díjszabás méretezheti.

## <a name="optimize-for-files"></a>A fájlok optimalizálása

* Szabályozhatja, hogy hány ADF-t használó partíciókat. Minden forrás és fogadó átalakítás, valamint minden egyes átalakítás beállíthatja a particionálási séma kidolgozásához. A kisebb fájlok azt tapasztalhatja, "Egypartíciós" is néha működik jobb és gyorsabb, mint a Spark a kisméretű fájlok particionálásához kéri.
* Ha a forrásadatok nem rendelkezik elegendő információt, válassza a "Ciklikus időszeletelés" particionálás és állítsa be a partíciók számát.
* Ha feltárhatja az adatait, és hogy rendelkezik-e, hogy megfelelő kivonatoló kulcsok oszlopokat, használja a kivonatot particionálás lehetőséget.

### <a name="file-naming-options"></a>Fájlelnevezési beállításai

* Az átalakított adatok írása az ADF-leképezés adatfolyamok alapértelmezett jellege, hogy egy adatkészletet, amely egy Blob vagy ADLS társított szolgáltatás rendelkezik írni. Az adatkészlet egy mappa vagy a tároló, nem egy megnevezett fájljának átirányítása kell beállítania.
* Data flow használata az Azure Databricks Spark végrehajtásra, ami azt jelenti, hogy a kimeneti osztani több fájl alapján vagy a Spark, a particionálás alapértelmezett vagy a particionálási séma, amikor explicit módon választotta.
* Egy nagyon gyakori művelet az ADF adatfolyamok, hogy válassza a "Kimeneti egyetlen fájl a" úgy, hogy az összes kimeneti rész fájl egy egyetlen kimeneti fájl egyesített együtt.
* Ez a művelet azonban megköveteli, hogy a kimenet egy adott partíció egy egyetlen fürtcsomóponton csökkenti.
* Ne feledje, ha a népszerű ezt a lehetőséget választja. Ha sok nagy forrásfájlok összefűzhet vannak egy kimeneti fájl partíció csomópont fürterőforrások kívül futtathatja.
* Elkerülése érdekében számítási csomópont erőforrásokat, ne az alapértelmezett vagy explicit particionálási sémát ADF, amely optimalizálja a teljesítmény, és vegye fel a kimeneti mappa fájljainak egy későbbi másolási tevékenység, amely egyesíti az összes, a rész a folyamat új egyetlen a fájl. Ezzel a technikával alapvetően elkülöníti a művelet a fájl egyesítése átalakítás, és csak "kimenet egyetlen fájl a" ugyanazt az eredményt éri el.

## <a name="next-steps"></a>További lépések
Tekintse meg a többi adatfolyam cikkeket a teljesítménnyel kapcsolatos:

- [Adatfolyamok optimalizálása lap](concepts-data-flow-optimize-tab.md)
- [Data Flow tevékenység](control-flow-execute-data-flow-activity.md)
- [Az adatfolyam teljesítményének figyelése](concepts-data-flow-monitoring.md)
