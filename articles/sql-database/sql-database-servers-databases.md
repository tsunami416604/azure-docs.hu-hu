---
title: "& Kezelése az Azure SQL-kiszolgálók és adatbázisok létrehozása |} Microsoft Docs"
description: "Tudnivalók Azure SQL Database-kiszolgálóhoz és az adatbázis fogalmait, illetve kapcsolatos kiszolgálók és adatbázisok létrehozására és kezelésére."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 10/11/2017
ms.author: carlrab
ms.openlocfilehash: 469db4f3faf12cbd778f18b7bc74ec6b86b412c7
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2017
---
# <a name="create-and-manage-azure-sql-database-servers-and-databases"></a>Azure SQL Database-kiszolgálók és adatbázisok létrehozása és kezelése

Azure SQL-adatbázis egy olyan felügyelt adatbázis a Microsoft Azure rendszerben, a rendszer létrehoz egy [Azure erőforráscsoport](../azure-resource-manager/resource-group-overview.md) meghatározott számú [számítási és tárolási erőforrásokat a különböző munkaterhelések](sql-database-service-tiers.md). Azure SQL-adatbázis nem tartozik egy Azure SQL Database logikai kiszolgáló, ami a rendszer létrehoz egy adott Azure-régiót. 

## <a name="an-azure-sql-database-can-be-a-single-pooled-or-partitioned-database"></a>Azure SQL-adatbázis egyetlen, a készletezett vagy a particionált adatbázis is lehet.

Egy Azure SQL adatbázis lehet:

- A [egyetlen adatbázis](sql-database-single-database-resources.md) saját erőforrások rendelkező
- Része egy [rugalmas készlet](sql-database-elastic-pool.md) , közösen használja az erőforráscsoport
- részét képezhetik [horizontálisan skálázott adatbázisok kibővíthető készletének](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling), amely önálló vagy készletezett adatbázisokból állhat;
- részét képezhetik egy [több-bérlős SaaS kialakítási mintában](sql-database-design-patterns-multi-tenancy-saas-applications.md) szereplő adatbáziskészletnek, mely adatbázisok lehetnek önálló, készletezett vagy mindkétféle adatbázisok. 

> [!TIP]
> Az érvényes adatbázisnevekkel kapcsolatban lásd az [adatbázis-azonosítókat](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) ismertető cikket. 
>
 
- A Microsoft Azure SQL Database által alapértelmezés szerint használt adatbázisrendezés az **SQL_LATIN1_GENERAL_CP1_CI_AS**, amelyben a **LATIN1_GENERAL** az angol (Egyesült Államok), a **CP1** az 1252-es kódlap, a **CI** a kis- és nagybetűk meg nem különböztetése, az **AS** pedig az ékezetérzékenység. További információk a rendezés beállításáról: [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).
- A Microsoft Azure SQL Database 7.3 vagy újabb tabulált adatfolyam (TDS) protokoll ügyfél verziója támogatja.
- Csak a TCP/IP-kapcsolatok engedélyezve vannak.

## <a name="what-is-an-azure-sql-logical-server"></a>Mi az az Azure SQL logikai kiszolgálóra?

Több adatbázis, beleértve a központi felügyeleti pontként működik a logikai kiszolgáló [rugalmas készletek](sql-database-elastic-pool.md) [bejelentkezések](sql-database-manage-logins.md), [tűzfal-szabályok](sql-database-firewall-configure.md), [naplózása szabályok](sql-database-auditing.md), [szabályzatok fenyegetés](sql-database-threat-detection.md), és [feladatátvételi csoportok](sql-database-geo-replication-overview.md). Egy logikai kiszolgáló lehet egy másik régióban, mint az erőforráscsoportot. A logikai kiszolgáló léteznie kell az Azure SQL-adatbázis létrehozása előtt. A kiszolgálón lévő összes adatbázis belül és a logikai kiszolgáló ugyanabban a régióban jönnek létre. 


> [!IMPORTANT]
> Az SQL Database-ben a kiszolgáló egy logikai szerkezet, amely nem azonos a helyszíni SQL Server-példánnyal. Az SQL Database szolgáltatás nem garantálja az adatbázisok helyét a logikai kiszolgálójukhoz képest, valamint nem kínál példányszintű hozzáférést és funkciókat.
> 

Logikai kiszolgáló létrehozása, ha a kiszolgáló megadta bejelentkezési fiókot és jelszót, amely rendszergazdai jogosultságokkal ezen a kiszolgálón a master adatbázis és az összes adatbázis létrehozása ezen a kiszolgálón. A kezdeti egy egy SQL-bejelentkezési fiók. Az Azure SQL Database hitelesítéshez támogatja az SQL-hitelesítést és az Azure Active Directory-hitelesítéssel. További információ a bejelentkezési és hitelesítési: [kezelése adatbázisok és bejelentkezések az Azure SQL Database](sql-database-manage-logins.md). A Windows-hitelesítés nem támogatott. 

> [!TIP]
> Érvényes erőforrás csoport és a kiszolgáló nevét, lásd: [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).
>

Az Azure Database logikai kiszolgáló:

- Egy Azure-előfizetésen belül jön létre, de az erőforrásaival együtt áthelyezhető másik előfizetésre.
- Az adatbázisok, rugalmas készletek és adattárházak szülőerőforrása.
- Egy névtér biztosít az adatraktárak, adatbázisok és rugalmas készletek
- Egy olyan logikai tároló erős élettartama szemantikájú - kiszolgáló törlése, és törli a tartalmazott adatbázisok rugalmas készletek és az adatraktárak
- Részt vesz [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](/active-directory/role-based-access-control-what-is) -adatbázisok rugalmas készletek és az adatraktárak belül a kiszolgáló hozzáférési jogosultsága öröklése a kiszolgálóról
- Esetén az adatbázist, a rugalmas készletek és a adatraktárak az Azure-erőforrás identitás magasrendű tényező (lásd az URL-séma, adatbázisok és a készletek) felügyeleti célokra
- Közösen helyezi el egy adott régió erőforrásait.
- Kapcsolódási végpontot biztosít az adatbázis-hozzáféréshez (<serverName>.database.windows.net)
- Egy master adatbázishoz való kapcsolódással hozzáférést biztosít a tárolt erőforrásokra vonatkozó metaadatokhoz a DMV-ken keresztül 
- Itt a hatókört felügyeleti házirendek, hogy az adatbázis - bejelentkezések vonatkozik, tűzfal, naplózni, a fenyegetés észlelési stb. 
- A szülő előfizetésen belül a kvóta által korlátozott (alapértelmezés szerint - előfizetésenként hat kiszolgáló [lásd itt korlátozza előfizetés](../azure-subscription-service-limits.md))
- A hatókör adatbázis kvóta és a DTU-kvótát biztosít az erőforrások (például 45 000 DTU) tartalmaz
- Engedélyezve van az abban található erőforrás képességeinek versioning hatóköre 
- Kiszolgálószintű rendszerbiztonsági tagként bejelentkezve a kiszolgáló minden adatbázisa felügyelhető.
- Képes az olyan helyszíni SQL Server-példányokhoz hasonló bejelentkezések kezelésére, amelyek a kiszolgáló egy vagy több adatbázisához hozzáféréssel rendelkeznek, és korlátozott rendszergazdai jogosultságokkal ruházhatók fel. További információk: [Bejelentkezések](sql-database-manage-logins.md).

## <a name="azure-sql-databases-protected-by-sql-database-firewall"></a>Az Azure SQL Database-tűzfal által védett SQL-adatbázisok

Az adatok védelme érdekében a [SQL Database-tűzfal](sql-database-firewall-configure.md) megakadályozza, hogy minden hozzáférés meg az adatbázis-kiszolgáló vagy bármely olyan az adatbázisok kívül a kapcsolatot az Azure-előfizetés-kapcsolaton keresztül közvetlenül a kiszolgálóhoz. Ahhoz, hogy további kapcsolatot, kell [hozzon létre egy vagy több tűzfalszabályok](sql-database-firewall-configure.md#creating-and-managing-firewall-rules). Létrehozása és a rugalmas készletek kezelése [rugalmas készletek](sql-database-elastic-pool.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-portal"></a>Kezelheti az Azure SQL kiszolgáló, adatbázisok, és tűzfal az Azure portál használatával

Az Azure SQL-adatbázis erőforráscsoport időben vagy magán a kiszolgálón létrehozásakor is létrehozhat. Több módon lekérhesse az új SQL server űrlapokhoz, hozzon létre egy új SQL-kiszolgáló vagy egy új adatbázis létrehozásának részeként. 

### <a name="create-a-blank-sql-server-logical-server"></a>Hozzon létre egy üres SQL-kiszolgáló (logikai kiszolgáló)

Létrehozása az Azure SQL Database-kiszolgáló (adatbázis) nélkül használja a [Azure-portálon](https://portal.azure.com), keresse meg az SQL server (a logikai kiszolgáló) üres űrlapból.  

### <a name="create-a-blank-or-sample-sql-database"></a>Üres, vagy a minta SQL-adatbázis létrehozása

Egy Azure SQL adatbázis használatával létrehozásához a [Azure-portálon](https://portal.azure.com)üres SQL Database űrlap váltson, és adja meg a kért információkat. Az Azure SQL-adatbázis erőforráscsoport és logikai kiszolgáló időben, vagy maga az adatbázis létrehozásakor is létrehozhat. Hozzon létre egy üres adatbázist, vagy az Adventure Works LT. alapú minta-adatbázis létrehozása 

  ![adatbázis létrehozása-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Az adatbázis árképzési szint kiválasztásával további információkért lásd: [szolgáltatásszintek](sql-database-service-tiers.md).
>

### <a name="manage-an-existing-sql-server"></a>Meglévő SQL server kezelése

Egy meglévő kiszolgáló kezeléséhez, keresse fel a kiszolgálót, számos módszer – például adott SQL-adatbázis oldal, használja a **SQL Server-kiszolgálók** lap, vagy a **összes erőforrás** lap. 

Kezelheti egy meglévő adatbázist, navigáljon a **SQL-adatbázisok** lapon, majd kattintson a kezelni kívánt adatbázist. Az alábbi képernyőfelvételen látható, hogy hogyan kezdheti meg az adatbázis egy kiszolgálószintű tűzfal beállítása a **áttekintése** adatbázis lap. 

   ![kiszolgálói tűzfalszabály](./media/sql-database-get-started-portal/server-firewall-rule.png) 

> [!IMPORTANT]
> Adatbázis teljesítménye tulajdonságok beállítása: [szolgáltatásszintek](sql-database-service-tiers.md).
>

> [!TIP]
> Tekintse meg az Azure portál – gyors üzembe helyezési oktatóanyag [Azure SQL-adatbázis létrehozása az Azure portálon](sql-database-get-started-portal.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-powershell"></a>Azure SQL-kiszolgálók, a adatbázisok és a PowerShell-lel tűzfalak kezelése

Létrehozása és kezelése az Azure SQL server, adatbázisok és tűzfalak az Azure PowerShell, a következő PowerShell-parancsmagokat használja. Ha szeretné telepíteni vagy frissíteni a PowerShell, lásd: [telepítése Azure PowerShell modul](/powershell/azure/install-azurerm-ps). Létrehozása és a rugalmas készletek kezelése [rugalmas készletek](sql-database-elastic-pool.md).

| Parancsmag | Leírás |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Létrehoz egy adatbázis |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Egy vagy több adatbázis beolvasása|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Az adatbázis tulajdonságainak megadása, vagy a meglévő adatbázis áthelyezése rugalmas készletbe|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Egy adatbázis eltávolítása|
|[Új-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)|Létrehoz egy erőforráscsoport]
|[Új AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver)|A kiszolgáló létrehozása|
|[Get-AzureRmSqlServer](/powershell/module/azurerm.sql/get-azurermsqlserver)|Kiszolgálók adatait adja vissza|
|[Set-AzureRmSqlServer](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlserver)|Kiszolgáló tulajdonságainak módosítása|
|[Remove-AzureRmSqlServer](/powershell/module/azurerm.sql/remove-azurermsqlserver)|Eltávolít egy kiszolgálót|
|[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule)|Létrehoz egy kiszolgálószintű tűzfalszabályt |
|[Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule)|Kiszolgáló tűzfalszabályainak beolvasása|
|[Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule)|Egy tűzfalszabály módosítása|
|[Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule)|Egy tűzfalszabály törlése a kiszolgálóról.|
| Új AzureRmSqlServerVirtualNetworkRule | Létrehoz egy [ *virtuális hálózati szabály*](sql-database-vnet-service-endpoint-rule-overview.md), amely a virtuális hálózati szolgáltatási végpont alhálózat alapján. |

> [!TIP]
> Tekintse meg a PowerShell gyors üzembe helyezési útmutató [PowerShell használatával egyetlen Azure SQL-adatbázis létrehozása](sql-database-get-started-portal.md). PowerShell-példa parancsfájlok, lásd: [a PowerShell szolgáltatás használatával hozzon létre egy Azure SQL-adatbázis és a tűzfalszabályok konfigurálása](scripts/sql-database-create-and-configure-database-powershell.md) és [figyelő és a skála egyetlen SQL adatbázis-PowerShell-lel](scripts/sql-database-monitor-and-scale-database-powershell.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-cli"></a>Kezelheti az Azure SQL kiszolgáló, adatbázisok, és tűzfal az Azure parancssori felület használatával

Létrehozásához és kezeléséhez az Azure SQL server, adatbázisok és a tűzfalon a [Azure CLI](/cli/azure/overview), használja a következő [Azure CLI SQL Database](/cli/azure/sql/db) parancsok. A [Cloud Shell-lel](/azure/cloud-shell/overview) futtassa a parancssori felületet a böngészőben, vagy [telepítse](/cli/azure/install-azure-cli) macOS, Linux, illetve Windows rendszeren. Létrehozása és a rugalmas készletek kezelése [rugalmas készletek](sql-database-elastic-pool.md).

| Parancsmag | Leírás |
| --- | --- |
|[az sql-adatbázis létrehozása](/cli/azure/sql/db#az_sql_db_create) |Létrehoz egy adatbázis|
|[az sql db listája](/cli/azure/sql/db#az_sql_db_list)|Felsorolja az összes adatbázist és az adatraktárak egy kiszolgálón, vagy minden adatbázisok rugalmas készlethez|
|[az sql db lista-verziók](/cli/azure/sql/db#az_sql_db_list_editions)|Listák elérhető szolgáltatási célkitűzések és tárolási korlátai|
|[az sql db lista-módjait](/cli/azure/sql/db#az_sql_db_list_usages)|Adatbázis-módjait beolvasása|
|[az sql db megjelenítése](/cli/azure/sql/db#az_sql_db_show)|Egy adatbázis vagy adatraktár beolvasása|
|[az sql-adatbázis frissítése](/cli/azure/sql/db#az_sql_db_update)|Frissíti az adatbázist|
|[az sql-adatbázis törlése](/cli/azure/sql/db#az_sql_db_delete)|Egy adatbázis eltávolítása|
|[az csoport létrehozása](/cli/azure/group#az_group_create)|Létrehoz egy erőforráscsoportot|
|[az sql-kiszolgáló létrehozása](/cli/azure/sql/server#az_sql_server_create)|A kiszolgáló létrehozása|
|[az sql server listája](/cli/azure/sql/server#az_sql_server_list)|Kiszolgálók listája|
|[az sql server lista-módjait](/cli/azure/sql/server#az_sql_server_list-usages)|Kiszolgáló módjait adja vissza|
|[az sql server megjelenítése](/cli/azure/sql/server#az_sql_server_show)|A kiszolgáló beolvasása|
|[az sql server frissítése](/cli/azure/sql/server#az_sql_server_update)|A kiszolgáló frissítése|
|[az sql server törlése](/cli/azure/sql/server#az_sql_server_delete)|Kiszolgáló törlése|
|[az sql server-tűzfalszabály létrehozása](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Létrehoz egy tűzfalszabály létrehozása|
|[az sql server tűzfal-szabályok listája](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|A kiszolgálón a tűzfalszabályok listája|
|[az sql server-tűzfalszabály megjelenítése](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|A tűzfalszabályok részleteit tartalmazza|
|[az sql server tűzfal-szabály frissítése](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|A tűzfal szabály frissítése|
|[az sql server-tűzfalszabály törlése](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Egy tűzfalszabály törlése|

> [!TIP]
> Tekintse meg az Azure CLI gyors üzembe helyezési útmutató [az Azure parancssori felület használatával egyetlen Azure SQL-adatbázis létrehozása](sql-database-get-started-cli.md). Az Azure parancssori felület parancsfájlpéldákat, lásd: [használata CLI egy Azure SQL-adatbázis létrehozása és konfigurálása a tűzfalszabályok](scripts/sql-database-create-and-configure-database-cli.md) és [használata CLI figyelését, valamint egy SQL-adatbázis méretezése](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-transact-sql"></a>Azure SQL-kiszolgálók, a adatbázisok és a tűzfalak Transact-SQL használatával kezelése

Létrehozása és kezelése az Azure SQL kiszolgáló, adatbázisok és tűzfalak a Transact-SQL, használja a következő T-SQL-parancsokat. Ezek a parancsok használata az Azure-portálon kiadhatja [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), vagy bármely más programot, amely egy Azure SQL Database-kiszolgálóhoz csatlakozhat, és adja át a Transact-SQL-parancsokat. Rugalmas készletek kezelése, lásd: [rugalmas készletek](sql-database-elastic-pool.md).

> [!IMPORTANT]
> Nem hozható létre vagy Transact-SQL használatával kiszolgáló törlése.
>

| Parancs | Leírás |
| --- | --- |
|[ADATBÁZIS (az Azure SQL Database) létrehozása](/sql/t-sql/statements/create-database-azure-sql-database)|Egy új adatbázist hoz létre. Kell csatlakoznia a főadatbázison való futtatásával hozzon létre egy új adatbázist.|
| [Az ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Azure SQL-adatbázis módosítása. |
|[Az ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Egy Azure SQL Data Warehouse módosítja.|
|[ADATBÁZIS (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Adatbázis törlése.|
|[sys.database_service_objectives (az Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Egy Azure SQL database vagy az Azure SQL Data Warehouse esetében adja vissza a edition (szolgáltatási réteg), a szolgáltatási cél (IP-címek) és a rugalmas készlet nevét. Ha be van jelentkezve a főadatbázishoz egy Azure SQL adatbázis-kiszolgáló, az összes adatbázis ad vissza adatokat. Az Azure SQL Data Warehouse kell csatlakoznia a fő adatbázist.|
|[sys.dm_db_resource_stats (az Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Az Azure SQL Database adatbázishoz CPU, a i/o és a memória-felhasználás adja vissza. Egy sor létezik 15 másodpercenként akkor is, ha nincs az adatbázisban nem tevékenység.|
|[sys.resource_stats (az Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|CPU használatát és a tároló adatait jeleníti meg az Azure SQL-adatbázis. Az adatok gyűjtése és 5 perces időközönként belül.|
|[sys.database_connection_stats (az Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|SQL-adatbázis adatbázis csatlakozási eseményeket, és adatbázis-kapcsolat sikeres és sikertelen áttekintést nyújt a statisztikákat tartalmaz. |
|[sys.event_log (az Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Sikeres Azure SQL Database adatbázis-kapcsolatok, a csatlakozási hibák és a holtpontok adja vissza. Ezek az információk segítségével nyomon követése, vagy az adatbázis-tevékenység az SQL Database hibaelhárítása.|
|[sp_set_firewall_rule (az Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Létrehozza vagy frissíti az SQL Database-kiszolgálóhoz kiszolgálószintű tűzfal beállításait. Ez a tárolt eljárás csak érhető el a főadatbázison való futtatásával a kiszolgálószintű fő bejelentkezéssel. Egy kiszolgálószintű tűzfalszabályt csak segítségével hozhatók létre Transact-SQL Azure-szintű engedélyekkel rendelkező felhasználó az első kiszolgálószintű tűzfalszabály létrehozása után|
|[sys.firewall_rules (az Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|A kiszolgálószintű tűzfal beállításai a Microsoft Azure SQL Database társított információt ad vissza.|
|[sp_delete_firewall_rule (az Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Az SQL Database-kiszolgálóhoz kiszolgálószintű tűzfal beállításainak eltávolítása. Ez a tárolt eljárás csak érhető el a főadatbázison való futtatásával a kiszolgálószintű fő bejelentkezéssel.|
|[sp_set_database_firewall_rule (az Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Létrehozza vagy frissíti az adatbázis-szintű tűzfalszabályok az Azure SQL Database vagy az SQL Data Warehouse. Adatbázis tűzfalszabályainak konfigurálható a master adatbázis, valamint az SQL Database felhasználói adatbázisokat. Adatbázis tűzfalszabályainak használatával tartalmazott adatbázis-felhasználók esetén hasznos. |
|[sys.database_firewall_rules (az Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Az adatbázis-szintű tűzfal beállításait a Microsoft Azure SQL Database társított információt ad vissza. |
|[sp_delete_database_firewall_rule (az Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Az Azure SQL Database vagy az SQL Data Warehouse adatbázis szintű tűzfal beállítást eltávolítja. |


> [!TIP]
> Gyors üzembe helyezési útmutató a Microsoft Windows SQL Server Management Studio használatával, lásd: [Azure SQL Database: használható SQL Server Management Studio való kapcsolódás és lekérdezés az adatok](sql-database-connect-query-ssms.md). A gyors üzembe helyezési útmutató az macOS, a Linux vagy a Windows Visual Studio Code használatával, lásd: [Azure SQL Database: használja a Visual Studio Code való kapcsolódás és lekérdezés az adatok](sql-database-connect-query-vscode.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-rest-api"></a>Azure SQL kiszolgáló, adatbázisok és a REST API használatával tűzfalak kezelése

Létrehozása és kezelése az Azure SQL server, adatbázisok és a tűzfalon a REST API-kérés használja.

| Parancs | Leírás |
| --- | --- |
|[Kiszolgálók – létrehozása vagy frissítése](/rest/api/sql/servers/createorupdate)|Létrehozza vagy frissíti az új kiszolgáló.|
|[Kiszolgálók – törlése](/rest/api/sql/servers/delete)|SQL-kiszolgáló törlése.|
|[Kiszolgálók - Get](/rest/api/sql/servers/get)|Lekérdezi a kiszolgálót.|
|[Kiszolgálók – lista](/rest/api/sql/servers/list)|Kiszolgálók listáját adja vissza.|
|[Kiszolgálók - erőforráscsoport listája](/rest/api/sql/servers/listbyresourcegroup)|Egy erőforráscsoportban található kiszolgálók listáját adja vissza.|
|[Kiszolgálók – frissítés](/rest/api/sql/servers/update)|Frissíti a meglévő kiszolgáló.|
|[Sql-kiszolgálók –](/rest/api/sql/servers%20-%20sql)|Meghatározza, hogy egy erőforrás hozhatja létre a megadott név.|
|[-Adatbázis létrehozása vagy frissítése](/rest/api/sql/databases/createorupdate)|Létrehoz egy új adatbázist, vagy egy meglévő adatbázist frissíti.|
|[Adatbázis - Get](/rest/api/sql/databases/get)|Lekérdezi egy adatbázis.|
|[Adatbázis - hozza ki a rugalmas készlet](/rest/api/sql/databases/getbyelasticpool)|Egy adatbázis lekérdezi a rugalmas készletekben belül.|
|[Ajánlott rugalmas készlet hozza - adatbázisok](/rest/api/sql/databases/getbyrecommendedelasticpool)|Lekérdezi egy adatbázist egy recommented rugalmas készletet belül.|
|[Adatbázis - listát a rugalmas készlet](/rest/api/sql/databases/listbyelasticpool)|A rugalmas készletekben található adatbázisok listáját adja vissza.|
|[Adatbázis - lista által javasolt rugalmas készlet](/rest/api/sql/databases/listbyrecommendedelasticpool)|Adatbázisok belül egy recommented rugalmas készletet listáját adja vissza.|
|[Adatbázis - kiszolgáló listája](/rest/api/sql/databases/listbyserver)|A Server-adatbázisok listáját adja vissza.|
|[Adatbázis - frissítés](/rest/api/sql/databases/update)|Egy meglévő adatbázist frissíti.|
|[Tűzfal - szabályok létrehozása vagy frissítése](/rest/api/sql/firewallrules/createorupdate)|Létrehozza vagy frissíti egy tűzfalszabályt.|
|[Tűzfalszabályok - törlése](/rest/api/sql/firewallrules/delete)|Törli a tűzfalszabályt.|
|[Tűzfalszabályok - Get](/rest/api/sql/firewallrules/get)|Egy tűzfalszabály beolvasása.|
|[Tűzfalszabályok - kiszolgáló listája](/rest/api/sql/firewallrules/listbyserver)|Tűzfalszabályok listáját adja vissza.|

## <a name="next-steps"></a>Következő lépések

- Adatbázisok rugalmas készleteket használó készletezését kapcsolatos további tudnivalókért lásd: [rugalmas készletek](sql-database-elastic-pool.md).
- Az Azure SQL Database szolgáltatással kapcsolatos további információkért lásd: [Mi az SQL Database?](sql-database-technical-overview.md).
- SQL Server-adatbázis migrálása az Azure-ba, lásd: [áttelepítése az Azure SQL Database](sql-database-cloud-migrate.md).
- A támogatott funkciókkal kapcsolatos tudnivalókat lásd: [Funkciók](sql-database-features.md).
