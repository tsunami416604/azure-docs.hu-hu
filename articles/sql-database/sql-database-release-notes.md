---
title: Kibocsátási megjegyzések
description: Ismerje meg az Azure SQL Database szolgáltatás és az Azure SQL Database dokumentációjának új funkcióit és fejlesztéseit
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: sstein
ms.openlocfilehash: b677fd7fe2b14e1c42443478a887ddfa2481dfbf
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011446"
---
# <a name="sql-database-release-notes"></a>SQL Database kiadási megjegyzések

Ez a cikk az SQL Database jelenleg nyilvános előzetes verzióban elérhető szolgáltatásait sorolja fel. Az SQL Database frissítéseiről és fejlesztéseiről az [SQL Database szolgáltatás frissítései című témakörben található.](https://azure.microsoft.com/updates/?product=sql-database) Az Azure-szolgáltatások frissítéseit és fejlesztéseit a [Szolgáltatás frissítései](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Funkciók a nyilvános előzetes verzióban

### <a name="single-database"></a>[Egyetlen adatbázis](#tab/single-database)

| Szolgáltatás | Részletek |
| ---| --- |
| Új Fsv2 sorozatú és M sorozatú hardvergenerációk| További információt a [Hardvergenerációk című témakörben](sql-database-service-tiers-vcore.md#hardware-generations)talál.|
| Gyorsított adatbázis-helyreállítás egyetlen adatbázissal és rugalmas készletekkel | További információt a [Gyorsított adatbázis-helyreállítás című témakörben](sql-database-accelerated-database-recovery.md)talál.|
|Közelítő darabszám eltérő|További információt a [Hozzávetőleges szám eltérő című témakörben talál.](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing)|
|Batch mód a Rowstore-on (a 150-es kompatibilitási szint alatt)|További információt a [Batch Mode on Rowstore című témakörben talál.](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore)|
| Adatfelderítés és besorolás  |További információt az [Azure SQL Database és az SQL Data Warehouse adatfelderítési & besoroláscímű témakörben talál.](sql-database-data-discovery-and-classification.md)|
| Rugalmas adatbázis-feladatok | További információt a [Rugalmas feladatok létrehozása, konfigurálása és kezelése](elastic-jobs-overview.md)című témakörben talál. |
| Rugalmas lekérdezések | További információt a [Rugalmas lekérdezés – áttekintés című témakörben talál.](sql-database-elastic-query-overview.md) |
| Rugalmas tranzakciók | [Elosztott tranzakciók felhőalapú adatbázisok között.](sql-database-elastic-transactions-overview.md) |
|Memóriatámogatás visszajelzése (sor mód) (a 150-es kompatibilitási szint alatt)|További információt a [Memóriatámogatás visszajelzése (sormód) című témakörben talál.](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)|
| Lekérdezésszerkesztő az Azure Portalon |További információ: [Az Azure Portal SQL-lekérdezésszerkesztőjének használata adatok csatlakoztatásához és lekérdezéséhez](sql-database-connect-query-portal.md)című témakörben található.|
| R szolgáltatások / gépi tanulás egyetlen adatbázissal és rugalmas készletekkel |További információt a [Machine Learning Services az Azure SQL Database-ben című témakörben talál.](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)|
|SQL Analytics|További információt az [Azure SQL Analytics című témakörben talál.](../azure-monitor/insights/azure-sql.md)|
|Táblázat változó Halasztott összeállítás (a 150-es kompatibilitási szint alatt)|További információt a [Táblaváltozó halasztott összeállítása című témakörben talál.](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation)|
| &nbsp; |

### <a name="managed-instance"></a>[Felügyelt példány](#tab/managed-instance)

| Szolgáltatás | Részletek |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Példánykészletek</a> | A kisebb SQL-példányok felhőbe való áttelepítésének kényelmes és költséghatékony módja. |
| <a href="https://aka.ms/managed-instance-aadlogins">Példányszintű Azure AD-kiszolgálói egyszerű (bejelentkezési adatok)</a> | Kiszolgálószintű bejelentkezések létrehozása <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">külső szolgáltatóból származó bejelentkezés létrehozása</a> utasítással. |
| [Tranzakciós replikáció](sql-database-managed-instance-transactional-replication.md) | Replikálja a táblákból származó módosításokat más, felügyelt példányokon, egyetlen adatbázisokon vagy SQL Server-példányokon elhelyezett adatbázisokba, vagy frissítse a táblákat, ha egyes sorok más felügyelt példányokban vagy SQL Server-példányokban módosulnak. További információt a [Replikáció konfigurálása Azure SQL Database felügyelt példányadatbázisában](replication-with-sql-database-managed-instance.md)című témakörben talál. |
| Fenyegetések észlelése |További információt a [Fenyegetésészlelés konfigurálása az Azure SQL Database felügyelt példányában című témakörben talál.](sql-database-managed-instance-threat-detection.md)|

---

## <a name="managed-instance---new-features-and-known-issues"></a>Felügyelt példány – új szolgáltatások és ismert problémák

### <a name="managed-instance-h2-2019-updates"></a>H2 2019 felügyelt példány frissítései

- [Szolgáltatásáltal támogatott alhálózat-konfiguráció](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) Az alhálózati konfiguráció kontinamán kezelheti az adatforgalmat felügyelt példányok mellett, így biztosíthatja a felügyeleti forgalom folyamatos áramlását
- [Az átlátszó adattitkosítás (TDE) a Bring Your Own Key (BYOK) funkcióval](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) lehetővé teszi a saját kulcsú (BYOK) forgatókönyvet az inaktív adatvédelemhez, és lehetővé teszi a szervezetek számára, hogy elkülönítsék a kulcsok és az adatok kezelési feladatait.
- [Az automatikus feladatátvételi csoportok](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) lehetővé teszik az összes adatbázis replikálását az elsődleges példányból egy másik régió másodlagos példányába.
- Konfigurálja a felügyelt példány viselkedését [globális nyomkövetési jelzőkkel.](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/)

### <a name="managed-instance-h1-2019-updates"></a>H1 2019-es h1-es sikeres példány frissítései

A következő funkciók engedélyezve vannak a Felügyelt példány telepítési modelljében a H1 2019-ben:
  - Az Azure havi kreditjével rendelkező előfizetések támogatása <a href="https://aka.ms/sql-mi-visual-studio-subscribers">a Visual Studio-előfizetők számára</a> és a megnövelt területi [korlátok.](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)
  - A <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 és SharePoint 2019 </a> és a <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a> támogatása
  - Példányokat hozhat létre <a href="https://aka.ms/managed-instance-collation">a kiszolgálószintű rendezéssel</a> és az Ön által választott <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">időzónával.</a>
  - A felügyelt példányokat <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">mostantól beépített tűzfal</a>védi.
  - Állítsa konfigurálja a példányokat [nyilvános végpontok használatára,](sql-database-managed-instance-public-endpoint-configure.md) [proxy felülbírálása](sql-database-connectivity-architecture.md#connection-policy) a kapcsolat jobb hálózati teljesítmény, <a href="https://aka.ms/four-cores-sql-mi-update">4 virtuális magok Gen5 hardvergenerálása</a> vagy <a href="https://aka.ms/managed-instance-configurable-backup-retention">konfigurálja biztonsági mentés megőrzése akár 35 napig</a> point-in-time visszaállítás. A hosszú távú biztonsági mentés megőrzése (legfeljebb 10 év) továbbra sem engedélyezett, így <a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">a csak másolási biztonsági mentések</a> alternatívaként is használható.
  - Az új funkciók lehetővé teszik <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">az adatbázis geo-visszaállítását egy másik adatközpontba a PowerShell használatával,</a> [az adatbázis átnevezése](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [a virtuális fürt törlése](sql-database-managed-instance-delete-virtual-cluster.md)segítségével.
  - Az új beépített [példányközreműködői szerepkör](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) lehetővé teszi a vám (SoD) biztonsági elveknek való megfelelését és a vállalati szabványoknak való megfelelést.
  - A felügyelt példány a következő Azure Government-régiókban érhető el ga (US Gov Texas, US Gov Arizona), valamint Kína Északi 2 és Kína Keleti 2. A következő nyilvános régiókban is elérhető: Ausztrália Középső, Ausztrália Központi 2, Brazília Déli, Franciaország Déli, Egyesült Arab Emírségek középső, Egyesült Arab Emírségek Északi, Dél-Afrika Észak, Dél-Afrika Nyugat.

### <a name="known-issues"></a>Ismert problémák

|Probléma  |Felfedezett dátum  |status  |Megoldott dátum  |
|---------|---------|---------|---------|
|[A felügyelt példányra nem alkalmazott erőforráscsoport engedélyei](#permissions-on-resource-group-not-applied-to-managed-instance)|Február 2020|Kerülő megoldás||
|[A manuális feladatátvétel korlátozása a portálon keresztül a feladatátvételi csoportok számára](#limitation-of-manual-failover-via-portal-for-failover-groups)|2020. január|Kerülő megoldás||
|[Az SQL Agent-szerepköröknek explicit EXECUTE-engedélyekre van szükségük a nem rendszergazdai bejelentkezésekhez](#in-memory-oltp-memory-limits-are-not-applied)|2019. december|Kerülő megoldás||
|[Az SQL Agent-feladatokat megszakíthatja az ügynökfolyamat újraindítása](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|2019. december|Nincs megoldás|Már 2020|
|[Az SSDT nem támogatja az AAD bejelentkezéseket és a felhasználókat](#aad-logins-and-users-are-not-supported-in-ssdt)|2019. november|Nincs megoldás||
|[A memórián belüli OLTP memóriakorlátok nincsenek alkalmazva](#in-memory-oltp-memory-limits-are-not-applied)|2019. október|Kerülő megoldás||
|[Helytelen hiba történt a nem üres fájl eltávolítása közben](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|2019. október|Kerülő megoldás||
|[A szolgáltatásszint módosítása és a példányműveletek létrehozása a folyamatban lévő adatbázis-visszaállítás blokkolásával történik](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|2019 szept.|Kerülő megoldás||
|[Előfordulhat, hogy a feladatátvétel után újra kell konfigurálni az üzleti legkritikusabb szolgáltatási szint erőforrás-kormányzóját](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|2019 szept.|Kerülő megoldás||
|[Az adatbázisközi service broker párbeszédeket újra inicializálni kell a szolgáltatásiszint-frissítés után](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|2019. augusztus|Kerülő megoldás||
|[Az Azure AD bejelentkezési típusok beszemélyesítése nem támogatott](#impersonification-of-azure-ad-login-types-is-not-supported)|2019. július|Nincs megoldás||
|[@querya paraméter nem támogatott a sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|2019. április|Nincs megoldás||
|[A tranzakciós replikációt újra kell konfigurálni a földrajzi feladatátvétel után](#transactional-replication-must-be-reconfigured-after-geo-failover)|2019 márc.|Nincs megoldás||
|[Az ideiglenes adatbázis a RESTORE művelet során használatos](#temporary-database-is-used-during-restore-operation)||Kerülő megoldás||
|[A TEMPDB szerkezete és tartalma újra létrejön](#tempdb-structure-and-content-is-re-created)||Nincs megoldás||
|[A tárhely túllépése kis adatbázisfájlokkal](#exceeding-storage-space-with-small-database-files)||Kerülő megoldás||
|[Az adatbázisnevek helyett megjelenített GUID-értékek](#guid-values-shown-instead-of-database-names)||Kerülő megoldás||
|[A hibanaplók nem maradnak meg](#error-logs-arent-persisted)||Nincs megoldás||
|[Az ugyanazon példányon belüli két adatbázis tranzakcióhatóköre nem támogatott](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Kerülő megoldás||
|[A CLR-modulok és a csatolt kiszolgálók néha nem hivatkozhatnak helyi IP-címre](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Kerülő megoldás||
|Az adatbázis konzisztenciáját nem ellenőrzi a DBCC CHECKDB, miután visszaállította az adatbázist az Azure Blob Storage-ból.||Feloldva|2019. november|
|Az üzleti legkritikusabb szintről az általános célú szintre történő időponthoz való visszaállítás nem lesz sikeres, ha a forrásadatbázis memórián belüli OLTP-objektumokat tartalmaz.||Feloldva|2019. október|
|Database Mail szolgáltatás külső (nem Azure-beli) levelezési kiszolgálókkal biztonságos kapcsolat használatával||Feloldva|2019. október|
|A felügyelt példány nem támogatja a tartalmazott adatbázisokat||Feloldva|2019. augusztus|

### <a name="permissions-on-resource-group-not-applied-to-managed-instance"></a>A felügyelt példányra nem alkalmazott erőforráscsoport engedélyei

Felügyelt példány közreműködői RBAC szerepkör, ha egy erőforráscsoportra (RG) alkalmazott nem alkalmazzák a felügyelt példány, és nincs hatása.

**Megoldás:** A felügyelt példány közreműködői szerepkör beállítása a felhasználók számára az előfizetés szintjén.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>A manuális feladatátvétel korlátozása a portálon keresztül a feladatátvételi csoportok számára

Ha a feladatátvételi csoport különböző Azure-előfizetések vagy erőforráscsoportok példányai között terjed, a feladatátvételi csoport elsődleges példányából nem indítható manuális feladatátvétel.

**Megoldás:** Feladatátvétel kezdeményezése a geoszekunder példány portálon keresztül.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>Az SQL Agent-szerepköröknek explicit EXECUTE-engedélyekre van szükségük a nem rendszergazdai bejelentkezésekhez

Ha nem rendszergazdai bejelentkezéseket ad hozzá az [SQL Agent rögzített adatbázisszerepköreihez,](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles)fennáll a probléma, amely miatt explicit EXECUTE-engedélyeket kell megadni a fő tárolt eljárásoknak a bejelentkezések működéséhez. Ha ez a probléma merül fel, a következő hibaüzenet jelenik meg: "A végrehajtás engedély megtagadva az objektumon, <object_name> (Microsoft SQL Server, Error: 229)" hibaüzenet jelenik meg.

**Megoldás:** Miután bejelentkezéseket adott hozzá az SQL Agent rögzített adatbázis-szerepköreihez: SQLAgentUserRole, SQLAgentReaderRole vagy SQLAgentOperatorRole, az ezekhez a szerepkörökhöz hozzáadott bejelentkezések mindegyike végrehajtja az alábbi T-SQL parancsfájlt, hogy explicit módon adjon EXECUTE-engedélyeket a felsorolt tárolt eljárásokhoz.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>Az SQL Agent-feladatokat megszakíthatja az ügynökfolyamat újraindítása

Az SQL Agent minden feladat indításakor új munkamenetet hoz létre, fokozatosan növelve a memóriafelhasználást. Annak elkerülése érdekében, hogy elérje a belső memóriakorlátot, amely blokkolja az ütemezett feladatok végrehajtását, az ügynökfolyamat újraindul, amint a memóriafelhasználás eléri a küszöbértéket. Ez azt eredményezheti, hogy megszakítja a feladatok végrehajtása fut az újraindítás pillanatában.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>A memórián belüli OLTP memóriakorlátok nincsenek alkalmazva

Az üzleti legkritikusabb szolgáltatási szint bizonyos esetekben nem alkalmazza megfelelően [a memóriaoptimalizált objektumok maximális memóriakorlátait.](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) A felügyelt példány lehetővé teheti a számítási feladatok számára, hogy több memóriát használjanak a memórián belüli OLTP-műveletekhez, ami hatással lehet a példány rendelkezésre állására és stabilitására. A memóriában lévő OLTP-lekérdezések, amelyek elérik a határértékeket, előfordulhat, hogy nem sikerül azonnal. Ez a probléma hamarosan megoldódik. A több memórián belüli OLTP memóriát használó lekérdezések hamarabb sikertelenek lesznek, ha elérik a [korlátokat.](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space)

**Megoldás:** [A memórián belüli OLTP-tárhelyhasználat figyelése](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) [az SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) használatával annak érdekében, hogy a munkaterhelés ne használjon többet a rendelkezésre álló memóriánál. Növelje a virtuális magok számától függő memóriakorlátokat, vagy optimalizálja a munkaterhelést, hogy kevesebb memóriát használjon.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Helytelen hiba történt a nem üres fájl eltávolítása közben

Az SQL Server/Managed Instance [nem engedélyezi a felhasználónak, hogy olyan fájlt dobjon ki, amely nem üres](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Ha utasítással `ALTER DATABASE REMOVE FILE` próbál meg eltávolítani egy nem üres `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` adatfájlt, a hiba nem lesz azonnal visszaküldve. A felügyelt példány folyamatosan megpróbálja eldobni a fájlt, és `Internal server error`a művelet 30 min után sikertelen lesz a .

**Megoldás:** Távolítsa el a fájl `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` tartalmát a paranccsal. Ha ez az egyetlen fájl a fájlcsoportban, a fájl zsugorítása előtt törölnie kell az adatokat a fájlcsoporthoz társított táblából vagy partícióból, és szükség esetén be kell töltenie ezeket az adatokat egy másik táblába/partícióra.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>A szolgáltatásszint módosítása és a példányműveletek létrehozása a folyamatban lévő adatbázis-visszaállítás blokkolásával történik

A `RESTORE` folyamatban lévő utasítás, az Adatáttelepítési szolgáltatás áttelepítési folyamata és a beépített időpont-visszaállítás letiltja a szolgáltatásszint frissítését vagy a meglévő példány átméretezését, és új példányok létrehozását a visszaállítási folyamat befejezéséig. A visszaállítási folyamat blokkolja ezeket a műveleteket a felügyelt példányokon és a példánykészleteken ugyanabban az alhálózaton, ahol a visszaállítási folyamat fut. A példánykészletek példányait ez nem érinti. A szolgáltatásiréteg-műveletek létrehozása vagy módosítása nem fog sikertelen, vagy időtúllépések – a visszaállítási folyamat befejezése vagy megszakítása után folytatódnak.

**Megoldás:** Várja meg, amíg a visszaállítási folyamat befejeződik, vagy szakítsa meg a visszaállítási folyamatot, ha a létrehozási vagy frissítési szolgáltatásiréteg-művelet magasabb prioritású.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Előfordulhat, hogy a feladatátvétel után újra kell konfigurálni az üzleti legkritikusabb szolgáltatási szint erőforrás-kormányzóját

[Erőforrás-kormányzó](/sql/relational-databases/resource-governor/resource-governor) szolgáltatás, amely lehetővé teszi, hogy korlátozza a felhasználói számítási feladatokhoz rendelt erőforrások helytelenül osztályozza néhány felhasználói számítási feladatok után feladatátvétel vagy a felhasználó által kezdeményezett szolgáltatásszint módosítása (például a változás a maximális virtuális mag vagy a maximális példány tárolási mérete).

**Megoldás:** Rendszeresen vagy az SQL Agent feladat részeként futtathatja `ALTER RESOURCE GOVERNOR RECONFIGURE` az SQL-feladatot, amikor a példány elindul, ha [erőforrás-kormányzót](/sql/relational-databases/resource-governor/resource-governor)használ.

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>Az adatbázisközi service broker párbeszédeket újra inicializálni kell a szolgáltatásiszint-frissítés után

Az adatbázisközi service broker párbeszédek a szolgáltatásréteg-művelet módosítása után leállítják az üzenetek kézbesítését más adatbázisok szolgáltatásaihoz. Az üzenetek **nem vesznek el,** és megtalálhatók a feladói várólistában. A virtuális magok vagy a példánytároló méretének `service_broke_guid` bármilyen módosítása a felügyelt példányban a [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) nézet ben az összes adatbázis értékének módosítását eredményezi. Minden `DIALOG` [olyan, a BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) utasítással létrehozott, amely más adatbázisban lévő service brokerekre hivatkozik, leállítja az üzenetek kézbesítését a célszolgáltatásnak.

**Megoldás:** A szolgáltatási szint frissítése és után újrainicializálása előtt állítsa le az adatbázisközi Service Broker párbeszédeket használó tevékenységeket. Ha a szolgáltatási szint módosítása után még vannak kézbesítetlen üzenetek, olvassa el az üzeneteket a forrásvárólistából, és küldje el újra őket a célvárólistába.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>Az Azure AD bejelentkezési típusok beszemélyesítése nem támogatott

Az AAD-rendszer-biztonsági tagok használatával `EXECUTE AS USER` vagy `EXECUTE AS LOGIN` a következő abokkal történő megszemélyesítés nem támogatott:
-   AliasaD-felhasználók. Ebben az esetben a `15517`következő hibaüzenet jelenik meg.
- AAD bejelentkezések és a felhasználók alapján AAD alkalmazások vagy egyszerű szolgáltatás. Ebben az esetben `15517` a következő `15406`hibákjelennek meg, és a .

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@querya paraméter nem támogatott a sp_send_db_mail

A `@query` paraméter a [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) eljárás nem működik.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>A tranzakciós replikációt újra kell konfigurálni a földrajzi feladatátvétel után

Ha a tranzakciós replikáció engedélyezve van egy automatikus feladatátvételi csoport ban lévő adatbázisban, a felügyelt példány rendszergazdájának meg kell tisztítania a régi elsődleges összes kiadványt, és újra kell konfigurálnia őket az új elsődlegesen, miután egy másik régióba történő feladatátvétel történik. További részletekért lásd a [replikációt.](sql-database-managed-instance-transact-sql-information.md#replication)

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>Az SSDT nem támogatja az AAD bejelentkezéseket és a felhasználókat

Az SQL Server Data Tools nem támogatja teljes mértékben az Azure Active Directory-bejelentkezéseket és felhasználókat.

### <a name="temporary-database-is-used-during-restore-operation"></a>Az ideiglenes adatbázis a RESTORE művelet során használatos

Amikor egy adatbázis visszaállítása felügyelt példányon történik, a visszaállítási szolgáltatás először létrehoz egy üres adatbázist a kívánt névvel a példány nevének lefoglalásához. Egy idő után ez az adatbázis el lesz dobva, és elindul a tényleges adatbázis visszaállítása. *A visszaállítási* állapotban lévő adatbázis ideiglenesen véletlenszerű GUID értékkel fog rendelkezni a név helyett. Az ideiglenes név a visszaállítási folyamat befejezése `RESTORE` után az utasításban megadott kívánt névre változik. A kezdeti fázisban a felhasználó hozzáférhet az üres adatbázishoz, és akár táblákat is létrehozhat, vagy adatokat tölthet be az adatbázisban. Ez az ideiglenes adatbázis a második fázis indításakor el lesz dobva.

**Megoldás:** Ne férjen hozzá a visszaállított adatbázishoz, amíg nem látja, hogy a visszaállítás befejeződött.

### <a name="tempdb-structure-and-content-is-re-created"></a>A TEMPDB szerkezete és tartalma újra létrejön

Az `tempdb` adatbázis mindig 12 adatfájlra van felosztva, és a fájlszerkezet nem módosítható. A fájlonkénti maximális méret nem módosítható, és új `tempdb`fájlok nem adhatók hozzá a alkalmazáshoz. `Tempdb`a példány indításakor vagy átadásakor mindig üres adatbázisként jön `tempdb` létre, és a végrehajtott módosítások nem őrződnek meg.

### <a name="exceeding-storage-space-with-small-database-files"></a>A tárhely túllépése kis adatbázisfájlokkal

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`és `RESTORE DATABASE` utasítások sikertelen lehet, mert a példány elérheti az Azure Storage-korlátot.

Minden általános célú felügyelt példány legfeljebb 35 TB-os tárhellyel rendelkezik az Azure Premium Lemezterület számára fenntartva. Minden adatbázisfájl külön fizikai lemezre kerül. A lemezméretek 128 GB, 256 GB, 512 GB, 1 TB vagy 4 TB lehetnek. A lemezen nem használt terület nem kerül felszámításra, de az Azure Premium Disk méreteinek teljes összege nem haladhatja meg a 35 TB-ot. Bizonyos esetekben egy felügyelt példány, amely nem igényel 8 TB-ot összesen előfordulhat, hogy meghaladja a 35 TB-os Azure-korlátot a tárolási méret belső töredezettség miatt.

Egy általános célú felügyelt példány például egy nagy méretű, 1,2 TB méretű fájllal rendelkezhet egy 4 TB-os lemezen. Azt is lehet, hogy 248 fájlokat 1 GB-os méretű minden helyezett külön 128 GB-os lemezek. Ebben a példában:

- A lefoglalt lemeztár teljes mérete 1 x 4 TB + 248 x 128 GB = 35 TB.
- A példány adatbázisainak teljes fenntartott területe 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Ez a példa azt mutatja be, hogy bizonyos körülmények között a fájlok adott elosztása miatt előfordulhat, hogy egy felügyelt példány eléri a 35 TB-os korlátot, amely egy csatlakoztatott Azure Premium Disk számára van fenntartva, ha nem várható el.

Ebben a példában a meglévő adatbázisok továbbra is működnek, és problémamentesen növekedhetnek, amíg nem adnak hozzá új fájlokat. Új adatbázisok nem hozhatók létre és nem állíthatók vissza, mert nincs elég hely az új lemezmeghajtók számára, még akkor sem, ha az összes adatbázis teljes mérete nem éri el a példányméretkorlátot. Az ebben az esetben visszaadott hiba nem egyértelmű.

A [fennmaradó fájlok számát a](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) rendszernézetek segítségével azonosíthatja. Ha eléri ezt a korlátot, próbálja meg [kiüríteni és törölni a kisebb fájlok at a DBCC SHRINKFILE utasítással,](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) vagy váltson át az [üzleti legkritikusabb szintre, amely nem rendelkezik ezzel a korláttal.](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)

### <a name="guid-values-shown-instead-of-database-names"></a>Az adatbázisnevek helyett megjelenített GUID-értékek

Számos rendszernézet, teljesítményszámláló, hibaüzenet, XEvents és hibanapló-bejegyzés a tényleges adatbázisnevek helyett GUID adatbázis-azonosítókat jelenít meg. Ne hagyatkozzon ezekre a GUID-azonosítókra, mert a jövőben tényleges adatbázisnevekre cseréli őket.

**Megoldás:** A sys.databases nézet használatával feloldhatja a tényleges adatbázisnevet a fizikai adatbázis névből, guid adatbázisazonosítók formájában megadva

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>A hibanaplók nem maradnak meg

A felügyelt példányban elérhető hibanaplók nem maradnak meg, és méretük nem szerepel a maximális tárolási korlátban. A hibanaplók automatikusan törlődhetnek, ha feladatátvétel történik. Előfordulhat, hogy a hibanapló előzményei hiányosak lehetnek, mert a felügyelt példányt több virtuális gépen is áthelyezték.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Az ugyanazon példányon belüli két adatbázis tranzakcióhatóköre nem támogatott

A `TransactionScope` .NET osztály nem működik, ha két lekérdezést küld két adatbázisnak ugyanazon a példányon belül ugyanazon tranzakcióhatókör alatt:

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

Bár ez a kód ugyanazon a példányon belüli adatokkal működik, MSDTC-t igényelt.

**Megoldás:** Az [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) segítségével két kapcsolat helyett egy másik adatbázist használjon kapcsolati környezetben.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>A CLR-modulok és a csatolt kiszolgálók néha nem hivatkozhatnak helyi IP-címre

A felügyelt példányban elhelyezett CLR-modulok és a csatolt kiszolgálókvagy az aktuális példányra hivatkozó elosztott lekérdezések néha nem oldhatók fel egy helyi példány IP-címével. Ez a hiba átmeneti probléma.

**Megoldás:** Ha lehetséges, használjon környezeti kapcsolatokat egy CLR-modulban.

## <a name="updates"></a>Frissítések

Az SQL Database frissítéseinek és fejlesztéseinek listáját az [SQL Database szolgáltatás frissítései című témakörben található.](https://azure.microsoft.com/updates/?product=sql-database)

Az összes Azure-szolgáltatás frissítéseit és fejlesztéseit a [Szolgáltatás frissítései .](https://azure.microsoft.com/updates)

## <a name="contribute-to-content"></a>Tartalmi közreműködés

Az Azure SQL Database dokumentációjához való hozzájáruláshoz tekintse meg a [Közreműködő dokumentumútmutatót.](https://docs.microsoft.com/contribute/)
