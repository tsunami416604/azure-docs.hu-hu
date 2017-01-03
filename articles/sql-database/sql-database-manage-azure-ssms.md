---
title: "SQL Database-adatbázis kezelése az SSMS használatával | Microsoft Docs"
description: "Megtudhatja, hogyan kezelhetők az SQL Database-kiszolgálók és adatbázisok az SQL Server Management Studióval."
services: sql-database
documentationcenter: .net
author: stevestein
manager: jhubbard
editor: tysonn
ms.assetid: da6f3608-5993-4134-a497-ff2811e9f31f
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/29/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: d9ff74a49742fa77f5989b8b05e0567e3ca81dc5
ms.openlocfilehash: 89cb8827745b31b3a77b64d5cafd586957d60d30


---
# <a name="managing-azure-sql-database-using-sql-server-management-studio"></a>Az Azure SQL Database szolgáltatás felügyelete az SQL Server Management Studio használatával
> [!div class="op_single_selector"]
> * [Azure Portal](sql-database-manage-portal.md)
> * [SSMS](sql-database-manage-azure-ssms.md)
> * [PowerShell](sql-database-manage-powershell.md)
> 
> 

Az SQL Server Management Studio (SSMS) használatával felügyelhetők az Azure SQL Database-kiszolgálók és adatbázisok. Ez a témakör részletesen ismerteti az SSMS-sel elvégezhető gyakori feladatokat. Mielőtt hozzákezdene, hozzon létre egy kiszolgálót és egy adatbázist az Azure SQL Database-ben. További információ: [Az első SQL Database-adatbázis létrehozása](sql-database-get-started.md) és [Csatlakozás és lekérdezés az SSMS használatával](sql-database-connect-query-ssms.md).

> [!TIP]
> A kiszolgálók és a kiszolgálóalapú tűzfalak létrehozásáról, a kiszolgáló tulajdonságainak megtekintéséről, az SQL Server Management Studióval való csatlakozásról, a master adatbázis lekérdezéséről, a mintaadatbázis, illetve üres adatbázis létrehozásáról, az adatbázis tulajdonságainak lekérdezéséről, valamint az SQL Server Management Studióval való kapcsolódásról és a mintaadatbázis lekérdezéséről szóló oktatóanyagok az [Első lépéseket ismertető oktatóanyagban](sql-database-get-started.md) találhatók.
>

Az Azure SQL Database használatakor mindig ajánlott az SSMS legújabb verziójának letöltése. 

> [!IMPORTANT]
> Mindig az SSMS legújabb verzióját használja, mivel folyamatosan fejlesztjük és optimalizáljuk az Azure SQL Database legújabb frissítéseivel való együttműködésre. A legújabb verzió letöltése: [Az SQL Server Management Studio letöltése](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="create-and-manage-azure-sql-databases"></a>Azure SQL Database-adatbázisok létrehozása és kezelése
Ha csatlakozik a **master** adatbázishoz, létrehozhat új adatbázisokat a kiszolgálón, valamint módosíthat vagy elvethet meglévő adatbázisokat. Az alábbi lépések több gyakori adatbázis-kezelési feladat Management Studióval való elvégzésének módját ismertetik. A feladatok elvégzése előtt győződjön meg arról, hogy csatlakozik a **master** adatbázishoz a kiszolgáló első beállításakor létrehozott kiszolgálószintű fő bejelentkezéssel.

A Management Studióban egy lekérdezési ablak megnyitásához nyissa meg az Adatbázisok mappát, bontsa ki a **Rendszeradatbázisok** mappát, majd kattintson a jobb gombbal a **master** elemre, majd kattintson az **Új lekérdezés** elemre.

* A **CREATE DATABASE** utasítással hozhat létre egy adatbázist. További információ: [CREATE DATABASE (SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx). A következő utasítás egy **myTestDB** nevű adatbázist hoz lére, és megadja, hogy az egy Standard szintű, S0 kiadású adatbázis, az alapértelmezett 250 GB-os maximális mérettel.
  
      CREATE DATABASE myTestDB
      (EDITION='Standard',
       SERVICE_OBJECTIVE='S0');

Kattintson a **Végrehajtás** parancsra a lekérdezés futtatásához.

* Az **ALTER DATABASE** utasítás használatával módosíthat egy meglévő adatbázist, például ha módosítani kívánja az adatbázis nevét és kiadását. További információ: [ALTER DATABASE (SQL Database)](https://msdn.microsoft.com/library/ms174269.aspx). Az alábbi utasítás S1 kiadásúra módosítja az előző lépésben létrehozott adatbázist.
  
      ALTER DATABASE myTestDB
      MODIFY
      (SERVICE_OBJECTIVE='S1');
* A **DROP DATABASE** utasítással törölheti a meglévő adatbázisokat. További információ: [DROP DATABASE (SQL Database)](https://msdn.microsoft.com/library/ms178613.aspx). Az alábbi utasítás törli a **myTestDB** adatbázist, most azonban ne vesse el, mert a következő lépésben szükség lesz rá a bejelentkezések létrehozásához.
  
      DROP DATABASE myTestBase;
* A master adatbázis rendelkezik a **sys.databases** nézettel, amellyel az összes adatbázis részleteit megtekintheti. Az összes meglévő adatbázis megtekintéséhez futtassa a következő utasítást:
  
      SELECT * FROM sys.databases;
* Az SQL Database nem támogatja a **USE** utasítást az adatbázisok közötti váltáshoz. Ehelyett közvetlen kapcsolatot kell létesítenie a céladatbázissal.

> [!NOTE]
> Az adatbázisok létrehozására és módosítására használt számos Transact-SQL utasítást a saját kötegén belül kell futtatni, és nem csoportosíthatók más Transact-SQL utasításokkal. További információkért tekintse meg az egyes utasításokra vonatkozó információkat.
> 
> 

## <a name="create-and-manage-logins"></a>Bejelentkezések létrehozása és kezelése
A **master** adatbázis tartalmazza a bejelentkezéseket, illetve azt, hogy mely bejelentkezéseknek van engedélyük adatbázisok vagy más bejelentkezések létrehozására. A bejelentkezések kezeléséhez csatlakozzon a **master** adatbázishoz a kiszolgáló első beállításakor létrehozott kiszolgálószintű fő bejelentkezéssel. A **CREATE LOGIN**, **ALTER LOGIN**, vagy **DROP LOGIN** utasításokkal futtathat lekérdezéseket a master adatbázison, amely az egész kiszolgálón kezeli a bejelentkezéseket. További információ: [Adatbázisok és bejelentkezések kezelése az SQL Database-ben](http://msdn.microsoft.com/library/azure/ee336235.aspx). 

* A **CREATE LOGIN** utasítással hozhat létre kiszolgálószintű bejelentkezést. További információ: [CREATE LOGIN (SQL Database)](https://msdn.microsoft.com/library/ms189751.aspx). Az alábbi utasítás egy **login1** nevű bejelentkezést hoz létre. A **password1** jelszót helyettesítse tetszőleges jelszóval.
  
      CREATE LOGIN login1 WITH password='password1';
* A **CREATE USER** utasítással adatbázisszintű engedélyeket adhat ki. Minden bejelentkezést a **master** adatbázisban. Ha másik adatbázishoz való csatlakozáshoz van szüksége bejelentkezésre, akkor adatbázisszintű engedélyeket kell hozzárendelnie az adatbázishoz a **CREATE USER** utasítással. További információ: [CREATE USER (SQL Database)](https://msdn.microsoft.com/library/ms173463.aspx). 
* Ha a login1 bejelentkezéshez a **myTestDB** nevű adatbázisra vonatkozó engedélyeket kíván hozzárendelni, kövesse az alábbi lépéseket:
  
  1. Ha frissíteni kívánja az Object Explorert, hogy megtekinthesse a létrehozott **myTestDB** nevű adatbázist, kattintson a jobb gombbal a kiszolgáló nevére az Object Explorerben, majd kattintson a **Frissítés** parancsra.  
     
     Ha bontotta a kapcsolatot, a Fájl menü **Object Explorer csatlakoztatása** parancsával csatlakozhat újra.
  2. Kattintson a jobb gombbal a **myTestDB** adatbázisra, majd válassza az **Új lekérdezés** elemet.
  3. Futtassa a következő utasítást a myTestDB adatbázison egy **login1User** nevű adatbázis-felhasználó létrehozásához, amely a **login1** kiszolgálószintű bejelentkezésnek felel meg.
     
         CREATE USER login1User FROM LOGIN login1;
* Az **sp\_addrolemember** tárolt eljárással adhatja meg a felhasználói fióknak a megfelelő szintű engedélyeket az adatbázisban. További információk: [sp_addrolemember (Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx). Az alábbi utasítás csak olvasási engedélyeket biztosít **login1User** felhasználónak az adatbázisban azzal, hogy a **login1User** felhasználót hozzáadja a **db\_datareader** szerepkörhöz.
  
      exec sp_addrolemember 'db_datareader', 'login1User';    
* Az **ALTER LOGIN** utasítás használatával módosíthat egy meglévő bejelentkezést, például ha módosítani kívánja a bejelentkezés jelszavát. További információ: [ALTER LOGIN (SQL Database)](https://msdn.microsoft.com/library/ms189828.aspx). Az **ALTER LOGIN** utasítást a **master** adatbázison kell futtatni. Váltson vissza arra a lekérdezési ablakra, amelyik az adott adatbázishoz kapcsolódik. Az alábbi utasítás új jelszó létrehozásával módosítja a **login1** bejelentkezést. A **newPassword** jelszót helyettesítse tetszőleges jelszóval, az **oldPassword** jelszót pedig a bejelentkezéshez tartozó aktuális jelszóval.
  
      ALTER LOGIN login1
      WITH PASSWORD = 'newPassword'
      OLD_PASSWORD = 'oldPassword';
* A **DROP LOGIN** utasítással törölhet egy meglévő bejelentkezést. A bejelentkezés kiszolgálószintű törlésével az adatbázis többi hozzá tartozó felhasználói fiókját is törli. További információ: [DROP DATABASE (SQL Database)](https://msdn.microsoft.com/library/ms178613.aspx). A **DROP LOGIN** utasítást a **master** adatbázison kell futtatni. Az utasítás törli a **login1** bejelentkezést.
  
      DROP LOGIN login1;
* A master adatbázis rendelkezik a **sys.sql\_bejelentkezések** nézettel, amellyel az összes bejelentkezést megtekintheti. Az összes meglévő bejelentkezés megtekintéséhez futtassa a következő utasítást:
  
      SELECT * FROM sys.sql_logins;

## <a name="monitor-sql-database-using-dynamic-management-views"></a>SQL Database monitorozása a dinamikus felügyeleti nézetek használatával
Az SQL Database számos dinamikus felügyeleti nézetet támogat, amelyekkel monitorozhatók az egyes adatbázisok. Az alábbiakban néhány példát láthat az ilyen nézetekkel lekérhető monitorozási adatokra. Részletes információ és további használati példák: [Az SQL Database monitorozása a dinamikus felügyeleti nézetekkel](https://msdn.microsoft.com/library/azure/ff394114.aspx).

* A dinamikus felügyeleti nézetek lekérdezéséhez **VIEW DATABASE STATE** engedélyek szükségesek. Ha a **VIEW DATABASE STATE** engedélyt kívánja hozzárendelni az adatbázis egy adott felhasználójához, akkor csatlakozzon az adatbázishoz, majd futtassa rajta az alábbi utasítást:
  
      GRANT VIEW DATABASE STATE TO login1User;
* Számítsa ki az adatbázis méretét a **sys.dm\_db\_partition\_stats** nézettel. A **sys.dm\_db\_partition\_stats** nézet az adatbázis minden partíciójának oldalszám- és sorszámadatait adja vissza, amelyek alapján kiszámítható az adatbázis mérete. Az alábbi lekérdezés az adatbázis megabájtban megadott méretét adja vissza:
  
      SELECT SUM(reserved_page_count)*8.0/1024
      FROM sys.dm_db_partition_stats;   
* A **sys.dm\_exec\_connections** és a **sys.dm\_exec\_sessions** nézetekkel az aktuális felhasználói kapcsolatokról és az adatbázishoz kapcsolódó belső feladatokról kérhet le információt. Az alábbi lekérdezés az aktuális kapcsolatról ad vissza információkat:
  
      SELECT
          e.connection_id,
          s.session_id,
          s.login_name,
          s.last_request_end_time,
          s.cpu_time
      FROM
          sys.dm_exec_sessions s
          INNER JOIN sys.dm_exec_connections e
            ON s.session_id = e.session_id;
* A **sys.dm\_exec\_query\_stats** nézetet használhatja a gyorsítótárazott lekérdezési tervek összesített teljesítménystatisztikáinak lekérdezéséhez. Az alábbi lekérdezés a processzorhasználati idő szerinti első öt lekérdezés információit adja vissza.
  
      SELECT TOP 5 query_stats.query_hash AS "Query Hash",
          SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
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




<!--HONumber=Jan17_HO1-->


