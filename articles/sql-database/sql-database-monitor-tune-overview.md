---
title: Monitorozás és teljesítmény finombeállítása – Azure SQL Database | Microsoft Docs
description: Tippek a teljesítmény finomhangolásához Azure SQL Database a kiértékelés és fejlesztés révén.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: 5df9df1474489d7f1b1fb4e1089143cca63a3e42
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71935601"
---
# <a name="monitoring-and-performance-tuning"></a>Monitorozás és teljesítmény-finomhangolás

A Azure SQL Database olyan eszközöket és metódusokat biztosít, amelyekkel könnyen figyelheti a használatot, hozzáadhat vagy eltávolíthat erőforrásokat (például CPU-t, memóriát vagy I/O-t), elháríthatja a lehetséges problémákat, és javaslatokat tehet az adatbázisok teljesítményének növelésére. A Azure SQL Database szolgáltatásai automatikusan kijavíthatják az adatbázisokban felmerülő problémákat. 

Az automatikus hangolás lehetővé teszi, hogy az adatbázis alkalmazkodjon a számítási feladatokhoz, és automatikusan optimalizálja a teljesítményt. Előfordulhat azonban, hogy egyes egyéni problémák hibaelhárítást igényelnek. Ez a cikk ismerteti az ajánlott eljárásokat és néhány olyan eszközt, amelyekkel elháríthatja a teljesítménnyel kapcsolatos problémákat.

A következő lépésekkel biztosíthatja, hogy egy adatbázis probléma nélkül fusson:
- [Figyelje meg az adatbázis teljesítményét](#monitor-database-performance) , és győződjön meg arról, hogy az adatbázishoz rendelt erőforrások kezelhetik a munkaterhelést. Ha az adatbázis eléri az erőforrás-korlátozásokat, vegye figyelembe a következőket:
   - A legfontosabb erőforrás-fogyasztási lekérdezések azonosítása és optimalizálása.
   - További erőforrások hozzáadása [a szolgáltatási szintek frissítésével](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources).
- [Teljesítményproblémák elhárítása](#troubleshoot-performance-problems) a lehetséges problémák okának megállapítása és a probléma kiváltó okának azonosítása érdekében. A kiváltó ok azonosítása után végezze el a probléma megoldásához szükséges lépéseket.

## <a name="monitor-database-performance"></a>Adatbázis teljesítményének monitorozása

Az Azure-beli SQL-adatbázisok teljesítményének figyeléséhez először figyelje a választott adatbázis-teljesítmény szintjéhez képest felhasznált erőforrásokat. A következő erőforrások figyelése:
 - **CPU-használat**: Ellenőrizze, hogy az adatbázis hosszabb ideig eléri-e a CPU-használat 100 százalékát. A magas CPU-használat azt jelezheti, hogy azonosítania és hangolnia kell a legtöbb számítási teljesítményt használó lekérdezéseket. A magas CPU-használat azt is jelezheti, hogy az adatbázist vagy a példányt magasabb szolgáltatási szintre kell frissíteni. 
 - **Várakozási statisztika**: A [sys. DM _os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) használatával határozza meg, hogy mennyi ideig várakozik a lekérdezések. A lekérdezések erőforrásokra, várakozási sorokra vagy külső várakozásokra is várnak. 
 - **I/o-használat**: Ellenőrizze, hogy az adatbázis eléri-e a mögöttes tároló IO-korlátait.
 - **Memóriahasználat**: Az adatbázis vagy a példány számára rendelkezésre álló memória mennyisége arányos a virtuális mag számával. Győződjön meg arról, hogy a memória elég a munkaterheléshez. A lap várható élettartama azon paraméterek egyike, amelyek azt jelezhetik, hogy a rendszer milyen gyorsan távolítja el a lapokat a memóriából.

A Azure SQL Database szolgáltatás olyan eszközöket és erőforrásokat tartalmaz, amelyek segítenek a lehetséges teljesítménnyel kapcsolatos problémák megoldásában. A [teljesítmény-finomhangolási javaslatok](sql-database-advisor.md)áttekintésével azonosíthatja a lekérdezési teljesítmény javításának és optimalizálásának lehetőségeit az erőforrások módosítása nélkül. 

A hiányzó indexek és rosszul optimalizált lekérdezések az adatbázis gyenge teljesítményének gyakori okai. A számítási feladatok teljesítményének növeléséhez finomhangolási javaslatokat is alkalmazhat. Azt is megteheti, hogy Azure SQL Database az összes azonosított javaslat alkalmazásával [automatikusan optimalizálja a lekérdezések teljesítményét](sql-database-automatic-tuning.md) . Ezután ellenőrizze, hogy a javaslatok javították-e az adatbázis teljesítményét.

> [!NOTE]
> Az indexelés csak önálló adatbázisban és rugalmas készletekben érhető el. Az indexelés felügyelt példányban nem érhető el.

Az adatbázis teljesítményének figyeléséhez és hibakereséséhez válasszon az alábbi lehetőségek közül:

- A [Azure Portal](https://portal.azure.com)válassza az **SQL-adatbázisok** lehetőséget, majd válassza ki az adatbázist. A **figyelési** diagramon keresse meg a maximális kihasználtságot megközelítő erőforrásokat. A DTU-felhasználás alapértelmezés szerint megjelenik. Válassza a **Szerkesztés** lehetőséget a megjelenített időtartomány és értékek módosításához.
- Az eszközök, például a SQL Server Management Studio számos hasznos jelentést biztosítanak, például a [teljesítmény-irányítópultot](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard). Ezekkel a jelentésekkel figyelheti az erőforrás-használatot, és azonosíthatja a leggyakoribb erőforrás-felhasználási lekérdezéseket. A [query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed) segítségével azonosíthatja azokat a lekérdezéseket, amelyeknek a teljesítménye romlott.
- A [Azure Portal](https://portal.azure.com)a legtöbb erőforrást használó lekérdezések azonosításához használja a [lekérdezési terheléselemző](sql-database-query-performance.md) . Ez a funkció csak önálló adatbázisban és rugalmas készletekben érhető el.
- A [SQL Database Advisor](sql-database-advisor-portal.md) használatával megtekintheti az indexek, a parametrizálja lekérdezések és a séma problémáinak létrehozásához és eldobásához szükséges javaslatokat. Ez a funkció csak önálló adatbázisban és rugalmas készletekben érhető el.
- Az [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) használatával automatikusan figyelheti az adatbázis teljesítményét. Teljesítményproblémák észlelésekor a rendszer diagnosztikai naplót hoz létre. A napló a probléma részleteit és a kiváltó okok elemzését (RCA) tartalmazza. Ha lehetséges, teljesítmény-javító javaslatot kell megadnia.
- Az [automatikus hangolás engedélyezése lehetővé](sql-database-automatic-tuning-enable.md) teszi, hogy Azure SQL Database automatikusan javítsa a teljesítménnyel kapcsolatos problémákat.
- A teljesítménnyel kapcsolatos problémák részletesebb hibaelhárításával segítséget nyújthat a [dinamikus felügyeleti nézetek (DMV)](sql-database-monitoring-with-dmvs.md), a [kiterjesztett események](sql-database-xevent-db-diff-from-svr.md)és a [lekérdezési tároló](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) használata.

> [!TIP]
> Miután azonosította a teljesítménnyel kapcsolatos problémát, tekintse meg a [teljesítményre vonatkozó útmutatót](sql-database-performance-guidance.md) , amellyel megtalálhatja a Azure SQL Database teljesítményének javításához szükséges technikákat.

## <a name="troubleshoot-performance-problems"></a>Teljesítménnyel kapcsolatos problémák elhárítása

A teljesítménnyel kapcsolatos problémák diagnosztizálásához és megoldásához először keresse meg az egyes aktív lekérdezések állapotát, valamint azokat a feltételeket, amelyek az egyes számítási feladatok állapotára vonatkozó teljesítménnyel kapcsolatos problémákat okoznak. A Azure SQL Database teljesítményének javítása érdekében meg kell ismernie, hogy az alkalmazás minden aktív lekérdezési kérelme futó állapotban vagy várakozási állapotban van. A Azure SQL Database teljesítménybeli problémáinak elhárítása során tartsa szem előtt a következő diagramot.

![Munkaterhelési állapotok](./media/sql-database-monitor-tune-overview/workload-states.png)

A számítási feladatok teljesítményével kapcsolatos problémát a CPU-tartalom okozhatja (egy *futó* feltételt) vagy egy bizonyos, a *várakozással kapcsolatos* feltételre váró lekérdezéseket.

A futtatással kapcsolatos problémákat az alábbiak okozhatják:
- **Fordítási problémák**: Az SQL-lekérdezés-optimalizáló az elavult statisztikák miatt előkészítheti a legoptimálisabb tervet, a feldolgozandó sorok számának és a szükséges memória pontatlan becslésének megfelelően. Ha tudja, hogy a lekérdezés a múltban vagy egy másik példányon (felügyelt példányon vagy SQL Server-példányon) lett végrehajtva, hasonlítsa össze a tényleges végrehajtási terveket, és ellenőrizze, hogy azok eltérnek-e. Próbáljon meg lekérdezési tippeket alkalmazni, vagy újjáépíteni a statisztikákat vagy indexeket a jobb terv beszerzéséhez. A problémák automatikus mérsékléséhez engedélyezze a Azure SQL Database automatikus tervének javítását.
- **Végrehajtási problémák**: Ha a lekérdezési terv optimális, valószínűleg az adatbázis erőforrás-korlátait, például a napló írási sebességét éri el. Vagy lehet, hogy olyan töredezett indexeket használ, amelyeket újra kell építeni. A végrehajtási problémák akkor is előfordulhat, ha az egyidejű lekérdezések nagy száma ugyanazokat az erőforrásokat igényel. A *várakozással kapcsolatos* problémák általában a végrehajtási problémákkal kapcsolatosak, mert a nem hatékonyan végrehajtandó lekérdezések valószínűleg bizonyos erőforrásokra várnak.

A várakozással kapcsolatos problémákat az alábbiak okozhatják:
- **Blokkolás**: Előfordulhat, hogy egy lekérdezés zárolja az adatbázisban lévő objektumokat, míg mások megpróbálnak hozzáférni ugyanahhoz az objektumhoz. A blokkoló lekérdezéseket DMV vagy figyelési eszközök használatával is azonosíthatja.
- **IO-problémák**: Előfordulhat, hogy a lekérdezések a lapokat az adatvagy a naplófájlba való írásra várnak. Ebben az esetben `INSTANCE_LOG_RATE_GOVERNOR`a, `WRITE_LOG`a vagy `PAGEIOLATCH_*` a wait statisztikát a DMV-ben találja.
- **Tempdb problémák**: Ha a munkaterhelés ideiglenes táblákat használ, vagy a TempDB kifolyások vannak, akkor előfordulhat, hogy a lekérdezések TempDB átviteli sebességgel rendelkeznek. 
- **Memóriával kapcsolatos problémák**: Ha a munkaterhelés nem rendelkezik elegendő memóriával, az oldal várható élettartama csökkenhet, vagy előfordulhat, hogy a lekérdezések kevesebb memóriát igényelnek. Bizonyos esetekben a lekérdezés-optimalizáló beépített intelligencia javítja a memóriával kapcsolatos problémákat.
 
A következő szakaszokban ismertetjük az egyes típusú problémák azonosításának és hibaelhárításának módját.

## <a name="performance-problems-related-to-running"></a>Futó teljesítménnyel kapcsolatos problémák

Általános útmutatóként, ha a CPU-használat konzisztens a 80%-os vagy újabb értéknél, a teljesítménnyel kapcsolatos probléma nem működik. A futtatással kapcsolatos problémát a nem elegendő processzor-erőforrás okozhatja. Vagy lehet, hogy a következő feltételek valamelyikéhez kapcsolódik:

- Túl sok futó lekérdezés
- Túl sok lekérdezés fordítása
- Egy vagy több olyan végrehajtó lekérdezés, amely a legoptimálisabb lekérdezési tervet használja

Ha egy futó teljesítménnyel kapcsolatos problémát tapasztal, a cél a pontos probléma azonosítása egy vagy több módszer használatával. Ezek a módszerek a futtatással kapcsolatos problémák azonosításának leggyakoribb módjai:

- A [Azure Portal](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) használatával figyelheti a CPU-kihasználtság százalékos arányát.
- Használja a következő [DMV](sql-database-monitoring-with-dmvs.md):

  - A [sys. DM _db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV egy SQL-adatbázis CPU-, I/O-és memória-felhasználását adja vissza. Egy sor minden 15 másodperces intervallumban létezik, még akkor is, ha nincs tevékenység az adatbázisban. A korábbi adatait egy óráig őrzi meg a rendszer.
  - A [sys. resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV visszaadja a CPU-használati és adattárolási adatokat Azure SQL Database számára. Az adatok gyűjtése és összesítése öt percenként történik.

> [!IMPORTANT]
> A sys. DM _db_resource_stats és a sys. resource_stats DMV használó T-SQL-lekérdezések CPU-használati problémáinak elhárításához tekintse meg a [CPU-teljesítménnyel kapcsolatos problémák azonosítása](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues)című témakört.

### <a name="ParamSniffing"></a>PSP-problémákkal rendelkező lekérdezések

Ha a lekérdezés-optimalizáló olyan lekérdezés-végrehajtási tervet hoz létre, amely csak az adott paraméterérték (vagy az értékek halmaza) számára érhető el, és a gyorsítótárazott csomag nem optimális, akkor az egymást követő értékekben használt paraméterek értékeinél nem optimális. kivégzések. Azok a csomagok, amelyek nem optimálisak, a lekérdezési teljesítménnyel kapcsolatos problémákat okozhatnak, és csökkenthetik a munkaterhelés teljes átviteli sebességét. 

További információ a paraméterek elemzéséről és a lekérdezések feldolgozásáról: a [lekérdezés-feldolgozási architektúra útmutatója](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Több megkerülő megoldás is csökkentheti a PSP-problémákat. Minden megkerülő megoldáshoz kapcsolódó kompromisszumok és hátrányok tartoznak:

- A lekérdezés [](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) végrehajtásához használja az újrafordítási lekérdezési célzást. Ez a megkerülő megoldás a minőség javítása érdekében a fordítási időt és a CPU-t növeli. A `RECOMPILE` beállítás gyakran nem lehetséges olyan munkaterhelések esetén, amelyeknél magas átviteli sebesség szükséges.
- Használja a [(z) (optimalizálás a...)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) lekérdezési mutatót a tényleges paraméterérték felülbírálására egy jellemző paraméter értékkel, amely egy olyan tervet hoz létre, amely elég jó a legtöbb paraméter-érték lehetőséghez. Ehhez a beállításhoz jól ismerni kell az optimális paramétereket és a társított terv jellemzőit.
- A [(z) (ismeretlen)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) lekérdezési mutatóval felülbírálhatja a tényleges paraméter értékét, és Ehelyett használhatja a sűrűség vektor átlagát. Ezt úgy is megteheti, hogy beírja a bejövő paraméterek értékét a helyi változók között, majd a predikátumok helyi változóit használja a paraméterek használata helyett. Ennél a javításnál az átlagos sűrűségnek *elég jónak*kell lennie.
- Tiltsa le a paraméterek teljes elemzését a [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) -lekérdezési mutató használatával.
- A gyorsítótárban lévő újrafordítások elkerülése érdekében használja a [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) lekérdezési mutatóját. Ez a megkerülő megoldás azt feltételezi, hogy a megfelelő közös csomag már a gyorsítótárban van. Az automatikus statisztikákat is letilthatja, hogy csökkentse a jó terv kizárásának esélyét, és egy új, rossz tervet fog lefordítani.
- A tervet úgy kényszerítheti, hogy explicit módon a [használati terv](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) lekérdezése célzást használja a lekérdezés újraírásával és a lekérdezés szövegének hozzáadásával. Vagy állítson be egy adott csomagot a Query Store használatával vagy az [automatikus hangolás](sql-database-automatic-tuning.md)engedélyezésével.
- Cserélje le az egyetlen eljárást egy beágyazott eljárásokra, amelyek mindegyike felhasználható a feltételes logika és a hozzá tartozó paraméterek értékei alapján.
- Dinamikus karakterlánc-végrehajtási alternatívák létrehozása statikus eljárás definíciójában.

A PSP-problémák megoldásával kapcsolatos további információkért tekintse meg ezeket a blogbejegyzéseket:

- [Egy paramétert érzek](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)
- [A (z) és a dinamikus SQL vs. eljárások és a paraméteres lekérdezések minőségének megtervezése](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [SQL-lekérdezés optimalizálási módszerei SQL Serverban: Paraméter-elemzés](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Helytelen paraméterezés által okozott tevékenységek fordítása

Ha egy lekérdezés literálokat tartalmaz, vagy az adatbázismotor automatikusan felparaméterezi az utasítást, vagy a felhasználó explicit módon felparaméterezi az utasítást, hogy csökkentse a fordítások számát. A lekérdezések nagy számú fordítása ugyanazzal a mintával, de a különböző literális értékek magas CPU-használatot eredményezhetnek. Hasonlóképpen, ha csak részben parametrizálja egy olyan lekérdezést, amely továbbra is literálokkal rendelkezik, akkor az adatbázismotor még nem parametrizálja a lekérdezést.  

Példa egy részleges paraméteres lekérdezésre:

```sql
SELECT * 
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

Ebben a példában `t1.c1` a veszi `@p1`, de `t2.c2` továbbra is a GUID-ot használja literálként. Ebben az esetben, ha megváltoztatja a értékét `c2`, a lekérdezés más lekérdezésként lesz kezelve, és új fordítás fog történni. Ebben a példában a fordítások csökkentése érdekében parametrizálja a GUID azonosítót is.

Az alábbi lekérdezés a lekérdezési kivonatok számát jeleníti meg, hogy a lekérdezés megfelelően van-e konfigurálva:

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

### <a name="factors-that-affect-query-plan-changes"></a>A lekérdezési terv módosítását befolyásoló tényezők

Egy lekérdezés-végrehajtási terv újrafordítása olyan generált lekérdezési tervet eredményezhet, amely eltér az eredeti gyorsítótárazott tervtől. Lehetséges, hogy egy meglévő eredeti csomagot is automatikusan újrafordítanak a különböző okok miatt:
- A séma módosításait a lekérdezés hivatkozik.
- A táblákban lévő adatváltozásokra a lekérdezés hivatkozik. 
- A lekérdezési környezet beállításai módosultak.

A lefordított tervet különböző okok miatt lehet kiadni a gyorsítótárból, például:

- A példány újraindul.
- Adatbázis-hatókörű konfiguráció módosításai.
- Memória nyomása.
- Explicit kérelmek a gyorsítótár törléséhez.

Ha újrafordítási emlékeztetőt használ, a rendszer nem gyorsítótárazza a csomagot.

Az újrafordítás (vagy a gyorsítótár kiürítése utáni friss fordítás) továbbra is az eredetivel megegyező lekérdezés-végrehajtási terv létrehozását eredményezheti. Ha a terv a korábbi vagy az eredeti csomag alapján változik, a következő magyarázatok valószínűek:

- **Megváltozott fizikai kialakítás**: Például az újonnan létrehozott indexek hatékonyabban fedezik a lekérdezés követelményeit. Az új indexeket új fordításban lehet használni, ha a lekérdezés-optimalizáló úgy dönt, hogy az új indexet használja, mint az eredetileg a lekérdezés-végrehajtás első verziójára kiválasztott adatstruktúra használata.  A hivatkozott objektumok fizikai változásai a fordítási idő új tervének megválasztásához vezethetnek.

- **Kiszolgálói erőforrások eltérései**: Ha egy rendszer egyik csomagja eltér a tervtől egy másik rendszeren, az erőforrások rendelkezésre állása, például a rendelkezésre álló processzorok száma befolyásolhatja, hogy melyik terv jön létre.  Ha például egy rendszer több processzorral rendelkezik, lehetséges, hogy egy párhuzamos tervet választ ki. 

- **Különböző statisztikák**: Előfordulhat, hogy a hivatkozott objektumokhoz tartozó statisztikák módosultak, vagy az eredeti rendszer statisztikái lényegesen eltérőek lehetnek.  Ha a statisztikai változás és az újrafordítás történik, a lekérdezés-optimalizáló a változástól kezdődő statisztikát használja. Az átdolgozott statisztikai adatok eloszlása és gyakorisága eltérő lehet az eredeti fordítástól.  Ezek a változások a kardinális becslések létrehozásához használatosak. (A*kardinális becslések* a logikai lekérdezési fában áthaladó sorok száma.) A kardinális becslések változásai a különböző fizikai operátorok és a hozzájuk tartozó műveletek elvégzésére is kiválaszthatók.  A statisztikában még kisebb változások is megváltoztathatják a lekérdezés végrehajtási tervét.

- **Módosult az adatbázis kompatibilitási szintje vagy a kardinális kalkulátor verziója**:  Az adatbázis-kompatibilitási szint változásai olyan új stratégiákat és szolgáltatásokat is tartalmazhatnak, amelyek eltérő lekérdezés-végrehajtási tervet eredményezhetnek.  Az adatbázis-kompatibilitási szinten kívül a letiltott vagy engedélyezett nyomkövetési jelző 4199 vagy az adatbázis-hatókörű konfigurációs QUERY_OPTIMIZER_HOTFIXES módosult állapota is befolyásolhatja a lekérdezés végrehajtási tervének választási lehetőségeit a fordítás ideje alatt.  A nyomkövetési jelzők 9481 (örökölt CE) és 2312 (az alapértelmezett CE kényszerített) a tervet is érintik. 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Probléma-lekérdezések feloldása vagy további erőforrások biztosítása

A probléma azonosítása után beállíthatja a problémák lekérdezését, vagy frissítheti a számítási méretet vagy a szolgáltatási szintet, hogy növelje az SQL-adatbázis kapacitását a CPU-követelmények befogadásához. 

További információkért lásd: [önálló adatbázis-erőforrások méretezése Azure SQL Database](sql-database-single-database-scale.md) és [rugalmas készlet erőforrásainak méretezése a Azure SQL Databaseban](sql-database-elastic-pool-scale.md). A felügyelt példányok skálázásával kapcsolatos információkért lásd: [szolgáltatási rétegbeli erőforrás-korlátok](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="performance-problems-caused-by-increased-workload-volume"></a>Megnövekedett munkaterhelés-mennyiség által okozott teljesítménnyel kapcsolatos problémák

Az alkalmazások forgalmának növekedése és a számítási feladatok mennyisége nagyobb CPU-használatot eredményezhet. A probléma megfelelő diagnosztizálásához azonban körültekintően kell eljárnia. Ha nagy CPU-problémát lát, válaszoljon ezekre a kérdésekre annak megállapítására, hogy a növekedés a munkaterhelés kötetének változásai miatt következik-e be:

- Az alkalmazás lekérdezései okozzák a nagy CPU-problémát?
- A legfontosabb CPU-fogyasztási lekérdezések esetében:

   - Több végrehajtási terv lett társítva ugyanahhoz a lekérdezéshez? Ha igen, miért?
   - Ugyanazzal a végrehajtási tervvel rendelkező lekérdezések esetében a végrehajtási idők konzisztensek voltak? Növekedett a végrehajtások száma? Ha igen, a munkaterhelés növekedése valószínűleg teljesítménnyel kapcsolatos problémákat okoz.

Összefoglalva, ha a lekérdezés végrehajtási terve nem eltérően lett végrehajtva, de a CPU-használat a végrehajtások számával együtt növekedett, a teljesítménnyel kapcsolatos probléma valószínűleg a munkaterhelés növekedésével lesz összefüggésben.

Nem mindig könnyű megállapítani a CPU-problémát okozó számítási feladatok kötetének változását. Vegye figyelembe a következő tényezőket: 

- **Módosított erőforrás-használat**: Vegyünk például egy olyan forgatókönyvet, amelyben a CPU-használat hosszabb ideig 80%-ra nőtt.  A CPU-használat önmagában nem jelenti azt, hogy a munkaterhelés mennyisége megváltozott. A lekérdezés-végrehajtási terv és az adateloszlás változásainak regressziói is hozzájárulhatnak az erőforrás-használathoz, még akkor is, ha az alkalmazás ugyanazt a munkaterhelést hajtja végre.

- **Új lekérdezés megjelenése**: Egy alkalmazás különböző időpontokban is elvégezheti a lekérdezések új készletét.

- **A kérelmek számának növelése vagy csökkentése**: Ez a forgatókönyv a számítási feladatok legnyilvánvalóbb mérőszáma. A lekérdezések száma nem mindig felel meg az erőforrás-felhasználásnak. Ez a metrika azonban még mindig jelentős jel, feltéve, hogy más tényezők nem változnak.

## <a name="waiting-related-performance-problems"></a>Várakozó teljesítménnyel kapcsolatos problémák 

Ha biztos abban, hogy a teljesítménnyel kapcsolatos probléma nem kapcsolódik a nagy CPU-használathoz vagy a futtatáshoz, a probléma a várakozással kapcsolatos. A CPU-erőforrásokat tehát nem hatékonyan használják, mert a CPU egy másik erőforrásra vár. Ebben az esetben azonosítsa a CPU-erőforrások várakozási körét. 

Ezeket a metódusokat általában a várakozási típusok leggyakoribb kategóriáinak megjelenítésére használják:

- A [lekérdezési tároló](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) használatával megkeresheti az egyes lekérdezések várakozási statisztikáit az idő múlásával. A lekérdezési tárolóban a várakozási típusok a várakozási kategóriákba vannak összevonva. A várakozási kategóriák leképezése a [sys. query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- A [sys. DM _db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) a művelet során végrehajtott szálak által észlelt összes várakozással kapcsolatos információt ad vissza. Ennek az összesített nézetnek a segítségével diagnosztizálhatja a teljesítménnyel kapcsolatos problémákat Azure SQL Database, valamint adott lekérdezésekkel és kötegekkel is.
- A [sys. DM _os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) használatával adatokat adhat vissza az egyes erőforrásokra várakozó feladatok várólistáján.

A nagy CPU-forgatókönyvek esetében előfordulhat, hogy a Query Store és a wait statisztikája nem tükrözi a CPU-használatot, ha:

- A nagy CPU-fogyasztású lekérdezések végrehajtása még folyamatban van.
- A nagy CPU-fogyasztásra felhasználható lekérdezések akkor futnak, amikor feladatátvétel történt.

A lekérdezési tárolót nyomon követő DMV és a várakozási statisztikának csak a sikeresen befejezett és időkorlátú lekérdezések eredményei láthatók. Nem jelenítenek meg adatokat a jelenleg végrehajtás alatt álló utasításokhoz, amíg az utasítások befejeződik. A dinamikus felügyeleti nézet [sys. DM _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) nyomon követheti a jelenleg végrehajtás alatt álló lekérdezéseket és a kapcsolódó munkavégző időt.

A cikk elején található diagram azt mutatja, hogy a leggyakoribb várakozások a következők:

- Zárolások (blokkolás)
- I/O
- TempDB kapcsolatos tartalom
- Memória-engedélyezési várakozások

> [!IMPORTANT]
> A DMV-T használó T-SQL-lekérdezések esetében a várakozással kapcsolatos problémák elhárításához tekintse meg a következő témakört:
>
> - [I/O-teljesítménnyel kapcsolatos problémák azonosítása](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Memória-engedélyezési várakozások azonosítása](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox várakozások és zárak](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improve-database-performance-with-more-resources"></a>Az adatbázis teljesítményének növelése több erőforrással

Ha egyetlen végrehajtható elem sem javíthatja az adatbázis teljesítményét, megváltoztathatja Azure SQL Databaseban elérhető erőforrások mennyiségét. Rendeljen hozzá több erőforrást egy önálló adatbázis [DTU-szolgáltatási rétegének](sql-database-service-tiers-dtu.md) megváltoztatásával. Bármikor megnövelheti a rugalmas készlet Edtu. Ha a [virtuális mag-alapú vásárlási modellt](sql-database-service-tiers-vcore.md)használja, módosítsa a szolgáltatási szintet, vagy növelje az adatbázishoz lefoglalt erőforrásokat.

Önálló adatbázisok esetén az adatbázis teljesítményének növelése érdekében igény szerint [válthat szolgáltatási szinteket vagy számítási erőforrásokat](sql-database-single-database-scale.md) . Több adatbázis esetében érdemes [rugalmas készleteket](sql-database-elastic-pool-guidance.md) használni az erőforrások automatikus méretezéséhez.

## <a name="tune-and-refactor-application-or-database-code"></a>Alkalmazás-vagy adatbázis-kód hangolása és újrabontása

Optimalizálhatja az adatbázishoz tartozó programkódot, módosíthatja az indexeket, kikényszerítheti a terveket, vagy a mutatókat használva manuálisan igazíthatja az adatbázist a munkaterheléshez. A manuális hangolással és a kód újraírásával kapcsolatos információkért lásd: [teljesítmény-finomhangolási útmutató](sql-database-performance-guidance.md).

## <a name="next-steps"></a>További lépések

- Ha engedélyezni szeretné az automatikus hangolást a Azure SQL Databaseban, és lehetővé teszi, hogy az Automatikus hangolási funkció teljes mértékben kezelhesse a számítási feladatokat, tekintse meg az [automatikus](sql-database-automatic-tuning-enable.md)
- A Manuális hangolás használatához tekintse át [a Azure Portal hangolásával kapcsolatos ajánlásokat](sql-database-advisor-portal.md). Manuálisan alkalmazza a lekérdezések teljesítményét javító javaslatokat.
- Módosítsa az adatbázisban elérhető erőforrásokat a [Azure SQL Database szolgáltatási szintjeinek](sql-database-performance-guidance.md)módosításával.
