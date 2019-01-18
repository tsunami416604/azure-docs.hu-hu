---
title: Hozzon létre, illetve kezelheti az Azure SQL-kiszolgáló és az önálló adatbázisok |} A Microsoft Docs
description: Ismerje meg a logikai kiszolgálók és az önálló adatbázisok létrehozására és kezelésére.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 01/17/2019
ms.openlocfilehash: f43c2cd5a3c155258cd698f6b55854bc0df9f861
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54388593"
---
# <a name="create-and-manage-logical-servers-and-single-databases-in-azure-sql-database"></a>Logikai kiszolgáló és az Azure SQL Database önálló adatbázisok létrehozása és kezelése

Hozzon létre, és kezelheti az Azure SQL Database logikai kiszolgálóiról és az önálló adatbázisok az Azure portal, PowerShell, Azure CLI, REST API és a Transact-SQL használatával.

## <a name="azure-portal-manage-logical-servers-and-databases"></a>Az Azure Portalon: Logikai kiszolgálóiról és adatbázisairól kezelése

Az Azure SQL database erőforráscsoport előre, vagy a kiszolgáló létrehozásakor is létrehozhat. Több módszerrel hozzon létre új SQL-kiszolgáló vagy egy új adatbázis létrehozása során egy új SQL server űrlapot bemutató.

### <a name="create-a-blank-sql-server-logical-server"></a>Hozzon létre egy üres SQL server (logikai kiszolgáló)

Hozzon létre egy Azure SQL Database-kiszolgálón (nélkül egy adatbázis), a [az Azure portal](https://portal.azure.com), nyissa meg egy üres SQL server (logikai kiszolgáló) képernyő.  

### <a name="create-a-blank-or-sample-sql-database"></a>Hozzon létre egy üres vagy minta SQL-adatbázis

Hozzon létre egy Azure SQL database, a [az Azure portal](https://portal.azure.com), nyissa meg egy üres SQL Database űrlapját, és adja meg a kért adatokat. Az Azure SQL database erőforrás-csoport és a logikai kiszolgáló időben, vagy maga az adatbázis létrehozásakor is létrehozhat. Hozzon létre egy üres adatbázist, vagy hozzon létre egy mintaadatbázist az Adventure Works LT. alapján

  ![adatbázis létrehozása-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Az adatbázis tarifacsomagjának kiválasztásával további információkért lásd: [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md).

Felügyelt példány létrehozásához lásd: [felügyelt példány létrehozása](sql-database-managed-instance-get-started.md)

## <a name="manage-an-existing-sql-server"></a>Egy meglévő SQL-kiszolgáló kezelése

Meglévő kiszolgáló kezeléséhez, a kiszolgáló több különféle módszerek – például adott SQL database oldalon keresse meg a **SQL Server-kiszolgálók** lapon vagy a **összes erőforrás** lap.

Létező adatbázis kezeléséhez, lépjen a **SQL-adatbázisok** lapon, majd kattintson a kezelni kívánt adatbázisra. A következő képernyőképen látható, hogy hogyan kezdheti meg az adatbázis kiszolgálószintű tűzfal beállítása az **áttekintése** egy adatbázishoz tartozó lap.

   ![kiszolgálói tűzfalszabály](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> Adatbázis teljesítményének tulajdonságok beállítása: [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md).
> [!TIP]
> Az Azure portal rövid útmutatójában talál [egy Azure SQL database létrehozása az Azure Portalon](sql-database-get-started-portal.md).

## <a name="powershell-manage-logical-servers-and-databases"></a>PowerShell: Logikai kiszolgálóiról és adatbázisairól kezelése

Létrehozása és kezelése az Azure SQL logikai kiszolgálók, egyetlen vagy készletezett adatbázisok és logikai kiszolgálóhoz tűzfalak az Azure PowerShell-lel, használja a következő PowerShell-parancsmagokat. Ha telepíteni vagy frissíteni a PowerShell, lásd: kell [Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps).

> [!TIP]
> PowerShell-példa szkriptek, lásd: [használja a Powershellt egy Azure SQL-adatbázis létrehozása és a egy logikai kiszolgáló tűzfalszabály konfigurálása](scripts/sql-database-create-and-configure-database-powershell.md) és [figyelés és méret egy önálló SQL database PowerShell-lel](scripts/sql-database-monitor-and-scale-database-powershell.md).

| Parancsmag | Leírás |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Létrehoz egy adatbázist |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Egy vagy több adatbázis beolvasása|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Beállítja egy adatbázis tulajdonságait, vagy egy meglévő adatbázist helyezi át a rugalmas készlet|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Egy adatbázis eltávolítása|
|[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)|Létrehoz egy erőforráscsoportot|
|[New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver)|Kiszolgáló létrehozása|
|[Get-AzureRmSqlServer](/powershell/module/azurerm.sql/get-azurermsqlserver)|-Kiszolgálóira vonatkozó adatokat ad vissza|
|[Set-AzureRmSqlServer](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlserver)|-Kiszolgáló tulajdonságainak módosítása|
|[Remove-AzureRmSqlServer](/powershell/module/azurerm.sql/remove-azurermsqlserver)|Eltávolít egy kiszolgálót|
|[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule)|Létrehoz egy kiszolgálószintű tűzfalszabályt |
|[Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule)|Tűzfalszabályok kiszolgáló beolvasása|
|[Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule)|Módosítja egy tűzfalszabályt egy kiszolgálón|
|[Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule)|Egy tűzfalszabály töröl egy kiszolgálóról.|
| New-AzureRmSqlServerVirtualNetworkRule | Létrehoz egy [ *virtuális hálózati szabályt*](sql-database-vnet-service-endpoint-rule-overview.md)egy alhálózatot, amely egy virtuális hálózati szolgáltatásvégpont alapján. |

## <a name="azure-cli-manage-logical-servers-and-databases"></a>Azure CLI: Logikai kiszolgálóiról és adatbázisairól kezelése

Létrehozása és kezelése az Azure SQL server, adatbázisok és tűzfalak az [Azure CLI-vel](/cli/azure), használja a következő [Azure CLI az SQL Database](/cli/azure/sql/db) parancsokat. A [Cloud Shell-lel](/azure/cloud-shell/overview) futtassa a parancssori felületet a böngészőben, vagy [telepítse](/cli/azure/install-azure-cli) macOS, Linux, illetve Windows rendszeren. Rugalmas készletek kezelése és létrehozása: [rugalmas készletek](sql-database-elastic-pool.md).

> [!TIP]
> Az Azure CLI-vel a rövid útmutatóban talál [egyetlen Azure SQL-adatbázis létrehozása az Azure CLI-vel](sql-database-cli-samples.md). Azure CLI-példa szkriptek, lásd: [parancssori felület használata egy Azure SQL-adatbázis létrehozása és tűzfalszabály konfigurálása](scripts/sql-database-create-and-configure-database-cli.md) és [használható parancssori felület egy egyetlen SQL-adatbázis monitorozása és skálázása](scripts/sql-database-monitor-and-scale-database-cli.md).
>

| Parancsmag | Leírás |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Létrehoz egy adatbázist|
|[az sql db list](/cli/azure/sql/db#az-sql-db-list)|Felsorolja az összes adatbázisok és adattárházak a kiszolgálón, vagy egy rugalmas készletben található összes adatbázis|
|[az sql db list-editions](/cli/azure/sql/db#az-sql-db-list-editions)|Listák elérhető szolgáltatási célkitűzések és a tárolási korlátok|
|[az sql db list-usages](/cli/azure/sql/db#az-sql-db-list-usages)|Adatbázis-használat értéket ad vissza|
|[az sql db show](/cli/azure/sql/db#az-sql-db-show)|Egy adatbázis sem az adattárházra beolvasása|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Frissíti az adatbázist|
|[az sql db delete](/cli/azure/sql/db#az-sql-db-delete)|Egy adatbázis eltávolítása|
|[az group create](/cli/azure/group#az-group-create)|Létrehoz egy erőforráscsoportot|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Kiszolgáló létrehozása|
|[az sql server list](/cli/azure/sql/server#az-sql-server-list)|Kiszolgálók listája|
|[az sql server list-usages](/cli/azure/sql/server#az-sql-server-list-usages)|Kiszolgáló usages adja vissza|
|[az sql server show](/cli/azure/sql/server#az-sql-server-show)|Lekérdezi egy kiszolgálóra|
|[az sql server frissítése](/cli/azure/sql/server#az-sql-server-update)|A kiszolgáló frissítése|
|[az sql server delete](/cli/azure/sql/server#az-sql-server-delete)|A kiszolgáló törlése|
|[az sql server firewall-rule létrehozása](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Létrehoz egy kiszolgálói tűzfalszabályt|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|A tűzfalszabályokat a kiszolgáló listákat|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|A részletek a tűzfalszabályok megjelenítése|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|A tűzfalszabályok frissítése|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Tűzfalszabály törlése|

## <a name="transact-sql-manage-logical-servers-and-databases"></a>Transact-SQL: Logikai kiszolgálóiról és adatbázisairól kezelése

Hozzon létre és kezeli az Azure SQL server, adatbázisok és tűzfalak a Transact-SQL, használja a következő T-SQL-parancsokat. Ezek a parancsok az Azure portal használatával adhat ki [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), vagy bármely más programot, amely képes csatlakozni egy Azure SQL Database-kiszolgálóhoz, és adja át a Transact-SQL parancsok. Rugalmas készletek kezeléséhhez lásd: [rugalmas készletek](sql-database-elastic-pool.md).

> [!TIP]
> Ehhez a rövid útmutatóhoz, a Microsoft Windows SQL Server Management Studio használatával, lásd: [Azure SQL Database: SQL Server Management Studio használatával csatlakozhat, és adatokat kérdezhet le](sql-database-connect-query-ssms.md). A rövid útmutató a macOS, Linux vagy Windows Visual Studio Code használatával, lásd: [Azure SQL Database: Csatlakozás és adatok lekérdezése a Visual Studio Code használatával](sql-database-connect-query-vscode.md).
> [!IMPORTANT]
> Nem hozható létre, vagy kiszolgáló törlése a Transact-SQL használatával.

| Parancs | Leírás |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)|Létrehoz egy új adatbázist. Új adatbázis létrehozásához a master adatbázishoz internetkapcsolatra.|
| [ALTER DATABASE (Azure SQL Database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Módosítja egy Azure SQL database. |
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Töröl egy adatbázist.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Adja vissza az edition (szolgáltatási réteg), a szolgáltatási cél (tarifacsomag) és a rugalmas készlet nevét, egy Azure SQL database vagy az Azure SQL Data Warehouse esetében. Ha be van jelentkezve a master adatbázishoz az Azure SQL Database kiszolgáló, információkat az összes adatbázis adja vissza. Az Azure SQL Data Warehouse akkor kapcsolódnia kell a master adatbázisban.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Egy Azure SQL Database-adatbázishoz a CPU, IO, és a memória használati adja vissza. Egy sor létezik 15 másodpercenként, még akkor is, ha ott nem nincs tevékenység az adatbázisban.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Az Azure SQL Database CPU-használatát és a tárolási adatokat adja vissza. Az adatokat gyűjti, és öt perces intervallumok belül.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Az SQL Database adatbázis csatlakozási eseményeket, adatbázis-kapcsolat sikeres és sikertelen áttekintését nyújtó statisztikákat tartalmaz. |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Sikeres Azure SQL Database adatbázis-kapcsolatok, a csatlakozási hibák és a holtpontok adja vissza. Ezen információk használatával nyomon követheti, vagy az SQL Database-adatbázis-tevékenységek hibáinak elhárítása.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Létrehozza vagy frissíti az SQL Database-kiszolgálóhoz kiszolgálószintű tűzfal beállításait. Ez a tárolt eljárás csak akkor használható a master adatbázisban, hogy a kiszolgálószintű fő bejelentkezéssel. Egy kiszolgálószintű tűzfalszabályt csak hozhatók létre az első kiszolgálószintű tűzfalszabály létrehozása az Azure-szintű engedélyekkel rendelkező felhasználó által után Transact-SQL használatával|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|A kiszolgálószintű tűzfal beállításai a Microsoft Azure SQL Database társított kapcsolatos információkat ad vissza.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Kiszolgálószintű tűzfal beállításainak eltávolítása az SQL Database-kiszolgálóhoz. Ez a tárolt eljárás csak akkor használható a master adatbázisban, hogy a kiszolgálószintű fő bejelentkezéssel.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Létrehozza vagy frissíti az adatbázisszintű tűzfalszabályok az Azure SQL Database vagy az SQL Data warehouse-bA. Tűzfalszabályok is konfigurálható, a master adatbázisban, valamint a felhasználói adatbázisokat az SQL Database. Tűzfalszabályok akkor hasznos, ha használatával tartalmazott adatbázis felhasználóit. |
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Az adatbázisszintű tűzfal beállításai a Microsoft Azure SQL Database társított kapcsolatos információkat ad vissza. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Adatbázisszintű tűzfal beállítása az Azure SQL Database vagy az SQL Data Warehouse eltávolítja. |

## <a name="rest-api-manage-logical-servers-and-databases"></a>REST API: Logikai kiszolgálóiról és adatbázisairól kezelése

Létrehozása és kezelése az Azure SQL server, adatbázisok és tűzfalak, ezek a REST API-kérelmek használja.

| Parancs | Leírás |
| --- | --- |
|[Kiszolgálók – létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Létrehozza vagy frissíti az új kiszolgálóra.|
|[Kiszolgálók – törlés](https://docs.microsoft.com/rest/api/sql/servers/delete)|SQL-kiszolgáló törlése.|
|[Kiszolgálók – Get](https://docs.microsoft.com/rest/api/sql/servers/get)|Lekérdezi a kiszolgálót.|
|[Kiszolgálók – lista](https://docs.microsoft.com/rest/api/sql/servers/list)|Előfizetés a kiszolgálók listáját adja vissza.|
|[Kiszolgálók – lista erőforráscsoport alapján](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|A kiszolgálók listáját adja vissza egy erőforráscsoportban.|
|[Kiszolgálók – frissítés](https://docs.microsoft.com/rest/api/sql/servers/update)|Frissíti egy meglévő kiszolgálóra.|
|[-Adatbázis létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Új adatbázis létrehozása vagy frissítése egy meglévő adatbázist.|
|[Adatbázisok – törlés](https://docs.microsoft.com/rest/api/sql/databases/delete)|Töröl egy adatbázist.|
|[Adatbázisok – Get](https://docs.microsoft.com/rest/api/sql/databases/get)|Egy adatbázis beolvasása.|
|[Adatbázis - listát a rugalmas készlet](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Rugalmas készletben található adatbázisok listáját adja vissza.|
|[Adatbázis - kiszolgáló által lista](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|A kiszolgálón az adatbázisok listáját adja vissza.|
|[Adatbázis - frissítés](https://docs.microsoft.com/rest/api/sql/databases/update)|Frissíti egy meglévő adatbázist.|
|[A tűzfal - szabályok létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|Létrehozza vagy frissíti egy tűzfalszabályt.|
|[Tűzfalszabályok – törlés](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|Törli a tűzfalszabályt.|
|[Tűzfalszabályok - Get](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|Lekérdezi egy tűzfalszabályt.|
|[Tűzfalszabályok - lista-kiszolgáló](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|Tűzfalszabályok listáját adja vissza.|

## <a name="next-steps"></a>További lépések

- Az SQL Server-adatbázis áttelepítése az Azure-bA kapcsolatos további információkért lásd: [áttelepítése az Azure SQL Database](sql-database-cloud-migrate.md).
- A támogatott funkciókkal kapcsolatos tudnivalókat lásd: [Funkciók](sql-database-features.md).
