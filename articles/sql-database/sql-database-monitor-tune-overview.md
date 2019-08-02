---
title: A & Performance tuning figyelése – Azure SQL Database | Microsoft Docs
description: Tippek a teljesítmény finomhangolásához Azure SQL Database a kiértékelés és fejlesztés révén.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: c52b41c4e6d0618b4df9b2aed985bbd22d89f419
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567196"
---
# <a name="monitoring-and-performance-tuning"></a>Monitorozás és teljesítmény-finomhangolás

Azure SQL Database eszközöket és metódusokat biztosít a használat egyszerű figyeléséhez, erőforrások hozzáadásához vagy eltávolításához (processzor, memória, I/O), a lehetséges problémák elhárításához és az adatbázisok teljesítményének javításához szükséges javaslatok megtalálásához. Azure SQL Database számos funkcióval rendelkezik, amelyek automatikusan kijavíthatják az adatbázisokban felmerülő problémákat, és lehetővé teszik az adatbázisok számára a számítási feladatokhoz való alkalmazkodást és a teljesítmény automatikus optimalizálását. Vannak azonban olyan egyéni problémák is, amelyekhez szükség lehet a hibaelhárításra. Ez a cikk a teljesítménnyel kapcsolatos problémák megoldásához használható ajánlott eljárásokat és eszközöket ismerteti.

Két fő tevékenységnek kell megtörténnie, amelyekkel biztosítható, hogy egy adatbázis probléma nélkül fusson:
- Az [adatbázis teljesítményének figyelésével](#monitoring-database-performance) győződjön meg arról, hogy az adatbázishoz rendelt erőforrások képesek kezelni a munkaterhelést. Ha úgy látja, hogy egy adatbázis eléri az erőforrás-korlátokat, az elsődleges erőforrás-felhasználással kapcsolatos lekérdezéseket azonosítani és optimalizálni kell, vagy a szolgáltatási csomag frissítésével több erőforrást is hozzá kell adni.
- A [teljesítménnyel kapcsolatos problémák elhárításával](#troubleshoot-performance-issues) azonosíthatja a lehetséges problémák okát, azonosíthatja a probléma alapvető okát, és végrehajthatja a problémát elhárító műveletet.

## <a name="monitoring-database-performance"></a>Adatbázis teljesítményének figyelése

Egy Azure SQL-adatbázis teljesítményének figyelése az erőforrás-használatnak a kiválasztott adatbázis teljesítményszintjéhez viszonyított figyelésével kezdődik. A következő erőforrásokat kell figyelnie:
 - **CPU-használat** – ellenőrizze, hogy az adatbázis hosszabb időn keresztül eléri-e a CPU-használat 100%-át. Ez arra utalhat, hogy az adatbázist vagy a példányt magasabb szolgáltatási szintre kell frissíteni, vagy a számítási teljesítmény többségét használó lekérdezéseket azonosítani és hangolni kell.
 - **Várakozási statisztikák** – itt megtudhatja, hogy a lekérdezések miért várnak bizonyos erőforrásokra. A lekérdezések megvárhatják, hogy a rendszer beolvassa vagy menti az adatfájlokat az adatbázisfájlokba, és megvárja, hogy elérte az egyes erőforrások korlátját stb.
 - **I/o-használat** – ellenőrizze, hogy az adatbázis eléri-e a mögöttes tároló i/o-korlátait.
 - **Memóriahasználat** – az adatbázishoz vagy példányhoz rendelkezésre álló memória mennyisége arányos a virtuális mag számával, és ellenőrizze, hogy ez elég-e a munkaterhelés számára. A lap várható élettartama azon paraméterek egyike, amelyek azt jelezhetik, hogy a rendszer milyen gyorsan távolítja el a lapokat a memóriából.

A Azure SQL Database szolgáltatás **tartalmazza azokat az eszközöket és erőforrásokat, amelyek segítenek a lehetséges teljesítménnyel kapcsolatos problémák**megoldásában. A [teljesítmény-finomhangolási javaslatok](sql-database-advisor.md)áttekintésével a lehetőségek könnyen azonosíthatók a lekérdezési teljesítmény javításához és optimalizálásához anélkül, hogy az erőforrásokat módosítaná. A hiányzó indexek és rosszul optimalizált lekérdezések az adatbázis gyenge teljesítményének gyakori okai. Ezek a hangolási javaslatok a munkaterhelés teljesítményének növelésére alkalmazhatók. Az Azure SQL Database lehetővé teszi [a lekérdezések teljesítményének automatikus optimalizálását](sql-database-automatic-tuning.md) az összes azonosított javaslat alkalmazásával és annak ellenőrzésével, hogy ez javítja az adatbázis teljesítményét.

Az adatbázis teljesítményének figyeléséhez és hibaelhárításához a következő lehetőségek állnak rendelkezésre:

- A [Azure Portal](https://portal.azure.com)kattintson az **SQL-adatbázisok**elemre, jelölje ki az adatbázist, majd a figyelési diagram használatával keresse meg a maximális kihasználtságot megközelítő erőforrásokat. A DTU-felhasználás alapértelmezés szerint megjelenik. Kattintson a **Szerkesztés** gombra a megjelenített időtartomány és értékek módosításához.
- Az olyan eszközök, mint a SQL Server Management Studio számos hasznos jelentést biztosítanak, például egy [teljesítmény](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard?view=sql-server-2017) -irányítópultot az erőforrások kihasználtságának figyeléséhez, valamint a legnépszerűbb erőforrás-felhasználási lekérdezések és a [lekérdezési tárolók](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed) azonosításához a romlott teljesítmény.
- A [Azure Portal](https://portal.azure.com) [lekérdezési terheléselemző](sql-database-query-performance.md) használatával azonosíthatja azokat a lekérdezéseket, amelyek a legtöbb erőforrást töltik. Ez a funkció csak önálló adatbázis és rugalmas készletekben érhető el.
- A [SQL Database Advisor](sql-database-advisor-portal.md) használatával megtekintheti az indexek létrehozásához és eldobásához, a lekérdezések parameterizing és a séma-problémák kijavításához szükséges javaslatokat. Ez a funkció csak önálló adatbázis és rugalmas készletekben érhető el.
- Az [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) az adatbázis teljesítményének automatikus figyelésére használható. A teljesítményproblémák észlelése után a rendszer diagnosztikai naplót hoz létre a probléma részleteivel és a kiváltó okok elemzésével (RCA). Ha lehetséges, a teljesítmény javítására vonatkozó javaslat van megadva.
- [Engedélyezze az automatikus hangolást](sql-database-automatic-tuning-enable.md) , és hagyja, hogy az Azure SQL Database automatikusan javítsa az azonosított teljesítménnyel kapcsolatos problémákat.
- A teljesítménnyel kapcsolatos problémák részletesebb hibaelhárításához használja a [dinamikus felügyeleti nézeteket (DMV)](sql-database-monitoring-with-dmvs.md), a [kiterjesztett eseményeket](sql-database-xevent-db-diff-from-svr.md)és a [lekérdezési tárolót](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) .

> [!TIP]
> A [](sql-database-performance-guidance.md) fenti módszerek közül egy vagy több használatával javíthatja a Azure SQL Database teljesítményének növeléséhez használható technikákat.

## <a name="troubleshoot-performance-issues"></a>Teljesítményproblémák elhárítása

A teljesítménnyel kapcsolatos problémák diagnosztizálásához és megoldásához kezdje az egyes aktív lekérdezések állapotát, valamint azokat a feltételeket, amelyek az egyes számítási feladatok állapotához kapcsolódó teljesítménnyel kapcsolatos problémákat okoznak. A Azure SQL Database teljesítményének javítása érdekében Ismerje meg, hogy az alkalmazás minden aktív lekérdezési kérelme fut vagy várakozási állapotban van. Azure SQL Database teljesítményével kapcsolatos hibák elhárításakor tartsa szem előtt a következő diagramot, ahogy olvasta a problémát a teljesítménnyel kapcsolatos problémák diagnosztizálásához és megoldásához.

![Munkaterhelési állapotok](./media/sql-database-monitor-tune-overview/workload-states.png)

A teljesítménnyel kapcsolatos problémákkal rendelkező munkaterhelések esetén a teljesítménnyel kapcsolatos probléma oka lehet a CPU-tartalom (egy futó szolgáltatáshoz **kapcsolódó** feltétel), vagy az egyes lekérdezések a vártnál (a **várakozással kapcsolatos** feltételnél) várnak.

A futtatással **kapcsolatos** problémák oka a következő lehet:
- **Fordítási problémák** – az SQL-lekérdezés-optimalizáló az elavult statisztikák miatt elősegítheti a legoptimálisabb tervet, a feldolgozandó sorok számának vagy a szükséges memória becslésének helytelen becslését. Ha tudjuk, hogy a lekérdezés a múltban vagy más példányon (felügyelt példány vagy SQL Server példány) gyorsabban lett végrehajtva, hajtsa végre a tényleges végrehajtási terveket, és hasonlítsa össze őket, hogy azok eltérőek legyenek. Próbáljon meg lekérdezési tippeket alkalmazni, vagy újjáépíteni a statisztikákat, vagy hozza létre az indexeket, hogy minél jobban megtervezze a tervet. A problémák automatikus mérsékléséhez engedélyezze a Azure SQL Database automatikus tervének javítását.
- **Végrehajtási problémák** – ha a lekérdezési terv optimális, valószínűleg az adatbázis egyes erőforrás-korlátozásait, például a napló írási átviteli sebességét, vagy a detöredezett indexeket használ, amelyeket újra kell építeni. A végrehajtással kapcsolatos problémák nagy számú egyidejű lekérdezéssel is járhatnak. A legtöbb esetben a **várakozással kapcsolatos** problémák a végrehajtással kapcsolatos problémákhoz kapcsolódnak, mert a nem hatékonyan végrehajtó lekérdezések valószínűleg bizonyos erőforrásokra várnak.

A várakozással **kapcsolatos** problémák oka a következő lehet:
- **Blokkolás** – az egyik lekérdezés a zárolást az adatbázisban lévő egyes objektumokon tárolhatja, míg mások ugyanahhoz az objektumhoz próbálnak hozzáférni. A blokkoló lekérdezések könnyen azonosíthatók a DMV vagy a monitorozási eszközök használatával.
- **IO-problémák** – előfordulhat, hogy a lekérdezések a lapoknak az adatvagy a naplófájlba való írására várnak. Ebben az esetben lásd `INSTANCE_LOG_RATE_GOVERNOR` `WRITE_LOG`, vagy `PAGEIOLATCH_*` várjon statisztikát a DMV-ben.
- **Tempdb kapcsolatos problémák** – ha a munkaterhelés sok ideiglenes táblázatot használ, vagy sok tempdb-kifolyás van a tervekben, lehetséges, hogy a lekérdezések tempdb átviteli sebességgel rendelkeznek. 
- **Memóriával kapcsolatos problémák** – előfordulhat, hogy a munkaterheléshez nincs elég memória, így az oldal várható élettartama csökkenhet, vagy a lekérdezések a szükségesnél kevesebb memóriát kapnak. Bizonyos esetekben a lekérdezés-optimalizáló beépített intelligenciával elháríthatja ezeket a problémákat.
 
 A következő szakaszokban ismertetjük a problémák némelyikének azonosítását és elhárítását.

## <a name="running-related-performance-issues"></a>Futó teljesítménnyel kapcsolatos problémák

Általános útmutatóként, ha a CPU-kihasználtság a 80%-os vagy annál újabb értéknél konzisztens, akkor egy futó teljesítménnyel kapcsolatos probléma merül fel. Ha fut kapcsolatos probléma merül fel, előfordulhat, hogy nem áll rendelkezésre elegendő CPU-erőforrás, vagy a következő feltételek valamelyikével lehet kapcsolatban:

- Túl sok futó lekérdezés
- Túl sok lekérdezés fordítása
- Egy vagy több végrehajtási lekérdezés egy alhálózattal optimális lekérdezési tervet használ

Ha megállapítható, hogy egy futó teljesítménnyel kapcsolatos probléma merül fel, a cél a pontos probléma azonosítása egy vagy több módszer használatával. A futtatással kapcsolatos problémák azonosításának leggyakoribb módszerei a következők:

- Használja a [Azure Portal](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) a CPU százalékos kihasználtságának figyeléséhez.
- Használja a következő [dinamikus felügyeleti nézeteket](sql-database-monitoring-with-dmvs.md):

  - a [sys. DM _db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) egy Azure SQL Database CPU-, I/O-és memória-felhasználását adja vissza. 15 másodpercenként egy sor létezik, még akkor is, ha nincs tevékenység az adatbázisban. A korábbi adatait egy óráig őrzi meg a rendszer.
  - a [sys. resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) egy Azure SQL Database CPU-használati és tárolási adatait adja vissza. Az adatok gyűjtése és összesítése öt percen belül történik.

> [!IMPORTANT]
> A CPU-kihasználtsággal kapcsolatos problémák megoldásához a következő DMV használó T-SQL-lekérdezések esetében tekintse meg a [CPU-teljesítménnyel kapcsolatos problémák azonosítása](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues)című témakört.

### <a name="ParamSniffing"></a>A paraméterekkel kapcsolatos bizalmas lekérdezés-végrehajtási tervtel kapcsolatos problémák elhárítása a lekérdezésekkel

A megkülönböztető csomag (PSP) paraméter olyan forgatókönyvre hivatkozik, amelyben a lekérdezés-optimalizáló olyan lekérdezés-végrehajtási tervet hoz létre, amely csak az adott paraméterérték (vagy értékek halmaza) esetében optimális, és a gyorsítótárazott terv ezután nem optimális a következőben használt paraméterek értékeinél: egymást követő végrehajtások. A nem optimális csomagok ezután lekérdezik a teljesítménnyel kapcsolatos problémákat, és elérhetik a munkaterhelés teljesítményének csökkenését. További információ a paraméterek elemzéséről és a lekérdezések feldolgozásáról: a [lekérdezés-feldolgozási architektúra útmutatója](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

A problémák enyhítésére számos Áthidaló megoldás van használatban, amelyek mindegyike a kapcsolódó kompromisszumokkal és hátrányokkal jár:

- A lekérdezés [](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) végrehajtásához használja az újrafordítási lekérdezési célzást. Ez a megkerülő megoldás a minőség javítása érdekében a fordítási időt és a CPU-t növeli. A `RECOMPILE` kapcsoló használata gyakran nem lehetséges a nagy átviteli sebességet igénylő munkaterhelések esetében.
- Használja a [(z) (optimalizálás a...)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) lekérdezési mutatót a tényleges paraméter értékének felülbírálására egy jellemző paraméter értékkel, amely elég jó tervet hoz létre a legtöbb paraméter-érték lehetőséghez.   Ehhez a beállításhoz jól ismerni kell az optimális paramétereket és a társított terv jellemzőit.
- A [(z) (ismeretlenre optimalizált)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) lekérdezési mutató használatával felülbírálhatja az Exchange tényleges paraméter értékét a sűrűségi vektor átlagának használatával. Ennek egy másik módja a bejövő paraméterek értékeinek helyi változókba való rögzítése, majd a predikátumokon belüli helyi változók használata a paraméterek használata helyett. Az átlagos sűrűségnek *megfelelőnek* kell lennie ehhez az adott javításhoz.
- Tiltsa le a paraméterek teljes körű elemzését a [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) -lekérdezési mutató használatával.
- A [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) lekérdezési célzásával megakadályozhatja az újrafordítást a gyorsítótárban. Ez a megkerülő megoldás feltételezi, hogy a gyorsítótárban már szerepel a *megfelelő* közös csomag. Emellett letilthatja a statisztikák automatikus frissítéseit is, hogy csökkentse a jó terv kizárásának esélyét, és egy új, rossz terv fordítását.
- A terv kikényszerítéséhez explicit módon [használja a használati terv](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) lekérdezési mutatóját (explicit módon megadásával, egy adott csomag beállításával a lekérdezési tároló használatával vagy az [automatikus hangolás](sql-database-automatic-tuning.md)engedélyezésével).
- Cserélje le az egyetlen eljárást egy beágyazott eljárásokra, amelyek mindegyike felhasználható a feltételes logika és a hozzá tartozó paraméterek értékei alapján.
- Dinamikus karakterlánc-végrehajtási alternatívák létrehozása statikus eljárás definíciójában.

Az ilyen típusú problémák megoldásával kapcsolatos további információkért lásd:

- Ezt [érzek egy paraméter](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/) blogbejegyzést
- A [dinamikus SQL és a séma minősége a paraméteres lekérdezésekhez](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/) blogbejegyzés
- Az [SQL-lekérdezés optimalizálási módszerei a SQL Serverban: Paraméter-elemzési](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/) blogbejegyzés

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>Hibák elhárítása a helytelen paraméterezés miatt

Ha egy lekérdezés literálokat tartalmaz, vagy az adatbázismotor úgy dönt, hogy automatikusan parametrizálja az utasítást, vagy a felhasználó explicit módon parametrizálja azt a fordítások számának csökkentése érdekében. Egy lekérdezés nagy számú fordítása ugyanazzal a mintázattal, de a különböző literális értékek magas CPU-kihasználtságot eredményezhetnek. Hasonlóképpen, ha csak részben parametrizálja egy olyan lekérdezést, amely továbbra is literálokkal rendelkezik, az adatbázismotor még nem parametrizálja.  Az alábbi példa egy részlegesen paraméteres lekérdezést mutat be:

```sql
SELECT * FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

Az előző példában `t1.c1` `@p1` a továbbra is a GUID-ot használja literálként. `t2.c2` Ebben az esetben, ha módosítja a értékét `c2`, a lekérdezés más lekérdezésként lesz kezelve, és új fordítás fog történni. Az előző példában szereplő fordítások csökkentése érdekében a megoldás a GUID parametrizálja is.

Az alábbi lekérdezés a lekérdezési kivonatok száma alapján megállapítja, hogy a lekérdezés megfelelően van-e konfigurálva, vagy sem:

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
### <a name="factors-influencing-query-plan-changes"></a>A lekérdezési terv módosításait befolyásoló tényezők

Egy lekérdezés-végrehajtási terv újrafordítása olyan generált lekérdezési tervet eredményezhet, amely eltér az eredetileg gyorsítótárazott adatoktól. Számos oka lehet annak, hogy egy meglévő eredeti csomag automatikusan újrafordításra kerül:
- A lekérdezés által hivatkozott séma módosításai
- A lekérdezés által hivatkozott táblák adatváltozásai 
- A lekérdezési környezet beállításainak módosításai 

Egy lefordított terv számos okból kiválasztható a gyorsítótárból, beleértve a példányok újraindítását, az adatbázis-hatókörű konfiguráció módosításait, a memória terhelését és az explicit kérelmeket a gyorsítótár kiürítéséhez. Emellett az újrafordítási mutató használata azt is jelenti, hogy a csomag nem lesz gyorsítótárazva.

Egy újrafordítás (vagy a gyorsítótár kiürítése utáni friss fordítás) továbbra is egy azonos lekérdezés-végrehajtási terv generációját eredményezheti az eredetileg megfigyelt adatokból.  Ha azonban a terv a korábbi vagy az eredeti csomaggal összehasonlítva módosul, a következő leggyakoribb magyarázat a lekérdezés-végrehajtási terv módosításának okáról:

- **Módosult a fizikai kialakítás**. Például új indexek jöttek létre, amelyek hatékonyabban fedik le a lekérdezés követelményeit. Ezek az új fordításban is használhatók, ha a lekérdezés-optimalizáló úgy dönt, hogy az új indexet használja, mint amit a lekérdezés-végrehajtás első verziójának eredetileg kiválasztott adatstruktúrájának használatakor használ.  A hivatkozott objektumok fizikai változásai a fordítási idő új tervének megválasztásához vezethetnek.

- **Kiszolgálói erőforrás**-eltérések. Abban az esetben, ha az egyik csomag eltér a "A" rendszertől és a "B rendszertől" – az erőforrások rendelkezésre állása, például a rendelkezésre álló processzorok száma, befolyásolhatja, hogy melyik tervet hozza létre.  Ha például egy rendszer nagyobb számú processzorral rendelkezik, lehetséges, hogy egy párhuzamos tervet választ ki. 

- **Különböző statisztikák**. A hivatkozott objektumokhoz tartozó statisztikák módosultak, vagy lényegesen eltérnek az eredeti rendszer statisztikáinak.  Ha a statisztikai változás és az újrafordítás történik, a lekérdezés-optimalizáló az adott időpontra vonatkozó statisztikai adatokat fogja használni. A felülvizsgált statisztikák jelentős mértékben eltérő adateloszlásokkal és gyakorisággal rendelkezhetnek, amelyek nem az eredeti fordításban szerepelnek.  Ezek a változások a kardinális becslések becslésére szolgálnak (a logikai lekérdezési fában átáramló sorok száma).  A kardinális becslések változásai a különböző fizikai operátorok és a hozzájuk tartozó műveletek kiválasztására vezethetnek.  A statisztikában még kisebb változások is megváltoztathatják a lekérdezés végrehajtási tervét.

- **Módosult az adatbázis-kompatibilitási szint vagy a Cardinals kalkulátor verziója**.  Az adatbázis-kompatibilitási szint változásai olyan új stratégiákat és szolgáltatásokat is tartalmazhatnak, amelyek eltérő lekérdezés-végrehajtási tervet eredményezhetnek.  Az adatbázis-kompatibilitási szinten túl a 4199 nyomkövetési jelző letiltása vagy engedélyezése, illetve az adatbázis-hatókörű konfiguráció QUERY_OPTIMIZER_HOTFIXES befolyásolhatja a lekérdezés végrehajtási tervének választásait is a fordítás ideje alatt.  A nyomkövetési jelzők a 9481 (örökölt régi CE) és a 2312 (az alapértelmezett CE kényszerített) megtervezésére is hatással vannak. 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Probléma-lekérdezések feloldása vagy további erőforrások biztosítása

A probléma azonosítása után beállíthatja a problémák lekérdezését, vagy frissítheti a számítási méretet vagy a szolgáltatási szintet, hogy növelje az Azure SQL Database kapacitását a CPU-követelmények befogadásához. Az önálló adatbázisok erőforrásainak méretezésével kapcsolatos információkért lásd: az [önálló adatbázis-erőforrások](sql-database-single-database-scale.md) méretezése Azure SQL Database és a rugalmas készletek erőforrásainak méretezése: [rugalmas készlet erőforrásainak méretezése a Azure SQL Databaseban](sql-database-elastic-pool-scale.md). A felügyelt példányok skálázásával kapcsolatos információkért lásd: [példány szintű erőforrás-korlátok](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).

### <a name="determine-if-running-issues-due-to-increase-workload-volume"></a>A számítási feladatok mennyiségének növelését okozó problémák meghatározása

Az alkalmazások forgalmának és munkaterhelésének növekedésével növelheti a CPU-kihasználtságot, de a probléma megfelelő diagnosztizálásához körültekintően kell eljárnia. Nagy CPU-forgatókönyv esetén válaszoljon ezekre a kérdésekre annak megállapítására, hogy valóban a CPU-növekedés a munkaterhelés-kötetek változásai miatt következik-e be:

1. Az alkalmazás lekérdezései okozzák a nagy CPU-problémát?
2. A legfontosabb CPU-fogyasztási lekérdezésekhez (amelyek azonosíthatók):

   - Állapítsa meg, hogy van-e több végrehajtási terv társítva ugyanahhoz a lekérdezéshez. Ha igen, állapítsa meg, hogy miért.
   - Ugyanazzal a végrehajtási tervvel rendelkező lekérdezések esetén állapítsa meg, hogy a végrehajtási idők konzisztensek-e, és hogy a végrehajtások száma megnőtt-e. Ha igen, a munkaterhelés növekedése miatt valószínűleg teljesítményproblémák merülhetnek fel.

Ha azt szeretné összefoglalni, hogy a lekérdezés végrehajtási terve nem máshogy lett végrehajtva, de a CPU-kihasználtság a végrehajtások számával együtt növekedett, akkor valószínűleg a munkaterhelés-növeléssel kapcsolatos teljesítmény probléma merül fel.

Nem mindig könnyű megállapítani, hogy van-e számítási munkaterhelés-változás, amely egy CPU-problémát vezet.   Megfontolandó tényezők: 

- **Az erőforrás-használat megváltozott**

  Vegyünk például egy olyan forgatókönyvet, amelyben a CPU hosszabb időn keresztül 80%-ra emelkedett.  A CPU-használat önmagában nem jelenti azt, hogy a munkaterhelés mennyisége megváltozott.  A lekérdezés végrehajtási tervének regressziói és adateloszlási módosításai szintén hozzájárulhatnak az erőforrás-használathoz, még akkor is, ha az alkalmazás ugyanazokat a számítási feladatokat hajtja végre.

- **Új lekérdezés jelent meg**

   Egy alkalmazás különböző időpontokban hozhat létre új lekérdezéseket.

- **Megnövekedett vagy csökkenő kérelmek száma**

   Ez a forgatókönyv a számítási feladatok legnyilvánvalóbb mérőszáma. A lekérdezések száma nem mindig felel meg az erőforrás-felhasználásnak. Ez a metrika azonban még mindig jelentős jel, feltéve, hogy más tényezők nem változnak.

## <a name="waiting-related-performance-issues"></a>Várakozással kapcsolatos teljesítménnyel kapcsolatos problémák

Ha biztos benne, hogy nem a nagy CPU-hoz kapcsolódó teljesítménybeli teljesítménnyel kapcsolatos problémát tapasztal, a várakozással kapcsolatos teljesítménnyel kapcsolatos probléma merül fel. A CPU-erőforrásokat tehát nem hatékonyan használják, mert a CPU egy másik erőforrásra vár. Ebben az esetben a következő lépés a CPU-erőforrások várakozásának megállapítása. A legáltalánosabb módszerek a legnagyobb várakozási típusok kategóriáinak megjelenítéséhez:

- A [lekérdezési tároló](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) a lekérdezési várakozási idő függvényében időt biztosít. A lekérdezési tárolóban a várakozási típusok a várakozási kategóriákba vannak összevonva. A várakozási kategóriák és a várakozási típusok hozzárendelésének elérhetőnek kell lennie a [sys. query_store_wait_stats fájlban](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- a [sys. DM _db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) a művelet során végrehajtott szálak által észlelt összes várakozással kapcsolatos információkat adja vissza. Ennek az összesített nézetnek a segítségével diagnosztizálhatja a teljesítménnyel kapcsolatos problémákat Azure SQL Database, valamint adott lekérdezésekkel és kötegekkel is.
- a [sys. DM _os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) az egyes erőforrásokra várakozó feladatok várakozási sorával kapcsolatos információkat adja vissza.

A nagy CPU-helyzetekben a lekérdezési tároló és a várakozási statisztika nem mindig tükrözi a CPU-kihasználtságot a következő két okból:

- Előfordulhat, hogy a nagy CPU-fogyasztást igénylő lekérdezések végrehajtása továbbra is végrehajtható, és a lekérdezések nem fejeződött be
- Feladatátvétel bekövetkeztekor a nagy CPU-fogyasztást igénylő lekérdezések futnak

Lekérdezés-és várakozási statisztikák – a dinamikus felügyeleti nézetek nyomon követése csak a sikeresen befejezett és időtúllépéssel rendelkező lekérdezések eredményét jeleníti meg, és nem jeleníti meg az aktuálisan végrehajtandó utasítások adatait (amíg nem fejeződik be). A dinamikus felügyeleti nézet [sys. DM _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) lehetővé teszi a jelenleg végrehajtás alatt álló lekérdezések és a hozzájuk tartozó munkavégző idő nyomon követését.

Ahogy az előző ábrán is látható, a leggyakoribb várakozások a következők:

- Zárolások (blokkolás)
- I/O
- `tempdb`kapcsolódó tartalom
- Memória-engedélyezési várakozások

> [!IMPORTANT]
> A várakozással kapcsolatos problémák elhárításához az alábbi DMV használó T-SQL-lekérdezéseket itt tekintheti meg:
>
> - [I/O-teljesítménnyel kapcsolatos problémák azonosítása](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Teljesítménnyel `tempdb` kapcsolatos problémák azonosítása](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Memória-engedélyezési várakozások azonosítása](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox – várakozások és zárak](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox - usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improving-database-performance-with-more-resources"></a>Az adatbázis teljesítményének növelése további erőforrásokkal

Végül, ha nincs olyan végrehajtható elem, amely javíthatja az adatbázis teljesítményét, megváltoztathatja a Azure SQL Databaseban rendelkezésre álló erőforrások mennyiségét. Több erőforrást is hozzárendelhet úgy, hogy megváltoztatja egy önálló adatbázis [DTU-szolgáltatási szintjét](sql-database-service-tiers-dtu.md) , vagy bármikor megnövelheti egy rugalmas készlet edtu. Ha a [virtuális mag-alapú vásárlási modellt](sql-database-service-tiers-vcore.md)használja, módosíthatja a szolgáltatási szintet, vagy növelheti az adatbázishoz lefoglalt erőforrásokat.

1. Önálló adatbázisok esetén igény szerint [módosíthatja a szolgáltatási szinteket](sql-database-single-database-scale.md) vagy a [számítási erőforrásokat](sql-database-single-database-scale.md) az adatbázis teljesítményének növelése érdekében.
2. Több adatbázis esetében érdemes [rugalmas készleteket](sql-database-elastic-pool-guidance.md) használni az erőforrások automatikus méretezéséhez.

## <a name="tune-and-refactor-application-or-database-code"></a>Alkalmazás-vagy adatbázis-kód hangolása és újrabontása

Az alkalmazás kódja módosítható az adatbázis optimális kihasználásához, az indexek módosításához, a kikényszerített tervekhez, vagy a célzások használatával manuálisan is igazíthatja az adatbázist a munkaterheléshez. Útmutatást és tippeket talál a manuális hangoláshoz és a kód átírásához a [teljesítmény-útmutató témakörben](sql-database-performance-guidance.md) .

## <a name="next-steps"></a>További lépések

- Ha engedélyezni szeretné az automatikus hangolást a Azure SQL Databaseban, és lehetővé teszi, hogy az Automatikus hangolási funkció teljes mértékben kezelhesse a számítási feladatokat, olvassa el az [automatikus hangolás](sql-database-automatic-tuning-enable.md)
- A Manuális hangolás használatához tekintse át [Azure Portal hangolási javaslatait](sql-database-advisor-portal.md) , és manuálisan alkalmazza azokat, amelyek javítják a lekérdezések teljesítményét.
- Az adatbázisban elérhető erőforrások módosítása a [Azure SQL Database szolgáltatási szintjeinek](sql-database-performance-guidance.md) módosításával
