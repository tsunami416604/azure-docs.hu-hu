---
title: Az SQL-adatbázissal kapcsolatos gyakori csatlakozási problémák elhárítása
description: Az Azure SQL Database-kapcsolattal kapcsolatos problémák elhárításának és az SQL-adatbázis egyéb konkrét problémáinak megoldásához szükséges lépések
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019
author: ramakoni1
ms.author: ramakoni
ms.reviewer: carlrab,vanto
ms.date: 01/14/2020
ms.openlocfilehash: 578d076dfc2fc383051c0c3a8528cdbb0fbcdb15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208783"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-microsoft-azure-sql-database"></a>A Microsoft Azure SQL Database-zel kapcsolatos csatlakozási problémák és egyéb hibák elhárítása

Hibaüzeneteket kap, amikor meghiúsul az Azure SQL Database-hez való kapcsolódás. Ezeket a csatlakozási problémákat okozhatja az Azure SQL Database újrakonfigurálása, a tűzfal beállításai, a kapcsolat időtúllépése, a helytelen bejelentkezési adatok, vagy az ajánlott eljárások és tervezési irányelvek alkalmazásalkalmazás-tervezési folyamat során történő alkalmazásának [alkalmazásának alkalmazás-tervezési](sql-database-develop-overview.md) irányelveinek alkalmazása. Emellett ha eléri az Azure SQL Database egyes erőforrásainak maximális korlátját, nem tud csatlakozni az Azure SQL Database-hez.

## <a name="transient-fault-error-messages-40197-40613-and-others"></a>Átmeneti hibaüzenetek (40197, 40613 és mások)

Az Azure-infrastruktúra az SQL Database szolgáltatásban fellépő nagy mértékű terhelés esetén dinamikusan újra tudja konfigurálni a kiszolgálókat.  E dinamikus viselkedés miatt megszakadhat az ügyfélprogram és az SQL Database közötti kapcsolat. Ezt a fajta hibaállapotot *átmeneti hibának nevezik.* Az adatbázis-újrakonfigurálási események egy tervezett esemény (például szoftverfrissítés) vagy egy nem tervezett esemény (például folyamatösszeomlás vagy terheléselosztás) miatt következnek be. A legtöbb újrakonfigurálási esemény általában rövid életű, és legfeljebb 60 másodperc alatt kell végrehajtani. Ezek az események azonban néha hosszabb időt vesz igénybe, például ha egy nagy tranzakció hosszú ideig tartó helyreállítást okoz. Az alábbi táblázat azokat a különböző átmeneti hibákat sorolja fel, amelyeket az alkalmazások az SQL Database-hez való csatlakozáskor kaphatnak

### <a name="list-of-transient-fault-error-codes"></a>Átmeneti hibahibakódok listája


| Hibakód | Severity | Leírás |
| ---:| ---:|:--- |
| 4060 |16 |A bejelentkezés által kért "%.&#x2a;ls" adatbázis nem nyitható meg. A bejelentkezés nem sikerült. További információ: [4000–4999 hibák](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |A szolgáltatás hibát észlelt a kérés feldolgozása során. Próbálkozzon újra. Hibakód: %d.<br/><br/>Ez a hibaüzenet akkor jelenik meg, ha a szolgáltatás szoftver- vagy hardverfrissítések, hardverhibák vagy bármely más feladatátvételi probléma miatt nem működik. A 40197-es hiba üzenetébe ágyazott hibakód (%d) további információt nyújt a hiba vagy feladatátvétel körülményeiről. A 40197-es hibaüzenetbe néhány példa a 40020, 40143, 40166 és 40540.<br/><br/>Az SQL Database-kiszolgálóhoz való újracsatlakozás automatikusan az adatbázis kifogástalan másolatát kapcsolja össze. Az alkalmazásnak el kell kapnia a 40197-es hibát, hibaelhárítási hiba esetén naplóznia kell a beágyazott hibakódot (%d), és meg kell próbálnia újra csatlakozni az SQL Database-hez, amíg az erőforrások rendelkezésre nem állnak, és a kapcsolat újra létre nem jön. További információ: [Tranziens hibák](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 40501 |20 |A szolgáltatás jelenleg foglalt. Próbálja meg újra a kérelmet 10 másodperc után. Incidensazonosító: %ls. Kód: %d. További információkért lásd: <br/>&bull;&nbsp; [Adatbázis-kiszolgáló erőforráskorlátai](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [DTU-alapú korlátok az egyes adatbázisokhoz](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-alapú korlátok rugalmas készletekhez](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [virtuálismag-alapú korlátozások egyetlen adatbázishoz](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [virtuálismag-alapú korlátok rugalmas készletekhez](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Felügyelt példány erőforráskorlátok](sql-database-managed-instance-resource-limits.md).|
| 40613 |17 |A(z) '%.&#x2a;ls' adatbázis jelenleg nem érhető el a(z) '%.&#x2a;ls' kiszolgálón. Próbálkozzon később újra a kapcsolattal. Ha a probléma továbbra is fennáll, lépjen kapcsolatba az ügyfélszolgálattal, és adja meg nekik a(z) "%.&#x2a;ls" munkamenet-nyomkövetési azonosítóját.<br/><br/> Ez a hiba akkor fordulhat elő, ha már létezik dedikált rendszergazdai kapcsolat (DAC) az adatbázishoz. További információ: [Tranziens hibák](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 49918 |16 |A kérelem nem dolgozható fel. Nincs elég erőforrás a kérelem feldolgozásához.<br/><br/>A szolgáltatás jelenleg foglalt. Próbálkozzon később újra a kéréssel. További információkért lásd: <br/>&bull;&nbsp; [Adatbázis-kiszolgáló erőforráskorlátai](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [DTU-alapú korlátok az egyes adatbázisokhoz](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-alapú korlátok rugalmas készletekhez](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [virtuálismag-alapú korlátozások egyetlen adatbázishoz](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [virtuálismag-alapú korlátok rugalmas készletekhez](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Felügyelt példány erőforráskorlátok](sql-database-managed-instance-resource-limits.md). |
| 49919 |16 |Nem lehet feldolgozni a létrehozási vagy frissítési kérelmet. Túl sok folyamatban lévő létrehozása vagy frissítése folyamatban lévő előfizetéshez "%ld".<br/><br/>A szolgáltatás elfoglalt az előfizetés vagy a kiszolgáló több létrehozási vagy frissítési kérelmet dolgoz fel. A kérelmek jelenleg le vannak tiltva az erőforrások optimalizálásához. Lekérdezés [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) függőben lévő műveletekhez. Várjon, amíg a függőben lévő létrehozási vagy frissítési kérelmek befejeződnek, vagy törölje az egyik függőben lévő kérelmet, és később próbálkozzon újra. További információkért lásd: <br/>&bull;&nbsp; [Adatbázis-kiszolgáló erőforráskorlátai](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [DTU-alapú korlátok az egyes adatbázisokhoz](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-alapú korlátok rugalmas készletekhez](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [virtuálismag-alapú korlátozások egyetlen adatbázishoz](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [virtuálismag-alapú korlátok rugalmas készletekhez](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Felügyelt példány erőforráskorlátok](sql-database-managed-instance-resource-limits.md). |
| 49920 |16 |A kérelem nem dolgozható fel. Túl sok folyamatban lévő művelet a(z) "%ld" előfizetéshez.<br/><br/>A szolgáltatás az előfizetéstöbb kérésének feldolgozása van elfoglalva. A kérelmek jelenleg le vannak tiltva az erőforrások optimalizálásához. A [sys.dm_operation_status lekérdezése](https://msdn.microsoft.com/library/dn270022.aspx) a művelet állapotához. Várjon, amíg a függőben lévő kérelmek befejeződnek, vagy törölje az egyik függőben lévő kérelmet, és később próbálkozzon újra. További információkért lásd: <br/>&bull;&nbsp; [Adatbázis-kiszolgáló erőforráskorlátai](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [DTU-alapú korlátok az egyes adatbázisokhoz](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-alapú korlátok rugalmas készletekhez](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [virtuálismag-alapú korlátozások egyetlen adatbázishoz](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [virtuálismag-alapú korlátok rugalmas készletekhez](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Felügyelt példány erőforráskorlátok](sql-database-managed-instance-resource-limits.md). |
| 4221 |16 |Az olvasási másodlagos bejelentkezés nem sikerült a "HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING" hosszú várakozása miatt. A replika nem érhető el a bejelentkezéshez, mert a sorverziók hiányoznak a kópia újrahasznosítása során repülés közben irt. A probléma megoldható az elsődleges replika aktív tranzakcióinak visszaforgatásával vagy véglegesítésével. Ennek a feltételnek az előfordulása minimalizálható a hosszú írási tranzakciók elkerülésével az elsődlegesen. |


### <a name="steps-to-resolve-transient-connectivity-issues"></a>Az átmeneti kapcsolódási problémák megoldásának lépései

1. Ellenőrizze a [Microsoft Azure Service Dashboard](https://azure.microsoft.com/status) minden olyan ismert kimaradások, amelyek során a hibákat jelentett az alkalmazás.
2. Alkalmazások, amelyek egy felhőszolgáltatáshoz, például az Azure SQL Database-hez csatlakozik kell várni a rendszeres újrakonfigurálási események és valósítsa meg újrapróbálkozási logika kezelni ezeket a hibákat, ahelyett, hogy ezeket a hibákat alkalmazáshibák a felhasználók számára. 
3. Ahogy egy adatbázis megközelíti az erőforrás-korlátokat, úgy tűnhet, hogy egy átmeneti kapcsolódási probléma. Lásd: [Erőforráskorlátok](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached).
4. Ha a kapcsolódási problémák továbbra is fennállnak, vagy ha az az időtartam, amelyre az alkalmazás a hibába ütközik, meghaladja a 60 másodpercet, vagy ha egy adott napon a hiba több előfordulása jelenik meg, az [Azure-támogatás](https://azure.microsoft.com/support/options) **kérése** lehetőséget választva nyújtson be egy Azure-támogatási kérelmet.

#### <a name="implementing-retry-logic"></a>Újrapróbálkozási logika megvalósítása
Erősen ajánlott, hogy az ügyfélprogram újrapróbálkozási logikával rendelkezik, hogy újra létre tudja hozni a kapcsolatot, miután az átmeneti hiba időt adott magának a kijavításához.  Javasoljuk, hogy az első újrapróbálkozás előtt 5 másodpercet késleltesse. Az 5 másodpercnél rövidebb késleltetés után az újrapróbálkozás a felhőszolgáltatás elárasztásának kockázatával jár. Minden ezt követő újrapróbálkozás esetén a késleltetés exponenciálisan, legfeljebb 60 másodpercig növekszik.

Az újrapróbálkozási logikára vonatkozó példákat a következő témakörben talál:
- [Rugalmasan csatlakozhat az SQL-hez ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net)
- [Csatlakozzon rugalmasan az SQL-hez a PHP-vel](https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

Az átmeneti hibák kezelésével kapcsolatos további információkért az alkalmazás felülvizsgálatában
* [Az SQL Database-rel kapcsolatos átmeneti csatlakozási hibák elhárítása](sql-database-connectivity-issues.md)

A ADO.NET használó ügyfelek *blokkolási időszakának* megvitatása elérhető az [SQL Server kapcsolatkészletezésben (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-database-server"></a>Hálózattal vagy példányspecifikus hibával történt az SQL Database-kiszolgálóval való kapcsolat létrehozása közben

A probléma akkor fordul elő, ha az alkalmazás nem tud csatlakozni a kiszolgálóhoz.

A probléma megoldásához próbálkozzon a Gyakori csatlakozási [problémák kijavításához](#steps-to-fix-common-connection-issues) című szakasz lépéseivel (a bemutatott sorrendben).

## <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>A kiszolgáló/példány nem található vagy nem érhető el (26., 40., 10053. hiba)

#### <a name="error-26-error-locating-server-specified"></a>26-os hiba: Hiba történt a kiszolgáló megkeresése miatt

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>40-es hiba: Nem lehetett kapcsolatot nyitni a kiszolgálóval

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>10053-as hiba: Átviteli szintű hiba történt, amikor a kiszolgálótól kapott eredményeket

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Ezek a problémák akkor fordulnak elő, ha az alkalmazás nem tud csatlakozni a kiszolgálóhoz.

A problémák megoldásához próbálkozzon a Gyakori csatlakozási [problémák kijavításához](#steps-to-fix-common-connection-issues) című szakasz lépéseivel (a bemutatott sorrendben).

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>Tűzfalproblémák miatt nem lehet csatlakozni a kiszolgálóhoz

### <a name="error-40615-cannot-connect-to--servername-"></a>40615-ös hiba: Nem lehet csatlakozni < kiszolgálónévhez >

A probléma megoldásához [konfigurálja a tűzfalbeállításait az SQL Database-en az Azure Portalon keresztül.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>5. hiba: Nem lehet csatlakozni < kiszolgálónévhez >

A probléma megoldásához győződjön meg arról, hogy az 1433-as port nyitva áll az ügyfél és az internet közötti tűzfalak on-át kimenő kapcsolataiszámára.

További információt [a Windows tűzfal konfigurálása az SQL Server eléréséhez](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)című témakörben talál.

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Nem lehet bejelentkezni a kiszolgálóra (hiba: 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>A bejelentkezés nem sikerült a "< felhasználónév >" felhasználó esetében

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

A probléma megoldásához lépjen kapcsolatba a szolgáltatás rendszergazdájával, és adjon meg egy érvényes SQL Server-felhasználónevet és -jelszót.

A szolgáltatás rendszergazdája általában a következő lépésekkel adja hozzá a bejelentkezési hitelesítő adatokat:

1. Jelentkezzen be a kiszolgálóra az SQL Server Management Studio (SSMS) segítségével.
2. Futtassa a következő SQL-lekérdezést annak ellenőrzéséhez, hogy a bejelentkezési név le van-e tiltva:

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Ha a név le van tiltva, engedélyezze a következő utasítással:

   ```sql
   Alter login <User name> enable
   ```

4. Ha az SQL bejelentkezési felhasználónév nem létezik, hozza létre az alábbi lépésekkel:

   1. Az SSMS-ben kattintson duplán a **Biztonság** elemre a kibontásához.
   2. Kattintson a jobb gombbal a **Logins** (Bejelentkezések) elemre, majd válassza a **New login** (Új bejelentkezés) lehetőséget.
   3. A létrehozott, helyőrzőkkel rendelkező parancsfájlban szerkesztheti és futtatja a következő SQL-lekérdezést:

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```

5. Kattintson duplán a **Database** (Adatbázis) elemre.
6. Jelölje ki azt az adatbázist, amelynek a felhasználónak engedélyt szeretne adni.
7. Kattintson duplán a **Security** (Biztonság) elemre.
8. Kattintson a jobb gombbal a **Users** (Felhasználók) elemre, majd válassza a **New User** (Új felhasználó) lehetőséget.
9. A létrehozott, helyőrzőkkel rendelkező parancsfájlban szerkesztheti és futtatja a következő SQL-lekérdezést:

   ```sql
   CREATE USER <user_name, sysname, user_name>
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   -- Add user to the database owner role

   EXEC sp_addrolemember N’db_owner’, N’<user_name, sysname, user_name>’
   GO
   ```

   > [!NOTE]
   > Adott felhasználók `sp_addrolemember` adott adatbázisszerepkörökhöz való hozzárendelésére is használható.

További információt az [Adatbázisok és bejelentkezések kezelése az Azure SQL Database-ben című témakörben talál.](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)

## <a name="connection-timeout-expired-errors"></a>A kapcsolat időtúllépési hibái lejárta

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904): A kapcsolat időtúllépése lejárt

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904): Az időbeli meghosszabbítás lejárt

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System.Data.Entity.Core.EntityException: Az alapul szolgáló szolgáltató nem sikerült a megnyitáskor

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Nem lehet csatlakozni < kiszolgáló nevéhez >

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Ezek a kivételek kapcsolati vagy lekérdezési problémák miatt fordulhatnak elő. Annak ellenőrzéséhez, hogy a hibát kapcsolódási problémák [okozzák-e, olvassa el A hiba ellenőrzése kapcsolódási probléma (Emon) (Erősítse meg, hogy a hibát kapcsolódási probléma okozza-e ) (Ellenőrizze, hogy a hibát kapcsolódási probléma okozza-e.](#confirm-whether-an-error-is-caused-by-a-connectivity-issue)

A kapcsolat időtúlárai azért fordulnak elő, mert az alkalmazás nem tud csatlakozni a kiszolgálóhoz. A probléma megoldásához próbálkozzon a Gyakori csatlakozási [problémák kijavításához](#steps-to-fix-common-connection-issues) című szakasz lépéseivel (a bemutatott sorrendben).

## <a name="resource-governance-errors"></a>Erőforrás-irányítási hibák

### <a name="error-10928-resource-id-d"></a>10928-as hiba: Erőforrásazonosító: %d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

A probléma kerülő megoldásához próbálkozzon az alábbi módszerekkel:

* Ellenőrizze, hogy vannak-e hosszú ideig futó lekérdezések.

  > [!NOTE]
  > Ez egy minimalista megközelítés, amely nem oldja meg a problémát.

1. Futtassa a következő SQL-lekérdezést a [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) nézet megtekintéséhez a blokkoló kérelmek megtekintéséhez:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Határozza meg a fejblokkoló **bemeneti pufferét.**
3. Hangolja be a fejblokkoló lekérdezést.

   Részletes hibaelhárítási eljárás: [Jól fut a lekérdezés a felhőben?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

Ha az adatbázis következetesen eléri a korlátot annak ellenére, hogy a blokkoló és a hosszú ideig futó lekérdezések, fontolja meg a frissítés egy kiadás több [erőforrást Kiadások](https://azure.microsoft.com/pricing/details/sql-database/)).

A dinamikus felügyeleti nézetekről a [Rendszer dinamikus felügyeleti nézetei című témakörben](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)talál további információt.

Az adatbázis-korlátokról az [Sql Database erőforráskorlátai az Azure SQL Database Server rendszerhez](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)című témakörben talál további információt.

### <a name="error-10929-resource-id-1"></a>10929-es hiba: Erőforrás-azonosító: 1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>40501-es hiba: A szolgáltatás jelenleg foglalt

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Ez egy motor szabályozási hiba, ami azt jelzi, hogy az erőforrás-korlátok túllépése.

Az erőforráskorlátokról az [Adatbázis-kiszolgáló erőforráskorlátai](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)című témakörben talál további információt.

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>40544-es hiba: Az adatbázis elérte a méretkvótáját

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Ez a hiba akkor fordul elő, ha az adatbázis elérte a méretkvótáját.

A következő lépések segíthetnek a probléma megoldásában, vagy további lehetőségeket kínálnak:

1. Ellenőrizze az adatbázis aktuális méretét az Azure Portalon található irányítópult használatával.

   > [!NOTE]
   > Annak megállapításához, hogy mely táblák fogyasztják a legtöbb helyet, és ezért potenciális antisztatajelöltek, futtassa a következő SQL-lekérdezést:

   ```sql
   SELECT o.name,
    SUM(p.row_count) AS 'Row Count',
    SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. Ha az aktuális méret nem haladja meg a kiadásban támogatott maximális méretet, az ALTER DATABASE segítségével növelheti a MAXSIZE beállítást.
3. Ha az adatbázis már túllépte a kiadáshoz támogatott maximális méretet, próbálkozzon az alábbi lépésekkel:

   * Normál adatbázis-törlési tevékenységek végrehajtása. Például tisztítsa meg a nem kívánt adatokat csonkolással/törléssel, vagy helyezze át az adatokat az SQL Server Integration Services (SSIS) vagy a tömeges másolási program (bcp) segédprogram használatával.
   * Adatok at particionálhat vagy törölhet, indexeket dobhat le, vagy a dokumentációban tájékozódhat a lehetséges megoldásokról.
   * Az adatbázis-méretezésről az [Egyetlen adatbázis-erőforrások méretezése](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale) és [a rugalmas készlet erőforrások méretezése](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale)című témakörben található.

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>40549-es hiba: A munkamenet megszakad, mert hosszú ideig futó tranzakcióval rendelkezik

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Ha ismételten ezt a hibát észleli, próbálja meg az alábbi lépésekkel megoldani a problémát:

1. A sys.dm_exec_requests nézetben megtekintheti azokat a nyitott munkameneteket, amelyek nagy értéket képviselnek a total_elapsed_time oszlopban. Az ellenőrzést a következő SQL-parancsfájl futtatásával hajtsa végre:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Határozza meg a hosszú ideig futó lekérdezés bemeneti pufferét.
3. Hangolja be a lekérdezést.

Fontolja meg a lekérdezések kötegelését is. A kötegelt állapotról az [SQL Database-alkalmazások teljesítményének javítása érdekében a kötegelés használata](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance)című témakörben talál további információt.

Részletes hibaelhárítási eljárás: [Jól fut a lekérdezés a felhőben?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>40551-es hiba: A munkamenet a TEMPDB túlzott használata miatt megszakadt

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

A probléma kerülő megoldásához hajtsa végre az alábbi lépéseket:

1. Módosítsa a lekérdezéseket az ideiglenes táblaterület-használat csökkentéséhez.
2. Ideiglenes objektumokat dobjon le, miután már nincs rájuk szükség.
3. A táblák csonkolása vagy a nem használt táblák eltávolítása.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>40552-es hiba: A munkamenet a tranzakciónapló túlzott területének túlzott használata miatt megszakadt

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

A hiba elhárításához próbálkozzon a következő módszerekkel:

* A probléma beszúrási, frissítési vagy törlési műveletek miatt fordulhat elő.
Próbálja meg csökkenteni az azonnal működő sorok számát kötegelés vagy több kisebb tranzakcióra való felosztás végrehajtásával.
* A probléma az index újraépítési műveletei miatt fordulhat elő. A probléma kerülő megoldásához győződjön meg arról, hogy a táblázatban * érintett sorok száma (a mező átlagos mérete, amely bájtban + 80-ban frissül) < 2 gigabájt (GB).

  > [!NOTE]
  > Az index újraépítése esetén a frissített mező átlagos méretét az átlagos indexmérettel kell helyettesíteni.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>40553-as hiba: A munkamenet a túlzott memóriahasználat miatt megszakadt

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

A probléma kerülő megoldásához próbálja meg optimalizálni a lekérdezést.

Részletes hibaelhárítási eljárás: [Jól fut a lekérdezés a felhőben?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

### <a name="table-of-additional-resource-governance-error-messages"></a>További erőforrás-irányítási hibaüzenetek táblázata

| Hibakód | Severity | Leírás |
| ---:| ---:|:--- |
| 10928 |20 |Erőforrásazonosító: %d. Az adatbázis %s korlátja %d, és elérte. További információt az [SQL Database egy- és készletezésű adatbázisokra vonatkozó erőforráskorlátai című témakörben talál.](sql-database-resource-limits-database-server.md)<br/><br/>Az erőforrásazonosító azt az erőforrást jelzi, amely elérte a korlátot. Munkavégző szálak esetén az Erőforrás-azonosító = 1. Munkamenetek esetén az Erőforrás-azonosító = 2.<br/><br/>A hibáról és megoldásáról további információt a következő témakörben talál: <br/>&bull;&nbsp; [Adatbázis-kiszolgáló erőforráskorlátai](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [DTU-alapú korlátok az egyes adatbázisokhoz](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-alapú korlátok rugalmas készletekhez](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [virtuálismag-alapú korlátozások egyetlen adatbázishoz](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [virtuálismag-alapú korlátok rugalmas készletekhez](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Felügyelt példány erőforráskorlátok](sql-database-managed-instance-resource-limits.md). |
| 10929 |20 |Erőforrásazonosító: %d. A(z) %s minimális garancia: %d, a maximális korlát %d, az adatbázis jelenlegi használata pedig %d. A kiszolgáló azonban jelenleg túl elfoglalt ahhoz, hogy az adatbázishoz tartozó %d-nél nagyobb kéréseket támogassa. Az erőforrásazonosító azt az erőforrást jelzi, amely elérte a korlátot. Munkavégző szálak esetén az Erőforrás-azonosító = 1. Munkamenetek esetén az Erőforrás-azonosító = 2. További információkért lásd: <br/>&bull;&nbsp; [Adatbázis-kiszolgáló erőforráskorlátai](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [DTU-alapú korlátok az egyes adatbázisokhoz](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-alapú korlátok rugalmas készletekhez](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [virtuálismag-alapú korlátozások egyetlen adatbázishoz](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [virtuálismag-alapú korlátok rugalmas készletekhez](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Felügyelt példány erőforráskorlátok](sql-database-managed-instance-resource-limits.md). <br/>Ellenkező esetben próbálkozzon később. |
| 40544 |20 |Az adatbázis elérte a méretkvótáját. Adatok at particionálhat vagy törölhet, indexeket dobhat le, vagy a dokumentációban tájékozódhat a lehetséges megoldásokról. Az adatbázis-méretezésről az [Egyetlen adatbázis-erőforrások méretezése](sql-database-single-database-scale.md) és [a rugalmas készlet erőforrások méretezése](sql-database-elastic-pool-scale.md)című témakörben található.|
| 40549 |16 |A munkamenet megszakad, mert hosszú ideig futó tranzakcióval rendelkezik. Próbálja meg lerövidíteni a tranzakciót. A kötegelt állapotról az [SQL Database-alkalmazások teljesítményének javítása érdekében a kötegelés használata](sql-database-use-batching-to-improve-performance.md)című témakörben talál további információt.|
| 40550 |16 |A munkamenet megszakadt, mert túl sok zárolást szerzett be. Próbáljon meg kevesebb sort olvasni vagy módosítani egyetlen tranzakcióban. A kötegelt állapotról az [SQL Database-alkalmazások teljesítményének javítása érdekében a kötegelés használata](sql-database-use-batching-to-improve-performance.md)című témakörben talál további információt.|
| 40551 |16 |A munkamenet túlzott `TEMPDB` használat miatt megszakadt. Próbálja meg módosítani a lekérdezést az ideiglenes táblaterület használatának csökkentése érdekében.<br/><br/>Ha ideiglenes objektumokat használ, takarítson meg helyet az adatbázisban azáltal, `TEMPDB` hogy ideiglenes objektumokat dob le, miután a munkamenetnek már nincs rájuk szüksége. Az SQL Database tempdb-használatáról további információt a [Tempdb adatbázis az SQL Database ben című témakörben talál.](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)|
| 40552 |16 |A munkamenet a tranzakciónapló-terület túlzott használata miatt megszakadt. Próbáljon meg kevesebb sort módosítani egyetlen tranzakcióban. A kötegelt állapotról az [SQL Database-alkalmazások teljesítményének javítása érdekében a kötegelés használata](sql-database-use-batching-to-improve-performance.md)című témakörben talál további információt.<br/><br/>Ha tömeges beszúrásokat hajt `bcp.exe` végre `System.Data.SqlClient.SqlBulkCopy` a segédprogrammal `-b batchsize` vagy `BatchSize` az osztállyal, próbálja meg a vagy a beállításokat használni a kiszolgálóra minden tranzakcióban átmásolt sorok számának korlátozására. Ha az `ALTER INDEX` utasítással újraépít egy indexet, próbálja meg használni ezt a `REBUILD WITH ONLINE = ON` beállítást. A virtuálismag-beszerzési modell tranzakciónapló-méretéről a következő témakörökben talál további információt: <br/>&bull;&nbsp; [virtuálismag-alapú korlátozások egyetlen adatbázishoz](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [virtuálismag-alapú korlátok rugalmas készletekhez](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Felügyelt példány erőforráskorlátok](sql-database-managed-instance-resource-limits.md).|
| 40553 |16 |A munkamenet túlzott memóriahasználat miatt megszakadt. Próbálja meg módosítani a lekérdezést úgy, hogy kevesebb sort dolgozzon fel.<br/><br/>A Transact-SQL-kódban lévő műveletek számának `ORDER BY` és `GROUP BY` műveleteinek csökkentése csökkenti a lekérdezés memóriaigényét. Az adatbázis-méretezésről az [Egyetlen adatbázis-erőforrások méretezése](sql-database-single-database-scale.md) és [a rugalmas készlet erőforrások méretezése](sql-database-elastic-pool-scale.md)című témakörben található.|

## <a name="elastic-pool-errors"></a>Rugalmas készlethibák

A következő hibák rugalmas készletek létrehozásával és használatával kapcsolatosak:

| Hibakód | Severity | Leírás | Korrekciós intézkedés |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |A rugalmas készlet elérte a tárolási korlátot. A rugalmas készlet tárolási felhasználása nem haladhatja meg a (%d) NAGY-kat. Kísérlet az adatok írására egy adatbázisba, ha elérte a rugalmas készlet tárolási korlátját. Az erőforráskorlátokról az: <br/>&bull;&nbsp; [DTU-alapú korlátok rugalmas készletekhez](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [virtuálismag-alapú korlátok rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md). <br/> |Fontolja meg a DDO-k növelését és/vagy a tároló hozzáadása a rugalmas készlethez, ha lehetséges, a tárolási korlát növelése érdekében, csökkentse az egyes adatbázisok által használt tárolót a rugalmas készleten belül, vagy távolítsa el az adatbázisokat a rugalmas készletből. A rugalmas készletméretezésről a [Rugalmas készlet erőforrások méretezése.](sql-database-elastic-pool-scale.md)|
| 10929 | 16 |A(z) %s minimális garancia: %d, a maximális korlát %d, az adatbázis jelenlegi használata pedig %d. A kiszolgáló azonban jelenleg túl elfoglalt ahhoz, hogy az adatbázishoz tartozó %d-nél nagyobb kéréseket támogassa. Az erőforráskorlátokról az: <br/>&bull;&nbsp; [DTU-alapú korlátok rugalmas készletekhez](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [virtuálismag-alapú korlátok rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md). <br/> Ellenkező esetben próbálkozzon később. DTU / virtuális mag min adatbázisonként; DTU / virtuális mag maximális adatbázisonként. A rugalmas készlet összes adatbázisában lévő egyidejű dolgozók (kérelmek) teljes száma megpróbálta túllépni a készletkorlátot. |Fontolja meg a dru-k vagy virtuális magok a rugalmas készlet, ha lehetséges, a dolgozói korlát növelése érdekében, vagy távolítsa el az adatbázisokat a rugalmas készlet. |
| 40844 | 16 |A(z) '%ls' kiszolgáló "%ls" adatbázisa "%ls" kiadású adatbázis egy rugalmas készletben, és nem lehet folyamatos másolási kapcsolat.  |N/A |
| 40857 | 16 |A kiszolgálóhoz nem található rugalmas készlet: '%ls', rugalmas készlet neve: '%ls'. A megadott rugalmas készlet nem létezik a megadott kiszolgálón. | Adjon meg egy érvényes rugalmas készlet nevet. |
| 40858 | 16 |A(z) '%ls' rugalmas készlet már létezik a következő kiszolgálón: '%ls'. A megadott rugalmas készlet már létezik a megadott SQL Database-kiszolgálón. | Adja meg az új rugalmas készlet nevét. |
| 40859 | 16 |A rugalmas készlet nem támogatja a(z) "%ls" szolgáltatási szintet. A megadott szolgáltatásszint nem támogatott a rugalmas készlet kiépítéshez. |Adja meg a megfelelő kiadást, vagy hagyja üresen a szolgáltatási szintet az alapértelmezett szolgáltatási szint használatához. |
| 40860 | 16 |A(z) '%ls' rugalmas készlet és a(z) '%ls' szolgáltatáscél kombinációja érvénytelen. Rugalmas készlet és a szolgáltatásszint csak akkor adható meg együtt, ha az erőforrás típusa "ElasticPool" meg van adva. |Adja meg a rugalmas készlet és a szolgáltatási szint helyes kombinációját. |
| 40861 | 16 |Az adatbázis kiadása '%. *ls' nem lehet más, mint a rugalmas készlet szolgáltatási szint, amely "%.* Ez az, hogy nem tudom, mit mondjak. Az adatbázis-kiadás eltér a rugalmas készlet szolgáltatási szint. |Ne adjon meg olyan adatbázis-kiadást, amely eltér a rugalmas készlet szolgáltatási szintjéétól.  Ne feledje, hogy az adatbázis-kiadást nem kell megadni. |
| 40862 | 16 |Rugalmas készlet nevét meg kell adni, ha a rugalmas készlet szolgáltatás célja meg van adva. Rugalmas készlet szolgáltatás célja nem azonosítja egyedileg egy rugalmas készlet. |Adja meg a rugalmas készlet nevét, ha a rugalmas készlet szolgáltatás célja. |
| 40864 | 16 |A rugalmas készlet DRU-inak legalább (%d) DRU-knak kell lenniük a"%.*ls" szolgáltatási szinthez. Kísérlet a dit-ek a rugalmas készlet a minimális korlát alatt. |Próbálja meg a dit-ek a rugalmas készlet legalább a minimális korlátot. |
| 40865 | 16 |A rugalmas készlet DRU-i nem haladhatják meg a (%d) DRU-kat a"%.*ls" szolgáltatási szinthez. Kísérlet a dit-ek a rugalmas készlet a maximális korlát fölé. |Próbálja meg újra a rugalmas készlet DIT-jeit a maximális korlátnál nem nagyobbra. |
| 40867 | 16 |Az adatbázisonkénti DTU-max értéknek legalább (%d) kell lennie a(z) "%.*ls" szolgáltatási szinthez. Kísérlet a DTU maximális adatbázisonkénti beállítására a támogatott korlát alatt. | Fontolja meg a rugalmas készlet szolgáltatási szint, amely támogatja a kívánt beállítást. |
| 40868 | 16 |Az adatbázisonkénti DTU-érték nem haladhatja meg a(z) "%.*ls" szolgáltatási szint (%d) értéket. Kísérlet a DTU-maximális adatbázisonkénti beállítására a támogatott korláton túl. | Fontolja meg a rugalmas készlet szolgáltatási szint, amely támogatja a kívánt beállítást. |
| 40870 | 16 |Az adatbázisonkénti DTU-érték nem haladhatja meg a(z) "%.*ls" szolgáltatási szint (%d) szintjét. Kísérlet a DTU min adatbázisonként a támogatott korláton túl. | Fontolja meg a rugalmas készlet szolgáltatási szint, amely támogatja a kívánt beállítást. |
| 40873 | 16 |Az adatbázisok (%d) és a DTU-szám adatbázisonként (%d) nem haladhatja meg a rugalmas készlet (%d) DTU-it. Kísérlet a DTU-min megadására a rugalmas készletben lévő adatbázisokhoz, amelyek meghaladják a rugalmas készlet DTU-it. | Fontolja meg a rugalmas készlet DTU-k növelését, vagy csökkentse a DTU-t adatbázisonként, vagy csökkentse a rugalmas készletben lévő adatbázisok számát. |
| 40877 | 16 |A rugalmas készlet csak akkor törölhető, ha nem tartalmaz adatbázisokat. A rugalmas készlet egy vagy több adatbázist tartalmaz, ezért nem törölhető. |Távolítsa el az adatbázisokat a rugalmas készletből annak érdekében, hogy törölje. |
| 40881 | 16 |A(z) '%.*ls' rugalmas készlet elérte az adatbázisszám-korlátot.  A rugalmas készlet adatbázis-számkori korlátja nem haladhatja meg a(z) %dUS-t tartalmazó rugalmas készlet (%d) esetén. Kísérlet a rugalmas készlet létrehozásához vagy hozzáadásához, ha elérte a rugalmas készlet adatbázis-számkorlátját. | Fontolja meg a rugalmas készlet DRU-k növelését, ha lehetséges, az adatbázis-korlát növelése érdekében, vagy távolítsa el az adatbázisokat a rugalmas készletből. |
| 40889 | 16 |A(z) "%.*ls" rugalmas készlet DRU-i vagy tárolási korlátja nem csökkenthető, mivel ez nem biztosítana elegendő tárhelyet az adatbázisai számára. Kísérlet a rugalmas készlet tárolási korlátjának a tárolási használat alá csökkentésére. | Fontolja meg az egyes adatbázisok tárolási használatának csökkentését a rugalmas készletben, vagy távolítsa el az adatbázisokat a készletből a DDO-k vagy a tárolási korlát csökkentése érdekében. |
| 40891 | 16 |Az adatbázisonkénti DTU-perc (%d) nem haladhatja meg az adatbázisonkénti DTU-maximális értéket (%d). Kísérlet a DTU min adatbázisonként iadértékének beállítására, mint az adatbázisonkénti DTU-maximális érték. |Győződjön meg arról, hogy a DTU-min adatbázisonként nem haladja meg az adatbázisonkénti DTU-maximális értéket. |
| TBD | 16 |A rugalmas készletben lévő egyes adatbázisok tárolási mérete nem haladhatja meg a(z) '%.*ls' szolgáltatásiréteg rugalmas készletáltal engedélyezett maximális méretet. Az adatbázis maximális mérete meghaladja a rugalmas készlet szolgáltatási szintje által engedélyezett maximális méretet. |Állítsa be az adatbázis maximális méretét a rugalmas készlet szolgáltatási szintje által engedélyezett maximális méret határain belül. |

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>A bejelentkezés által kért "fő" adatbázis nem nyitható meg. A bejelentkezés nem sikerült

A probléma oka az, hogy a fióknak nincs engedélye a fő adatbázis elérésére. Alapértelmezés szerint azonban az SQL Server Management Studio (SSMS) megpróbál csatlakozni a fő adatbázishoz.

A probléma megoldásához kövesse az alábbi lépéseket:

1. Az SSMS bejelentkezési képernyőjén válassza a **Beállítások**lehetőséget, majd a **Kapcsolat tulajdonságai lehetőséget.**
2. A **Csatlakozás adatbázishoz** mezőbe írja be a felhasználó alapértelmezett adatbázisnevét alapértelmezett bejelentkezési adatbázisként, majd válassza a **Csatlakozás lehetőséget.**

   ![Kapcsolat tulajdonságai](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Annak ellenőrzése, hogy a hibát kapcsolódási probléma okozta-e

Annak ellenőrzéséhez, hogy a hibát kapcsolódási probléma okozza-e, tekintse át a veremnyomkövetést azon keretek között, amelyek a következőhöz hasonló kapcsolatok megnyitását mutatják (vegye figyelembe az **SqlConnection** osztályra mutató hivatkozást):

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Amikor a kivételt lekérdezési problémák váltják ki, a következőhöz hasonló hívási verem jelenik meg (vegye figyelembe az **SqlCommand** osztályra való hivatkozást). Ebben a helyzetben [hangolja be a lekérdezéseket.](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

A teljesítmény finomhangolásával kapcsolatos további útmutatásért tekintse meg az alábbi forrásokat:

* [Az Azure SQL-indexek és statisztikák karbantartása](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [Kézi hangolás lekérdezési teljesítmény az Azure SQL Database-ben](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [Az Azure SQL Database teljesítményének figyelése dinamikus felügyeleti nézetek használatával](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [A Lekérdezési tároló működtetése az Azure SQL-adatbázisban](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)

## <a name="steps-to-fix-common-connection-issues"></a>A gyakori kapcsolati problémák javítási lépései

1. Győződjön meg arról, hogy a TCP/IP ügyfélprotokollként engedélyezve van az alkalmazáskiszolgálón. További információt az [Ügyfélprotokollok konfigurálása című témakörben talál.](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols) Azokon az alkalmazáskiszolgálókon, ahol nincs enek TELEPÍTVE az SQL Server-eszközök, ellenőrizze, hogy a TCP/IP engedélyezve **van-e a cliconfg.exe** (SQL Server Client Network segédprogram) futtatásával.
2. Ellenőrizze az alkalmazás kapcsolati karakterláncát, hogy megfelelően van-e konfigurálva. Ellenőrizze például, hogy a kapcsolati karakterlánc a megfelelő portot (1433) és a teljesen minősített kiszolgálónevet adja-e meg.
Lásd: [Sql Server-kapcsolatadatainak beolvassa.](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information)
3. Próbálja meg növelni a kapcsolat időtúllépési értékét. Javasoljuk, hogy legalább 30 másodperces csatlakozási időtúllépést használjon.
4. Tesztelje az alkalmazáskiszolgáló és az Azure SQL-adatbázis közötti kapcsolatot az [SQL Server management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms), egy UDL-fájl, a ping vagy a telnet használatával. További információt az [SQL Server kapcsolódási problémáinak elhárítása](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) és [a diagnosztikai problémák elhárítása című témakörben talál.](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics)

   > [!NOTE]
   > Hibaelhárítási lépésként tesztelheti a kapcsolatot egy másik ügyfélszámítógépen is.

5. Ajánlott eljárásként győződjön meg arról, hogy az újrapróbálkozási logika a helyén van. Az újrapróbálkozási logikáról további információt [az SQL Database átmeneti hibáinak és csatlakozási hibáinak elhárítása című témakörben](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)talál.

Ha ezek a lépések nem oldják meg a problémát, próbáljon meg további adatokat gyűjteni, majd forduljon az ügyfélszolgálathoz. Ha az alkalmazás egy felhőalapú szolgáltatás, engedélyezze a naplózást. Ez a lépés a hiba UTC-időbélyegzőjét adja vissza. Emellett az SQL Azure visszaadja a nyomkövetési azonosítót. [A Microsoft ügyfélszolgálata](https://azure.microsoft.com/support/options/) felhasználhatja ezeket az információkat.

A naplózás engedélyezéséről az [Alkalmazások diagnosztika naplózásának engedélyezése az Azure App Service-ben](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

* [Azure SQL kapcsolatarchitektúra](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)
* [Az Azure SQL Database és a Data Warehouse hálózati hozzáférés-vezérlései](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)
