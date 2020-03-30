---
title: Lekérdezési teljesítményproblémák típusai az Azure SQL Database-ben
description: Ebből a cikkből megismerheti az Azure SQL Database lekérdezési teljesítményproblémáinak típusait, és megtudhatja, hogyan azonosíthatja és oldhatja meg a lekérdezéseket ezekkel a problémákkal
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: e155321c2727bf4ee871ef7be7b61b6a523ec1fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256132"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Az Azure SQL Database lekérdezési teljesítménybeli szűk keresztmetszeteinek észlelhető típusai

Teljesítményszűk keresztmetszet ének feloldásakor először határozza meg, hogy a szűk keresztmetszet akkor fordul-e elő, amikor a lekérdezés futó vagy várakozó állapotban van. Ettől a meghatározástól függően különböző felbontások alkalmazandók. Az alábbi ábra segítségével megismerheti azokat a tényezőket, amelyek futással kapcsolatos vagy várakozással kapcsolatos problémát okozhatnak. Az egyes problématípusokkal kapcsolatos problémákat és megoldásokat ebben a cikkben tárgyaljuk.

Az Azure SQL Database [Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns) vagy az SQL Server [DMV-k](sql-database-monitoring-with-dmvs.md) segítségével észlelheti az ilyen típusú teljesítménybeli szűk keresztmetszeteket.

![Számítási feladatok állapotai](./media/sql-database-monitor-tune-overview/workload-states.png)

**Futással kapcsolatos problémák:** A futással kapcsolatos problémák általában az összeállítási problémákhoz kapcsolódnak, amelyek az optimálistól elmaradó lekérdezési tervvagy végrehajtási problémákat eredményeznek az elégtelen vagy túlhasznált erőforrásokhoz kapcsolódóan.
**Várakozással kapcsolatos problémák**: A várakozással kapcsolatos problémák általában a következőkhöz kapcsolódnak:

- Zárolások (blokkolás)
- I/o
- A TempDB használatával kapcsolatos versengés
- Memóriatámogatás várakozásai

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>Fordítási problémák, amelyek az optimálistól elmaradó lekérdezési tervet eredményeznek

Az SQL Query Optimizer által létrehozott, optimálistól elmaradó terv lehet a lassú lekérdezési teljesítmény oka. Előfordulhat, hogy az SQL-lekérdezés-optimalizáló egy hiányzó index, elavult statisztika, a feldolgozandó sorok számának helytelen becslése vagy a szükséges memória pontatlan becslése miatt nem optimális abbéli tervet hoz létre. Ha tudja, hogy a lekérdezés végrehajtása korábban vagy egy másik példányban (felügyelt példány vagy SQL Server-példány) történt, hasonlítsa össze a tényleges végrehajtási terveket, és nézze meg, hogy különböznek-e.

- Azonosítsa a hiányzó indexeket az alábbi módszerek egyikével:

  - Használja [az Intelligens elemzési](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index)adatokat.
  - [Adatbázis-tanácsadó](sql-database-advisor.md) egy- és összevont adatbázisokhoz.
  - DMV-k. Ez a példa bemutatja a hiányzó index hatását, hogyan észlelheti a [hiányzó indexeket](sql-database-performance-guidance.md#identifying-and-adding-missing-indexes) a DMV-k használatával, és a hiányzó indexajánlás megvalósításának hatását.
- Próbálja meg alkalmazni [a lekérdezési tippeket,](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) [frissítse a statisztikákat,](https://docs.microsoft.com/sql/t-sql/statements/update-statistics-transact-sql)vagy építse újra az [indexeket](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes) a jobb terv érdekében. Engedélyezze [az automatikus tervjavítást](sql-database-automatic-tuning.md) az Azure SQL Database-ben a problémák automatikus csökkentéséhez.

  Ez a [példa](sql-database-performance-guidance.md#query-tuning-and-hinting) egy paraméterezett lekérdezés miatt az optimálistól elmaradó lekérdezési terv hatását mutatja be, hogyan észlelhető ez a feltétel, és hogyan használható a lekérdezési emlékeztető feloldása.

- Próbálja meg módosítani az adatbázis kompatibilitási szintjét, és intelligens lekérdezésfeldolgozást valósítson meg. Az SQL Query Optimizer az adatbázis kompatibilitási szintjétől függően eltérő lekérdezési tervet hozhat létre. A magasabb kompatibilitási szintek [intelligensebb lekérdezésfeldolgozási lehetőségeket biztosítanak.](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing)

  - A lekérdezésfeldolgozásról további információt a [Lekérdezésfeldolgozási architektúra útmutatójában talál.](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide)
  - Az adatbázis-kompatibilitási szintek módosításáról és a kompatibilitási szintek közötti különbségekről az [ALTER DATABASE című témakörben](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)olvashat bővebben.
  - Ha többet szeretne megtudni a számosság becsléséről, olvassa el [a Számosság becslése](https://docs.microsoft.com/sql/relational-databases/performance/cardinality-estimation-sql-server)

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>Lekérdezések feloldása az optimálistól elmaradó lekérdezésvégrehajtási tervekkel

A következő szakaszok ismertetik, hogyan oldhatja fel a lekérdezéseket az optimálistól elmaradó lekérdezés-végrehajtási tervvel.

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a>Paraméterek-érzékeny tervvel (PSP) kapcsolatos problémákkal kapcsolatos lekérdezések

Paraméterfüggő terv (PSP) probléma akkor fordul elő, ha a lekérdezés-optimalizáló olyan lekérdezés-végrehajtási tervet hoz létre, amely csak egy adott paraméterértékhez (vagy értékhalmazhoz) optimális, és a gyorsítótárazott terv nem optimális az egymást követő paraméterek ben használt paraméterértékekhez Kivégzések. A nem optimális sémák ezután lekérdezési teljesítményproblémákat okozhatnak, és csökkenthetik a teljes számítási terhelés imát.

A paraméterek szippantásáról és a lekérdezésfeldolgozásról a [Lekérdezésfeldolgozás architektúra útmutatójában talál](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)további információt.

Számos kerülő megoldás enyhítheti a PSP-problémákat. Minden megoldáshoz kapcsolódó kompromisszumok és hátrányok tartoznak:

- Használja a [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) lekérdezési tippet minden lekérdezés végrehajtásakor. Ez a megoldás a fordítási időt és a nagyobb CPU-t biztosítja a jobb tervminőség érdekében. A `RECOMPILE` beállítás gyakran nem lehetséges a nagy átviteli sebességű számítási feladatok esetében.
- Az [OPTION (OPTIMIZE FOR...)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) lekérdezési emlékeztetővel felülbírálhatja a tényleges paraméterértékét egy tipikus paraméterértékkel, amely olyan tervet hoz létre, amely elég jó a legtöbb paraméterérték-lehetőséghez. Ehhez a beállításhoz az optimális paraméterértékek és a kapcsolódó tervjellemzők megfelelő megértésére van szükség.
- Az [OPTION (OPTIMIZE FOR UNKNOWN)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) lekérdezési emlékeztetővel felülbírálhatja a tényleges paraméterértékét, és helyette a sűrűségvektor átlagát használhatja. Ezt úgy is megteheti, hogy rögzíti a bejövő paraméterértékeket a helyi változókban, majd a helyi változókat használja a predikátumokban a paraméterek használata helyett. Ehhez a javításhoz az átlagos sűrűségnek *elég jónak*kell lennie .
- A paraméterszaglás letiltása teljes mértékben a [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) lekérdezési tipp használatával.
- A [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) lekérdezési tipp segítségével megakadályozhatja az újrafordítást a gyorsítótárban. Ez a megoldás feltételezi, hogy a megfelelő közös terv már a gyorsítótárban van. Az automatikus statisztikai frissítéseket is letilthatja, így csökkentheti annak az esélyét, hogy a jó tervet kilakoltatják, és egy új rossz tervet állítanak össze.
- A terv kényszerítése a [USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) lekérdezési emlékeztető jének explicit használatával a lekérdezés újraírásával és a tipp nek a lekérdezés szövegében való hozzáadásával. Vagy állítson be egy adott tervet a Query Store használatával vagy az [automatikus hangolás](sql-database-automatic-tuning.md)engedélyezésével.
- Cserélje le az egyetlen eljárást egy beágyazott eljárásokra, amelyek mindegyike használható feltételes logika és a kapcsolódó paraméterértékek alapján.
- Hozzon létre dinamikus karakterlánc-végrehajtási alternatívákat egy statikus eljárásdefinícióhoz.

A PSP-problémák megoldásáról az alábbi blogbejegyzésekben talál további információt:

- [Egy paramétert érzek.](https://docs.microsoft.com/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Conor és dinamikus SQL vs. eljárások vs. terv minősége paraméteres lekérdezések](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [SQL-lekérdezésoptimalizálási technikák az SQL Server rendszerben: Paraméterszaglás](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Nem megfelelő paraméterezés okozta fordítási tevékenység

Ha egy lekérdezéskonstansok vannak, az adatbázismotor automatikusan paraméterezi az utasítást, vagy a felhasználó kifejezetten paraméterezi az utasítást a fordítások számának csökkentése érdekében. Az azonos mintát, de különböző literálértékeket használó lekérdezések nagy számú összeállítása magas CPU-használatot eredményezhet. Hasonlóképpen, ha csak részben paraméterez i egy olyan lekérdezést, amely nek továbbra is vannak konstansai, az adatbázismotor nem paraméterezi tovább a lekérdezést.

Íme egy példa egy részben paraméterezett lekérdezésre:

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

Ebben a `t1.c1` példában a , `@p1`de `t2.c2` továbbra is a GUID-ot veszi szó szerintiként. Ebben az esetben, ha módosítja a `c2`értékét, a lekérdezés tegy másik lekérdezésként kezeli, és egy új fordítás történik. A példában szereplő fordítások csökkentéséhez a GUID-t is paraméterezheti.

A következő lekérdezés a lekérdezési kivonatonkénti lekérdezések számát jeleníti meg annak megállapításához, hogy a lekérdezés megfelelően paraméterezett-e:

```sql
SELECT TOP 10
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

### <a name="factors-that-affect-query-plan-changes"></a>A lekérdezési terv változásait befolyásoló tényezők

A lekérdezésvégrehajtási terv újrafordítása az eredeti gyorsítótárazott tervtől eltérő létrehozott lekérdezési tervet eredményezhet. Egy meglévő eredeti csomagot különböző okok miatt automatikusan újra kell fordítani:

- A séma változásaira a lekérdezés hivatkozik
- A táblák adatváltozásaira a lekérdezés hivatkozik
- A lekérdezési környezet beállításai megváltoztak

A lefordított terv különböző okok miatt kerülhet ki a gyorsítótárból, például:

- A példány újraindul
- Adatbázis-hatókörrel végzett konfigurációmódosításai
- Memóriaterhelés
- Explicit kérelmek a gyorsítótár törlésére

Ha recompile sugok, a terv nem lesz gyorsítótárazva.

Az újrafordítás (vagy a gyorsítótár-kilakoltatás utáni friss összeállítás) továbbra is eredményezheti az eredetivel azonos lekérdezés-végrehajtási terv létrehozását. Amikor a terv megváltozik az előző vagy az eredeti tervhez képest, ezek a magyarázatok valószínűse:

- **Módosított fizikai kialakítás:** Például az újonnan létrehozott indexek hatékonyabban fedik le a lekérdezés követelményeit. Az új indexek akkor használhatók egy új összeállításhoz, ha a lekérdezésoptimalizáló úgy dönt, hogy az új index használata optimálisabb, mint a lekérdezés végrehajtásának első verziójához eredetileg kiválasztott adatstruktúra használata. A hivatkozott objektumok fizikai módosításai fordításkor új tervválasztási lehetőséget eredményezhetnek.

- **Kiszolgálói erőforrások közötti különbségek**: Ha egy terv az egyik rendszerben eltér a tervtől egy másik rendszerben, az erőforrások rendelkezésre állása, például a rendelkezésre álló processzorok száma befolyásolhatja, hogy melyik terv jön létre. Ha például egy rendszer több processzorral rendelkezik, akkor egy párhuzamos terv választható.

- **Különböző statisztikák**: A hivatkozott objektumokhoz társított statisztikák megváltoztak, vagy lényegesen eltérhetnek az eredeti rendszer statisztikáitól. Ha a statisztika megváltozik, és újrafordítás történik, a lekérdezés-optimalizáló a beállítások módosításától kezdve használja a statisztikákat. A felülvizsgált statisztikai adatok eloszlása és gyakorisága eltérhet az eredeti összeállítás adataitól. Ezek a módosítások számossági becslések létrehozására szolgálnak. (*Számossági becslések* azoknak a soroknak a száma, amelyek várhatóan átfolynak a logikai lekérdezési fán.) A számossági becslések módosítása ily vezethet, hogy különböző fizikai operátorokat és a kapcsolódó műveleteket válasszon. Még a statisztikák kisebb módosításai is módosulhatnak a lekérdezés végrehajtási tervében.

- **Megváltozott adatbázis-kompatibilitási szint vagy számossági becslési verzió:** Az adatbázis-kompatibilitási szint módosításai olyan új stratégiákat és szolgáltatásokat tehetnek lehetővé, amelyek más lekérdezés-végrehajtási tervet eredményezhetnek. Az adatbázis-kompatibilitási szinten túl a 4199-es letiltott vagy engedélyezett nyomkövetési jelző vagy az adatbázis-hatókörrel rendelkező konfigurációs QUERY_OPTIMIZER_HOTFIXES megváltozott állapota a fordításkor a lekérdezés-végrehajtási terv választási lehetőségeit is befolyásolhatja. Trace zászlók 9481 (force legacy CE) és 2312 (force default CE) is befolyásolja a tervet.

## <a name="resource-limits-issues"></a>Erőforráskorlátokkal kapcsolatos problémák

Lassú lekérdezési teljesítmény nem kapcsolódik az optimálistól elmaradó lekérdezési tervek és a hiányzó indexek általában az elégtelen vagy túlhasznált erőforrások. Ha a lekérdezési terv optimális, a lekérdezés (és az adatbázis) lehet, hogy az adatbázis, a rugalmas készlet vagy a felügyelt példány erőforráskorlátait üti meg. Egy példa lehet a szolgáltatási szint felesleges naplóírási átviteli áteresztődése.

- Erőforrásproblémák észlelése az Azure Portalon: Ha meg szeretné tudni, hogy az erőforrás-korlátok a probléma, olvassa el az [SQL Database erőforrás-figyelés.](sql-database-monitor-tune-overview.md#sql-database-resource-monitoring) Egyetlen adatbázisok és rugalmas készletek, [lásd: Database Advisor teljesítményjavaslatok](sql-database-advisor.md) és [a Query Performance Insights.](sql-database-query-performance.md)
- Erőforráskorlátok észlelése az [Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) használatával
- Erőforrásproblémák észlelése [a DMV-k](sql-database-monitoring-with-dmvs.md)használatával:

  - A [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV egy SQL-adatbázis PROCESSZOR-, I/O-és memóriafelhasználását adja vissza. 15 másodperces időközökhöz egy sor létezik, még akkor is, ha nincs tevékenység az adatbázisban. Az előzményadatok at egy órán keresztül karbantartják.
  - A [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV az Azure SQL Database processzorhasználati és tárolási adatait adja vissza. Az adatokgyűjtése és összesítése ötperces időközönként történik.
  - [Számos egyedi lekérdezés, amelyek összesítve magas CPU-t fogyasztanak](sql-database-monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)
  - 

Ha a problémát elégtelen erőforrásként azonosítja, frissítheti az erőforrásokat, hogy növelje az SQL-adatbázis kapacitását a PROCESSZOR-követelmények elnyeléséhez. További információ: [Egyetlen adatbázis-erőforrások méretezése az Azure SQL Database-ben](sql-database-single-database-scale.md) és [a Rugalmas készlet erőforrásainak méretezése az Azure SQL Database-ben.](sql-database-elastic-pool-scale.md) A felügyelt példányok méretezéséről a [Szolgáltatásiszintű erőforráskorlátok](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)című témakörben talál további információt.

## <a name="performance-problems-caused-by-increased-workload-volume"></a>A megnövekedett munkaterhelés-mennyiség által okozott teljesítményproblémák

Az alkalmazásforgalom és a munkaterhelés irása növelheti a PROCESSZOR-használatot. De óvatosnak kell lennie, hogy megfelelően diagnosztizálni ezt a problémát. Ha nagy processzorproblémákat lát, válaszoljon ezekre a kérdésekre annak megállapításához, hogy a növekedést a munkaterhelés irásának módosítása okozza-e:

- Az alkalmazás lekérdezései okozzák a magas CPU-problémát?
- A [legjobb CPU-fogyasztó lekérdezések, amelyek azonosítani tudja:](sql-database-monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past)

  - Több végrehajtási terv van társítva ugyanahhoz a lekérdezéshez? Ha igen, miért?
  - Az azonos végrehajtási tervvel rendelkező lekérdezések esetében konzisztensek voltak-e a végrehajtási idők? Nőtt a végrehajtás száma? Ha igen, a munkaterhelés növekedése valószínűleg teljesítményproblémákat okoz.

Összefoglalva, ha a lekérdezés végrehajtási terv nem hajtott végre másképpen, de a PROCESSZOR-használat nőtt a végrehajtási szám, a teljesítmény probléma valószínűleg a számítási feladatok növekedésével kapcsolatos.

Nem mindig könnyű azonosítani a számítási feladatok volumenének változását, amely processzorproblémát okoz. Tekintsük ezeket a tényezőket:

- **Megváltozott erőforrás-használat:** Fontolja meg például egy olyan forgatókönyv, amelyben a processzorhasználat nőtt 80 százalékra hosszabb ideig. Cpu-használat önmagában nem jelenti azt, hogy a számítási feladatok kötete megváltozott. Regressziók a lekérdezés végrehajtási terv és az adatelosztás változásai is hozzájárulhat nak több erőforrás-használat annak ellenére, hogy az alkalmazás végrehajtja ugyanazt a számítási feladatot.

- **Új lekérdezés megjelenése:** Előfordulhat, hogy egy alkalmazás különböző időpontokban új lekérdezéseket hajt végre.

- **A kérelmek számának növekedése vagy csökkenése**: Ez a forgatókönyv a munkaterhelés legkézenfekvőbb mértéke. A lekérdezések száma nem mindig felel meg több erőforrás-kihasználtság. Ez a mérőszám azonban még mindig jelentős jel, feltételezve, hogy más tényezők változatlanok.

Az Intelligent Insights segítségével észlelheti a [munkaterhelés növekedését,](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) és [megtervezheti a regressziók.](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression)

## <a name="waiting-related-problems"></a>Várakozással kapcsolatos problémák

Miután megszüntette az optimálisnál alacsonyabb szintű tervet és *a várakozással kapcsolatos* problémákat, amelyek végrehajtási problémákkal kapcsolatosak, a teljesítményprobléma általában a lekérdezések valószínűleg valamilyen erőforrásra várnak. A várakozással kapcsolatos problémákat a következők okozhatják:

- **Blokkolás**:

  Előfordulhat, hogy az egyik lekérdezés zárolja az adatbázis objektumait, míg mások ugyanazokat az objektumokat próbálják elérni. A blokkoló lekérdezéseket a [DMV-k](sql-database-monitoring-with-dmvs.md#monitoring-blocked-queries) vagy [az Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md#locking)segítségével azonosíthatja.
- **Io problémák**

  Előfordulhat, hogy a lekérdezések arra várnak, hogy a lapokat az adatokba vagy a naplófájlokba írják. Ebben az esetben `INSTANCE_LOG_RATE_GOVERNOR`ellenőrizze `WRITE_LOG`a `PAGEIOLATCH_*` , , vagy várjon statisztikát a DMV-ben. Lásd: DMV-k használata [az io-teljesítményproblémák azonosítására.](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
- **TempDB problémák**

  Ha a számítási feladatok ideiglenes táblákat használnak, vagy a tervekben TempDB-kiömlések vannak, a lekérdezések probléma merülhetnek fel a TempDB átviteli mivel. Lásd: DMV-k használata [a TempDB-problémák identitásához.](sql-database-monitoring-with-dmvs.md#identify-tempdb-performance-issues)
- **Memóriával kapcsolatos problémák**

  Ha a munkaterhelés nem rendelkezik elegendő memóriával, az oldal várható élettartama csökkenhet, vagy a lekérdezések kevesebb memóriát kapnak, mint amire szükségük van. Bizonyos esetekben a Lekérdezésoptimalizáló beépített intelligenciája megoldja a memóriával kapcsolatos problémákat. Lásd: DMV-k használata a [memóriatámogatási problémák azonosítására](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues).

### <a name="methods-to-show-top-wait-categories"></a>A legnépszerűbb várakozási kategóriák megjelenítésének módszerei

Ezeket a módszereket gyakran használják a várakozási típusok legfontosabb kategóriáinak megjelenítésére:

- Az Intelligens elemzési adatok segítségével azonosíthatja a [megnövekedett várakozási idő](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) miatti teljesítménycsökkenéssel rendelkező lekérdezéseket
- A [Query Store segítségével](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) megkeresheti az egyes lekérdezések várakozási statisztikáit az idő múlásával. A Query Store-ban a várakozási típusok várakozási kategóriákba vannak egyesítve. A várakozási kategóriák leképezése a [sys.query_store_wait_stats.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table)
- A [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) segítségével adja vissza a lekérdezési művelet során végrehajtott szálak által észlelt összes várakozásadatait. Ezt az összesített nézetet használhatja az Azure SQL Database és adott lekérdezések és kötegek teljesítményproblémáinak diagnosztizálásához. A lekérdezések várakozhatnak az erőforrásokra, a várólista-várakoztatásokra vagy a külső várakozásokra.
- A [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) segítségével információt küldhet az egyes erőforrásokra várakozó feladatok várólistájáról.

Nagy PROCESS-szintű forgatókönyvek esetén előfordulhat, hogy a Query Store és a wait statisztikái nem tükrözik a PROCESSZOR-használatot, ha:

- A nagy processzort fogyasztó lekérdezések végrehajtása még folyamatban van.
- A nagy processzort használó lekérdezések futottak, amikor feladatátvétel történt.

A Query Store áruházat nyomon követő és várakozási statisztikákat nyomon követő dmv-k csak a sikeresen befejezett és időbeli kimenő lekérdezések eredményeit jelenítik meg. Nem jelenítik meg az adatokat az utasítások befejezéséig az utasítások végrehajtása kor. A [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) dinamikus felügyeleti nézetben nyomon követheti az éppen futó lekérdezéseket és a kapcsolódó munkaidőt.

> [!TIP]
> További eszközök:
>
> - [TigerToolbox vár és retesz](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>További lépések

[SQL-adatbázis figyelése és hangolása – áttekintés](sql-database-monitor-tune-overview.md)