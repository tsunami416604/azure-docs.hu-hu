---
title: Kibocsátási megjegyzések
description: Ismerje meg a Azure SQL Database szolgáltatás új szolgáltatásait és funkcióit, valamint a Azure SQL Database dokumentációját
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: sstein
ms.openlocfilehash: 27a62223970b0f697465ce9aa050f3fccbcae464
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106423"
---
# <a name="sql-database-release-notes"></a>SQL Database kibocsátási megjegyzések

Ez a cikk a jelenleg nyilvános előzetes verzióban elérhető SQL Database-szolgáltatásokat sorolja fel. SQL Database frissítésekhez és a tökéletesítésekhez lásd: [SQL Database Service Updates](https://azure.microsoft.com/updates/?product=sql-database). Az egyéb Azure-szolgáltatások frissítéseihez és tökéletesítéséhez tekintse meg a [Service Updates](https://azure.microsoft.com/updates)című témakört.

## <a name="features-in-public-preview"></a>A nyilvános előzetes verzióban elérhető funkciók

### <a name="single-database"></a>[Önálló adatbázis](#tab/single-database)

| Szolgáltatás | Részletek |
| ---| --- |
| Új Fsv2 és M-sorozatú hardveres generációk| További információ: [Hardware Generations](sql-database-service-tiers-vcore.md#hardware-generations).|
| Gyorsított adatbázis-helyreállítás önálló adatbázisokkal és rugalmas készletekkel | További információ: [gyorsított adatbázis-helyreállítás](sql-database-accelerated-database-recovery.md).|
|Megközelítőleg eltérő számú|További információkért lásd: [eltérők megközelítő darabszáma](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Batch mód a Sortárindex létrehozását (a 150-es kompatibilitási szint alatt)|További információ: [Batch Mode on sortárindex létrehozását](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Adatfelderítés és besorolás  |További információ: [Azure SQL Database és SQL Data Warehouse adatfelderítési & besorolása](sql-database-data-discovery-and-classification.md).|
| Rugalmas adatbázis-feladatok | További információ: [rugalmas feladatok létrehozása, konfigurálása és kezelése](elastic-jobs-overview.md). |
| Rugalmas lekérdezések | További információ: [rugalmas lekérdezés áttekintése](sql-database-elastic-query-overview.md). |
| Rugalmas tranzakciók | [Elosztott tranzakciók felhőalapú adatbázisok között](sql-database-elastic-transactions-overview.md). |
|Memória-engedélyezési visszajelzés (sor mód) (a 150-es kompatibilitási szint alatt)|További információ: [memória-engedélyezési visszajelzés (sor mód)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| A Azure Portal lekérdezés-szerkesztője |További információ: [a Azure Portal SQL Query Editor használata az adatok összekapcsolásához és lekérdezéséhez](sql-database-connect-query-portal.md).|
| R-szolgáltatások/gépi tanulás önálló adatbázisokkal és rugalmas készletekkel |További információ: [Machine Learning Services Azure SQL Database](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
|SQL Analytics|További információ: [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).|
|Táblázat változó késleltetett fordítása (a 150-es kompatibilitási szint alatt)|További információ: [Table változó késleltetett fordítása](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instance"></a>[Felügyelt példány](#tab/managed-instance)

| Szolgáltatás | Részletek |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Példánykészletek</a> | A kisebb SQL-példányok felhőbe való áttelepíthető kényelmes és költséghatékony módszer. |
| <a href="https://aka.ms/managed-instance-aadlogins">Példány szintű Azure AD-kiszolgálói rendszerbiztonsági tag (bejelentkezések)</a> | Hozzon létre kiszolgálói szintű bejelentkezéseket <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">a külső szolgáltatói utasítás Create login</a> parancsával. |
| [Tranzakciós replikáció](sql-database-managed-instance-transactional-replication.md) | A táblák változásainak replikálása más, felügyelt példányokra, különálló adatbázisokra vagy SQL Server példányokra helyezett adatbázisokra, illetve a táblák frissítése, ha egyes sorok módosulnak más felügyelt példányokban vagy SQL Server példányokban. További információ: [replikáció konfigurálása Azure SQL Database felügyelt példány-adatbázisban](replication-with-sql-database-managed-instance.md). |
| Fenyegetések észlelése |További információ: [a veszélyforrások észlelésének konfigurálása Azure SQL Database felügyelt példányban](sql-database-managed-instance-threat-detection.md).|
| Biztonsági mentés hosszú távú megőrzése | További információ: [Azure SQL Database felügyelt példányának hosszú távú biztonsági mentésének konfigurálása](sql-database-managed-instance-long-term-backup-retention-configure.md). | 

---

## <a name="managed-instance---new-features-and-known-issues"></a>Felügyelt példány – új funkciók és ismert problémák

### <a name="managed-instance-h2-2019-updates"></a>Felügyelt példány H2 2019-frissítések

- [Szolgáltatással segített alhálózat konfigurációja](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) Biztonságos és kényelmes módszer az alhálózati konfiguráció kezeléséhez, ahol szabályozhatja az adatforgalmat, miközben felügyelt példány biztosítja a felügyeleti forgalom zavartalan áramlását.
- Az [transzparens adattitkosítás (TDE) és bring your own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) lehetővé teszi a saját kulcsú (BYOK) környezet használatát a REST-alapú adatvédelem érdekében, és lehetővé teszi a szervezetek számára a kulcsok és adatok elkülönítését a felügyeleti feladatok elvégzéséhez.
- Az [automatikus feladatátvételi csoportok](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) lehetővé teszik, hogy az elsődleges példány összes adatbázisát egy másik régió másodlagos példányára replikálja.
- Felügyelt példányok viselkedésének konfigurálása [globális nyomkövetési jelzők](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/)segítségével.

### <a name="managed-instance-h1-2019-updates"></a>Felügyelt példány H1 2019-frissítések

A következő funkciók engedélyezve vannak a felügyelt példány üzembe helyezési modelljében a H1 2019-ben:
  - <a href="https://aka.ms/sql-mi-visual-studio-subscribers">Azure havi Kredittel</a> rendelkező előfizetések támogatása a Visual Studio-előfizetőknek és a megnövekedett [regionális korlátoknak](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)megfelelően.
  - A <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 és SharePoint 2019 </a> és a <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a> támogatása
  - Hozzon létre olyan példányokat, amelyek <a href="https://aka.ms/managed-instance-collation">kiszolgáló szintű rendezést</a> és <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">időzónát</a> választanak.
  - A felügyelt példányok már védettek a <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">beépített tűzfallal</a>.
  - Konfigurálja a példányokat [nyilvános végpontok](sql-database-managed-instance-public-endpoint-configure.md)használatára, a [proxy felülbírálási](sql-database-connectivity-architecture.md#connection-policy) kapcsolatainak jobb hálózati teljesítmény eléréséhez, <a href="https://aka.ms/four-cores-sql-mi-update">4 virtuális mag a Gen5</a> , illetve a <a href="https://aka.ms/managed-instance-configurable-backup-retention">biztonsági másolatok megőrzésének beállítása akár 35 napra</a> az időponthoz való visszaállításhoz. A biztonsági másolatok hosszú távú megőrzése (akár 10 évig) továbbra sem engedélyezett, így a <a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">csak a másolásra alkalmas biztonsági mentések</a> is használhatók Alternatív megoldásként.
  - Az új funkciók lehetővé teszik, hogy az <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">adatbázist egy másik adatközpontba állítsa be a PowerShell használatával</a>, az [adatbázis átnevezésével](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), a [virtuális fürt törlésével](sql-database-managed-instance-delete-virtual-cluster.md).
  - Az új beépített [példány-közreműködő szerepkör](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) lehetővé teszi a vám (SOD) megfelelőségének a biztonsági elvekkel való elválasztását, valamint a vállalati szabványoknak való megfelelést.
  - A felügyelt példány a következő Azure Government-régiókban érhető el: GA (US Gov Texas, US Gov Arizona), valamint a Észak-Kína 2 és Kelet-Kína 2. A következő nyilvános régiókban érhető el: Közép-Ausztrália, Közép-Ausztrália, Dél-Brazília, Dél-Franciaország, Egyesült Arab Emírségek, Észak-Európa, Dél-Afrika, Dél-Afrika, Nyugat-Európa.

### <a name="known-issues"></a>Ismert problémák

|Probléma  |Felderített dátum  |status  |Feloldás dátuma  |
|---------|---------|---------|---------|
|[A felügyelt példányra nem vonatkozik az erőforráscsoport engedélyei](#permissions-on-resource-group-not-applied-to-managed-instance)|Feb 2020|Megkerülő megoldás||
|[Feladatátvételi csoportok manuális feladatátvételének korlátozása a portálon keresztül](#limitation-of-manual-failover-via-portal-for-failover-groups)|Jan 2020|Megkerülő megoldás||
|[Az SQL-ügynök szerepköreinek explicit végrehajtási engedélyekre van szükségük a nem sysadmin bejelentkezésekhez](#in-memory-oltp-memory-limits-are-not-applied)|Dec 2019|Megkerülő megoldás||
|[Az SQL-ügynök feladatait az ügynök folyamatának újraindítása okozhatja](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Dec 2019|Nincs Áthidaló megoldás|Mar 2020|
|[A HRE-bejelentkezések és a felhasználók nem támogatottak a SSDT](#aad-logins-and-users-are-not-supported-in-ssdt)|November 2019|Nincs Áthidaló megoldás||
|[A memóriában tárolt OLTP-korlátok nincsenek alkalmazva](#in-memory-oltp-memory-limits-are-not-applied)|TOT 2019|Megkerülő megoldás||
|[Helytelen hiba történt a nem üres fájl eltávolítására tett kísérlet során.](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|TOT 2019|Megkerülő megoldás||
|[A szolgáltatási szintek módosítása és a példány-létrehozási műveletek blokkolva vannak az adatbázis-visszaállítás folyamatban](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Sep 2019|Megkerülő megoldás||
|[Előfordulhat, hogy a üzletileg kritikus szolgáltatási szinten újra kell konfigurálni a Resource Governor a feladatátvétel után](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Sep 2019|Megkerülő megoldás||
|[A szolgáltatási réteg frissítése után újra kell inicializálni a több adatbázis Service Broker párbeszédpaneleit.](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|Augusztus 2019|Megkerülő megoldás||
|[Az Azure AD bejelentkezési típusok megszemélyesítése nem támogatott](#impersonification-of-azure-ad-login-types-is-not-supported)|Jul 2019|Nincs Áthidaló megoldás||
|[@querya paraméter nem támogatott sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|Apr 2019|Nincs Áthidaló megoldás||
|[A tranzakciós replikációt újra kell konfigurálni a Geo-feladatátvétel után](#transactional-replication-must-be-reconfigured-after-geo-failover)|Mar 2019|Nincs Áthidaló megoldás||
|[A VISSZAÁLLÍTÁSi művelet során a rendszer ideiglenes adatbázist használ](#temporary-database-is-used-during-restore-operation)||Megkerülő megoldás||
|[A TEMPDB szerkezete és tartalma újra létrejön](#tempdb-structure-and-content-is-re-created)||Nincs Áthidaló megoldás||
|[Kis méretű adatbázisfájlok esetén a tárterület nagyobb](#exceeding-storage-space-with-small-database-files)||Megkerülő megoldás||
|[Az adatbázis neve helyett GUID-értékek láthatók](#guid-values-shown-instead-of-database-names)||Megkerülő megoldás||
|[A hibanapló nem marad meg](#error-logs-arent-persisted)||Nincs Áthidaló megoldás||
|[Ugyanazon példányon belül két adatbázis tranzakciós hatóköre nem támogatott](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Megkerülő megoldás|2020. március|
|[A CLR-modulok és a csatolt kiszolgálók időnként nem hivatkozhatnak helyi IP-címekre.](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Megkerülő megoldás||
|Az adatbázis konzisztencia-ellenőrzése a DBCC CHECKDB UTASÍTÁST használatával nem történt meg az Azure-beli Blob Storage adatbázis visszaállítása után.||Feloldva|November 2019|
|Az adott időponthoz tartozó adatbázis-visszaállítás üzletileg kritikus rétegről általános célú szintjére nem lesz sikeres, ha a forrásadatbázis memóriában tárolt OLTP objektumokat tartalmaz.||Feloldva|TOT 2019|
|Database Mail funkció külső (nem Azure-beli) levelezési kiszolgálókkal biztonságos kapcsolatok használatával||Feloldva|TOT 2019|
|A felügyelt példányok nem támogatják a foglalt adatbázisokat.||Feloldva|Augusztus 2019|

### <a name="permissions-on-resource-group-not-applied-to-managed-instance"></a>A felügyelt példányra nem vonatkozik az erőforráscsoport engedélyei

A felügyelt példány közreműködői RBAC szerepköre nem alkalmazható a felügyelt példányra, és nincs hatása.

**Megkerülő megoldás**: a felügyelt példány közreműködői szerepkörének beállítása a felhasználók számára az előfizetési szinten.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Feladatátvételi csoportok manuális feladatátvételének korlátozása a portálon keresztül

Ha a feladatátvételi csoport különböző Azure-előfizetések vagy-erőforráscsoportok példányai között kiterjed, a manuális feladatátvétel nem indítható el a feladatátvételi csoport elsődleges példányáról.

**Áthidaló megoldás**: kezdeményezzen feladatátvételt a portálon keresztül a Geo-másodlagos példányon keresztül.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>Az SQL-ügynök szerepköreinek explicit végrehajtási engedélyekre van szükségük a nem sysadmin bejelentkezésekhez

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

Az SQL Agent egy új munkamenetet hoz létre minden alkalommal, amikor elindít egy feladatot, és fokozatosan növeli a memória-felhasználást. Ha el szeretné kerülni a belső memória korlátját, amely letiltja az ütemezett feladatok végrehajtását, akkor az ügynök folyamata újraindul, miután a memória-felhasználás eléri a küszöbértéket. Ez a művelet az újraindítás pillanatában futó feladatok végrehajtásának megszakításához vezethet.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>A memóriában tárolt OLTP-korlátok nincsenek alkalmazva

Üzletileg kritikus a szolgáltatási réteg nem fogja megfelelően alkalmazni [a memóriára optimalizált objektumok maximális memóriára vonatkozó korlátozásait](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) bizonyos esetekben. A felügyelt példányok lehetővé tehetik, hogy a számítási feladatok nagyobb memóriát használjanak a memóriában tárolt OLTP-műveletekhez, ami hatással lehet a példány rendelkezésre állására és stabilitására. Előfordulhat, hogy a korlátokat elérő memóriabeli OLTP-lekérdezések nem lesznek azonnal sikertelenek. Ezt a problémát hamarosan megjavítja a rendszer. A memóriában lévő OLTP memóriát használó lekérdezések hamarabb sikertelenek lesznek, ha elérik a [határértékeket](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space).

**Áthidaló megoldás:** [figyelje a MEMÓRIÁban tárolt OLTP](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) a [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) használatával, hogy a számítási feladatok ne használják a rendelkezésre álló memóriát. Növelje a memória korlátait, amelyek a virtuális mag számától függenek, vagy optimalizálja a számítási feladatokat kevesebb memória használatára.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Helytelen hiba történt a nem üres fájl eltávolítására tett kísérlet során.

SQL Server/felügyelt példány nem [engedi, hogy a felhasználó eldobjon egy nem üres fájlt](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Ha nem üres adatfájlt próbál meg eltávolítani az utasítás használatával `ALTER DATABASE REMOVE FILE` , a rendszer nem `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` küldi el azonnal a hibát. A felügyelt példány továbbra is megpróbálja eldobni a fájlt, és a 30min követően `Internal server error`a művelet sikertelen lesz.

**Áthidaló megoldás**: távolítsa el a fájl tartalmát `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` a parancs használatával. Ha ez az egyetlen fájl a fájlcsoportja, akkor törölnie kell az ehhez a fájlcsoportja tartozó táblából vagy partícióból származó adatait a fájl zsugorítása előtt, és opcionálisan be kell töltenie ezeket az adatok egy másik táblába/partícióba.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>A szolgáltatási szintek módosítása és a példány-létrehozási műveletek blokkolva vannak az adatbázis-visszaállítás folyamatban

A `RESTORE` folyamatos utasítás, az adatáttelepítési szolgáltatás áttelepítési folyamata és a beépített idővisszaállítási funkció letiltja a meglévő példány frissítési vagy átméretezését, valamint új példányok létrehozását, amíg a visszaállítási folyamat be nem fejeződik. A visszaállítási folyamat letiltja ezeket a műveleteket a felügyelt példányokon és példány-készleteken ugyanabban az alhálózatban, ahol a visszaállítási folyamat fut. A példányok példányai nem érintettek. A szolgáltatási réteg műveleteinek létrehozása vagy módosítása nem fog sikerülni vagy időtúllépés – a visszaállítási folyamat befejeződése vagy megszakítása után folytatódni fog.

**Áthidaló megoldás**: Várjon, amíg a visszaállítási folyamat befejeződik, vagy szakítsa meg a visszaállítási folyamatot, ha a Service-réteg létrehozása vagy frissítése magasabb prioritással rendelkezik.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Előfordulhat, hogy a üzletileg kritikus szolgáltatási szinten újra kell konfigurálni a Resource Governor a feladatátvétel után

[Resource Governor](/sql/relational-databases/resource-governor/resource-governor) funkció, amely lehetővé teszi a felhasználói munkaterheléshez rendelt erőforrások korlátozását, előfordulhat, hogy a feladatátvétel vagy a felhasználó által kezdeményezett változás miatt helytelenül osztályoz bizonyos felhasználói munkaterheléseket (például a maximális virtuális mag vagy a példányok maximális tárolási méretének módosítása).

**Megkerülő megoldás**: időnként vagy az SQL-ügynök feladatának részeként futtassa `ALTER RESOURCE GOVERNOR RECONFIGURE` az SQL-feladatot, amikor a példány elindul, ha [Resource Governor](/sql/relational-databases/resource-governor/resource-governor)használ.

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>A szolgáltatási réteg frissítése után újra kell inicializálni a több adatbázis Service Broker párbeszédpaneleit.

Az adatbázisok közötti Service Broker párbeszédpanelek nem teszik elérhetővé az üzeneteket más adatbázisokban lévő szolgáltatásoknak a szolgáltatási réteg módosítása után. Az üzenetek **nem vesznek** el, és a küldő várólistáján találhatók. A felügyelt példányban a virtuális mag vagy a példány tárolási méretének változása miatt `service_broke_guid` a [sys. Databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) nézetet fogja megváltoztatni az összes adatbázisra vonatkozóan. A `DIALOG` más adatbázisban található Service Broker szolgáltatásra HIVATKOZÓ [BEGIN Dialog](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) utasítással létrehozott minden egyes létrehozott üzenet nem fog üzeneteket kézbesíteni a célként megadott szolgáltatásnak.

**Áthidaló megoldás:** Állítson le minden olyan tevékenységet, amely több adatbázison Service Broker párbeszédet használ a szolgáltatási réteg frissítése előtt, majd újra inicializálja őket. Ha a szolgáltatási szintek változása után még nem elérhető üzenetek vannak, olvassa el a forrás-üzenetsor üzeneteit, és küldje el újra a cél várólistára.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>Az Azure AD bejelentkezési típusok megszemélyesítése nem támogatott

A következő HRE- `EXECUTE AS USER` rendszerbiztonsági tag vagy `EXECUTE AS LOGIN` a használatával történő megszemélyesítés nem támogatott:
-    Alias HRE-felhasználók. Ebben az esetben `15517`a következő hibaüzenetet adja vissza.
- HRE-bejelentkezések és felhasználók HRE-alkalmazások vagy egyszerű szolgáltatások alapján. Ebben az esetben `15517` a következő hibákat adja vissza: `15406`és.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@querya paraméter nem támogatott sp_send_db_mail

A `@query` [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) eljárásban szereplő paraméter nem működik.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>A tranzakciós replikációt újra kell konfigurálni a Geo-feladatátvétel után

Ha a tranzakciós replikáció engedélyezve van egy automatikus feladatátvételi csoportban lévő adatbázison, a felügyelt példány rendszergazdájának törölnie kell a régi elsődleges összes kiadványt, és újra kell konfigurálnia azokat az új elsődlegesen a feladatátvételt követően egy másik régióba. További részletekért lásd: [replikáció](sql-database-managed-instance-transact-sql-information.md#replication) .

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>A HRE-bejelentkezések és a felhasználók nem támogatottak a SSDT

SQL Server Data Tools nem támogatja teljes mértékben az Azure Active Directory-bejelentkezéseket és-felhasználókat.

### <a name="temporary-database-is-used-during-restore-operation"></a>A VISSZAÁLLÍTÁSi művelet során a rendszer ideiglenes adatbázist használ

Ha egy adatbázist felügyelt példányon állít vissza, a visszaállítási szolgáltatás először egy üres adatbázist hoz létre a kívánt névvel a példányon a név lefoglalásához. Némi idő elteltével ez az adatbázis el lesz dobva, és a rendszer elindítja a tényleges adatbázis visszaállítását. A *visszaállítási* állapotban lévő adatbázis neve helyett véletlenszerű GUID-értékkel fog rendelkezni. Az ideiglenes név az `RESTORE` utasításban megadott kívánt névre változik a visszaállítási folyamat befejeződése után. A kezdeti fázisban a felhasználó hozzáférhet az üres adatbázishoz, és akár táblákat is létrehozhat, vagy betöltheti az adatbázist. Ezt az ideiglenes adatbázist a rendszer elveti, ha a visszaállítási szolgáltatás elindítja a második fázist.

**Megkerülő megoldás**: ne nyissa meg a visszaállítani kívánt adatbázist, amíg nem látja, hogy a visszaállítás befejeződött.

### <a name="tempdb-structure-and-content-is-re-created"></a>A TEMPDB szerkezete és tartalma újra létrejön

Az `tempdb` adatbázis mindig 12 adatfájlra van bontva, és a fájl szerkezete nem módosítható. A fájlok maximális mérete nem módosítható, és az új fájlok nem adhatók hozzá `tempdb`a következőhöz:. `Tempdb`a rendszer mindig üres adatbázisként hozza létre újra, amikor a példány elindul vagy feladatátvételt végez, és a folyamatban `tempdb` lévő módosításokat nem őrzi meg a rendszer.

### <a name="exceeding-storage-space-with-small-database-files"></a>Kis méretű adatbázisfájlok esetén a tárterület nagyobb

`CREATE DATABASE`a `ALTER DATABASE ADD FILE`, és `RESTORE DATABASE` az utasítások sikertelenek lehetnek, mert a példány elérheti az Azure Storage-korlátot.

Az egyes általános célú felügyelt példányok esetében akár 35 TB tárterület is rendelkezésre áll az Azure Premium lemezterület számára. Minden adatbázisfájl külön fizikai lemezre kerül. A lemezek mérete 128 GB, 256 GB, 512 GB, 1 TB vagy 4 TB lehet. A lemez nem használt lemezterülete nem számít fel díjat, de az Azure Premium-lemezek teljes mérete nem haladhatja meg a 35 TB-ot. Bizonyos esetekben előfordulhat, hogy egy felügyelt példány, amely nem igényel 8 TB-ot, a belső töredezettség miatt túllépheti a 35 TB-os Azure-korlátot a tárterületen.

Előfordulhat például, hogy egy általános célú felügyelt példány egy 4 TB-os lemezre helyezett 1,2 TB-os méretű fájllal rendelkezik. Az is előfordulhat, hogy a 248-es fájlok mérete 1 GB, amely külön 128 GB-os lemezekre van helyezve. Ebben a példában:

- A lefoglalt tárterület teljes mérete 1 x 4 TB + 248 x 128 GB = 35 TB.
- A példányon lévő adatbázisok teljes lefoglalt területe 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Ez a példa azt szemlélteti, hogy bizonyos körülmények között a fájlok adott eloszlása miatt a felügyelt példányok elérheti a csatlakoztatott Azure Premium-lemez számára fenntartott 35 TB-os korlátot, ha előfordulhat, hogy nem várható.

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

A felügyelt példányban elérhető naplók nem maradnak meg, és a méreteik nem szerepelnek a maximális tárolási korlátban. Előfordulhat, hogy a naplók automatikusan törlődnek, ha a feladatátvétel történik. Hiányosságok merülhetnek fel a hibák naplózási előzményeiben, mivel a felügyelt példányok többször is át lettek helyezve több virtuális gépre.

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

A felügyelt példányba helyezett CLR-modulok, valamint az aktuális példányra hivatkozó elosztott lekérdezések nem tudják feloldani a helyi példányok IP-címét. Ez a hiba átmeneti probléma.

**Áthidaló megoldás:** Ha lehetséges, használjon egy CLR-modul környezeti kapcsolatait.

## <a name="updates"></a>Frissítések

SQL Database frissítéseinek és tökéletesítésének listáját lásd: [SQL Database szolgáltatás frissítései](https://azure.microsoft.com/updates/?product=sql-database).

Az összes Azure-szolgáltatás frissítéséhez és tökéletesítéséhez tekintse meg a [Service Updates](https://azure.microsoft.com/updates)című témakört.

## <a name="contribute-to-content"></a>Tartalmi közreműködés

A Azure SQL Database dokumentációhoz való hozzájáruláshoz tekintse meg a [docs közreműködői útmutatóját](https://docs.microsoft.com/contribute/).
