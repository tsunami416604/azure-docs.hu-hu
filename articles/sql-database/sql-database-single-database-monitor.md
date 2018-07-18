---
title: Adatbázis teljesítményének figyelése Azure SQL Database adatbázisokban | Microsoft Docs
description: Tudja meg, hogyan figyelheti az adatbázisokat Azure- eszközökkel és dinamikus felügyeleti nézetekkel.
keywords: adatbázis-megfigyelés, felhőalapú adatbázis teljesítménye
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: carlrab
ms.openlocfilehash: dc04a9334b63656719a7633a8dd7154ed6cd6993
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092579"
---
# <a name="monitoring-database-performance-in-azure-sql-database"></a>Adatbázis teljesítményének figyelése Azure SQL Database adatbázisokban
Egy Azure SQL-adatbázis teljesítményének figyelése az erőforrás-használatnak a kiválasztott adatbázis teljesítményszintjéhez viszonyított figyelésével kezdődik. Azt határozza meg, hogy az adatbázis többletkapacitással rendelkezik, vagy nem tudja, mert az erőforrások vannak maximumot, és úgy dönt, hogy, ideje teljesítményszintje és az adatbázis a monitorozási funkciók a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) vagy [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md). Az adatbázist figyelheti grafikus eszközök használatával az [Azure Portalon](https://portal.azure.com) vagy SQL [dinamikus felügyeleti nézetek](https://msdn.microsoft.com/library/ms188754.aspx) használatával.

> [!TIP]
> Használat [Azure SQL-Intelligent Insights](sql-database-intelligent-insights.md) automatikus ellenőrzésére, az adatbázis teljesítményét. Ha a teljesítménybeli problémát észlel, a részletek és a legfelső szintű okok elemzése (RCA) a probléma diagnosztikai napló jön létre. Ha lehetséges teljesítményének fokozása javaslat biztosítunk.
>

## <a name="monitor-databases-using-the-azure-portal"></a>Adatbázisok figyelése Azure Portal használatával
Az [Azure Portalon](https://portal.azure.com/) az adatbázis kiválasztásával és a **Figyelés** diagramra történő kattintással figyelheti egy önálló adatbázist erőforrás-használat. Ekkor megjelenik a **Metrika** ablak, amelyet a **Diagram szerkesztése** gombra kattintva módosíthat. Adja hozzá a következő metrikákat:

* Processzorhasználat (%)
* DTU-kihasználtság (%)
* Adat IO kihasználtsága (%)
* Adatbázis méretének kihasználtsága

Ezek a metrikák hozzáadása után továbbra is megtekintheti őket a a **figyelés** további információt a diagramon az **metrika** ablak. A négy metrika az átlagos kihasználtság százalékos arányát jeleníti meg az adatbázis **DTU-jához** viszonyítva. Tekintse meg a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) cikkekben további információt a szolgáltatási szintekről.  

![Adatbázis-teljesítményének szolgáltatásszint-figyelése.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

A metrikákhoz riasztásokat is lehet konfigurálni. Kattintson a **Riasztás hozzáadása** gombra a **Metrika** ablakban. A riasztás konfigurálásához kövesse a Varázslót. Lehetőség van riasztást kérni, ha a metrikák túllépnek egy bizonyos küszöböt, vagy egy bizonyos küszöb alá esnek.

Például ha az adatbázisban munkaterhelés-növekedésére számít, beállíthatja, hogy riasztást kapjon elektronikus üzenet formájában abban az esetben, ha az adatbázisra vonatkozó bármelyik metrika eléri a 80 százalékot. Ez korai figyelmeztetésként szolgálhat annak eldöntésére, hogy mikor kell a következő, magasabb teljesítményszintre váltani.

A metrikák annak megállapításában is segíthetnek, hogy alacsonyabb teljesítményszintre válthat-e. Tegyük fel, hogy Standard S2 adatbázist használ, és a metrikák azt mutatják, hogy az adatbázis átlagos kihasználtsága egy adott időpontban nem több, mint 10 százalék. Ebben az esetben valószínű, hogy az adatbázis Standard S1 teljesítményszinten is megfelelően fog működni. Mielőtt azonban alacsonyabb teljesítményszintre váltana, vegye figyelembe az esetlegesen hirtelen megugró vagy ingadozó munkaterheléseket.

## <a name="monitor-databases-using-dmvs"></a>Adatbázisok figyelése dinamikus felügyeleti nézetek használatával
A portálon elérhető metrikák a következő rendszernézeteken keresztül is elérhetők: [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) a kiszolgáló logikai **fő**adatbázisában és [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) a felhasználói adatbázisban. Ha hosszabb ideig kevesebb részletes adatot kell figyelnie, akkor a **sys.resource_stats** nézetet válassza. Ha rövidebb ideig több részletes adatot kell figyelnie, akkor a **sys.dm_db_resource_stats** nézetet válassza. További információkat az [Útmutató az Azure SQL Database teljesítményfigyeléséhez](sql-database-single-database-monitor.md#monitor-resource-use) részben talál.

> [!NOTE]
> A **sys.dm_db_resource_stats** eredményhalmaza üres lesz, ha a kivezetett Web vagy Business kiadású adatbázisokra alkalmazzák.
>
>

### <a name="monitor-resource-use"></a>Erőforrás-használat figyelése

Erőforrás-használat használatával figyelheti [SQL Database lekérdezési Terheléselemző](sql-database-query-performance.md) és [Query Store](https://msdn.microsoft.com/library/dn817826.aspx).

Használatának két nézetet is ellenőrizheti:

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

#### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
Használhatja a [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) nézetben minden egyes SQL database-ben. A **sys.dm_db_resource_stats** a nézet jeleníti meg a legutóbbi erőforrás adatait a szolgáltatási szinthez képest. A CPU, adat IO, napló írási műveletek és a memória átlagos százalékos 15 másodpercenként rögzíti, és 1 órára vonatkozó karbantartása.

Mivel ez a nézet biztosít egy részletesebb erőforrás-használat, **sys.dm_db_resource_stats** első bármely jelenlegi-állapot elemzéshez vagy hibaelhárítási. Ez a lekérdezés például az elmúlt egy órában az aktuális adatbázisra vonatkozó átlagos és maximális erőforrás használatát mutatja:

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data IO in percent',
        MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

Más lekérdezések lévő példák [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

#### <a name="sysresourcestats"></a>sys.resource_stats
A [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) megtekintheti a **fő** adatbázis tartalmaz további információt, amely az adott szolgáltatási csomagot és teljesítményszintet szinten az SQL database teljesítményének figyeléséhez nyújt segítséget. Az adatok 5 percenként összegyűjtött, és körülbelül két hétben változatlan marad. Ez a nézet hasznos hosszabb távú előzményadatok elemzése érdekében hogyan az SQL database erőforrásokat használ.

A következő diagram bemutatja a CPU erőforrás használható egy prémium szintű adatbázis-P2 teljesítményszint óránként egy hét. Ehhez a diagramhoz egy hétfőn kezdődő, 5 munkanapok megjeleníti, és megjeleníti a hétvégi, amikor sok kisebb történik, az alkalmazás.

![Az SQL database erőforrás-használat](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Az adatokból, ez az adatbázis jelenleg rendelkezik egy maximális CPU-terhelés csak 50 %-os CPU-használat képest a P2 teljesítményszint (csúcspontját a "frissítő kedd"). Ha CPU a meghatározó tényező az alkalmazás erőforrás-profilt, akkor dönthet úgy, hogy P2-e a megfelelő teljesítményszintet garantálja, hogy a számítási feladatok mindig megfelel-e. Ha egy alkalmazás, hogy idővel egyre, célszerű egy további erőforrás az puffer kell, hogy az alkalmazás soha nem eléri a teljesítményszint korlátot. Ha növeli a teljesítményszintet, segíthet, amelyek akkor jelentkeznek, amikor egy adatbázis nem rendelkezik elegendő a kérelmek feldolgozását hatékonyan, különösen a késésre érzékeny környezetekben power ügyfél látható hibák elkerülése érdekében. Ilyen például, egy adatbázis, amely támogatja az adatbázis-hívások eredményei alapján weblapok aggasztó alkalmazás.

Többi alkalmazástípus esetében előfordulhat, hogy ugyanazon a grafikonon másképp értelmezi. Például ha egy alkalmazás megpróbálja feldolgozni az Bérlista-adatokat minden nap, és ugyanabban a diagramban rendelkezik, "batch-feladat" modell az ilyen típusú előfordulhat, hogy tegye részletes, P1 teljesítményszintet. A P1 teljesítményszint rendelkezik 100 dtu-k 200 dtu-k P2 teljesítményi szinten képest. A P1 teljesítményszintet biztosít a P2 teljesítményszint fele a teljesítmény. Így a P2 CPU-használat 50 %-os egyenlő 100 %-os CPU-használat a P1 szintben foglalt. Ha az alkalmazás nem rendelkezik időtúllépések, előfordulhat, hogy nem számít, hogy a feladat 2 óráig, azaz 2,5 órát, alatt Ha ma kapja meg. Ebbe a kategóriába tartozó alkalmazások valószínűleg általi használata P1 teljesítményszintet. Használja ki, hogy nincsenek-e a nap, erőforrás-használat esetén alacsonyabb, így bármilyen "big Data típusú csúcs" lehet, hogy kerülnek a csatornák egy későbbi részében a nap folyamán ideig is igénybe vehet. A P1 teljesítményszint mindaddig, amíg a feladat be tudja időben minden nap lehet helyes, az adott alkalmazás (és a pénzt).

Az Azure SQL Database közzéteszi felhasznált minden aktív adatbázis az erőforrás adatait a **sys.resource_stats** nézete a **fő** adatbázis az egyes kiszolgálókon. A tábla összesített értéket jelenít meg az 5 perces időközönként. Az alapszintű, Standard és prémium szolgáltatási szinteken, az adatok több mint 5 percig is eltarthat, a táblázatban jelennek meg, így ezek az adatok közel valós idejű elemzési helyett előzményadataik elemzésére hasznosabb. Lekérdezés a **sys.resource_stats** adatbázis legutóbbi előzményeinek megtekintéséhez megtekintése és ellenőrzése-i azt szeretné, hogy szükség esetén a teljesítmény akár a Foglalás választotta.

> [!NOTE]
> Akkor kapcsolódnia kell a **fő** adatbázis a logikai SQL adatbázis-kiszolgáló lekérdezéséhez **sys.resource_stats** a következő példákban.
> 
> 

Ez a példa bemutatja, hogyan érhető el ebben a nézetben az adatokat:

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![A sys.resource_stats katalógusnézet](./media/sql-database-performance-guidance/sys_resource_stats.png)

A következő példa bemutatja, hogy különböző módon használhatja a **sys.resource_stats** katalógus nézetét, ahol információkat találhat az SQL database erőforrásokat:

1. Az elmúlt egy hét erőforrás meg az adatbázis userdb1 használja, ez a lekérdezés futtatása:
   
        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;
2. Egyes funkcióiról, az erőforrás-mérőszámok feltárásához kell kiértékelni arról, hogy a számítási feladatok megfelel-e a teljesítményszintet,: Processzor, olvasási, írási, feldolgozók száma és munkamenetek számát. Íme a módosított lekérdezés használatával **sys.resource_stats** jelentéséhez ezen erőforrás-mérőszámok átlagos és maximális értékeit:
   
        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
3. Ezekkel az információkkal kapcsolatban az átlagos és maximális értékeket minden egyes erőforrás-metrika értékelhet arról, hogy az alkalmazások és szolgáltatások hogyan illeszkedik a kiválasztott teljesítményszint. Általában az átlagérték **sys.resource_stats** jó alapterv használata a célméretet szemben biztosítanak. Az elsődleges mérési meghajtó kell lennie. Például előfordulhat, hogy használni a Standard szolgáltatásszinten az S2 teljesítményszint. Átlagos százalékos használata a Processzor- és i/o-olvasási és írási műveletek az alábbiakban 40 %-os, feldolgozók átlagos száma 50 alá van és munkamenetek átlagos száma nem éri a 200-as. A számítási feladat lehet, hogy illeszkedik az S1 teljesítményszint. Könnyen látható, hogy az adatbázis megfelel a feldolgozó és a munkamenet korlátok. A megtekintéséhez, hogy egy adatbázis illeszkedik tartományállapot Processzor és a egy alacsonyabb teljesítményszintre olvassa be, és írási, ossza meg a dtu-k száma a jelenlegi teljesítményszint alacsonyabb teljesítményszintre dtu-k száma és az eredmény megszorozza 100:
   
    **S1 DTU-K / S2 DTU-K * 100 = 20 / 50 * 100 = 40**
   
    Ez a relatív teljesítmény különbség a két teljesítményszintek százalékban. Az erőforrások használatával Ez a mennyiség nem haladja meg, ha a számítási feladatok az alacsonyabb teljesítményszintre előfordulhat, hogy illeszkedik. Azonban szüksége, tekintse meg az erőforrás-használati értékek összes tartományát, és meghatározhatja, százalékkal gyakoriságát az adatbázis-munkaterhelés lenne illik az alacsonyabb teljesítményszintre. A következő lekérdezés kimenete a illesztése százalékos erőforrás dimenziónként, 40 %-kal, azt ebben a példában számított küszöbérték alapján:
   
        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Az adatbázis szolgáltatásiszint-célkitűzés (SLO) alapján, eldöntheti, hogy a számítási feladatok illeszkedik az alacsonyabb teljesítményszintre. Ha az adatbázis-munkaterhelés SLO 99,9 százalékos rendelkezésre állást, és az előző lekérdezés visszaadja az értékek nagyobb, mint az összes három erőforrás dimenzió 99,9 %-os, nagy valószínűséggel a számítási feladatok illeszkedik az alacsonyabb teljesítményszintre.
   
    Hibaoldal illesztése százalékos is biztosít abba, hogy helyezze át a következő magasabb teljesítményi szinthez felel meg az slo-t. Például userdb1 látható, a következő CPU-használat az elmúlt egy hét:
   
   | Átlagos CPU-százalék | Maximális Processzorhasználat százalékos |
   | --- | --- |
   | 24.5 |100.00 |
   
    Az átlagos Processzorhasználat lenne jól illeszkedik az adatbázis teljesítményszintjének teljesítményszint korlátot negyedéves szól. De a maximális érték jelenik meg, hogy az adatbázis eléri a korlátot, azt a teljesítményszintet. Szükséges a következő, magasabb teljesítményszintre váltani? Keresse meg, hogyan lehet több alkalommal a számítási feladat eléri 100 %-os, és hasonlítsa össze az adatbázis-munkaterhelés slo-t.
   
        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Ha a lekérdezés visszaad egy értéket kisebb, mint 99,9 %-a három erőforrás dimenziók bármelyikét érdemes vagy a következő magasabb teljesítményi szinthez, vagy alkalmazás hangolási módszerek használatáról az SQL database terhelésének csökkentése érdekében.
4. Ebben a gyakorlatban is figyelembe veszi a várható számítási feladatok növekedése a jövőben.

Rugalmas készletek esetén az ebben a szakaszban leírt technikákkal az adatbáziskészlet egyes adatbázisait is figyelheti. De ugyanígy figyelheti az adatbáziskészlet egészét is. További információkért lásd: [Rugalmas készlet figyelése és kezelése](sql-database-elastic-pool-manage-portal.md).


### <a name="maximum-concurrent-requests"></a>Egyidejű kérelmek maximális száma
Az egyidejű kérelmek számra megtekintéséhez futtassa az SQL Database a Transact-SQL-lekérdezést:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

Egy helyszíni SQL Server-adatbázis, a számítási feladatok elemzése, módosítsa a lekérdezést az adott adatbázis szűrés elemezni szeretné. Például ha egy helyszíni adatbázisból MyDatabase nevű, a Transact-SQL-lekérdezés adja vissza az egyidejű kérelmek száma ebben az adatbázisban:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

Ez az egyetlen időpontban csak egy pillanatkép időben. Ismerje meg a számítási feladatok és a párhuzamos kérés követelményeinek, szüksége lesz számos mintákat gyűjtsön idővel.

### <a name="maximum-concurrent-logins"></a>Egyidejű bejelentkezések maximális
Elemezheti a felhasználói és -minták meghatározására, hogy képet kapjon a bejelentkezések gyakoriságát. Emellett futtathatja valós terhelések győződjön meg arról, hogy nem állt jelen vagy egyéb korlátok ebben a cikkben bemutatjuk, hogy egy tesztkörnyezetben. Egyetlen lekérdezés vagy a dinamikus felügyeleti nézet (DMV), amely meg tudja jeleníteni az egyidejű bejelentkezési counts vagy előzmények nem áll rendelkezésre.

Ha több ügyfél használja ugyanazt a kapcsolati karakterláncot, a szolgáltatás végzi a hitelesítést minden egyes bejelentkezés. 10 felhasználó egyszerre csatlakozhat adatbázis ugyanazt a felhasználónevet és jelszót, ha 10 egyidejű bejelentkezések lenne. Ez a korlátozás csak a bejelentkezési és hitelesítési időtartama vonatkozik. Ha az azonos 10 felhasználó egymás után csatlakozhat az adatbázishoz, egyidejű bejelentkezések száma soha nem lenne 1-nél nagyobb.

> [!NOTE]
> Jelenleg ez a korlátozás nem vonatkozik a rugalmas készletekben található adatbázisokat.
> 
> 

### <a name="maximum-sessions"></a>Munkamenetek maximális száma
A jelenlegi aktív munkamenetek számának megtekintéséhez futtassa az SQL Database a Transact-SQL-lekérdezést:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Ha elemzett egy helyszíni SQL Server számítási feladatok, módosítsa a lekérdezést, hogy arra koncentrálhasson, egy adott adatbázishoz. Ez a lekérdezés segít meghatározni, hogy lehetséges munkamenet igényeinek, az adatbázis Ha áthelyezi az Azure SQL Database fontolgatja.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Ezeket a lekérdezéseket,-időponthoz számát adja vissza. Ha a több mintához idővel, rendelkezni fog a legjobb ismeretekkel a munkamenet használja.

Az SQL Database az elemzést követően megjelenik a korábbi statisztika munkamenetek lekérdezésével a [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) megtekintése és ellenőrzése a **active_session_count** oszlop. 

## <a name="next-steps"></a>További lépések

- Automatikus adatbázis-indexek finomhangolása és végrehajtási tervét használatával lekérdezheti [Azure SQL Database automatikus finomhangolása](sql-database-automatic-tuning.md).
- Automatikus adatbázis teljesítményének figyelése [Azure SQL-Intelligent Insights](sql-database-intelligent-insights.md). Ez a szolgáltatás biztosít a diagnosztikai adatokat, és alapvető okok teljesítménybeli problémák.
