---
title: Figyelés dinamikus felügyeleti nézetek használatával az Azure SQL Database |} A Microsoft Docs
description: Megtudhatja, hogyan észlelheti és diagnosztizálhatja a teljesítményproblémákat gyakran figyelése a Microsoft Azure SQL Database dinamikus felügyeleti nézetek használatával.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 08/08/2018
ms.openlocfilehash: 97907eee9982fdf6a804bc13edbf8c14efa4ce42
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47161386"
---
# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>Az Azure SQL Database felügyelete dinamikus felügyeleti nézetek használatával
A Microsoft Azure SQL Database lehetővé teszi, hogy a dinamikus felügyeleti nézetek által letiltott vagy hosszú ideig futó lekérdezéseket, erőforrás-keresztmetszetek, gyenge lekérdezési tervek és így tovább okozhatta teljesítményproblémák diagnosztizálásához egy részét. Ez a témakör információt nyújt az gyakori teljesítményproblémák észlelése dinamikus felügyeleti nézetek használatával.

Az SQL Database támogatja a dinamikus felügyeleti nézetek három kategóriába részben:

* Adatbázissal kapcsolatos dinamikus felügyeleti nézetek.
* Végrehajtási kapcsolatos dinamikus felügyeleti nézetek.
* Tranzakció-hoz kapcsolódó dinamikus felügyeleti nézetek.

A dinamikus felügyeleti nézetek részletes információkért lásd: [dinamikus felügyeleti nézetek és függvények (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) az SQL Server Online könyvekben.

## <a name="permissions"></a>Engedélyek
Az SQL Database dinamikus felügyeleti nézetek lekérdezéséhez szükséges **VIEW DATABASE STATE** engedélyeket. A **VIEW DATABASE STATE** engedélyt az aktuális adatbázisban lévő összes objektum adatait adja vissza.
Megadását a **VIEW DATABASE STATE** engedélyt egy adott adatbázis-felhasználót a következő lekérdezés futtatásával:

```GRANT VIEW DATABASE STATE TO database_user; ```

A helyszíni SQL Server-példányt, a dinamikus felügyeleti nézetek kiszolgáló állapot adatait adja vissza. SQL Database-ben, csak az aktuális logikai adatbázis kapcsolatos információkat ad vissza.

## <a name="calculating-database-size"></a>Adatbázis méretének kiszámítása
A következő lekérdezés (megabájtban) az adatbázis méretét adja vissza:

```
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

A következő lekérdezés az adatbázis méretét (megabájtban) objektumokat adja vissza:

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Kapcsolatok ellenőrzése
Használhatja a [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) kérhet le információt a kapcsolatok egy adott Azure SQL Database-kiszolgáló és minden kapcsolat részleteinek megtekintése. Emellett a [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) megtekintése hasznos, amikor az összes aktív felhasználói kapcsolat és belső feladatok adatainak lekérése.
Az alábbi lekérdezés lekérdezi az aktuális kapcsolat információkat:

```
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> Végrehajtásakor a **sys.dm_exec_requests** és **sys.dm_exec_sessions nézetek**, ha rendelkezik **VIEW DATABASE STATE** engedéllyel az adatbázis láthatja az összes végrehajtása munkamenetek a az adatbázisban. Ellenkező esetben tekintse meg az aktuális munkamenet.
> 
> 

## <a name="monitor-resource-use"></a>Erőforrás-használat figyelése

Erőforrás-használat használatával figyelheti [SQL Database lekérdezési Terheléselemző](sql-database-query-performance.md) és [Query Store](https://msdn.microsoft.com/library/dn817826.aspx).

Használatának két nézetet is ellenőrizheti:

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
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

### <a name="sysresourcestats"></a>sys.resource_stats
A [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) megtekintheti a **fő** adatbázis rendelkezik, amelyek segítségével az adott szolgáltatásszinten az SQL-adatbázis teljesítményének figyelése és a számítási méret további információt. Az adatok 5 percenként összegyűjtött, és körülbelül két hétben változatlan marad. Ez a nézet hasznos hosszabb távú előzményadatok elemzése érdekében hogyan az SQL database erőforrásokat használ.

A következő diagram bemutatja a CPU erőforrás használható egy prémium szintű adatbázis P2 számítási méretű óránként egy hét. Ehhez a diagramhoz egy hétfőn kezdődő, 5 munkanapok megjeleníti, és megjeleníti a hétvégi, amikor sok kisebb történik, az alkalmazás.

![Az SQL database erőforrás-használat](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Az adatokból, ez az adatbázis jelenleg rendelkezik egy maximális CPU-terhelés alig több mint 50 %-os CPU-használat a P2 viszonyított számítási méret (csúcspontját a "frissítő kedd"). Ha CPU a meghatározó tényező az alkalmazás erőforrás-profilt, majd dönthet, hogy P2-e a megfelelő számítási méret garantálja, hogy a számítási feladatok mindig megfelel-e. Ha egy alkalmazás, hogy idővel egyre, célszerű egy további erőforrás az puffer kell, hogy az alkalmazás soha nem eléri a teljesítményszint korlátot. Ha növeli a számítási méret, segíthet, amelyek akkor jelentkeznek, amikor egy adatbázis nem rendelkezik elegendő a kérelmek feldolgozását hatékonyan, különösen a késésre érzékeny környezetekben power ügyfél látható hibák elkerülése érdekében. Ilyen például, egy adatbázis, amely támogatja az adatbázis-hívások eredményei alapján weblapok aggasztó alkalmazás.

Többi alkalmazástípus esetében előfordulhat, hogy ugyanazon a grafikonon másképp értelmezi. Például ha egy alkalmazás megpróbálja feldolgozni az Bérlista-adatokat minden nap, és rendelkezik ugyanabban a diagramban, "batch-feladat" modell az ilyen típusú előfordulhat, hogy tegye részletes P1 szintű számítási méretben. P1 szintű számítási mérete 100 dtu-k 200 dtu-k, a P2-höz képest méretű számítási rendelkezik. P1 szintű számítási mérete a P2 fele a teljesítmény számítási méret nyújt. Így a P2 CPU-használat 50 %-os egyenlő 100 %-os CPU-használat a P1 szintben foglalt. Ha az alkalmazás nem rendelkezik időtúllépések, előfordulhat, hogy nem számít, hogy a feladat 2 óráig, azaz 2,5 órát, alatt Ha ma kapja meg. Egy alkalmazás ebbe a kategóriába valószínűleg használhatja a P1 szintű számítási méretét. Használja ki, hogy nincsenek-e a nap, erőforrás-használat esetén alacsonyabb, így bármilyen "big Data típusú csúcs" lehet, hogy kerülnek a csatornák egy későbbi részében a nap folyamán ideig is igénybe vehet. P1 szintű számítási mérete az adott alkalmazás (és pénz megtakarítása), érdemes lehet mindaddig, amíg a feladat be tudja időben minden nap.

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
2. Arról, hogy a számítási feladatok megfelel-e a számítási méret kipróbálhatók egyes funkcióiról, az erőforrás-mérőszámok feltárásához kell: Processzor, olvasási, írási, feldolgozók száma és munkamenetek számát. Íme a módosított lekérdezés használatával **sys.resource_stats** jelentéséhez ezen erőforrás-mérőszámok átlagos és maximális értékeit:
   
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
3. Ezekkel az információkkal kapcsolatban az átlagos és maximális értékeket minden egyes erőforrás-metrika értékelhet arról, hogy a számítási feladatok illeszkedik a kiválasztott számítási mérete. Általában az átlagérték **sys.resource_stats** jó alapterv használata a célméretet szemben biztosítanak. Az elsődleges mérési meghajtó kell lennie. Például előfordulhat, hogy használni a Standard szintű szolgáltatáscsomagban S2 számítási méretű. Átlagos százalékos használata a Processzor- és i/o-olvasási és írási műveletek az alábbiakban 40 %-os, feldolgozók átlagos száma 50 alá van és munkamenetek átlagos száma nem éri a 200-as. A számítási feladat lehet, hogy illeszkedik az S1 szintű számítási mérete. Könnyen látható, hogy az adatbázis megfelel a feldolgozó és a munkamenet korlátok. Lásd: e adatbázis illeszkedik a Processzor, olvasási és írási oszd meg alacsonyabb számítási méret, az alacsonyabb dtu-k száma számítási méret az aktuális számítási méret dtu-k száma alapján, és az eredmény megszorozza 100:
   
    **S1 DTU-K / S2 DTU-K * 100 = 20 / 50 * 100 = 40**
   
    Ez a relatív teljesítménybeli különbség a két számítási méret százalékban. Az erőforrások használatával Ez a mennyiség nem haladja meg, ha a számítási feladatok a kisebb számítási méret előfordulhat, hogy illeszkedik. Azonban szüksége, tekintse meg az erőforrás-használati értékek összes tartományát, és meghatározhatja, százalékos, milyen gyakran az adatbázisok számítási lenne elférnek a kisebb számítási mérete. A következő lekérdezés kimenete a illesztése százalékos erőforrás dimenziónként, 40 %-kal, azt ebben a példában számított küszöbérték alapján:
   
        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Az adatbázisokra vonatkozó szolgáltatási szint alapján, eldöntheti, hogy a számítási feladatok illeszkedik a kisebb számítási mérete. Ha az adatbázis számítási feladat célja 99,9 százalékos rendelkezésre állást, és az előző lekérdezés visszaadja az értékek nagyobb, mint az összes három erőforrás dimenzió 99,9 %-os, a számítási feladatok nagy valószínűséggel a kisebb számítási méret illeszkedik.
   
    Hibaoldal illesztése százalékos is biztosít abba, hogy helyezze át a következő nagyobb számítási méretét felel meg a célkitűzés. Például userdb1 látható, a következő CPU-használat az elmúlt egy hét:
   
   | Átlagos CPU-százalék | Maximális Processzorhasználat százalékos |
   | --- | --- |
   | 24.5 |100.00 |
   
    Az átlagos Processzorhasználat, a számítási méret, amely akkor jól illeszkedik a számítási az adatbázis mérete legfeljebb negyedév szól. De a maximális érték jelenik meg, hogy az adatbázis eléri a korlátot, a számítási méret. Szeretne áthelyezni a következő nagyobb számítási méret? Keresse meg, hogyan lehet több alkalommal a számítási feladat eléri 100 %-os, és hasonlítsa össze az adatbázisban munkaterhelés cél.
   
        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Ha a lekérdezés visszaad egy értéket kisebb, mint 99,9 %-a három erőforrás dimenziók bármelyikét érdemes vagy magasabb szintű számítási méretére, vagy alkalmazás hangolási módszerek használatáról az SQL database terhelésének csökkentése érdekében.
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

## <a name="monitoring-query-performance"></a>A lekérdezési teljesítmény figyelése
Lassú vagy hosszú ideig futó lekérdezések jelentős rendszer-erőforrásokat használhatnak fel. Ez a szakasz bemutatja, hogyan dinamikus felügyeleti nézetek használatával néhány gyakori lekérdezési teljesítmény problémák észleléséhez. Egy régebbi, de továbbra is hasznos lehet a hibaelhárításhoz, a hivatkozás a [teljesítménybeli problémák elhárítása az SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) a cikk a Microsoft TechNet webhelyen.

### <a name="finding-top-n-queries"></a>Legfontosabb N lekérdezések keresése
Az alábbi példa szerint átlagos CPU-idő az első öt lekérdezés adatait adja vissza. Ebben a példában a lekérdezések a lekérdezés kivonata alapján összesíti az, hogy logikailag egyenértékű lekérdezések azok összesített erőforrás-használat szerint vannak csoportosítva.

```
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
    MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
    ((CASE statement_end_offset
        WHEN -1 THEN DATALENGTH(ST.text)
        ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
     FROM sys.dm_exec_query_stats AS QS
     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Letiltott lekérdezések figyelése
Lassú vagy hosszú ideig futó lekérdezések hozzájárulhatnak a túlzott erőforrás-használat és a letiltott lekérdezések következménye lehet. A blokkolás okát gyenge alkalmazástervezést, a hibás terveket, a hasznos indexeket, és így tovább hiánya lehet. A sys.dm_tran_locks nézet segítségével az aktuális zárolási tevékenység adatainak lekérése az Azure SQL Database-ben. Például kódját, lásd: [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) az SQL Server Online könyvekben.

### <a name="monitoring-query-plans"></a>Figyelési lekérdezési tervek
Egy hatékony lekérdezési terv is előfordulhat, hogy növelje CPU-felhasználás. Az alábbi példában a [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) nézet segítségével meghatározhatja, melyik lekérdezésben használja a legtöbb összegző Processzor.

```
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Lásd még
[Az SQL Database bemutatása](sql-database-technical-overview.md)

