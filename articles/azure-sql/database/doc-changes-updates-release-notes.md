---
title: Újdonságok
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Ismerje meg a Azure SQL Database & SQL felügyelt példány-szolgáltatás és dokumentáció új funkcióit és tökéletesítéseit
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: sstein
ms.openlocfilehash: 9d3ba2f6106171b78584d74498df0bbe3d1306a6
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344798"
---
# <a name="whats-new-in-azure-sql-database--sql-managed-instance"></a>A Azure SQL Database & SQL felügyelt példányának újdonságai

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Ez a cikk a jelenleg nyilvános előzetes verzióban elérhető, Azure SQL Database és az Azure SQL felügyelt példányának funkcióit sorolja fel. SQL Database és SQL felügyelt példányok frissítései és tökéletesítései: [SQL Database & SQL felügyelt példányok szolgáltatásának frissítései](https://azure.microsoft.com/updates/?product=sql-database). Az egyéb Azure-szolgáltatások frissítéseihez és tökéletesítéséhez tekintse meg a [Service Updates](https://azure.microsoft.com/updates)című témakört.

## <a name="whats-new"></a>Újdonságok

A Azure SQL Database és az Azure SQL felügyelt példányának dokumentációja külön szakaszokra oszlik. Azt is frissítettük, hogyan hivatkozunk felügyelt példányra *Azure SQL Database felügyelt példányról* az *Azure SQL felügyelt*példányára.

Ezt azért hajtottuk végre, mert bizonyos funkciók és funkciók nagy mértékben változnak egyetlen adatbázis és felügyelt példány között, és egyre nagyobb kihívást jelent az Azure SQL Database és az Azure SQL felügyelt példánya között az egyes "megosztott" cikkekbe foglalt összetett árnyalatok ismertetése.

A különböző Azure SQL-termékek közötti pontosítás egyszerűsíti és egyszerűsíti az Azure-beli SQL Server adatbázismotor használatának folyamatát, függetlenül attól, hogy a Azure SQL Database egyetlen felügyelt adatbázisa, egy teljes körűen felügyelt példány, amely több adatbázist üzemeltet az Azure SQL felügyelt példányain, vagy az Azure-beli virtuális gépeken tárolt ismerős SQL Server terméket.

Vegye figyelembe, hogy ez a folyamat folyamatban van, és nem minden cikket frissítették még. Például a Transact-SQL (T-SQL) utasítások, a tárolt eljárások és a Azure SQL Database és az Azure SQL felügyelt példánya között megosztott számos szolgáltatás dokumentációja még nem fejeződött be, ezért köszönjük türelmét, hogy továbbra is tisztázza a tartalmat. 

Ez a táblázat a terminológia változásának gyors összehasonlítását biztosítja: 


|**Új kifejezés**  | **Előző kifejezés**  |**Magyarázat** |
|---------|---------|---------|
|**Felügyelt Azure SQL-példány** | *Felügyelt példány* Azure SQL Database| Az Azure SQL felügyelt példánya a saját terméke az Azure SQL családon belül, nem csupán a Azure SQL Databaseon belüli üzembe helyezési lehetőség. | 
|**Azure SQL Database**|*Önálló adatbázis* Azure SQL Database| Ha nincs kifejezetten megadva, a terméknév Azure SQL Database a rugalmas készletbe telepített önálló adatbázisokat és adatbázisokat is tartalmazza. |
|**Azure SQL Database**|*Rugalmas készlet* Azure SQL Database| Ha nincs kifejezetten megadva, a terméknév Azure SQL Database a rugalmas készletbe telepített önálló adatbázisokat és adatbázisokat is tartalmazza.  |
|**Azure SQL Database** |Azure SQL Database | Bár a kifejezés változatlan marad, az már csak az önálló adatbázisra és a rugalmas készletekre vonatkozik, és nem tartalmazza a felügyelt példányt. |
| **Azure SQL**| N/A | Ez az Azure-ban elérhető SQL Server adatbázismotor-termékek családját jelenti: Azure SQL Database, Azure SQL felügyelt példány és Azure-beli virtuális gépeken SQL Server. | 

## <a name="features-in-public-preview"></a>A nyilvános előzetes verzióban elérhető funkciók

### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

| Funkció | Részletek |
| ---| --- |
| Új Fsv2 és M-sorozatú hardveres generációk| További információ: [Hardware Generations](service-tiers-vcore.md#hardware-generations).|
| Gyorsított adatbázis-helyreállítás önálló adatbázisokkal és rugalmas készletekkel | További információ: [gyorsított adatbázis-helyreállítás](../accelerated-database-recovery.md).|
| Adatfelderítés és besorolás  |További információ: [Azure SQL Database és az Azure szinapszis Analytics adatfelderítési & besorolása](data-discovery-and-classification-overview.md).|
| Rugalmas adatbázis-feladatok | További információ: [rugalmas feladatok létrehozása, konfigurálása és kezelése](elastic-jobs-overview.md). |
| Rugalmas lekérdezések | További információ: [rugalmas lekérdezés áttekintése](elastic-query-overview.md). |
| Rugalmas tranzakciók | [Elosztott tranzakciók felhőalapú adatbázisok között](elastic-transactions-overview.md). |
| A Azure Portal lekérdezés-szerkesztője |További információ: [a Azure Portal SQL Query Editor használata az adatok összekapcsolásához és lekérdezéséhez](connect-query-portal.md).|
| R-szolgáltatások/gépi tanulás önálló adatbázisokkal és rugalmas készletekkel |További információ: [Machine Learning Services Azure SQL Database](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
|SQL Analytics|További információ: [Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).|
| &nbsp; |

### <a name="azure-sql-managed-instance"></a>[Felügyelt Azure SQL-példány](#tab/managed-instance)

| Funkció | Részletek |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Példánykészletek</a> | A kisebb SQL-példányok felhőbe való áttelepíthető kényelmes és költséghatékony módszer. |
| <a href="https://aka.ms/managed-instance-aadlogins">Példány szintű Azure AD-kiszolgálói rendszerbiztonsági tag (bejelentkezések)</a> | Hozzon létre példány-szintű bejelentkezéseket <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">a Create login utasítás használatával a külső szolgáltató</a> utasításban. |
| [Tranzakciós replikáció](../managed-instance/replication-transactional-overview.md) | A táblák módosításait replikálja más, SQL felügyelt példányokra, SQL-adatbázisokra vagy SQL Server példányokra helyezett adatbázisokra, vagy frissítheti a táblázatokat, ha egyes sorok módosulnak más SQL felügyelt példányokban vagy SQL Server példányban. További információ: [replikáció konfigurálása az Azure SQL felügyelt példányában](../managed-instance/replication-between-two-instances-configure-tutorial.md). |
| Fenyegetések észlelése |További információ: [a veszélyforrások észlelésének konfigurálása az Azure SQL felügyelt példányában](../managed-instance/threat-detection-configure.md).|
| Biztonsági mentés hosszú távú megőrzése | További információ: a [hosszú távú biztonsági mentések megőrzésének konfigurálása az Azure SQL felügyelt példányain](../managed-instance/long-term-backup-retention-configure.md), amely jelenleg korlátozott nyilvános előzetes verzióban érhető el. | 

---

## <a name="sql-managed-instance---new-features-and-known-issues"></a>SQL felügyelt példány – új funkciók és ismert problémák

### <a name="sql-managed-instance-h2-2019-updates"></a>SQL felügyelt példány H2 2019-frissítések

- [Szolgáltatással segített alhálózat konfigurációja](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) Biztonságos és kényelmes mód az alhálózati konfiguráció kezelésére, ahol az adatforgalom szabályozása mellett az SQL felügyelt példány biztosítja a felügyeleti forgalom zavartalan áramlását.
- Az [transzparens adattitkosítás (TDE) és bring your own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) lehetővé teszi a saját kulcsú (BYOK) környezet használatát a REST-alapú adatvédelem érdekében, és lehetővé teszi a szervezetek számára a kulcsok és adatok elkülönítését a felügyeleti feladatok elvégzéséhez.
- Az [automatikus feladatátvételi csoportok](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) lehetővé teszik, hogy az elsődleges példány összes adatbázisát egy másik régió másodlagos példányára replikálja.
- Konfigurálja az SQL felügyelt példányának viselkedését a [globális nyomkövetési jelzők](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/)segítségével.

### <a name="sql-managed-instance-h1-2019-updates"></a>SQL felügyelt példány H1 2019 frissítése

A következő funkciók engedélyezve vannak az SQL felügyelt példány üzembe helyezési modelljében a H1 2019-ben:
  - <a href="https://aka.ms/sql-mi-visual-studio-subscribers">Azure havi Kredittel</a> rendelkező előfizetések támogatása a Visual Studio-előfizetőknek és a megnövekedett [regionális korlátoknak](../managed-instance/resource-limits.md#regional-resource-limitations)megfelelően.
  - A <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 és SharePoint 2019 </a> és a <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a> támogatása
  - Hozzon létre SQL felügyelt példányokat az Ön által választott <a href="https://aka.ms/managed-instance-collation">példány-szintű rendezéssel</a> és <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">időzónával</a> .
  - Az SQL felügyelt példányai mostantól a [beépített tűzfallal](../managed-instance/management-endpoint-verify-built-in-firewall.md)védettek.
  - Konfigurálja a felügyelt SQL-példányokat [nyilvános végpontok](../managed-instance/public-endpoint-configure.md)használatára, [proxy felülbírálási](connectivity-architecture.md#connection-policy) kapcsolatban a hálózati teljesítmény növeléséhez, <a href="https://aka.ms/four-cores-sql-mi-update">4 virtuális mag a Gen5</a> , vagy <a href="https://aka.ms/managed-instance-configurable-backup-retention">konfigurálja a biztonsági másolatok megőrzését 35 napra</a> az időponthoz való visszaállításhoz. A [biztonsági másolatok hosszú távú megőrzése](long-term-retention-overview.md#sql-managed-instance-support) (akár 10 évig) jelenleg korlátozott nyilvános előzetes verzióban érhető el.  
  - Az új funkciók lehetővé teszik, hogy az <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">adatbázist egy másik adatközpontba állítsa be a PowerShell használatával</a>, az [adatbázis átnevezésével](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), a [virtuális fürt törlésével](../managed-instance/virtual-cluster-delete.md).
  - Az új beépített [példány-közreműködő szerepkör](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) lehetővé teszi a vám (SOD) megfelelőségének a biztonsági elvekkel való elválasztását, valamint a vállalati szabványoknak való megfelelést.
  - Az SQL felügyelt példánya a következő Azure Government régiókban érhető el a GA (US Gov Texas, US Gov Arizona), valamint a Észak-Kína 2 és Kelet-Kína 2 között. A következő nyilvános régiókban érhető el: Közép-Ausztrália, Közép-Ausztrália, Dél-Brazília, Dél-Franciaország, Egyesült Arab Emírségek, Észak-Európa, Dél-Afrika, Dél-Afrika, Nyugat-Európa.

### <a name="known-issues"></a>Ismert problémák

|Probléma  |Felderített dátum  |Állapot  |Feloldás dátuma  |
|---------|---------|---------|---------|
|[A manuális biztonsági mentés ELLENŐRZŐÖSSZEG nélküli visszaállítása sikertelen lehet](#restoring-manual-backup-without-checksum-might-fail)|2020. május|Megkerülő megoldás| |
|[Az ügynök nem válaszol a meglévő feladatok módosítása, letiltása vagy engedélyezése esetén](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|2020. május|Automatikusan lecsökkentve| |
|[Nem az SQL felügyelt példányára vonatkozó engedélyek vannak alkalmazva az erőforráscsoporthoz](#permissions-on-resource-group-not-applied-to-sql-managed-instance)|Feb 2020|Megkerülő megoldás||
|[Feladatátvételi csoportok manuális feladatátvételének korlátozása a portálon keresztül](#limitation-of-manual-failover-via-portal-for-failover-groups)|Jan 2020|Megkerülő megoldás||
|[Az SQL-ügynök szerepköreinek kifejezett EXECUTE engedélyekre van szükségük a nem sysadmin (nem rendszergazdai) bejelentkezésekhez](#in-memory-oltp-memory-limits-are-not-applied)|Dec 2019|Megkerülő megoldás||
|[Az SQL-ügynök feladatait az ügynök folyamatának újraindítása okozhatja](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Dec 2019|Feloldva|Mar 2020|
|[A HRE-bejelentkezések és a felhasználók nem támogatottak a SSDT](#aad-logins-and-users-are-not-supported-in-ssdt)|November 2019|Nincs Áthidaló megoldás||
|[A memóriában tárolt OLTP-korlátok nincsenek alkalmazva](#in-memory-oltp-memory-limits-are-not-applied)|TOT 2019|Megkerülő megoldás||
|[Helytelen hiba történt a nem üres fájl eltávolítására tett kísérlet során.](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|TOT 2019|Megkerülő megoldás||
|[A szolgáltatási szintek módosítása és a példány-létrehozási műveletek blokkolva vannak az adatbázis-visszaállítás folyamatban](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Sep 2019|Megkerülő megoldás||
|[Előfordulhat, hogy a üzletileg kritikus szolgáltatási szinten újra kell konfigurálni a Resource Governor a feladatátvétel után](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Sep 2019|Megkerülő megoldás||
|[A szolgáltatási réteg frissítése után újra kell inicializálni a több adatbázis Service Broker párbeszédpaneleit.](#cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade)|Augusztus 2019|Megkerülő megoldás||
|[Az Azure AD bejelentkezési típusok megszemélyesítése nem támogatott](#impersonification-of-azure-ad-login-types-is-not-supported)|Jul 2019|Nincs Áthidaló megoldás||
|[@querya paraméter nem támogatott sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|Apr 2019|Nincs Áthidaló megoldás||
|[A tranzakciós replikációt újra kell konfigurálni a Geo-feladatátvétel után](#transactional-replication-must-be-reconfigured-after-geo-failover)|Mar 2019|Nincs Áthidaló megoldás||
|[A VISSZAÁLLÍTÁSi művelet során a rendszer ideiglenes adatbázist használ](#temporary-database-is-used-during-restore-operation)||Megkerülő megoldás||
|[A TEMPDB szerkezete és tartalma újra létrejön](#tempdb-structure-and-content-is-re-created)||Nincs Áthidaló megoldás||
|[Kis méretű adatbázisfájlok esetén a tárterület nagyobb](#exceeding-storage-space-with-small-database-files)||Megkerülő megoldás||
|[Az adatbázis neve helyett GUID-értékek láthatók](#guid-values-shown-instead-of-database-names)||Megkerülő megoldás||
|[A hibanapló nem marad meg](#error-logs-arent-persisted)||Nincs Áthidaló megoldás||
|[Ugyanazon példányon belül két adatbázis tranzakciós hatóköre nem támogatott](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Megkerülő megoldás|Mar 2020|
|[A CLR-modulok és a csatolt kiszolgálók időnként nem hivatkozhatnak helyi IP-címekre.](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Megkerülő megoldás||
|Az adatbázis konzisztencia-ellenőrzése a DBCC CHECKDB UTASÍTÁST használatával nem történt meg az Azure-beli Blob Storage adatbázis visszaállítása után.||Feloldva|November 2019|
|Az adott időponthoz tartozó adatbázis-visszaállítás üzletileg kritikus rétegről általános célú szintjére nem lesz sikeres, ha a forrásadatbázis memóriában tárolt OLTP objektumokat tartalmaz.||Feloldva|TOT 2019|
|Database Mail funkció külső (nem Azure-beli) levelezési kiszolgálókkal biztonságos kapcsolatok használatával||Feloldva|TOT 2019|
|A felügyelt SQL-példányok nem támogatják a foglalt adatbázisokat.||Feloldva|Augusztus 2019|


### <a name="restoring-manual-backup-without-checksum-might-fail"></a>A manuális biztonsági mentés ELLENŐRZŐÖSSZEG nélküli visszaállítása sikertelen lehet

Bizonyos körülmények között a felügyelt példányon az ELLENŐRZŐÖSSZEG nélkül létrehozott adatbázisok manuális biztonsági mentését nem lehet visszaállítani. Ilyen esetekben próbálja meg újra visszaállítani a biztonsági mentést, amíg a művelet nem sikerült.

**Megkerülő megoldás**: manuális biztonsági mentést készíthet a felügyelt példányon tárolt adatbázisokról az ellenőrzőösszeg engedélyezésével.

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>Az ügynök nem válaszol a meglévő feladatok módosítása, letiltása vagy engedélyezése esetén

Bizonyos körülmények között, ha egy meglévő feladatot módosítanak, letiltják vagy engedélyezik, az ügynök nem válaszol. A probléma automatikusan az ügynök folyamatának újraindítását eredményező észleléskor lesz enyhítve.

### <a name="permissions-on-resource-group-not-applied-to-sql-managed-instance"></a>Nem az SQL felügyelt példányára vonatkozó engedélyek vannak alkalmazva az erőforráscsoporthoz

Az SQL felügyelt példány közreműködői RBAC szerepköre nem lett alkalmazva az SQL felügyelt példányára, és nincs hatása.

**Áthidaló megoldás**: állítsa be az SQL felügyelt példány közreműködői szerepkört a felhasználók számára az előfizetés szintjén.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Feladatátvételi csoportok manuális feladatátvételének korlátozása a portálon keresztül

Ha a feladatátvételi csoport különböző Azure-előfizetések vagy-erőforráscsoportok példányai között kiterjed, a manuális feladatátvétel nem indítható el a feladatátvételi csoport elsődleges példányáról.

**Áthidaló megoldás**: kezdeményezzen feladatátvételt a portálon keresztül a Geo-másodlagos példányon keresztül.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>Az SQL-ügynök szerepköreinek kifejezett EXECUTE engedélyekre van szükségük a nem sysadmin (nem rendszergazdai) bejelentkezésekhez

Ha nem sysadmin bejelentkezési adatokat ad hozzá az [SQL Agent rögzített adatbázis-szerepköreihez](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles), létezik egy olyan probléma, amelyben a bejelentkezési adatok működéséhez meg kell adni explicit végrehajtási engedélyeket a fő tárolt eljárásokhoz. A probléma előfordulásakor a "végrehajtás engedély megtagadva az objektumon <object_name> (Microsoft SQL Server, hiba: 229)" hibaüzenet jelenik meg.

**Megkerülő megoldás**: Miután hozzáadta a bejelentkezéseket az SQL Agent rögzített adatbázis-szerepköreihez: SQLAgentUserRole, SQLAgentReaderRole vagy sqlagentoperatorrole indíthatják, a szerepkörökhöz hozzáadott összes bejelentkezéshez hajtsa végre az alábbi T-SQL-szkriptet, hogy explicit módon adjon végrehajtási engedélyeket a felsorolt tárolt eljárásoknak.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>Az SQL-ügynök feladatait az ügynök folyamatának újraindítása okozhatja

**(Megoldva a 2020. márciusi)** Az SQL Agent egy új munkamenetet hoz létre minden alkalommal, amikor elindít egy feladatot, és fokozatosan növeli a memória-felhasználást. Ha el szeretné kerülni a belső memória korlátját, ami letiltja az ütemezett feladatok végrehajtását, az ügynök folyamata újraindul, miután a memória-felhasználás eléri a küszöbértéket. Ez a művelet az újraindítás pillanatában futó feladatok végrehajtásának megszakításához vezethet.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>A memóriában tárolt OLTP-korlátok nincsenek alkalmazva

Üzletileg kritikus a szolgáltatási réteg nem fogja megfelelően alkalmazni [a memóriára optimalizált objektumok maximális memóriára vonatkozó korlátozásait](../managed-instance/resource-limits.md#in-memory-oltp-available-space) bizonyos esetekben. A felügyelt SQL-példányok lehetővé tehetik, hogy a számítási feladatok nagyobb memóriát használjanak a memóriában tárolt OLTP-műveletekhez, ami hatással lehet a példány rendelkezésre állására és stabilitására. Előfordulhat, hogy a korlátokat elérő memóriabeli OLTP-lekérdezések nem lesznek azonnal sikertelenek. Ezt a problémát hamarosan megjavítja a rendszer. A memóriában lévő OLTP memóriát használó lekérdezések hamarabb sikertelenek lesznek, ha elérik a [határértékeket](../managed-instance/resource-limits.md#in-memory-oltp-available-space).

**Áthidaló megoldás:** [figyelje a MEMÓRIÁban tárolt OLTP](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitor-space) a [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) használatával, hogy a számítási feladatok ne használják a rendelkezésre álló memóriát. Növelje a memória korlátait, amelyek a virtuális mag számától függenek, vagy optimalizálja a számítási feladatokat kevesebb memória használatára.
 
### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Helytelen hiba történt a nem üres fájl eltávolítására tett kísérlet során.

SQL Server/SQL felügyelt példány nem [engedélyezi a felhasználó számára, hogy ne ejtsen el olyan fájlt, amely nem üres](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Ha nem üres adatfájlt próbál meg eltávolítani `ALTER DATABASE REMOVE FILE` az utasítás használatával, a `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` rendszer nem küldi el azonnal a hibát. Az SQL felügyelt példánya továbbra is megpróbálja eldobni a fájlt, és a művelet 30 perc után sikertelen lesz `Internal server error` .

**Áthidaló megoldás**: távolítsa el a fájl tartalmát a `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` parancs használatával. Ha ez az egyetlen fájl a fájlcsoportja, akkor törölnie kell az ehhez a fájlcsoportja tartozó táblából vagy partícióból származó adatait a fájl zsugorítása előtt, és opcionálisan be kell töltenie ezeket az adatok egy másik táblába/partícióba.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>A szolgáltatási szintek módosítása és a példány-létrehozási műveletek blokkolva vannak az adatbázis-visszaállítás folyamatban

`RESTORE`A folyamatos utasítás, az adatáttelepítési szolgáltatás áttelepítési folyamata és a beépített idővisszaállítási funkció letiltja a meglévő példány frissítési vagy átméretezését, valamint új példányok létrehozását, amíg a visszaállítási folyamat be nem fejeződik. A visszaállítási folyamat letiltja ezeket a műveleteket a felügyelt példányokon és példány-készleteken ugyanabban az alhálózatban, ahol a visszaállítási folyamat fut. A példányok példányai nem érintettek. A szolgáltatási réteg műveleteinek létrehozása vagy módosítása nem fog sikerülni vagy időtúllépés – a visszaállítási folyamat befejeződése vagy megszakítása után folytatódni fog.

**Áthidaló megoldás**: Várjon, amíg a visszaállítási folyamat befejeződik, vagy szakítsa meg a visszaállítási folyamatot, ha a Service-réteg létrehozása vagy frissítése magasabb prioritással rendelkezik.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Előfordulhat, hogy a üzletileg kritikus szolgáltatási szinten újra kell konfigurálni a Resource Governor a feladatátvétel után

[Resource Governor](/sql/relational-databases/resource-governor/resource-governor) funkció, amely lehetővé teszi a felhasználói munkaterheléshez rendelt erőforrások korlátozását, előfordulhat, hogy a feladatátvétel vagy a felhasználó által kezdeményezett változás miatt helytelenül osztályoz bizonyos felhasználói munkaterheléseket (például a maximális virtuális mag vagy a példányok maximális tárolási méretének módosítása).

**Megkerülő megoldás**: `ALTER RESOURCE GOVERNOR RECONFIGURE` időnként vagy az SQL-ügynök feladatának részeként futtassa az SQL-feladatot, amikor a példány elindul, ha [Resource Governor](/sql/relational-databases/resource-governor/resource-governor)használ.

### <a name="cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade"></a>A szolgáltatási réteg frissítése után újra kell inicializálni a több adatbázis Service Broker párbeszédpaneleit.

Az adatbázisok közötti Service Broker párbeszédpanelek nem teszik elérhetővé az üzeneteket más adatbázisokban lévő szolgáltatásoknak a szolgáltatási réteg módosítása után. Az üzenetek **nem vesznek** el, és a küldő várólistáján találhatók. A virtuális mag vagy a példány tárolási méretének változása az SQL felügyelt példányában a `service_broke_guid` [sys. Databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) nézet értékét fogja megváltoztatni az összes adatbázis esetében. A `DIALOG` más adatbázisban található Service Broker szolgáltatásra hivatkozó [BEGIN Dialog](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) utasítással létrehozott minden egyes létrehozott üzenet nem fog üzeneteket kézbesíteni a célként megadott szolgáltatásnak.

**Áthidaló megoldás:** A szolgáltatási réteg frissítése előtt állítson le minden olyan tevékenységet, amely több adatbázison Service Broker párbeszéd-beszélgetést használ, majd az újrainicializálása után. Ha a szolgáltatási szintek változása után még nem elérhető üzenetek vannak, olvassa el a forrás-üzenetsor üzeneteit, és küldje el újra a cél várólistára.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>Az Azure AD bejelentkezési típusok megszemélyesítése nem támogatott

A `EXECUTE AS USER` `EXECUTE AS LOGIN` következő HRE-rendszerbiztonsági tag vagy a használatával történő megszemélyesítés nem támogatott:
-    Alias HRE-felhasználók. Ebben az esetben a következő hibaüzenetet adja vissza `15517` .
- HRE-bejelentkezések és felhasználók HRE-alkalmazások vagy egyszerű szolgáltatások alapján. Ebben az esetben a következő hibákat adja vissza: `15517` és `15406` .

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@querya paraméter nem támogatott sp_send_db_mail

A `@query` [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) eljárásban szereplő paraméter nem működik.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>A tranzakciós replikációt újra kell konfigurálni a Geo-feladatátvétel után

Ha a tranzakciós replikáció engedélyezve van egy automatikus feladatátvételi csoportban lévő adatbázison, akkor az SQL felügyelt példányának rendszergazdájának törölnie kell a régi elsődleges összes kiadványt, és újra kell konfigurálnia azokat az új elsődlegesen a feladatátvételt követően egy másik régióba. További részletekért lásd: [replikáció](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication) .

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>A HRE-bejelentkezések és a felhasználók nem támogatottak a SSDT

SQL Server Data Tools nem támogatja teljes mértékben az Azure Active Directory-bejelentkezéseket és-felhasználókat.

### <a name="temporary-database-is-used-during-restore-operation"></a>A VISSZAÁLLÍTÁSi művelet során a rendszer ideiglenes adatbázist használ

Ha egy adatbázis a felügyelt SQL-példányon van helyreállítva, a visszaállítási szolgáltatás először egy üres adatbázist hoz létre a kívánt névvel a példányon a név lefoglalásához. Némi idő elteltével ez az adatbázis el lesz dobva, és a rendszer elindítja a tényleges adatbázis visszaállítását. A *visszaállítási* állapotban lévő adatbázis neve helyett véletlenszerű GUID-értékkel fog rendelkezni. Az ideiglenes név az utasításban megadott kívánt névre változik a `RESTORE` visszaállítási folyamat befejeződése után. A kezdeti fázisban a felhasználó hozzáférhet az üres adatbázishoz, és akár táblákat is létrehozhat, vagy betöltheti az adatbázist. Ezt az ideiglenes adatbázist a rendszer elveti, ha a visszaállítási szolgáltatás elindítja a második fázist.

**Megkerülő megoldás**: ne nyissa meg a visszaállítani kívánt adatbázist, amíg nem látja, hogy a visszaállítás befejeződött.

### <a name="tempdb-structure-and-content-is-re-created"></a>A TEMPDB szerkezete és tartalma újra létrejön

Az `tempdb` adatbázis mindig 12 adatfájlra van bontva, és a fájl szerkezete nem módosítható. A fájlok maximális mérete nem módosítható, és az új fájlok nem adhatók hozzá a következőhöz: `tempdb` . `Tempdb`a rendszer mindig üres adatbázisként hozza létre újra, amikor a példány elindul vagy feladatátvételt végez, és a folyamatban lévő módosításokat `tempdb` nem őrzi meg a rendszer.

### <a name="exceeding-storage-space-with-small-database-files"></a>Kis méretű adatbázisfájlok esetén a tárterület nagyobb

`CREATE DATABASE`a, `ALTER DATABASE ADD FILE` és `RESTORE DATABASE` az utasítások sikertelenek lehetnek, mert a példány elérheti az Azure Storage-korlátot.

Minden általános célú SQL felügyelt példány legfeljebb 35 TB tárterülettel rendelkezik, amely az Azure Premium lemezterület számára van fenntartva. Minden adatbázisfájl külön fizikai lemezre kerül. A lemezek mérete 128 GB, 256 GB, 512 GB, 1 TB vagy 4 TB lehet. A lemez nem használt lemezterülete nem számít fel díjat, de az Azure Premium-lemezek teljes mérete nem haladhatja meg a 35 TB-ot. Bizonyos esetekben előfordulhat, hogy egy olyan SQL felügyelt példány, amely nem igényel 8 TB-ot, a belső töredezettség miatt túllépheti a 35 TB-os Azure-korlátot a tárterület méretétől függően.

Előfordulhat például, hogy egy általános célú SQL felügyelt példányon egy 4 TB-os lemezre helyezett 1,2 TB méretű fájl található. Az is előfordulhat, hogy a 248-es fájlok mindegyike 1 GB méretű, és mindegyik külön 128 GB-os lemezre van helyezve. Ebben a példában:

- A lefoglalt tárterület teljes mérete 1 x 4 TB + 248 x 128 GB = 35 TB.
- A példányon lévő adatbázisok teljes lefoglalt területe 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Ez a példa azt szemlélteti, hogy bizonyos körülmények között a fájlok adott eloszlása miatt egy SQL-felügyelt példány elérheti a csatolt Azure Premium-lemez számára fenntartott 35 TB-os korlátot, ha nem várható.

Ebben a példában a meglévő adatbázisok továbbra is működőképesek maradnak, és bármilyen probléma nélkül növekednek, amíg új fájlok nem lettek hozzáadva. Új adatbázisok nem hozhatók létre és nem állíthatók vissza, mert nincs elég hely az új lemezmeghajtók számára, még akkor is, ha az összes adatbázis teljes mérete nem éri el a példány méretkorlát-korlátját. Az ebben az esetben visszaadott hiba nem egyértelmű.

[A fennmaradó fájlok számát](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) a rendszernézetek használatával is meghatározhatja. Ha eléri ezt a korlátot, próbálja meg [üresen hagyni, és töröljön néhány kisebb fájlt a DBCC SHRINKFILE utasítás használatával](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) , vagy váltson a [üzletileg kritikus szintjére, amely nem rendelkezik ezzel a korláttal](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Az adatbázis neve helyett GUID-értékek láthatók

A rendszernézetek, a teljesítményszámlálók, a hibaüzenetek, a Xevent típusú eseményekhez és a hibanapló-bejegyzések a tényleges adatbázis neve helyett GUID-adatbázis-azonosítókat jelenítenek meg. Ne használja ezeket a GUID azonosítókat, mert azokat a rendszer a későbbiekben a tényleges adatbázis-nevekkel cseréli le.

**Megkerülő megoldás**: a sys. Databases nézetet használva oldja fel a tényleges adatbázis nevét a fizikai adatbázis nevéből, a GUID adatbázis-azonosítók formájában megadva.

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>A hibanapló nem marad meg

Az SQL felügyelt példányában elérhető naplók nem maradnak meg, és a méreteik nem szerepelnek a maximális tárolási korlátban. Előfordulhat, hogy a naplók automatikusan törlődnek, ha a feladatátvétel történik. Előfordulhat, hogy rések vannak a hibák naplózási előzményeiben, mert az SQL felügyelt példánya többször is át lett helyezve több virtuális gépre.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Ugyanazon példányon belül két adatbázis tranzakciós hatóköre nem támogatott

**(Megoldva a 2020. márciusi)** A `TransactionScope` .net-osztály nem működik, ha két lekérdezést küldenek ugyanazon példányon belül két adatbázisnak ugyanazon tranzakció hatókörében:

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

**Áthidaló megoldás (2020 márciusa óta nem szükséges):** A [SqlConnection. ChangeDatabase (string)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) használatával használjon másik adatbázist kapcsolati környezetben a két kapcsolat használata helyett.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>A CLR-modulok és a csatolt kiszolgálók időnként nem hivatkozhatnak helyi IP-címekre.

A felügyelt SQL-példányba helyezett CLR-modulok és a társított kiszolgálók vagy az aktuális példányra hivatkozó elosztott lekérdezések néha nem tudják feloldani a helyi példányok IP-címét. Ez a hiba átmeneti probléma.

**Áthidaló megoldás:** Ha lehetséges, használjon egy CLR-modul környezeti kapcsolatait.

## <a name="updates"></a>Frissítések

SQL Database frissítéseinek és tökéletesítésének listáját lásd: [SQL Database szolgáltatás frissítései](https://azure.microsoft.com/updates/?product=sql-database).

Az összes Azure-szolgáltatás frissítéséhez és tökéletesítéséhez tekintse meg a [Service Updates](https://azure.microsoft.com/updates)című témakört.

## <a name="contribute-to-content"></a>Tartalmi közreműködés

Az Azure SQL-dokumentációhoz való hozzájárulásért tekintse meg a [docs közreműködői útmutatóját](https://docs.microsoft.com/contribute/).
