---
title: A Microsoft Azure SQL Database kapcsolódási problémáinak elhárítása | Microsoft Docs
description: A Microsoft Azure SQL Database kapcsolódási problémáinak elhárítása
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: 3717b36e37f7d9cca206b5a9e8e3cded50393748
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974421"
---
# <a name="troubleshooting-connectivity-issues-with-microsoft-azure-sql-database"></a>A Microsoft Azure SQL Database kapcsolódási problémáinak elhárítása

Hibaüzeneteket kap, amikor meghiúsul az Azure SQL Database-hez való kapcsolódás. A kapcsolat problémáit az SQL Azure-adatbázis újrakonfigurálása, a tűzfalbeállítások, a kapcsolat időtúllépése vagy a helytelen bejelentkezési információk okozhatják. Emellett, ha a Azure SQL Database erőforrásokra vonatkozó maximális korlátot eléri, nem tud kapcsolódni a Azure SQL Databasehoz.

## <a name="error-40613-database--x--on-server--y--is-not-currently-available"></a>40613-as hiba: A (z) < y > kiszolgálón < x > jelenleg nem érhető el

**Részletes hiba**

``40613: Database <DBname> on server <server name> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of '<Tracing ID>'.``

A probléma megoldása:

1. Győződjön meg arról, hogy az összes ismert leálláshoz a [Microsoft Azure szolgáltatás irányítópultja](https://status.azure.com/status) van. 
2. Ha nincsenek ismert kimaradások, lépjen a [Microsoft Azure támogatási webhelyére](http://azure.microsoft.com/support/options) , és nyisson meg egy támogatási esetet.

További információ: ["a kiszolgáló adatbázisa jelenleg nem érhető el" hiba történt](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-common-connection-issues#troubleshoot-transient-errors).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server"></a>Hálózattal kapcsolatos vagy példányra jellemző hiba történt a SQL Server kapcsolatának létrehozásakor

A probléma oka, hogy az alkalmazás nem tud csatlakozni a kiszolgálóhoz.

A probléma megoldásához próbálja meg az alábbi szakasz lépéseit (sorrendben) a **gyakori kapcsolódási problémák elhárításához szükséges lépések**című részben.

## <a name="the-server-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>A kiszolgáló nem található vagy nem érhető el (hiba 26, 40, 10053)

### <a name="error-26-error-locating-serverinstance-specified"></a>26. hiba: Hiba történt a megadott kiszolgáló/példány keresésekor

**Részletes hiba**

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

### <a name="error-40-could-not-open-a-connection-to-sql-server"></a>40-es hiba: Nem lehetett megnyitni a SQL Serverhoz való kapcsolódást

**Részletes hiba**

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>10053-es hiba: Átviteli szintű hiba történt a kiszolgáló eredményeinek fogadása során.

**Részletes hiba**

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Ezek a problémák azért fordulnak elő, mert az alkalmazás nem tud csatlakozni a kiszolgálóhoz.

A probléma megoldásához próbálja meg az alábbi szakasz lépéseit (sorrendben) a **gyakori kapcsolódási problémák elhárításához szükséges lépések**című részben.

## <a name="cannot-connect-to--servername--due-to-firewall-issues"></a>Tűzfallal kapcsolatos problémák miatt nem lehet csatlakozni a < servername >hoz

### <a name="error-40615-cannot-connect-to--servername-"></a>40615-ös hiba: Nem lehet csatlakozni a < servername >

A probléma megoldásához [konfigurálja a tűzfal beállításait SQL Database a Azure Portal használatával.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>5\. hiba: Nem lehet csatlakozni a < servername >

A probléma megoldásához ellenőrizze, hogy a 1433-es port nyitva van-e a kimenő kapcsolatok számára az ügyfél és az Internet közötti összes tűzfalon. 

További információ: [a Windows tűzfal konfigurálása a SQL Server hozzáférésének engedélyezéséhez](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="unable-to-login-to-the-server-errors-18456-40531"></a>Nem lehet bejelentkezni a kiszolgálóra (hibák 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>A következő felhasználó bejelentkezése sikertelen volt: "< Felhasználónév >"

**Részletes hiba**

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

A probléma megoldásához forduljon a szolgáltatás rendszergazdájához, és adjon meg egy érvényes SQL-felhasználónevet és-jelszót.

A szolgáltatás rendszergazdája általában a következő lépésekkel adhatja meg a bejelentkezési adatokat:

1. Jelentkezzen be a kiszolgálóra SQL Server Management Studio (SSMS) használatával.
2. A következő SQL-lekérdezéssel ellenőrizze, hogy a bejelentkezési név nincs-e letiltva:

   ```
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Ha a név le van tiltva, engedélyezze a következő utasítással: 

   ```
   Alter login <User name> enable
   ```

4. Ha az SQL-bejelentkezési Felhasználónév nem létezik, hozza létre a SSMS használatával:

   1. Dupla kattintással bontsa ki a **Security** (Biztonság) elemet. 
   2. Kattintson a jobb gombbal a **Logins** (Bejelentkezések) elemre, majd válassza a **New login** (Új bejelentkezés) lehetőséget. 
   3. A létrehozott, helyőrzőkkel kitöltött szkriptben szerkessze és futtassa a következő SQL-lekérdezést:
 
   ```
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```       
5. Kattintson duplán a **Database** (Adatbázis) elemre. 
6. Kattintson arra az adatbázisra, amelyhez a felhasználónak jogosultságot szeretne adni.
7. Kattintson duplán a **Security** (Biztonság) elemre. 
8. Kattintson a jobb gombbal a **Users** (Felhasználók) elemre, majd válassza a **New User** (Új felhasználó) lehetőséget. 
9. A létrehozott, helyőrzőkkel kitöltött szkriptben szerkessze és futtassa a következő SQL-lekérdezést: 

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

További információkért lásd az [Adatbázisok és bejelentkezések Azure SQL Database-ben történő kezelésével foglalkozó](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins) cikket.

## <a name="connection-timeout-expired-errors"></a>A kapcsolat időtúllépése lejárt hibák

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System. reSqlClient. SqlException (0x80131904): A kapcsolat időtúllépése lejárt.

**Részletes hiba**

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System. reSqlClient. SqlException (0x80131904): Időtúllépés lejár.

**Részletes hiba**

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System. Rea. Entity. Core. EntityException: Az alapul szolgáló szolgáltató megnyitása megtörtént.

**Részletes hiba**

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Nem lehet kapcsolódni < kiszolgálónévhez >. ""

**Részletes hiba**

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Ezek a kivételek a kapcsolatok vagy a lekérdezési problémák miatt fordulhatnak elő. Ennek a hibának a megerősítéséhez a kapcsolódási problémák miatt a következő címmel ellenőrizze, **hogy a hiba a kapcsolódási probléma miatt történt-e**:

A kapcsolat időtúllépése azért fordul elő, mert az alkalmazás nem tud csatlakozni a kiszolgálóhoz. A probléma megoldásához próbálja meg az alábbi szakasz lépéseit (sorrendben) a **gyakori kapcsolódási problémák elhárításához szükséges lépések**című részben.

## <a name="transient-errors-errors-40197-40545"></a>Átmeneti hibák (hibák 40197, 40545)

### <a name="error-40197-the-service-has-encountered-an-error-processing-your-request-please-try-again-error-code--code-"></a>40197-es hiba: A szolgáltatás hibát észlelt a kérelem feldolgozásakor. Kérjük, próbálkozzon újból. Hibakód < kód >

Ez a probléma akkor fordul elő, ha átmeneti hiba történt a háttérbeli újrakonfigurálás/feladatátvétel során.

A probléma megoldásához várjon egy rövid időszakot, és próbálkozzon újra. Ha a probléma továbbra is fennáll, nincs szükség támogatási esetre.

Ajánlott eljárásként győződjön meg arról, hogy az újrapróbálkozási logika van érvényben. Az újrapróbálkozási logikával kapcsolatos további információkért lásd: [átmeneti hibák és kapcsolódási hibák elhárítása SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

## <a name="connection-terminated-due-to-hitting-some-system-defined-limit"></a>A kapcsolódás megszakadt, mert valamilyen rendszerszintű korlátot adott meg

### <a name="error-10928-resource-id-d"></a>10928-as hiba: Erőforrás-azonosító:% d.

**Részletes hiba**

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

A probléma megkerüléséhez próbálkozzon az alábbi módszerek egyikével:

* Ellenőrizze, hogy vannak-e hosszan futó lekérdezések:

  > [!NOTE]
  > Ez egy minimalista megközelítés, amely esetleg nem feltétlenül oldja meg a problémát.

  1. A következő SQL-lekérdezés végrehajtásával tekintse meg a [sys. DM _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) nézetet a blokkoló kérelmek megtekintéséhez:

     ```
     SELECT * FROM dm_exec_requests
     ```

  2. A fej-blokkoló **bemeneti pufferének** meghatározása.
  3. A fej-blokkoló lekérdezésének hangolása.

    Részletes hibaelhárítási eljárás: a [lekérdezés a felhőben fut?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

* Ha az adatbázis a blokkolás és a hosszan futó lekérdezések kezelése előtt folyamatosan eléri a korlátot, érdemes lehet az új előzetes verzióra (például a [standard vagy a prémium szintű kiadásokra](https://azure.microsoft.com/pricing/details/sql-database/)) frissíteni.

A SQL Database díjszabási lehetőségekkel kapcsolatos további információkért tekintse át [Azure SQL Database díjszabását](https://azure.microsoft.com/pricing/details/sql-database/single/).

További információ a dinamikus felügyeleti nézetekről: [Rendszerdinamikus felügyeleti nézetek](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

További információ erről a hibaüzenetről: [Azure SQL Database Server erőforrás-korlátainak SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-10929-resource-id-1"></a>10929-es hiba: Erőforrás-azonosító: 1.

**Részletes hiba**

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

További információ erről a hibáról: [SQL Database-ügyfélprogramok hibaüzenetei](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages)

### <a name="error-40501-the-service-is-currently-busy"></a>40501-es hiba: A szolgáltatás jelenleg foglalt

**Részletes hiba**

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Ez egy hajtómű-szabályozási hiba, amely azt jelzi, hogy túllépik az erőforrás-korlátozásokat.

Az erőforrás-korlátokkal kapcsolatos további információkért lásd: az [adatbázis-kiszolgáló erőforrás-korlátai](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-40544--the-database-has-reached-its-size-quota"></a>40544-es hiba: Az adatbázis elérte a méretre vonatkozó kvótát

**Részletes hiba**

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Ez a hiba akkor fordul elő, amikor az adatbázis elérte a méretre vonatkozó kvótát.

A következő lépések segíthetnek a probléma megoldásában, vagy további lehetőségeket is megadhat.

1. Az Azure Portal irányítópultjának használatával vizsgálja meg az adatbázis aktuális méretét.

   > [!NOTE]
   > A következő SQL-lekérdezéssel állapíthatja meg, hogy mely táblák használják a legtöbb helyet és potenciális jelölteket a tisztításhoz:

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
3. Ha az adatbázis mérete már meghaladja a maximálisan támogatott méretet a kiadásban, az alábbi lépések egyikét hajthatja végre:
   1. A normál adatbázis-karbantartási tevékenységek végrehajtása (a nemkívánatos adatok megtisztítása a csonkítás/törlés stb. használatával, vagy az adatok áthelyezése a SSIS, a BCP-vel stb. használatával)
   2. Particionálja vagy törölje az adatvesztést, az indexek eldobását, vagy a lehetséges megoldásokról a dokumentációban tájékozódhat. 
   
   *  Az adatbázis-méretezéssel kapcsolatban lásd: [önálló adatbázis-erőforrások méretezése](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale) és [rugalmas készlet erőforrásainak méretezése](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale).

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>40549-es hiba: A munkamenet meg lett szakítva, mert hosszú ideig futó tranzakció van.

**Részletes hiba**

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Ha többször is megjelenik ez a hibaüzenet, próbálkozzon a következő lépésekkel a probléma megoldásához: 

1. A következő SQL-szkript végrehajtásával tekintse meg a sys. DM _exec_requests nézetet a total_elapsed_time oszlop magas értékkel rendelkező nyitott munkameneteinek megtekintéséhez:

   ```
   SELECT * FROM dm_exec_requests
   ```
2. Határozza meg a hosszú ideig futó lekérdezés bemeneti pufferét. 
3. A lekérdezés finomhangolása.

A lekérdezések kötegelt feldolgozását is érdemes figyelembe venni. A kötegelt feldolgozással kapcsolatos további információkért lásd: [a Batch használata az SQL Database alkalmazások teljesítményének javításához](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance).

Részletes hibaelhárítási eljárás: a [lekérdezés a felhőben fut?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>40551-es hiba: A munkamenet a túlzott TEMPDB-használat miatt megszakadt.

**Részletes hiba**

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

A probléma megkerüléséhez kövesse az alábbi lépéseket:

1. Módosítsa a lekérdezéseket, hogy csökkentse az ideiglenes tábla területének használatát. 
2. Ideiglenes objektumok eldobása, miután már nem szükségesek. 
3. Táblázatok csonkítása vagy a nem használt táblák eltávolítása.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>40552-es hiba: A munkamenet meg lett szakítva a tranzakciós naplók túlzott kihasználtsága miatt.

**Részletes hiba**

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

A probléma megoldásához kövesse az alábbi módszereket: 

* A probléma INSERT, Update vagy DELETE művelet miatt fordul elő. A kötegelt feldolgozás végrehajtásával vagy a több kisebb tranzakcióra való felosztásával próbálja meg csökkenteni a azonnal üzemeltetett sorok számát.
* A probléma az index újraépítési műveletei miatt fordul elő. Győződjön meg arról, hogy betartja a következő képletet: a (z) * táblázatban érintett sorok száma (bájt + 80) < 2 GB-os átlagos méret

  > [!NOTE]
  > Az index újraépítésekor a frissítendő mező átlagos méretét az indexek átlagos méretével kell helyettesíteni.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>40553-es hiba: A munkamenet túl sok memóriahasználat miatt megszakadt.

**Részletes hiba**

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

A probléma megkerüléséhez próbálja meg optimalizálni a lekérdezést.

Részletes hibaelhárítási eljárás: a [lekérdezés a felhőben fut?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).


### <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>A bejelentkezés által kért "Master" adatbázis nem nyitható meg. A bejelentkezés sikertelen.

Ez a probléma azért fordul elő, mert a fióknak nincs hozzáférési engedélye a Master adatbázishoz. Alapértelmezés szerint azonban a SQL Server Management Studio (SSMS) megpróbál csatlakozni a Master adatbázishoz.

A probléma megoldásához kövesse az alábbi lépéseket:

1. A SSMS bejelentkezési képernyőjén kattintson a **Beállítások**, majd a **kapcsolatok tulajdonságai**elemre. 
2. A **Kapcsolódás az adatbázishoz**mezőbe írja be a felhasználó alapértelmezett adatbázisának nevét az alapértelmezett bejelentkezési adatbázisként, majd kattintson a **Kapcsolódás**gombra.

   ![Cannot-Open-Database-Master. png](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-due-to-a-connectivity-issue"></a>Annak ellenőrzése, hogy hiba történt-e a kapcsolódási probléma miatt

Annak ellenőrzéséhez, hogy hiba történt-e a kapcsolódási probléma miatt, tekintse át a verem nyomkövetését olyan keretek esetében, amelyek a következőhöz hasonló kapcsolat megnyitására irányuló hívásokat jelenítenek meg (jegyezze fel a **SqlConnection** osztály hivatkozását):

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Ha a kivétel a lekérdezési problémák miatt történik, a következőhöz hasonló hívási verem jelenik meg (jegyezze fel a **SqlCommand** osztályra mutató hivatkozást). Ezekben a forgatókönyvekben [hangolja le a lekérdezéseket](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```
A teljesítmény finomhangolásával kapcsolatos további útmutatásért tekintse meg a következőket:

* [Az Azure SQL indexek és statisztikák karbantartása](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [A lekérdezés teljesítményének manuális finomhangolása Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [Teljesítmény monitorozása a dinamikus felügyeleti nézetek használatával Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [A lekérdezési tároló üzemeltetése Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)


## <a name="steps-to-fix-common-connection-issues"></a>A gyakori kapcsolódási problémák elhárításának lépései

1. Győződjön meg arról, hogy a TCP IP engedélyezve van-e az alkalmazáskiszolgáló ügyfél-protokollja számára. További információ: az [ügyféloldali protokollok konfigurálása](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols). Azokon az alkalmazás-kiszolgálókon, amelyeken nincs telepítve SQL Server eszköz, győződjön meg arról, hogy a TCP IP engedélyezve van-e a **Cliconfg. exe** (SQL Server Client Network segédprogram) futtatásával. 
2. Ellenőrizze az alkalmazás kapcsolódási karakterláncát, és győződjön meg arról, hogy megfelelően van-e konfigurálva. Győződjön meg például arról, hogy a kapcsolatok karakterlánca a megfelelő portot (1433) és a teljes kiszolgálónevet adja meg.
Lásd: [SQL Server-kapcsolatok adatainak beolvasása](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information).
3. Próbálja meg növelni a kapcsolat **időtúllépését**. A Microsoft legalább 30 másodperces kapcsolati időkorlát használatát javasolja. 
4. Tesztelje az alkalmazáskiszolgáló és az Azure SQL Database közötti kapcsolatot a [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms), a UDL-fájl, a ping és a Telnet használatával. További információ: [hibaelhárítás SQL Server csatlakozási problémák](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) és [diagnosztika a kapcsolódási problémákhoz](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics).

   > [!NOTE]
   > Hibaelhárítási lépésként megpróbálhatja egy másik ügyfélszámítógépen is tesztelni a kapcsolatot.

5. Ajánlott eljárásként győződjön meg arról, hogy az újrapróbálkozási logika van érvényben. Az újrapróbálkozási logikával kapcsolatos további információkért lásd: [átmeneti hibák és kapcsolódási hibák elhárítása SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

Ha az előző lépések nem oldják meg a problémát, próbálja meg több adatot gyűjteni, és forduljon az ügyfélszolgálathoz. Ha az alkalmazás egy felhőalapú szolgáltatás, engedélyezze a naplózást. Ez a lépés a hiba UTC időbélyegzőjét adja vissza. Emellett a SQL Azure visszaadja a nyomkövetési azonosítót. A [Microsoft ügyfél-támogatási szolgálata](http://azure.microsoft.com/support/options/) ezeket az információkat használhatja. 

A naplózás engedélyezésével kapcsolatos további információkért lásd: [a diagnosztikai naplózás engedélyezése a webalkalmazásokhoz a Azure app Serviceban](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/).

**Kapcsolódó dokumentumok**

* [Az Azure SQL kapcsolati architektúrája](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)<br>
* [A Azure SQL Database és az adatraktár hálózati hozzáférés-vezérlése](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)