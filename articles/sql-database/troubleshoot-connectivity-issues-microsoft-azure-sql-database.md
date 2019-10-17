---
title: A Microsoft Azure SQL Database kapcsolódási problémáinak elhárítása | Microsoft Docs
description: Ismerteti, hogyan lehet elhárítani a Azure SQL Database kapcsolódási problémáit.
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: 9de6d85e1fc54d60f999cfa18665067b3998a432
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390660"
---
# <a name="troubleshooting-connectivity-issues-with-microsoft-azure-sql-database"></a>A Microsoft Azure SQL Database kapcsolódási problémáinak elhárítása

Hibaüzeneteket kap, amikor meghiúsul az Azure SQL Database-hez való kapcsolódás. Ezeket a kapcsolódási problémákat a SQL Azure adatbázis-újrakonfigurálás, a tűzfal beállításai, a kapcsolat időtúllépése vagy a helytelen bejelentkezési adatok okozhatják. Emellett, ha a Azure SQL Database erőforrásokra vonatkozó maximális korlátot eléri, nem tud kapcsolódni a Azure SQL Databasehoz.

## <a name="error-40613-database--x--on-server--y--is-not-currently-available"></a>40613-es hiba: a (z) < y > kiszolgálón < x > jelenleg nem érhető el

**Részletes hiba**

``40613: Database <DBname> on server < server name > is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of '< Tracing ID >'.``

A probléma megoldása:

1. Győződjön meg arról, hogy az összes ismert leálláshoz a [Microsoft Azure szolgáltatás irányítópultja](https://status.azure.com/status) van. 
2. Ha nincsenek ismert kimaradások, lépjen a [Microsoft Azure támogatási webhelyére](http://azure.microsoft.com/support/options) , és nyisson meg egy támogatási esetet.

További információ: ["a kiszolgáló adatbázisa jelenleg nem érhető el" hibaüzenettel kapcsolatos hiba](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-common-connection-issues#troubleshoot-transient-errors).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server"></a>Hálózattal kapcsolatos vagy példányra jellemző hiba történt a SQL Server kapcsolatának létrehozásakor

A probléma akkor fordul elő, ha az alkalmazás nem tud csatlakozni a kiszolgálóhoz.

A probléma megoldásához próbálja meg a [gyakori kapcsolódási problémák elhárítása](#steps-to-fix-common-connection-issues) című szakaszban ismertetett lépéseket (a sorrend szerint).

## <a name="the-server-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>A kiszolgáló nem található vagy nem érhető el (hiba 26, 40, 10053)

### <a name="error-26-error-locating-serverinstance-specified"></a>26. hiba: a megadott kiszolgáló/példány keresésekor hiba történt

**Részletes hiba**

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

### <a name="error-40-could-not-open-a-connection-to-sql-server"></a>40-es hiba: nem sikerült csatlakozni a (z) SQL Server

**Részletes hiba**

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>10053-es hiba: átviteli szintű hiba történt a kiszolgáló eredményeinek fogadásakor

**Részletes hiba**

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Ezek a hibák azért fordulnak elő, mert az alkalmazás nem tud csatlakozni a kiszolgálóhoz.

A probléma megoldásához próbálja meg a [gyakori kapcsolódási problémák elhárítása](#steps-to-fix-common-connection-issues) című szakaszban ismertetett lépéseket (a sorrend szerint).

## <a name="cannot-connect-to-servername-due-to-firewall-issues"></a>Tűzfallal kapcsolatos problémák miatt nem lehet csatlakozni a <servername>-hoz

### <a name="error-40615-cannot-connect-to--servername-"></a>40615-es hiba: nem lehet csatlakozni < servername >

A probléma megoldásához [konfigurálja a tűzfal beállításait SQL Database a Azure Portal használatával.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>5\. hiba: nem lehet csatlakozni a (z) < servername >

A probléma megoldásához ellenőrizze, hogy a 1433-es port nyitva van-e a kimenő kapcsolatok számára az ügyfél és az Internet közötti összes tűzfalon.

További információ: [a Windows tűzfal konfigurálása a SQL Server hozzáférésének engedélyezéséhez](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Nem lehet bejelentkezni a kiszolgálóra (hibák 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>A következő felhasználó bejelentkezése sikertelen volt: "< Felhasználónév >"

**Részletes hiba**

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

A probléma megoldásához forduljon a szolgáltatás rendszergazdájához, és adjon meg egy érvényes SQL Server felhasználónevet és jelszót.

A szolgáltatás rendszergazdája általában a következő lépésekkel adhatja hozzá a bejelentkezési hitelesítő adatokat:

1. Jelentkezzen be a kiszolgálóra SQL Server Management Studio (SSMS) használatával.
2. Futtassa a következő SQL-lekérdezést, hogy meggyőződjön arról, hogy a bejelentkezési név le van-e tiltva:

   ```
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Ha a név le van tiltva, engedélyezze a következő utasítással: 

   ```
   Alter login <User name> enable
   ```

4. Ha az SQL-bejelentkezési Felhasználónév nem létezik, hozza létre a következő lépésekkel:

   1. A SSMS kattintson duplán a **Biztonság** elemre a kibontásához. 
   2. Kattintson a jobb gombbal a **Logins** (Bejelentkezések) elemre, majd válassza a **New login** (Új bejelentkezés) lehetőséget. 
   3. A generált parancsfájl helyőrzővel, szerkessze és futtassa a következő SQL-lekérdezést:
 
   ```
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```       
5. Kattintson duplán a **Database** (Adatbázis) elemre. 
6. Válassza ki azt az adatbázist, amelyhez engedélyt kíván adni a felhasználónak.
7. Kattintson duplán a **Security** (Biztonság) elemre. 
8. Kattintson a jobb gombbal a **Users** (Felhasználók) elemre, majd válassza a **New User** (Új felhasználó) lehetőséget. 
9. A generált parancsfájl helyőrzővel, szerkessze és futtassa a következő SQL-lekérdezést: 

   ```
   CREATE USER <user_name, sysname, user_name>          
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   
   -- Add user to the database owner role

   EXEC sp_addrolemember N’db_owner’, N’<user_name, sysname, user_name>’
   GO
   ```
   
   > [!NOTE]
   > A `sp_addrolemember` segítségével adott felhasználókat adott adatbázis-szerepkörökhöz is leképezheti.

További információ: [adatbázisok és bejelentkezések kezelése Azure SQL Databaseban](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="connection-timeout-expired-errors"></a>A kapcsolat időtúllépése lejárt hibák

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System. reSqlClient. SqlException (0x80131904): a kapcsolat időtúllépése lejárt

**Részletes hiba**

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System. reSqlClient. SqlException (0x80131904): időtúllépés lejárt

**Részletes hiba**

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System. adat. Entity. Core. EntityException: az alapul szolgáló szolgáltató nem sikerült a megnyitáskor

**Részletes hiba**

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Nem lehet kapcsolódni < kiszolgálónévhez >

**Részletes hiba**

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Ezek a kivételek a kapcsolatok vagy a lekérdezési problémák miatt fordulhatnak elő. Annak ellenőrzéséhez, hogy ezt a hibát a kapcsolódási problémák okozták-e, tekintse meg azt, [hogy a hibát a kapcsolódási probléma okozta](#confirm-whether-an-error-is-caused-by-a-connectivity-issue)-e.

A kapcsolat időtúllépése azért fordul elő, mert az alkalmazás nem tud csatlakozni a kiszolgálóhoz. A probléma megoldásához próbálja meg a [gyakori kapcsolódási problémák elhárítása](#steps-to-fix-common-connection-issues) című szakaszban ismertetett lépéseket (a sorrend szerint).

## <a name="transient-errors-errors-40197-40545"></a>Átmeneti hibák (hibák 40197, 40545)

### <a name="error-40197-the-service-has-encountered-an-error-processing-your-request-please-try-again-error-code--code-"></a>40197-es hiba: a szolgáltatás hibát észlelt a kérelem feldolgozásakor. Kérjük, próbálkozzon újból. Hibakód < kód >

Ez a probléma akkor fordul elő, ha átmeneti hiba történt egy újrakonfigurálás vagy feladatátvétel során a háttérben.

A probléma megoldásához várjon egy rövid időszakot, és próbálkozzon újra. Ha nem szűnik meg a probléma, nincs szükség támogatási esetre.

Ajánlott eljárásként győződjön meg arról, hogy az újrapróbálkozási logika van érvényben. Az újrapróbálkozási logikával kapcsolatos további információkért lásd: [átmeneti hibák és kapcsolódási hibák elhárítása SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

## <a name="connection-terminated-because-of-a-system-defined-limit"></a>A csatlakoztatás rendszer által meghatározott korlát miatt megszakadt

### <a name="error-10928-resource-id-d"></a>10928-es hiba: erőforrás-azonosító:% d

**Részletes hiba**

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

A probléma megkerüléséhez próbálkozzon az alábbi módszerek egyikével:

* Ellenőrizze, hogy vannak-e hosszan futó lekérdezések.

  > [!NOTE]
  > Ez egy minimalista megközelítés, amely esetleg nem oldja meg a problémát.

  1. Futtassa a következő SQL-lekérdezést, hogy ellenőrizze a [sys. DM _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) nézetét, hogy megjelenjenek-e a blokkoló kérelmek:

             ```
             SELECT * FROM dm_exec_requests
             ```

  2. A fej-blokkoló **bemeneti pufferének** meghatározása.
  3. A fej-blokkoló lekérdezésének hangolása.

    Részletes hibaelhárítási eljárás: a [lekérdezés a felhőben fut?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

* Ha az adatbázis folyamatosan eléri a korlátot a blokkoló és a hosszan futó lekérdezések kezelése előtt, érdemes lehet frissíteni az új előzetes verzióra (például a [standard vagy a Premium kiadásra](https://azure.microsoft.com/pricing/details/sql-database/)).

További információ a SQL Database díjszabási lehetőségeiről: [Azure SQL Database díjszabása](https://azure.microsoft.com/pricing/details/sql-database/single/).

További információ a dinamikus felügyeleti nézetekről: [rendszerdinamikus felügyeleti nézetek](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

További információ erről a hibaüzenetről: [Azure SQL Database Server erőforrás-korlátainak SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-10929-resource-id-1"></a>10929-es hiba: erőforrás-azonosító: 1

**Részletes hiba**

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

További információ erről a hibáról: [SQL Database-ügyfélprogramok hibaüzenetei](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages).

### <a name="error-40501-the-service-is-currently-busy"></a>40501-es hiba: a szolgáltatás jelenleg foglalt

**Részletes hiba**

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Ez egy hajtómű-szabályozási hiba, amely azt jelzi, hogy túllépik az erőforrás-korlátozásokat.

Az erőforrás-korlátokkal kapcsolatos további információkért lásd: az [adatbázis-kiszolgáló erőforrás-korlátai](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>40544-es hiba: az adatbázis elérte a mérete kvótáját

**Részletes hiba**

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Ez a hiba akkor fordul elő, amikor az adatbázis elérte a méretre vonatkozó kvótát.

A következő lépések segíthetnek a probléma megkerülésében vagy további lehetőségek megadásában:

1. Az Azure Portal irányítópultjának használatával vizsgálja meg az adatbázis aktuális méretét.

   > [!NOTE]
   > A következő SQL-lekérdezés futtatásával megállapíthatja, hogy mely táblák használják a legtöbb helyet, ezért lehetséges a kitakarításra alkalmas jelöltek futtatása:

   ```
   SELECT o.name,
    a.SUM(p.row_count) AS 'Row Count',
    b.SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. Ha az aktuális méret nem haladja meg a kiadásban támogatott maximális méretet, az ALTER DATABASE paranccsal növelheti a MAXSIZE beállítást. 
3. Ha az adatbázis már elmúlt a maximálisan támogatott méret a kiadásban, próbálkozzon az alábbi lépésekkel:
   - Normál adatbázis-karbantartási tevékenységek végrehajtása. Például megtisztíthatja a nemkívánatos adatmennyiséget a csonkítás/törlés paranccsal, vagy áthelyezheti az adatvesztést SQL Server Integration Services (SSIS) vagy a tömeges másolási program (BCP) segédprogram használatával.
   - Particionálja vagy törölje az adatvesztést, az indexek eldobását, vagy a lehetséges megoldásokról a dokumentációban tájékozódhat.

   - Az adatbázis-méretezéssel kapcsolatban lásd: [önálló adatbázis-erőforrások méretezése](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale) és [rugalmas készlet erőforrásainak méretezése](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale).

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>40549-es hiba: a munkamenet leáll, mert hosszú ideig futó tranzakció van

**Részletes hiba**

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Ha többször is megtapasztalja ezt a hibát, próbálja meg elhárítani a problémát a következő lépésekkel: 

1. Ellenőrizze a sys. DM _exec_requests nézetet, hogy megjelenjenek-e a total_elapsed_time oszlop magas értékkel rendelkező nyitott munkamenetei. A következő SQL-szkript futtatásával végezze el az ellenőrzés végrehajtását:

   ```
   SELECT * FROM dm_exec_requests
   ```
2. A hosszú ideig futó lekérdezés bemeneti pufferének meghatározása. 
3. A lekérdezés finomhangolása.

A lekérdezések kötegelt feldolgozását is érdemes figyelembe venni. A kötegelt feldolgozással kapcsolatos további információkért lásd: [a Batch használata az SQL Database alkalmazások teljesítményének javításához](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance).

Részletes hibaelhárítási eljárás: a [lekérdezés a felhőben fut?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>40551-es hiba: a munkamenet túlzott TEMPDB-használat miatt megszakadt

**Részletes hiba**

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

A probléma megkerüléséhez kövesse az alábbi lépéseket:

1. Módosítsa a lekérdezéseket, hogy csökkentse az ideiglenes tábla területének használatát. 
2. Ha már nincs szükség rájuk, dobja el az ideiglenes objektumokat. 
3. Táblázatok csonkítása vagy a nem használt táblák eltávolítása.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>40552-es hiba: a munkamenet a tranzakciós napló túlzott kihasználtsága miatt megszakadt

**Részletes hiba**

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

A hiba elhárításához próbálkozzon a következő módszerekkel:

* A probléma INSERT, Update vagy DELETE művelet miatt fordulhat elő. A kötegelt feldolgozás végrehajtásával vagy a több kisebb tranzakcióra való felosztásával próbálja meg csökkenteni a azonnal üzemeltetett sorok számát.
* A probléma az index újraépítési műveletei miatt fordulhat elő. A probléma megkerüléséhez győződjön meg arról, hogy a táblázatban szereplő sorok száma * (bájt + 80-ben frissített mező átlagos mérete) < 2 gigabájt (GB).

  > [!NOTE]
  > Az indexek újraépítése esetén a frissített mező átlagos méretét az indexek átlagos méretével kell helyettesíteni.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>40553-es hiba: a munkamenet túl sok memóriahasználat miatt megszakadt

**Részletes hiba**

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

A probléma megkerüléséhez próbálja meg optimalizálni a lekérdezést.

Részletes hibaelhárítási eljárás: a [lekérdezés a felhőben fut?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).


### <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>A bejelentkezés által kért "Master" adatbázis nem nyitható meg. A bejelentkezés sikertelen.

Ez a probléma azért fordul elő, mert a fióknak nincs engedélye a Master adatbázis elérésére. Alapértelmezés szerint azonban SQL Server Management Studio (SSMS) megpróbál csatlakozni a Master adatbázishoz.

A probléma megoldásához kövesse az alábbi lépéseket:

1. A SSMS bejelentkezési képernyőjén válassza a **Beállítások**, majd a **kapcsolatok tulajdonságai**elemet. 
2. A **Kapcsolódás az adatbázishoz** mezőben adja meg a felhasználó alapértelmezett adatbázisának nevét az alapértelmezett bejelentkezési adatbázisként, majd válassza a **Kapcsolódás**lehetőséget.

   ![Kapcsolat tulajdonságai](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Erősítse meg, hogy hiba történt-e egy csatlakozási probléma miatt

Annak ellenőrzéséhez, hogy hiba történt-e egy csatlakozási probléma miatt, tekintse át a verem nyomkövetését olyan keretek esetében, amelyek a következőhöz hasonló kapcsolat megnyitására irányuló hívásokat jelenítenek meg (jegyezze fel a **SqlConnection** osztályra mutató hivatkozást):

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Ha a kivételt a lekérdezési problémák váltották ki, az alábbihoz hasonló hívási verem jelenik meg (jegyezze fel a **SqlCommand** osztályra mutató hivatkozást). Ebben a helyzetben [a lekérdezések finomhangolása](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```
A teljesítmény finomhangolásával kapcsolatos további útmutatásért tekintse meg a következő forrásokat:

* [Az Azure SQL indexek és statisztikák karbantartása](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [A lekérdezés teljesítményének manuális finomhangolása Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [Teljesítmény-Azure SQL Database figyelése dinamikus felügyeleti nézetek használatával](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [A lekérdezési tároló üzemeltetése Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)


## <a name="steps-to-fix-common-connection-issues"></a>A gyakori kapcsolódási problémák elhárításának lépései

1. Győződjön meg arról, hogy a TCP/IP engedélyezve van-e az alkalmazáskiszolgáló ügyfél-protokollja számára. További információ: az [ügyféloldali protokollok konfigurálása](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols). Azokon az alkalmazás-kiszolgálókon, amelyeken nincs telepítve SQL Server eszköz, ellenőrizze, hogy a TCP/IP engedélyezve van-e a **Cliconfg. exe** (SQL Server Client Network segédprogram) futtatásával. 
2. Ellenőrizze az alkalmazás kapcsolódási karakterláncát, és győződjön meg arról, hogy megfelelően van-e konfigurálva. Győződjön meg például arról, hogy a kapcsolatok karakterlánca a megfelelő portot (1433) és teljes kiszolgálónevet adja meg.
Lásd: [SQL Server-kapcsolatok adatainak beolvasása](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information).
3. Próbálja meg növelni a kapcsolat időtúllépési értékét. Legalább 30 másodperces kapcsolati időtúllépés használatát javasoljuk. 
4. Tesztelje az alkalmazáskiszolgáló és az Azure SQL Database közötti kapcsolatot a [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms), a UDL-fájl, a ping vagy a Telnet használatával. További információ: [hibaelhárítás SQL Server csatlakozási problémák](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) és [diagnosztika a kapcsolódási problémákhoz](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics).

   > [!NOTE]
   > Hibaelhárítási lépésként egy másik ügyfélszámítógépen is tesztelheti a kapcsolatot.

5. Ajánlott eljárásként győződjön meg arról, hogy az újrapróbálkozási logika van érvényben. Az újrapróbálkozási logikával kapcsolatos további információkért lásd: [átmeneti hibák és kapcsolódási hibák elhárítása SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

Ha ezek a lépések nem oldják meg a problémát, próbálkozzon a további adatok gyűjtésével, majd forduljon az ügyfélszolgálathoz. Ha az alkalmazás egy felhőalapú szolgáltatás, engedélyezze a naplózást. Ez a lépés a hiba UTC időbélyegzőjét adja vissza. Emellett a SQL Azure visszaadja a nyomkövetési azonosítót. A [Microsoft ügyfél-támogatási szolgálata](http://azure.microsoft.com/support/options/) ezeket az információkat használhatja. 

A naplózás engedélyezésével kapcsolatos további információkért lásd: a [diagnosztikai naplózás engedélyezése a Azure app Service alkalmazásokban](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/).

**Kapcsolódó dokumentumok**

* [Azure SQL-kapcsolat architektúrája](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)<br>
* [A Azure SQL Database és az adatraktár hálózati hozzáférés-vezérlése](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)
