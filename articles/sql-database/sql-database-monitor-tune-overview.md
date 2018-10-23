---
title: Monitorozás és teljesítményhangolás – Azure SQL Database |} A Microsoft Docs
description: Tippek a teljesítmény-finomhangolási az Azure SQL Database révén értékelése és javítása.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 10/22/2018
ms.openlocfilehash: 60f60b9ef055ea38a2036c4f9c5b6aa2c1f6526d
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648054"
---
# <a name="monitoring-and-performance-tuning"></a>Figyelés és a teljesítmény hangolása

Az Azure SQL Database automatikusan kezeli, és a szolgáltatás rugalmas, ahol egyszerűen figyelheti, hozzáadhat vagy erőforrásból (CPU, memória, i/o), keresse meg a javaslatok, amelyek javítani az adatbázis teljesítményét, vagy lehetővé teszik az adatbázis alkalmazkodik számítási feladatához és automatikusan optimalizálhatja a teljesítményt.

## <a name="monitoring-database-performance"></a>Adatbázis teljesítményének figyelése

Egy Azure SQL-adatbázis teljesítményének figyelése az erőforrás-használatnak a kiválasztott adatbázis teljesítményszintjéhez viszonyított figyelésével kezdődik. Az Azure SQL Database lehetővé teszi, azokat a lehetőségeket és a lekérdezési teljesítmény optimalizálása áttekintésével erőforrások módosítása nélkül [teljesítmény-finomhangolási ajánlásait](sql-database-advisor.md). A hiányzó indexek és rosszul optimalizált lekérdezések az adatbázis gyenge teljesítményének gyakori okai. Ezeket a számítási feladat teljesítményét a hangolási javaslatokat is alkalmazható. Is tegye lehetővé, Azure SQL database [automatikus optimalizálása a lekérdezések teljesítményének](sql-database-automatic-tuning.md) alkalmazásával az összes azonosított javaslatok és adatbázis-teljesítmény javítása ellenőrzése.

A megfigyelés és hibaelhárítás az adatbázis teljesítményét a következő lehetőségek állnak rendelkezésére:

- Az a [az Azure portal](https://portal.azure.com), kattintson a **SQL-adatbázisok**, és válassza ki az adatbázist, majd használja a figyelés diagramra a hamarosan eléri a maximális erőforrások kereséséhez. DTU-fogyasztásának alapértelmezés szerint jelenik meg. Kattintson a **szerkesztése** módosíthatja az időtartományt és látható értékeket.
- Használat [lekérdezési Terheléselemző](sql-database-query-performance.md) azonosítására, hogy a legtöbbet az erőforrások költségek lekérdezéseket.
- Használat [az SQL Database Advisor](sql-database-advisor-portal.md) létrehozása és az indexek elvetését, a lekérdezések paraméterezése és a séma problémáinak javítása javaslatok megtekintése.
- Használat [Azure SQL-Intelligent Insights](sql-database-intelligent-insights.md) automatikus ellenőrzésére, az adatbázis teljesítményét. Ha a teljesítménybeli problémát észlel, a részletek és a legfelső szintű okok elemzése (RCA) a probléma diagnosztikai napló jön létre. Ha lehetséges teljesítményének fokozása javaslat biztosítunk.
- [Automatikus hangolás engedélyezése](sql-database-automatic-tuning-enable.md) , és lehetővé teszik az Azure SQL database automatikusan javítja az azonosított teljesítménybeli problémák.
- Használat [dinamikus felügyeleti nézetekkel (DMV-kkel)](sql-database-monitoring-with-dmvs.md), [bővített események](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-xevent-db-diff-from-svr), és a [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) részletesebb teljesítmény kapcsolatos hibák elhárítása.

> [!TIP]
> Lásd: [teljesítmény-útmutató](sql-database-performance-guidance.md) található módszereket, amelyek segítségével egy vagy több, a fenti módszerek, a teljesítménnyel kapcsolatos problémák azonosítása után az Azure SQL Database teljesítményének javítása.

## <a name="monitor-databases-using-the-azure-portal"></a>Adatbázisok figyelése Azure Portal használatával

Az a [az Azure portal](https://portal.azure.com/), figyelheti egy önálló adatbázis s kihasználtság az adatbázis kiválasztásával, kattintson a **figyelés** diagram. Ekkor megjelenik a **Metrika** ablak, amelyet a **Diagram szerkesztése** gombra kattintva módosíthat. Adja hozzá a következő metrikákat:

- Processzorhasználat (%)
- DTU-kihasználtság (%)
- Adat IO kihasználtsága (%)
- Adatbázis méretének kihasználtsága

Ezek a metrikák hozzáadása után továbbra is megtekintheti őket a a **figyelés** további információt a diagramon az **metrika** ablak. A négy metrika az átlagos kihasználtság százalékos arányát jeleníti meg az adatbázis **DTU-jához** viszonyítva. Tekintse meg a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) cikkekben további információt a szolgáltatási szintekről.  

![Adatbázis-teljesítményének szolgáltatásszint-figyelése.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

A metrikákhoz riasztásokat is lehet konfigurálni. Kattintson a **Riasztás hozzáadása** gombra a **Metrika** ablakban. A riasztás konfigurálásához kövesse a Varázslót. Lehetőség van riasztást kérni, ha a metrikák túllépnek egy bizonyos küszöböt, vagy egy bizonyos küszöb alá esnek.

Például ha az adatbázisban munkaterhelés-növekedésére számít, beállíthatja, hogy riasztást kapjon elektronikus üzenet formájában abban az esetben, ha az adatbázisra vonatkozó bármelyik metrika eléri a 80 százalékot. Segítségével ez korai figyelmeztetésként szolgálhat döntse el, ha lehetséges, hogy váltson át a következő legnagyobb számítási méret.

A teljesítmény-mérőszámok is segítségével meghatározhatja, hogy ha Ön tudni számítási kisebb méretre. Tegyük fel, hogy Standard S2 adatbázist használ, és a metrikák azt mutatják, hogy az adatbázis átlagos kihasználtsága egy adott időpontban nem több, mint 10 százalék. Ebben az esetben valószínű, hogy az adatbázis Standard S1 teljesítményszinten is megfelelően fog működni. Vegye figyelembe az esetlegesen hirtelen, vagy ingadozó munkaterheléseket a kisebb számítási méretre elvégzése előtt.

## <a name="troubleshoot-performance-issues"></a>Teljesítményproblémák elhárítása

Diagnosztizálhatja és megoldhatja a teljesítménnyel kapcsolatos problémákat, hogy első lépésként minden aktív lekérdezés és a feltételeket, amelyek az egyes számítási feladatok állapota a teljesítményproblémákat ismertetése. Azure SQL Database teljesítményének javításához tisztában azzal, hogy egyes aktív lekérdezési kérelmek az alkalmazásból vagy egy futó vagy várakozó állapotban. Az Azure SQL Database a teljesítménnyel kapcsolatos problémák elhárításához, a következő diagram tartsa szem előtt, olvassa el a cikk alapján diagnosztizálhatja és megoldhatja a teljesítménnyel kapcsolatos problémák az Ön nevében.

![Számítási feladatok állapotai](./media/sql-database-monitor-tune-overview/workload-states.png)

Teljesítménnyel kapcsolatos problémákkal rendelkező számítási a teljesítménnyel kapcsolatos problémák okozhatják CPU versengés (egy **futó kapcsolatos** feltétel) vagy az egyes lekérdezések valami várakozik (egy **várakozási kapcsolatos** feltétel ).

## <a name="running-related-performance-issues"></a>Futó kapcsolatos teljesítményproblémák

Vegye figyelembe a CPU-kihasználtság következetesen elérte vagy meghaladta a 80 %-os lehetősége van egy futó kapcsolatos teljesítménybeli problémát. Ha egy futó kapcsolatos problémák, nem elegendő Processzor-erőforrások okozhatja, vagy azt kapcsolatosak, a következő feltételek valamelyike:

- Túl sok futó lekérdezések
- Túl sok fordítása lekérdezések
- Egy vagy több végrehajtó lekérdezést használ az optimálisnál rosszabb lekérdezésterv

Ha azt állapítja meg, hogy rendelkezik-e a futó kapcsolatos teljesítménybeli problémát, a cél, a pontos probléma egy vagy több módszerrel azonosíthatja. A leggyakoribb futó kapcsolatos problémák azonosítására szolgáló módszerek a következők:

- Használja a [az Azure portal](#monitor-databases-using-the-azure-portal) százalékos processzorhasználat monitorozásához.
- Használja a következő [dinamikus felügyeleti nézetek](sql-database-monitoring-with-dmvs.md):

  - [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) adja vissza a memória, Processzor és i/o-felhasználási egy Azure SQL Database-adatbázishoz. Egy sor létezik 15 másodpercenként, még akkor is, ha ott nem nincs tevékenység az adatbázisban. A régebbi adatok egy órán keresztül változatlan marad.
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) CPU-használatát és a tárolási adatok az Azure SQL Database adja vissza. Az adatokat gyűjti, és öt perces intervallumok belül.

> [!IMPORTANT]
> A T-SQL-lekérdezések a DMV-vel a CPU-kihasználtság problémák elhárításához talál itt [azonosítása CPU-teljesítménnyel kapcsolatos problémák](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

Miután azonosította a problémát, a probléma Lekérdezések finomhangolása vagy a számítási méret frissítése vagy is elháríthatók a CPU-követelmények az Azure SQL database kapacitásának növelése a szolgáltatásszintet. Az önálló adatbázisok erőforrások méretezéssel kapcsolatos információkért lásd: [egyetlen adatbázis-erőforrások skálázása az Azure SQL Database](sql-database-single-database-scale.md) és rugalmas készletek az erőforrások skálázása, lásd: [méretezhető rugalmas adatbáziskészlet erőforrásainak az Azure SQL-ben Adatbázis](sql-database-elastic-pool-scale.md). Felügyelt példány méretezéssel kapcsolatos információkért lásd: [példányszintű erőforráskorlátok](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).

## <a name="waiting-related-performance-issues"></a>Várakozás kapcsolatos teljesítményproblémák

Biztos, hogy nem fennálló egy nagy-Processzor, a teljesítménycsökkenés futó kapcsolatos, miután fennálló várakozási kapcsolatos teljesítményprobléma. Nevezetesen a Processzor-erőforrások nem használ hatékonyan, mert a Processzor várakozik néhány erőforráson. Ebben az esetben a következő lépés, hogy milyen várakozik a Processzor-erőforrások azonosítására. A leggyakrabban használt módszerek megjelenítéséhez felső várjon típusú kategóriák:

- A [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) idővel várakozási statisztika lekérdezésenként biztosít. A Query Store várjon típusok mostantól várakozási kategóriák. A leképezés típusú várjon a kategóriák várakozási érhető el a [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql?view=sql-server-2017#wait-categories-mapping-table).
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) által végrehajtott művelet során észlelt összes vár kapcsolatos információkat ad vissza. Ez a összesített nézet segítségével diagnosztizálhatja a teljesítménnyel kapcsolatos problémák az Azure SQL Database és az adott lekérdezések és -kötegek.
- [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) feladat, amely néhány erőforrás várakozik a várakozási sorral kapcsolatos információkat ad vissza.

Amint az előző diagramon látható, a leggyakoribb vár a következők:

- Zárolások (zárolás)
- I/O
- `tempdb`-a versengés kapcsolatos
- Memória biztosítása vár

> [!IMPORTANT]
> A T-SQL-lekérdezések ezek DMV-e várakozási kapcsolatos problémák elhárításához talál itt:
>
> - [I/o-teljesítménybeli problémák azonosítása](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Azonosítsa `tempdb` teljesítménnyel kapcsolatos problémák](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Memória biztosítása vár azonosítása](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)

## <a name="improving-database-performance-with-more-resources"></a>További erőforrások az adatbázis teljesítményének növelése

Végül ha nem találhatók gyakorlatban hasznosítható elemek, amelyek javíthatja az adatbázis teljesítményét, módosíthatja az Azure SQL Database-ben rendelkezésre álló erőforrások mennyiségét. Hozzárendelhet további erőforrások módosításával a [szolgáltatási szint dtu-k](sql-database-service-tiers-dtu.md) egy önálló adatbázis vagy növelje az egy rugalmas készlet edtu-k tetszőleges időpontban. Azt is megteheti Ha használja a [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md), módosíthatja a szolgáltatási rétegben, vagy növelje az adatbázis számára elkülönített erőforrások.

1. Az önálló adatbázisokhoz is [szolgáltatásszintek módosítása](sql-database-service-tiers-dtu.md) vagy [számítási erőforrások](sql-database-service-tiers-vcore.md) igény szerinti adatbázis-teljesítmény javítása érdekében.
2. Több adatbázis esetén fontolja meg [rugalmas készletek](sql-database-elastic-pool-guidance.md) erőforrások automatikus méretezése.

## <a name="tune-and-refactor-application-or-database-code"></a>Hangolás és újrabontása alkalmazás vagy adatbázis-kód

Módosíthatja az alkalmazás kódjában, hogy több optimálisan az adatbázist, módosítsa az indexek, tervek kényszerített vagy mutatók segítségével manuálisan alkalmazkodik a számítási feladatok az adatbázis. Keresse meg az egyes tanácsokat és javaslatokat manuális finomhangolásához és a kód újraírását a [teljesítmény-útmutató témakör](sql-database-performance-guidance.md) cikk.

## <a name="next-steps"></a>További lépések

- Engedélyezze az Azure SQL Database automatikus finomhangolása és lehetővé teszik a számítási feladat teljes mértékben kezelése, automatikus finomhangolási funkció: [automatikus hangolás engedélyezése](sql-database-automatic-tuning-enable.md).
- Manuális hangolás használatához áttekintheti [finomhangolási javaslatai az Azure Portalon](sql-database-advisor-portal.md) , és manuálisan a alkalmazni azokat, amelyek javítják a lekérdezések teljesítményét.
- Módosítsa úgy módosítja az adatbázist a rendelkezésre álló erőforrások [Azure SQL Database-szolgáltatásszintek](sql-database-performance-guidance.md)
