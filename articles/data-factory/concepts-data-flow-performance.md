---
title: Leképezés adatfolyam teljesítmény és finomhangolási útmutató az Azure Data Factoryban |} A Microsoft Docs
description: További információ az adatfolyamok leképezés használata esetén az Azure Data Factoryban adatfolyamok teljesítményét befolyásoló legfontosabb tényezők.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: 7fca586083f70e0b0f7e593d5203392260cd2136
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66172340"
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

## <a name="optimizing-for-azure-sql-database"></a>Az Azure SQL Database-Optimalizálás

![Forrás-rész](media/data-flow/sourcepart2.png "rész forrás")

### <a name="you-can-match-spark-data-partitioning-to-your-source-database-partitioning-based-on-a-database-table-column-key-in-the-source-transformation"></a>Spark, az adatparticionálás, az adatforrás adatbázis-particionálást a forrás átalakítása az adatbázis-táblában oszlop kulcs alapján meg tudja

* Nyissa meg "Optimalizálása", és válassza a "Forrás". Egy adott tábla oszlop vagy egy típus beállítása a lekérdezésben.
* Ha "oszlop" lehetőséget választja, majd válassza ki a partíciós oszlopa.
* A kapcsolatok maximális számát is, állítsa be az Azure SQL DB-hez. Magasabb a beállítás az adatbázis kapcsolatait párhuzamos szerezhet próbálhatja ki. Bizonyos esetekben azonban csak korlátozott számú kapcsolatok gyorsabb működés eredményezhet.

### <a name="set-batch-size-and-query-on-source"></a>Köteg mérete és a lekérdezések a forráson

![Forrás](media/data-flow/source4.png "forrás")

* Köteg mérete beállítás utasítja az ADF adatok tárolása a memóriában helyett soronként-csoportok. Egy választható beállítás, és előfordulhat, hogy elfogynak a számítási csomópontokon, ha azok nem megfelelően mérete.
* Egy lekérdezési beállítás sorok jobb forrásban szűrését, mielőtt még vizsgálatát az adatfolyam-feldolgozási, így gyorsabb kezdeti adatgyűjtés is engedélyezheti.
* Ha egy lekérdezést használ, az Azure SQL DB, azaz READ UNCOMMITTED adhat hozzá opcionális lekérdezési javaslatok

### <a name="set-sink-batch-size"></a>Állítsa be a fogadó kötegmérete

![Fogadó](media/data-flow/sink4.png "fogadó")

* Annak érdekében, hogy az adatok floes sor soronként feldolgozását, állítsa be a "Batch"mérete a fogadó Azure SQL DB beállításaiban. Ez jelzi az ADF folyamat adatbázisba írja az megadott mérete alapján, és kötegekben.

### <a name="set-partitioning-options-on-your-sink"></a>Állítsa be a beállítások a fogadón particionálás

* Akkor is, ha nem rendelkezik a cél Azure SQL Database tábláiban particionált adatokkal, nyissa meg az optimalizálás lapot, és állítsa be a particionálást.
* Nagyon gyakran egyszerűen mondanunk ADF ciklikus időszeletelés helyett kényszerítése minden kapcsolat egyetlen partícióról csomópont/sokkal gyorsabb Adatbetöltési eredményez a végrehajtási Spark-fürtökön futó particionálás használata.

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>Az Azure integrációs modul számítási motor méretének növelése

![Új integrációs modul](media/data-flow/ir-new.png "új integrációs modul")

* Növelje a magok számát, amely növeli a csomópontok számát, és nyújt további feldolgozási teljesítmény, lekérdezéséhez és az Azure SQL DB-hez.
* Próbálja ki a "Compute-optimalizált" és "Memóriahasználatra optimalizált" további erőforrásokat a számítási csomópontok vonatkozó beállításokat.

### <a name="disable-indexes-on-write"></a>Tiltsa le az indexeket írás
* Az ADF folyamat tárolt eljárási tevékenység előtt az adatfolyam-tevékenység, amely letiltja a célként megadott táblákhoz, a fogadó az éppen írt indexei használja.
* Az adatfolyam tevékenység után adjon hozzá egy másik tárolt eljárás tevékenység, amely ezeket az indexeket engedélyezve van.

### <a name="increase-the-size-of-your-azure-sql-db"></a>Az Azure SQL Database méretének növelése
* Ütemezhet egy átméretezése a forrás és fogadó Azure SQL Database a folyamatban, hogy az átviteli sebesség növelése és a minimalizálása érdekében, ha dtu-k Azure szabályozás korlátozza a futtatása előtt.
* A folyamat-végrehajtás befejezése után az adatbázisokat, térjen vissza a normál futtatási díjszabás méretezheti.

## <a name="next-steps"></a>További lépések
Az adatfolyam cikkekben talál:

- [Az a folyamat áttekintése](concepts-data-flow-overview.md)
- [Data Flow tevékenység](control-flow-execute-data-flow-activity.md)

