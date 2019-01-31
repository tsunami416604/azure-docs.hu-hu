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
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 1f2dcb43878359d20d737cef6ceb492eb79b7f4c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468325"
---
# <a name="monitoring-and-performance-tuning"></a>Monitorozás és teljesítmény-finomhangolás

Azure SQL Database egy-egy ahol egyszerűen figyelheti, használati adatok automatikusan felügyelt és rugalmas szolgáltatás erőforrásainak hozzáadása vagy eltávolítása (CPU, memória, i/o), a keresési javaslatok, amelyek az adatbázis teljesítményét, vagy lehetővé teszik az adatbázis alkalmazkodik számítási feladatához és automatikusan optimalizálhatja a teljesítményt.

## <a name="monitoring-database-performance"></a>Adatbázis teljesítményének figyelése

Egy Azure SQL-adatbázis teljesítményének figyelése az erőforrás-használatnak a kiválasztott adatbázis teljesítményszintjéhez viszonyított figyelésével kezdődik. Az Azure SQL Database lehetővé teszi, azokat a lehetőségeket és a lekérdezési teljesítmény optimalizálása áttekintésével erőforrások módosítása nélkül [teljesítmény-finomhangolási ajánlásait](sql-database-advisor.md). A hiányzó indexek és rosszul optimalizált lekérdezések az adatbázis gyenge teljesítményének gyakori okai. Ezeket a számítási feladat teljesítményét a hangolási javaslatokat is alkalmazható. Is tegye lehetővé, Azure SQL database [automatikus optimalizálása a lekérdezések teljesítményének](sql-database-automatic-tuning.md) alkalmazásával az összes azonosított javaslatok és adatbázis-teljesítmény javítása ellenőrzése.

A megfigyelés és hibaelhárítás az adatbázis teljesítményét a következő lehetőségek állnak rendelkezésére:

- Az a [az Azure portal](https://portal.azure.com), kattintson a **SQL-adatbázisok**, és válassza ki az adatbázist, majd használja a figyelés diagramra a hamarosan eléri a maximális erőforrások kereséséhez. DTU-fogyasztásának alapértelmezés szerint jelenik meg. Kattintson a **szerkesztése** módosíthatja az időtartományt és látható értékeket.
- Használat [lekérdezési Terheléselemző](sql-database-query-performance.md) azonosítására, hogy a legtöbbet az erőforrások költségek lekérdezéseket.
- Használat [az SQL Database Advisor](sql-database-advisor-portal.md) létrehozása és az indexek elvetését, a lekérdezések paraméterezése és a séma problémáinak javítása javaslatok megtekintése.
- Használat [Azure SQL-Intelligent Insights](sql-database-intelligent-insights.md) automatikus ellenőrzésére, az adatbázis teljesítményét. Ha a teljesítménybeli problémát észlel, a részletek és a legfelső szintű okok elemzése (RCA) a probléma diagnosztikai napló jön létre. Ha lehetséges teljesítményének fokozása javaslat biztosítunk.
- [Automatikus hangolás engedélyezése](sql-database-automatic-tuning-enable.md) , és lehetővé teszik az Azure SQL database automatikusan javítja az azonosított teljesítménybeli problémák.
- Használat [dinamikus felügyeleti nézetekkel (DMV-kkel)](sql-database-monitoring-with-dmvs.md), [bővített események](sql-database-xevent-db-diff-from-svr.md), és a [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) részletesebb teljesítmény kapcsolatos hibák elhárítása.

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

### <a name="troubleshoot-queries-with-parameter-sensitive-query-execution-plan-issues"></a>Lekérdezések paraméter-és nagybetűket lekérdezés végrehajtási terv problémáinak hibaelhárítása

A paraméter kényes terv (PSP) probléma hivatkozik egy forgatókönyvet, ahol a lekérdezésoptimalizáló állít elő, a lekérdezés végrehajtási terv optimális csak egy adott paraméter értéke (vagy értékek) és a gyorsítótárazott tervet majd nem optimális a felhasznált paraméterértékek egymást követő végrehajtások. A nem optimális tervek majd eredményezhet lekérdezési teljesítményproblémákat és általános számítási feladatok átviteli teljesítménycsökkenést.

Nincsenek problémák, egyes társított kompromisszumot kínál a és a hátrányai segítségével több megkerülő megoldások:

- Használja a [ÚJRAFORDÍTOTTUK](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) lekérdezésmutató, minden egyes lekérdezés végrehajtása. Ez a megoldás a fordítási idő és nagyobb CPU jobb terv a minőségi rendeltetésű. Használatával a `RECOMPILE` beállítás pedig nem lehetséges a nagy átviteli sebességet megkövetelő számítási feladatokhoz.
- Használja a [lehetőséget (OPTIMALIZÁLÁS a...) ](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) lekérdezésmutató felülbírálhatja a tényleges paraméter értékét egy jellemző paraméter értéke, amely a legtöbb paraméter értéke lehetőségek elég jó terv.   Ez a beállítás optimális paraméterértékek és a társított terv jellemzők beható ismerete szükséges.
- Használat [(OPTIMALIZÁLÁSA az ismeretlen BEÁLLÍTÁST)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) lekérdezésmutató felülbírálása engedélyezések sűrűségű vektor átlagos használata a tényleges paraméter értéke. Ehhez egy másik úgy, hogy a bejövő paraméterértékek rögzítése a helyi változókba, majd a helyi változókat a predikátumok belül magukat a paraméterek használata helyett. Az átlagos sűrűségű kell *elég jó* az adott javítás.
- Tiltsa le a paraméter elemző használatával a [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) lekérdezés-végrehajtási.
- Használja a [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) , hogy a lekérdezés-végrehajtási újrafordítja a gyorsítótárban. Ez a megoldás feltételezi, hogy a *jó elegendő* közös csomag már egy, a gyorsítótár. Annak érdekében, hogy csökkenjen az esélye annak a kiürítés alatt jó terv és a hibás új terv kompilovaná statisztikákat az automatikus frissítések is letilthatók.
- A terv kényszerített explicit módon használatával [USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) lekérdezés-végrehajtási (Query Store használata egy konkrét csomaggal beállításával, explicit módon megadásával vagy engedélyezésével [az automatikus hangolás](sql-database-automatic-tuning.md).
- Cserélje le az egyetlen eljárás alapján feltételes logika és a kapcsolódó paraméterértékeket, amelyek mindegyike használható eljárások beágyazott készlet.
- A dinamikus karakterlánc végrehajtási alternatívái egy statikus definíció létrehozása.

Az ilyen típusú problémák megoldása kapcsolatos további információkért lásd:

- Ez [paraméter szag](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/) blogbejegyzés
- Ez [elemző a problémáról és megkerülő megoldások paraméter](https://blogs.msdn.microsoft.com/turgays/2013/09/10/parameter-sniffing-problem-and-possible-workarounds/) blogbejegyzés
- Ez [elefántviselkedési és egeret paraméter elemző](https://www.brentozar.com/archive/2013/06/the-elephant-and-the-mouse-or-parameter-sniffing-in-sql-server/) blogbejegyzés
- Ez [paraméterezett lekérdezéseknél terv minőségi és dinamikus sql](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/) blogbejegyzés

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>Fordítási tevékenység miatt nem megfelelő paraméterezés hibaelhárítása

Amikor lekérdezést literálok, vagy az adatbázismotor úgy dönt, hogy az utasítás automatikusan paraméterezni, vagy egy felhasználó explicit módon lehet paraméterezni, fordítható számának csökkentése érdekében. Használatával a ugyanazt a mintát, de különböző konstansértékekkel lekérdezés lefordítja nagy számú magas CPU-kihasználtság eredményezhet. Hasonlóképpen csak részben paraméterezni egy lekérdezést, amely folyamatosan literálok lehetnek, ha az adatbázismotorhoz parametrizálja, további.  Az alábbi, például a részlegesen paraméteres lekérdezés:

```sql
select * from t1 join t2 on t1.c1=t2.c1
where t1.c1=@p1 and t2.c2='961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

A korábbi példában `t1.c1` veszi `@p1` , de `t2.c2` továbbra is igénybe vehet, literal GUID. Ebben az esetben, ha módosítja az értéket `c2`, egy másik lekérdezést, a lekérdezés lesznek kezelve, és a egy új fordítási történik. Az előző példában a fordítások csökkentése érdekében a megoldás az, is paraméterezni a GUID Azonosítót.

A következő lekérdezést a lekérdezések száma lekérdezés kivonata meghatározni, ha a lekérdezés megfelelően paraméterezni, vagy nem jeleníti meg:

```sql
   SELECT  TOP 10  
      q.query_hash
      , count (distinct p.query_id ) AS number_of_distinct_query_ids
      , min(qt.query_sql_text) AS sampled_query_text
   FROM sys.query_store_query_text AS qt
      JOIN sys.query_store_query AS q
         ON qt.query_text_id = q.query_text_id
      JOIN sys.query_store_plan AS p 
         ON q.query_id = p.query_id
      JOIN sys.query_store_runtime_stats AS rs 
         ON rs.plan_id = p.plan_id
      JOIN sys.query_store_runtime_stats_interval AS rsi
         ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
   WHERE
      rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
      AND query_parameterization_type_desc IN ('User', 'None')
   GROUP BY q.query_hash
   ORDER BY count (distinct p.query_id) DESC
```

### <a name="resolve-problem-queries-or-provide-more-resources"></a>A probléma lekérdezéseket, vagy adjon meg további erőforrások

Miután azonosította a problémát, a probléma Lekérdezések finomhangolása vagy a számítási méret frissítése vagy is elháríthatók a CPU-követelmények az Azure SQL database kapacitásának növelése a szolgáltatásszintet. Az önálló adatbázisok erőforrások méretezéssel kapcsolatos információkért lásd: [egyetlen adatbázis-erőforrások skálázása az Azure SQL Database](sql-database-single-database-scale.md) és rugalmas készletek az erőforrások skálázása, lásd: [méretezhető rugalmas adatbáziskészlet erőforrásainak az Azure SQL-ben Adatbázis](sql-database-elastic-pool-scale.md). Felügyelt példány méretezéssel kapcsolatos információkért lásd: [példányszintű erőforráskorlátok](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).

### <a name="determine-if-running-issues-due-to-increase-workload-volume"></a>Határozza meg, ha fut a növekedés munkaterhelés kötet miatt problémák

Alkalmazás-forgalom és a számítási feladatok növekedése fiókot is nagyobb CPU-kihasználtság, de kell lennie arra, hogy megfelelően a probléma diagnosztizálásával kapcsolatos. Egy nagy-CPU forgatókönyvben válaszol az alábbi kérdésekre, annak megállapításához, hogy valóban a Processzor növelésével munkaterhelés hangerőmódosítások miatt:

1. Azok a lekérdezések az alkalmazásból a nagy-CPU probléma oka?
2. A felső CPU-igényes lekérdezéseket (azaz azonosíthatók):

   - Határozza meg, hogy voltak-e társított ugyanabból a lekérdezés több végrehajtási tervét. Ha igen, határozza meg, hogy miért.
   - A lekérdezéseket. a végrehajtás csomagot határozza meg, ha a végrehajtási időpontok konzisztens volt, és a végrehajtások száma nőtt. Igen, van-e nagy valószínűséggel teljesítményproblémák munkaterhelés növekedése miatt.

Összefoglalva, ha a lekérdezés-végrehajtási terv nem hajtható végre eltérően, de a CPU-kihasználtság mellett végrehajtások száma nőtt, valószínűleg egy számítási feladatok teljesítményének növekedését kapcsolatos probléma van.

Akkor sem mindig könnyen utasításoknak megfelelően, amelyek a CPU-probléma munkaterhelés kötet változik.   Megfontolandó tényezők: 

- **Módosított erőforrás-használat**

  Például vegyünk egy forgatókönyvet, ahol CPU-ra, 80 %-kal hosszabb ideig.  CPU-kihasználtság önmagában nem jelenti azt, számítási feladatok kötet megváltozott.  A lekérdezés végrehajtási terv regressziót és adatok terjesztést érintő változások is hozzájárulhat további erőforrás-használat annak ellenére, hogy az alkalmazás végrehajtja ugyanazt a pontos számítási feladatot.

- **Új lekérdezés jelent meg.**

   Egy alkalmazás a különböző időpontokban előfordulhat, hogy a lekérdezések új készletét meghajtó.

- **Kérések számának növelése vagy csökkenése**

   Ebben a forgatókönyvben a számítási feladatok a legnyilvánvalóbb mértékegysége. A lekérdezések számát nem mindig felel meg több erőforrás-használatot. Azonban ez a metrika még egy jelentős jel, feltéve, hogy a többi tényező nem változik.

## <a name="waiting-related-performance-issues"></a>Várakozás kapcsolatos teljesítményproblémák

Biztos, hogy nem fennálló egy nagy-Processzor, a teljesítménycsökkenés futó kapcsolatos, miután fennálló várakozási kapcsolatos teljesítményprobléma. Nevezetesen a Processzor-erőforrások nem használ hatékonyan, mert a Processzor várakozik néhány erőforráson. Ebben az esetben a következő lépés, hogy milyen várakozik a Processzor-erőforrások azonosítására. A leggyakrabban használt módszerek megjelenítéséhez felső várjon típusú kategóriák:

- A [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) idővel várakozási statisztika lekérdezésenként biztosít. A Query Store várjon típusok mostantól várakozási kategóriák. A leképezés típusú várjon a kategóriák várakozási érhető el a [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql?view=sql-server-2017#wait-categories-mapping-table).
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) által végrehajtott művelet során észlelt összes vár kapcsolatos információkat ad vissza. Ez a összesített nézet segítségével diagnosztizálhatja a teljesítménnyel kapcsolatos problémák az Azure SQL Database és az adott lekérdezések és -kötegek.
- [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) feladat, amely néhány erőforrás várakozik a várakozási sorral kapcsolatos információkat ad vissza.

A nagy-CPU-környezetben a Query Store és a várakozási statisztikák nem mindig tükrözi CPU-kihasználtság két ebből kifolyólag:

- Magas – Processzor fogyasztó lekérdezések még folyamatban, és a lekérdezések még nem fejeződött be
- A nagy-CPU fogyasztó lekérdezések feladatátvétel történt, amikor futottak.

Query Store és várakozási statisztika-követési dinamikus felügyeleti nézetek csak a sikeresen befejezett és időkorlátot lekérdezésekhez eredmények megjelenítése, és ne jelenjen meg az adatokat jelenleg végrehajtás alatt álló utasítások (mindaddig, amíg azok befejezése).  A dinamikus felügyeleti nézet [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) lehetővé teszi, hogy a jelenleg teljesítés alatt lekérdezések és a kapcsolódó feldolgozó idő nyomon követése.

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

1. Az önálló adatbázisokhoz is [szolgáltatásszintek módosítása](sql-database-single-database-scale.md) vagy [számítási erőforrások](sql-database-single-database-scale.md) igény szerinti adatbázis-teljesítmény javítása érdekében.
2. Több adatbázis esetén fontolja meg [rugalmas készletek](sql-database-elastic-pool-guidance.md) erőforrások automatikus méretezése.

## <a name="tune-and-refactor-application-or-database-code"></a>Hangolás és újrabontása alkalmazás vagy adatbázis-kód

Módosíthatja az alkalmazás kódjában, hogy több optimálisan az adatbázist, módosítsa az indexek, tervek kényszerített vagy mutatók segítségével manuálisan alkalmazkodik a számítási feladatok az adatbázis. Keresse meg az egyes tanácsokat és javaslatokat manuális finomhangolásához és a kód újraírását a [teljesítmény-útmutató témakör](sql-database-performance-guidance.md) cikk.

## <a name="next-steps"></a>További lépések

- Engedélyezze az Azure SQL Database automatikus finomhangolása és lehetővé teszik a számítási feladat teljes mértékben kezelése, automatikus finomhangolási funkció: [automatikus hangolás engedélyezése](sql-database-automatic-tuning-enable.md).
- Manuális hangolás használatához áttekintheti [finomhangolási javaslatai az Azure Portalon](sql-database-advisor-portal.md) , és manuálisan a alkalmazni azokat, amelyek javítják a lekérdezések teljesítményét.
- Módosítsa úgy módosítja az adatbázist a rendelkezésre álló erőforrások [Azure SQL Database-szolgáltatásszintek](sql-database-performance-guidance.md)
