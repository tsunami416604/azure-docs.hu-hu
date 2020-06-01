---
title: Az SQL Server használatának áttekintése az Azure Virtual Machines szolgáltatásban (Windows rendszeren) | Microsoft Docs
description: Ismerje meg, hogyan futtathat teljes SQL Server-kiadásokat az Azure Virtual Machines szolgáltatásban.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/27/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 56d461e2efb0923367a149e9b4234d03ed204a9c
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2020
ms.locfileid: "84229897"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>Mi az az Azure Virtual Machines szolgáltatásban futó SQL Server? (Windows)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)

Az [Azure virtuális gépeken futó SQL Serverrel](https://azure.microsoft.com/services/virtual-machines/sql-server/) teljes SQL Server-verziókat használhat a felhőben anélkül, hogy bármilyen helyszíni hardvert kellene fenntartania. Az SQL Servert futtató virtuális gépekkel a használatalapú licencelési költségek is leegyszerűsödnek.

Világszerte számos [földrajzi régióban](https://azure.microsoft.com/regions/) üzemelnek Azure virtuális gépek. Több különböző [gépméret](../../../virtual-machines/windows/sizes.md) is elérhető. A virtuális gépek rendszerkép-katalógusából a megfelelő verziójú, kiadású és operációs rendszerű, SQL Servert futtató virtuális gépet hozhat létre. Ez számos különböző SQL Server számítási feladatra teszi alkalmassá a virtuális gépeket.

## <a name="automated-updates"></a>Automatikus frissítések

Az SQL Server Azure virtuális gépek az [automatikus javítás](automated-patching.md) segítségével ütemezhetnek egy karbantartási időszakot a fontos Windows- és SQL Server-frissítések automatikus telepítéséhez.

## <a name="automated-backups"></a>Automatikus biztonsági mentések

Az SQL Server Azure virtuális gépek használni képesek az [automatikus biztonsági mentés](automated-backup.md) előnyeit, amely rendszeresen biztonsági másolatokat készít az adatbázisról a Blob Storage tárhelyen. Ezt a technikát manuálisan is alkalmazhatja. További információ: [Use Azure Storage for SQL Server Backup and Restore](azure-storage-sql-server-backup-restore-use.md) (Az Azure Storage használata az SQL Server biztonsági mentéséhez és helyreállításához).

Az Azure nagyvállalati szintű biztonsági mentési megoldást is kínál az Azure-beli virtuális gépeken futó SQL Serverhoz. Egy teljes körűen felügyelt biztonsági mentési megoldás, amely támogatja az Always On rendelkezésre állási csoportokat, a hosszú távú adatmegőrzést, az időponthoz kötött helyreállítást, valamint a központi felügyeletet és monitorozást. További információ: [Azure Backup for SQL Server in Azure VM](https://docs.microsoft.com/azure/backup/backup-azure-sql-database).
  

## <a name="high-availability"></a>Magas rendelkezésre állás

Ha magas rendelkezésre állásra van szüksége, fontolja meg az SQL Server rendelkezésre állási csoportok konfigurálását. Ehhez több SQL Server Azure virtuális gépre van szükség egy virtuális hálózaton. Konfigurálhatja magas rendelkezésre állású megoldását manuálisan, vagy használhat sablonokat az Azure Portalról az automatikus konfiguráláshoz. A magas rendelkezésre állási lehetőségekről szóló áttekintésért lásd az [Azure Virtual Machines szolgáltatásban futó SQL Server magas rendelkezésre állását és vészhelyreállítását](business-continuity-high-availability-disaster-recovery-hadr-overview.md) bemutató cikkben.

## <a name="performance"></a>Teljesítmény

Az Azure-beli virtuális gépek a számítási feladatokkal kapcsolatos különböző igényeknek megfelelően eltérő gépméretekkel érhetők el. Az SQL virtuális gépek automatikus, a teljesítménnyel kapcsolatos követelményekhez optimalizált tárolókonfigurálást is nyújtanak. További információ az SQL virtuális gépek konfigurálásáról: [Tárolókonfigurálás SQL Server rendszerű virtuális gépekhez](storage-configuration.md). További információ a teljesítmény finomhangolásáról: [Ajánlott eljárások az SQL Server teljesítményének Azure Virtual Machines szolgáltatásbeli növeléséhez](performance-guidelines-best-practices.md).

## <a name="get-started-with-sql-vms"></a>SQL virtuális gépek – első lépések

Első lépésként válassza ki a megfelelő verziójú, kiadású és operációs rendszerű SQL Server virtuális gép rendszerképét. Az alábbi szakaszokban közvetlen hivatkozások találhatók az Azure Portalra az SQL Server virtuálisgép-katalógus rendszerképeinek letöltéséhez.

> [!TIP]
> Az SQL-rendszerképek díjszabásával kapcsolatos további információkért tekintse meg [az SQL Servert futtató Azure-beli virtuális gépek díjszabási útmutatóját](pricing-guidance.md). 

### <a name="pay-as-you-go"></a><a id="payasyougo"></a>Utólagos fizetés
A következő táblázat a használatalapú fizetéssel elérhető SQL Server-rendszerképeket foglalja össze.

| Verzió | Operációs rendszer | Kiadás |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019enterprise), [standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019standard), [web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019web), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019sqldev) | 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

A Linux rendszerhez elérhető SQL Server-virtuálisgépek rendszerképeivel kapcsolatban lásd [az SQL Server használatának áttekintését az Azure Virtual Machines szolgáltatásban (Linux rendszeren)](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md).

> [!NOTE]
> Mostantól lehetőség van a díjköteles használati SQL Server VM licencelési modelljének módosítására saját licenc használatára. További információ: [az SQL virtuális gép licencelési modelljének módosítása](licensing-model-azure-hybrid-benefit-ahb-change.md). 

### <a name="bring-your-own-license"></a><a id="BYOL"></a> Saját licenc használata
Saját licencet is használhat (BYOL). Ebben az esetben csak a virtuális gépért kell fizetnie, az SQL Server licencelésével kapcsolatos egyéb költségek nélkül.  Saját licence használatával hosszú távon pénzt takaríthat meg a folyamatos éles számítási feladatok esetében. A lehetőség követelményeivel kapcsolatos információkért tekintse meg [az SQL Server Azure virtuális gépek díjszabási útmutatóját](pricing-guidance.md#byol).

Ha saját licencet szeretne létrehozni, lehetősége van egy meglévő, fizetős SQL-alapú virtuális gép átalakítására, vagy telepítheti az előre meghatározott **{BYOL}** lemezképet is. A licencelési modellnek a használati és BYOL közötti váltásával kapcsolatos további információkért lásd: [az SQL-alapú virtuális gép licencelési modelljének módosítása](licensing-model-azure-hybrid-benefit-ahb-change.md). 

| Verzió | Operációs rendszer | Kiadás |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolenterprise), [Standard BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolstandard)| 
| **SQL Server 2017** |Windows Server 2016 |[Vállalati BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Vállalati BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016), [standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Vállalati BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

Olyan régebbi rendszerképek is telepíthetők, amelyek nem érhetők el a Azure Portal a PowerShell használatával SQL Server. Az összes elérhető rendszerkép a PowerShell használatával való megtekintéséhez használja a következő parancsot:

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

A SQL Server virtuális gépek PowerShell használatával történő üzembe helyezésével kapcsolatos további információkért tekintse meg, hogyan hozhat létre [SQL Server Virtual Machines-t a Azure PowerShell](create-sql-vm-powershell.md)használatával.


### <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez
SQL Server virtuális gépe létrehozását követően csatlakozhat hozzá olyan eszközök és alkalmazások segítségével, mint az SQL Server Management Studio (SSMS). Részletes információkért lásd: [Csatlakozás SQL Servert futtató virtuális gépekhez az Azure-ban](ways-to-connect-to-sql.md).

### <a name="migrate-your-data"></a>Adatok áttelepítése
Ha van meglévő adatbázisa, érdemes áthelyeznie az újonnan kiépített SQL virtuális gépre. Az áttelepítési lehetőségekért és útmutatásért lásd: [Migrating a Database to SQL Server on an Azure VM](migrate-to-vm-from-sql-server.md) (Adatbázis áttelepítése egy Azure virtuális gépen SQL Serverre).

## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Azure SQL-erőforrások létrehozása és kezelése a Azure Portal

A Azure Portal egyetlen oldalt biztosít, ahol kezelheti az [összes Azure SQL-erőforrást,](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql) beleértve az SQL-alapú virtuális gépeket is.

Az **Azure SQL-erőforrások** oldal eléréséhez válassza az **Azure sql** lehetőséget a Azure Portal menüben, vagy keresse meg és válassza ki az **Azure SQL** elemet bármely oldalról.

![Az Azure SQL keresése](./media/sql-server-on-azure-vm-iaas-what-is-overview/search-for-azure-sql.png)

> [!NOTE]
> Az **Azure SQL** gyors és egyszerű módot biztosít az Azure SQL-adatbázisok, rugalmas készletek, logikai kiszolgálók, felügyelt példányok és virtuális gépek elérésére. Az Azure SQL nem szolgáltatás vagy erőforrás. 

A meglévő erőforrások kezeléséhez válassza ki a kívánt elemet a listában. Új Azure SQL-erőforrások létrehozásához válassza a **+ Hozzáadás**lehetőséget. 

![Azure SQL-erőforrás létrehozása](./media/sql-server-on-azure-vm-iaas-what-is-overview/create-azure-sql-resource.png)

A **+ Hozzáadás gombra**kattintva további információkat jeleníthet meg a különböző beállításokról a csempék **részleteinek megjelenítése** lehetőség kiválasztásával.

![adatbázisok csempe részletei](./media/sql-server-on-azure-vm-iaas-what-is-overview/sql-vm-details.png)

Részletes információ:

- [Önálló adatbázis létrehozása](../../database/single-database-create-quickstart.md)
- [Rugalmas készlet létrehozása](../../database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [Felügyelt példány létrehozása](../../managed-instance/instance-create-quickstart.md)
- [SQL-alapú virtuális gép létrehozása](sql-vm-create-portal-quickstart.md)

## <a name="sql-vm-image-refresh-policy"></a><a id="lifecycle"></a> Az SQL virtuálisgép-rendszerképek frissítési szabályzata
Az Azure csak egy virtuálisgép-rendszerképet tart fenn minden egyes támogatott operációs rendszer, verzió és kiadás kombináció számára. Ez azt jelenti, hogy a rendszer folyamatosan frissíti a rendszerképeket, a régebbi rendszerképek pedig el lesznek távolítva. További információkért tekintse meg az [SQL Server virtuális gépek GYIK](frequently-asked-questions-faq.md#images) dokumentumának **Rendszerképek** című szakaszát.

## <a name="customer-experience-improvement-program-ceip"></a>Felhasználói élmény fokozása program (CEIP)
A Felhasználói élmény fokozása program (CEIP) alapértelmezés szerint engedélyezve van. Ez a program rendszeres időközönként jelentéseket küld a Microsoftnak az SQL Server fejlesztése érdekében. A CEIP-pel nem kell felügyeleti feladatokat végezni, hacsak nem kívánja letiltani az üzembe helyezés után. A CEIP testreszabásához vagy letiltásához csatlakozzon a virtuális géphez a távoli asztalról. Ezután futtassa az **SQL Server hiba- és használatai jelentések** segédprogramot. A jelentések letiltásához kövesse az utasításokat. Az adatgyűjtésről további információért tekintse meg az [SQL Server adatvédelmi nyilatkozatát](https://docs.microsoft.com/sql/getting-started/microsoft-sql-server-privacy-statement).

## <a name="related-products-and-services"></a>Kapcsolódó termékek és szolgáltatások
### <a name="windows-virtual-machines"></a>Windows rendszerű virtuális gépek
* [Virtual Machines – áttekintés](../../../virtual-machines/windows/overview.md)

### <a name="storage"></a>Storage
* [A Microsoft Azure Storage bemutatása](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Hálózat
* [Virtual Network áttekintése](../../../virtual-network/virtual-networks-overview.md)
* [IP-címek az Azure-ban](../../../virtual-network/public-ip-addresses.md)
* [Teljes tartománynév létrehozása az Azure Portalon](../../../virtual-machines/linux/portal-create-fqdn.md)

### <a name="sql"></a>SQL
* [Az SQL Server dokumentációja](https://docs.microsoft.com/sql/index)
* [Az Azure SQL Database összehasonlítása](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>További lépések

Az SQL Server használatának első lépései az Azure virtuális gépeken:

* [SQL Servert futtató virtuális gép létrehozása az Azure Portalon](sql-vm-create-portal-quickstart.md)

Választ találhat az SQL virtuális gépek használatával kapcsolatos gyakori kérdésekre:

* [Az SQL Server használata Azure virtuális gépeken – gyakori kérdések](frequently-asked-questions-faq.md)

Az N szintű alkalmazások SQL Server IaaS való futtatására szolgáló hivatkozási architektúrák megtekintése

* [Windows N szintű alkalmazás az Azure-ban SQL Server](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [N szintű alkalmazás futtatása több Azure-régióban a magas rendelkezésre állás érdekében](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
