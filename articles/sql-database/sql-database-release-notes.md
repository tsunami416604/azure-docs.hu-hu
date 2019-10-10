---
title: Azure SQL Database kibocsátási megjegyzések | Microsoft Docs
description: Ismerje meg a Azure SQL Database szolgáltatás új szolgáltatásait és funkcióit, valamint a Azure SQL Database dokumentációját
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
ms.openlocfilehash: f1450399dc027a6977f4c99507e2e15b301272c4
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249388"
---
# <a name="sql-database-release-notes"></a>SQL Database kibocsátási megjegyzések

Ez a cikk a jelenleg nyilvános előzetes verzióban elérhető SQL Database-szolgáltatásokat sorolja fel. SQL Database frissítésekhez és a tökéletesítésekhez lásd: [SQL Database Service Updates](https://azure.microsoft.com/updates/?product=sql-database). Az egyéb Azure-szolgáltatások frissítéseihez és tökéletesítéséhez tekintse meg a [Service Updates](https://azure.microsoft.com/updates)című témakört.

## <a name="features-in-public-preview"></a>A nyilvános előzetes verzióban elérhető funkciók

### <a name="single-databasetabsingle-database"></a>[Önálló adatbázis](#tab/single-database)

| Szolgáltatás | Részletek |
| ---| --- |
| [Azure Private-hivatkozás](https://azure.microsoft.com/updates/private-link-now-available-in-preview/)| Azáltal, hogy az adatokat az Azure-hálózaton belül tartja, és nem teszi elérhetővé az internet felé, a privát kapcsolat egyszerűbbé teszi a hálózati architektúrát és biztonságossá teszi a kapcsolatot az Azure-beli végpontok között. Privát kapcsolat segítségével saját szolgáltatást is létrehozhat és nyújthat az Azure-ban. |
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
| Kiszolgáló nélküli számítási szint | További információ: [SQL Database kiszolgáló nélküli (előzetes verzió)](sql-database-serverless.md).|
|SQL-elemzés|További információ: [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).|
|Táblázat változó késleltetett fordítása (a 150-es kompatibilitási szint alatt)|További információ: [Table változó késleltetett fordítása](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[Felügyelt példány](#tab/managed-instance)

| Szolgáltatás | Részletek |
| ---| --- |
| <a href="/azure/sql-database/sql-database-managed-instance-connectivity-architecture#service-aided-subnet-configuration-public-preview-in-east-us-and-west-us">Szolgáltatással segített alhálózat konfigurációja</a> | Biztonságos és kényelmes mód az alhálózati konfiguráció kezelésére. |
| <a href="/azure/sql-database/sql-database-instance-pools">Példányok készletei</a> | A kisebb SQL-példányok felhőbe való áttelepíthető kényelmes és költséghatékony módszer. |
| <a href="https://aka.ms/managed-instance-tde-byok">Transzparens adattitkosítás (TDE) Bring Your Own Key (BYOK)</a> |További információ [: bring your own key támogatás az Azure SQL transzparens adattitkosítás ügyfél által felügyelt Azure Key Vault kulcsokkal](transparent-data-encryption-byok-azure-sql.md).|
| <a href="https://aka.ms/managed-instance-aadlogins">Példány szintű Azure AD-kiszolgálói rendszerbiztonsági tag (bejelentkezések)</a> | Hozzon létre kiszolgálói szintű bejelentkezéseket <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">a külső szolgáltatói utasítás Create login</a> parancsával. |
| [Tranzakciós replikáció](sql-database-managed-instance-transactional-replication.md) | A táblák változásainak replikálása más, felügyelt példányokra, különálló adatbázisokra vagy SQL Server példányokra helyezett adatbázisokra, illetve a táblák frissítése, ha egyes sorok módosulnak más felügyelt példányokban vagy SQL Server példányokban. További információ: [replikáció konfigurálása Azure SQL Database felügyelt példány-adatbázisban](replication-with-sql-database-managed-instance.md). |
| Fenyegetésészlelés |További információ: [a veszélyforrások észlelésének konfigurálása Azure SQL Database felügyelt példányban](sql-database-managed-instance-threat-detection.md).|
| Eldobott adatbázisok újbóli létrehozása felügyelt példányokkal |További információ: [eldobott adatbázisok ismételt létrehozása az Azure SQL felügyelt példányában](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| &nbsp; |

---

## <a name="new-features"></a>ÚJ funkciók

### <a name="managed-instance-h2-2019-updates"></a>Felügyelt példány H2 2019-frissítések

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

## <a name="fixed-known-issues"></a>Rögzített ismert problémák

- Az **augusztus 2019** -ben foglalt adatbázisok teljes mértékben támogatottak a felügyelt példányokban.

## <a name="updates"></a>Frissítések

SQL Database frissítéseinek és tökéletesítésének listáját lásd: [SQL Database szolgáltatás frissítései](https://azure.microsoft.com/updates/?product=sql-database).

Az összes Azure-szolgáltatás frissítéséhez és tökéletesítéséhez tekintse meg a [Service Updates](https://azure.microsoft.com/updates)című témakört.

## <a name="contribute-to-content"></a>Közreműködés a tartalomban

A Azure SQL Database dokumentációhoz való hozzájáruláshoz tekintse meg a [docs közreműködői útmutatóját](https://docs.microsoft.com/contribute/).
