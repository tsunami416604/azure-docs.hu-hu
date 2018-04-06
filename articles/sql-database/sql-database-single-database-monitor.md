---
title: Adatbázis teljesítményének figyelése Azure SQL Database adatbázisokban | Microsoft Docs
description: Tudja meg, hogyan figyelheti az adatbázisokat Azure- eszközökkel és dinamikus felügyeleti nézetekkel.
keywords: adatbázis-megfigyelés, felhőalapú adatbázis teljesítménye
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 09/20/2017
ms.author: carlrab
ms.openlocfilehash: 4bc2c8578157bd29894bfee221174501c5003a42
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="monitoring-database-performance-in-azure-sql-database"></a>Adatbázis teljesítményének figyelése Azure SQL Database adatbázisokban
Egy Azure SQL-adatbázis teljesítményének figyelése az erőforrás-használatnak a kiválasztott adatbázis teljesítményszintjéhez viszonyított figyelésével kezdődik. A figyelés segítségével megállapítható, ha az adatbázis többletkapacitással rendelkezik, vagy éppen elérte a maximumot az erőforrások kihasználtságában, és emiatt problémák jelentkezhetnek, így az is eldönthető, hogy módosítani kell-e az adatbázis teljesítményszintjét és [szolgáltatásszintjét](sql-database-service-tiers.md). Az adatbázist figyelheti grafikus eszközök használatával az [Azure Portalon](https://portal.azure.com) vagy SQL [dinamikus felügyeleti nézetek](https://msdn.microsoft.com/library/ms188754.aspx) használatával.

> [!TIP]
> Használjon [Azure SQL intelligens Insights](sql-database-intelligent-insights.md) automatikus ellenőrzésére, az adatbázis teljesítménye. Ha a teljesítménybeli problémát észlel, egy diagnosztikai naplófájl részleteit és a legfelső szintű ok elemzés (RCA) a probléma jön létre. Ha lehetséges teljesítményének fokozása javaslat valósul meg.
>

## <a name="monitor-databases-using-the-azure-portal"></a>Adatbázisok figyelése Azure Portal használatával
Az [Azure Portalon](https://portal.azure.com/) az adatbázis kiválasztásával és a **Figyelés** diagramra történő kattintással figyelheti egy önálló adatbázist erőforrás-használat. Ekkor megjelenik a **Metrika** ablak, amelyet a **Diagram szerkesztése** gombra kattintva módosíthat. Adja hozzá a következő metrikákat:

* Processzorhasználat (%)
* DTU-kihasználtság (%)
* Adat IO kihasználtsága (%)
* Adatbázis méretének kihasználtsága

Fenti metrikák hozzáadása után továbbra is megtekintheti azokat a **figyelés** további információkat a diagram a **metrika** ablak. A négy metrika az átlagos kihasználtság százalékos arányát jeleníti meg az adatbázis **DTU-jához** viszonyítva. A DTU-król részletesebben a [szolgáltatásszintekről](sql-database-service-tiers.md) szóló cikkben olvashat.

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

### <a name="monitor-resource-use"></a>A figyelő erőforrás-használat

Erőforrás-használat használatával figyelheti [SQL adatbázis-lekérdezési Terheléselemző](sql-database-query-performance.md) és [Lekérdezéstár](https://msdn.microsoft.com/library/dn817826.aspx).

Használati használatával két nézetet is végezhet figyelést:

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

#### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
Használhatja a [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) nézetben minden SQL-adatbázis. A **sys.dm_db_resource_stats** a nézet jeleníti meg a legutóbbi erőforrás használata adatokat a szolgáltatási rétegben viszonyítva. A CPU, a adat IO, a memória és a napló írási átlagos százalékos 15 másodpercenként tárolja, és 1 óráig megmaradjanak.

Mivel ez a nézet biztosít egy részletesebb erőforrás használata, **sys.dm_db_resource_stats** első bármely jelenlegi-állapot elemzéshez vagy hibaelhárítási. Ez a lekérdezés például keresztül az elmúlt egy órában az aktuális adatbázisra vonatkozó átlagos és maximális erőforrás használatát mutatja be:

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
A [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) tekintse meg a **fő** adatbázis tartalmaz további információt az SQL-adatbázis, az adott szolgáltatás és teljesítményszintet szinten teljesítményének figyeléséhez nyújt segítséget. Az adatok gyűjtése 5 percenként és körülbelül két héttel a változatlan marad. Ebben a nézetben a hosszabb távú előzményadatok elemzése hogyan az SQL-adatbázis erőforrást használ, érdemes használni.

A következő ábra a CPU erőforrás Premium adatbázis a használható P2 teljesítményszintjét óránként egy hét múlva jelzi. Ehhez a diagramhoz egy hétfőn kezdődő, 5 munkanapok jeleníti meg, és megjeleníti a hétvégi, ha sok kevésbé történik, az alkalmazás.

![SQL-adatbázis erőforrás-használat](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Az adatokból az adatbázis jelenleg tartalmaz egy csúcsterheléses CPU-terhelést, az imént több mint 50 %-a CPU-használat (kedd délig) P2 teljesítményszintjét viszonyítva. Ha CPU erőforrás alkalmazásprofilban meghatározó tényező, majd dönthet úgy, hogy P2-e a megfelelő teljesítményszintet garantálja, hogy a munkaterhelés mindig megfelelő. Ha egy alkalmazás idő haladtával egyre nő, célszerű egy további erőforrás puffer kell, hogy az alkalmazás soha nem eléri a teljesítmény-szintű határértéket. Ha a teljesítmény növelése elkerülheti felhasználói által látható a hibákat, akkor fordulhat elő, ha egy adatbázis nem tartalmaz elég power késésérzékeny környezetben különösen hatékony, dolgozza fel a kérelmeket. Példa: egy adatbázist, amely támogatja az adatbázis-hívások eredménye alapján weblapok festi alkalmazást.

Más alkalmazástípusok előfordulhat, hogy ugyanazon diagramhoz másképp értelmezi. Például ha egy alkalmazás megpróbálja feldolgozni az Bérlista adatok naponta és ugyanabban a diagramban, "kötegelt" modell az ilyen előfordulhat, hogy tegye részletes, P1 teljesítményszint szükséges. A P1 teljesítményszint szükséges van 100 dtu-k a P2 teljesítmény szinten 200 Dtu képest. A P1 teljesítményszint szükséges P2 teljesítményszintjének fele a teljesítményt biztosít. Igen P2 a CPU-használat az 50 % egyenlő P1 a CPU-használat 100 százalék. Ha az alkalmazás nem rendelkezik időtúllépések, előfordulhat, hogy nem számít, hogy a feladat végrehajtásához szükséges 2 óra vagy 2,5 órát, ha ma kapja meg. Egy alkalmazás ebbe a kategóriába tartozó valószínűleg használhat P1 teljesítményszint szükséges. Kihasználhatja a tényt, hogy vannak-e időszakokra, az erőforrás-használat esetén alacsonyabb, úgy, hogy a "nagy csúcs" lehet, hogy kerülnek a csatornák valamelyikére későbbi szakaszában a nap folyamán. A P1 teljesítményszint szükséges mindaddig, amíg a feladatok befejezéséhez minden nap idővel lehet jó az adott alkalmazás (és a Mentés pénzt).

A felhasználása előtt minden aktív adatbázis található erőforrás-információkat az Azure SQL Database tesz elérhetővé a **sys.resource_stats** nézete a **fő** adatbázisban az egyes kiszolgálókon. A tábla 5 perces időközönként céljából összesíti. A Basic, Standard és Premium szolgáltatásszintek az adatok több mint 5 perc – a táblázatban jelennek meg, így ezek az adatok közel valós idejű elemzési helyett korábbi elemzés hasznosabb is tarthat. Lekérdezés a **sys.resource_stats** adatbázis legutóbbi előzményeinek megtekintéséhez megtekintése és ellenőrzése hogy a Foglalás úgy döntött, hogy kézbesíteni a teljesítményt, ha szükséges.

> [!NOTE]
> Meg kell csatlakoztatni a **fő** a logikai SQL adatbázis-kiszolgáló lekérdezéséhez adatbázis **sys.resource_stats** a következő példákban.
> 
> 

Ez a példa bemutatja, hogyan van téve az adatok ebben a nézetben:

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![A sys.resource_stats katalógusnézet használatával derítheti ki](./media/sql-database-performance-guidance/sys_resource_stats.png)

A következő példa bemutatja, különböző módon használható a **sys.resource_stats** nézet segítségével szerezzen információt, hogy az SQL-adatbázis erőforrást használ, a katalógus:

1. Az elmúlt héten erőforrás lássunk az adatbázis userdb1 használja, ez a lekérdezés futtatása:
   
        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;
2. Értékelje ki, milyen mértékben a munkaterhelés megfelelő teljesítményszintjét, vissza kell részletekbe menően tárhatják fel minden szempontja, hogy az erőforrás-metrikák: Processzor, olvasási, írási, munkavállalók száma és munkamenetek száma. Ez egy új lekérdezés használatával **sys.resource_stats** erőforrás metrikákat átlagos és maximális értékeit jelentheti:
   
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
3. Ezen információk átlagos és maximális értékeiről minden erőforrás mérőszám felmérheti, milyen jól illeszkedik az a számítási feladatok a kiválasztott teljesítményszint szükséges. Általában a átlagérték **sys.resource_stats** jó alapterv használata elleni célméretét biztosítanak. Az elsődleges mérési memóriás kell lennie. Például előfordulhat, hogy használni a szabványos szolgáltatási rétegben a S2 teljesítményszint szükséges. Processzor- és I/O olvasási műveletek átlagos használni százalékos írási műveletek 40 százalék alatti, munkavállalók átlagos száma nem éri el 50 és a munkamenetek átlagos száma nem éri a 200-as. A számítási feladatok előfordulhat, hogy a S1 teljesítményszinttel illeszkedik. Könnyen látható, hogy megfelel-e az adatbázis a munkavégző és a munkamenet korlátok. Megtekintéséhez, hogy egy adatbázis illeszkedik CPU,-ben elérhető alacsonyabb teljesítményszintre beolvassa és írási műveletekről, a jelenlegi teljesítményszintje DTU száma szerint a alacsonyabb teljesítményszintre DTU száma felosztani és az eredmény megszorozza 100:
   
    **S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
   
    A két teljesítményszintet százalékban relatív teljesítménybeli különbségek eredménye. Ha az erőforrások felhasználását nem haladhatja meg ezt a mennyiséget, a munkaterhelés a alacsonyabb teljesítményszintre előfordulhat, hogy illeszkedik. Azonban meg kell nézze meg az erőforrás-használati értékek az összes tartomány, és határozza meg, százalékos, milyen gyakran szeretné felelnek meg az adatbázisban munkaterhelés a alacsonyabb teljesítményszintre. A következő lekérdezés a küszöbérték azt ebben a példában számított 40 %-kal alapján erőforrás dimenziónként illeszkedő százalékos kimenete:
   
        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Az adatbázis szolgáltatásiszint-célkitűzés (SLO) alapuló, eldöntheti, hogy a munkaterhelés illeszkedik az alacsonyabb teljesítményszintre. Ha az adatbázis munkaterhelés SLO 99,9 %, és az előző lekérdezés visszaadja az értékek összes három erőforrás dimenzió 99,9 %-nál nagyobb, a munkaterhelés, valószínűleg a alacsonyabb teljesítményszintre illeszkedik.
   
    Megnézi a illeszkedő százalékos is biztosít, hogy helyezze át a következő, magasabb teljesítményszintre felel meg a SLO betekintést. Például a userdb1 az elmúlt héten következő CPU használatát mutatja be:
   
   | Átlagos CPU-százaléka | CPU maximális százalék |
   | --- | --- |
   | 24.5 |100.00 |
   
    Átlagos CPU jól illeszkedik a teljesítményszintet az adatbázis teljesítményszintjét legfeljebb negyedéves tárgya. De a maximális érték látható, hogy a eléri a teljesítményszintjének mérethatárt. Helyezze át a következő, magasabb teljesítményszintre kell? Olvassa sokszor a munkaterhelés eléri 100 százalék, és hasonlítsa össze az adatbázisban munkaterhelés slo-t.
   
        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Ha a lekérdezés által visszaadott érték legalább 99,9 %-bármelyik három erőforrás dimenzió fontolja meg a következő, magasabb teljesítményszintre vagy áthelyezését, vagy az SQL-adatbázis a terhelés csökkentése az alkalmazás hangolási módszerek segítségével.
4. Ebben a gyakorlatban is figyelembe veszi a tervezett alkalmazások és szolgáltatások növelje a jövőben.

Rugalmas készletek esetén az ebben a szakaszban leírt technikákkal az adatbáziskészlet egyes adatbázisait is figyelheti. De ugyanígy figyelheti az adatbáziskészlet egészét is. További információkért lásd: [Rugalmas készlet figyelése és kezelése](sql-database-elastic-pool-manage-portal.md).


### <a name="maximum-concurrent-requests"></a>Egyidejű kérelmek maximális száma
Egyidejű kérelmek számát jelenti, hogy az SQL-adatbázis a Transact-SQL-lekérdezés futtatása:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

A munkaterhelés helyi SQL Server adatbázis elemzéséhez, módosítsa az adott adatbázis szűréshez elemezni kívánt lekérdezés. Például ha egy helyi adatbázist használ nevű adatbázis, a Transact-SQL lekérdezés által visszaadott egyidejű kérelmek száma az adatbázis:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

Ez az csak egy ponton pillanatkép időben. Ahhoz, hogy a munkaterhelés és egyidejűleg futtatható kérelmek vonatkozó követelmények jobb megértése, lesz szüksége adott idő alatt sok minták gyűjtéséhez.

### <a name="maximum-concurrent-logins"></a>Maximális párhuzamos bejelentkezések
A gyakoriság bejelentkezések képet kapjon a felhasználó és az alkalmazás mintáinak elemezheti. Is futtathatja valós terhelések egy tesztkörnyezetben, győződjön meg arról, hogy Ön éppen nem elérte-e ez vagy más korlátok, a cikkben arról lesz szó. Nincs egyetlen lekérdezés vagy dinamikus kezelési nézetet (DMV), amely meg tudja jeleníteni az egyidejű bejelentkezési száma vagy előzményeit.

Ha több ügyfél használja ugyanazt a kapcsolati karakterláncot, a szolgáltatás minden egyes bejelentkezés hitelesíti magát. Ha a 10 olyan felhasználót egy időben ugyanazt a felhasználónevet és jelszót használatával adatbázis csatlakoznak, nem lenne 10 egyidejű bejelentkezések. Ezt a határt csak a bejelentkezési és hitelesítési időtartama vonatkozik. Ha a ugyanazon 10 olyan felhasználót egymás után kapcsolódni az adatbázishoz, egyidejű bejelentkezések száma soha nem lenne 1-nél nagyobb.

> [!NOTE]
> Ezt a határt jelenleg nem vonatkozik a rugalmas készletek adatbázisok.
> 
> 

### <a name="maximum-sessions"></a>Munkamenetek maximális száma
Aktív munkamenetek jelenlegi száma megtekintéséhez futtassa az SQL-adatbázis a Transact-SQL-lekérdezést:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Ha egy helyi SQL Server munkaterhelés most elemzése, módosíthatja a lekérdezés egy adott adatbázis összpontosíthat. Ez a lekérdezés segít meghatározni a munkamenet funkciókra van szüksége az adatbázis, ha tervezi, helyezze át az Azure SQL Database.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Ebben az esetben ezeket a lekérdezéseket vissza időpontban számot eredményez. Ha adott idő alatt több mintához gyűjt, összekapcsolta a legjobb ismertetése a munkamenet használja.

SQL-adatbázis elemzéshez kaphat a korábbi statisztika munkamenetek lekérdezésével a [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) megtekintése és ellenőrzése a **active_session_count** oszlop. 

## <a name="next-steps"></a>További lépések

- Automatikusan adatbázis indexek beállítása és a lekérdezés végrehajtási terveket [Azure SQL Database automatikus hangolása](sql-database-automatic-tuning.md).
- Automatikus adatbázis teljesítményének figyelése [Azure SQL intelligens Insights](sql-database-intelligent-insights.md). Ez a szolgáltatás diagnosztikai információkat biztosít, és alapvető oka a teljesítménnyel kapcsolatos problémák elemzése.
