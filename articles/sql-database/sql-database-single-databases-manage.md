---
title: Kiszolgálók és önálló adatbázisok létrehozása, kezelése
description: Ismerkedjen meg SQL Database-kiszolgálók és önálló adatbázisok létrehozásával és kezelésével.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 02c4d7ba545282e3654f3889dd8000af33c728c7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268547"
---
# <a name="create-and-manage-sql-database-servers-and-single-databases-in-azure-sql-database"></a>SQL Database-kiszolgálók és önálló adatbázisok létrehozása és kezelése Azure SQL Database

SQL Database-kiszolgálókat és önálló adatbázisokat a Azure Portal, a PowerShell, az Azure CLI, a REST API és a Transact-SQL használatával hozhat létre és kezelhet.

## <a name="azure-portal-manage-sql-database-servers-and-single-databases"></a>Azure Portal: SQL Database-kiszolgálók és önálló adatbázisok kezelése

Az Azure SQL Database-adatbázishoz tartozó erőforráscsoport idő előtt vagy maga a kiszolgáló létrehozásakor hozható létre. Az SQL Server új űrlapjának beolvasására több módszer is rendelkezésre áll egy új SQL Server létrehozásával vagy egy új adatbázis létrehozásának részeként.

### <a name="create-a-blank-sql-database-server"></a>Üres SQL Database-kiszolgáló létrehozása

SQL Database kiszolgáló a [Azure Portal](https://portal.azure.com)használatával történő létrehozásához nyisson meg egy üres SQL Server-(logikai kiszolgáló) űrlapot.  

### <a name="create-a-blank-or-sample-sql-single-database"></a>Üres vagy egyetlen minta SQL-adatbázis létrehozása

Ha az [Azure Portal](https://portal.azure.com)használatával szeretne létrehozni egy Azure SQL-adatbázist, navigáljon üres SQL Database űrlaphoz, és adja meg a kért információkat. Az Azure SQL Database-adatbázishoz tartozó erőforráscsoportot és SQL Database kiszolgálót az idő előtt vagy az önálló adatbázis létrehozásakor is létrehozhatja. Létrehozhat egy üres adatbázist, vagy létrehozhat egy minta-adatbázist az Adventure Works LT alapján.

  ![adatbázis létrehozása-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Az adatbázis díjszabási rétegének kiválasztásával kapcsolatos információkért lásd: [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és [virtuális mag-alapú vásárlási modell](sql-database-service-tiers-vcore.md).

Felügyelt példány létrehozásához tekintse meg [a felügyelt példány létrehozása](sql-database-managed-instance-get-started.md) című témakört.

## <a name="manage-an-existing-sql-database-server"></a>Meglévő SQL Database-kiszolgáló kezelése

Meglévő SQL Database-kiszolgáló kezeléséhez navigáljon a kiszolgálóhoz számos módszer használatával – például az SQL-adatbázis adott oldaláról, az **SQL Server** -oldalról vagy a **minden erőforrás** lapról.

Meglévő adatbázis kezeléséhez navigáljon az SQL- **adatbázisok** oldalra, és kattintson a felügyelni kívánt adatbázisra. Az alábbi képernyőfelvételen megtudhatja, hogyan kezdheti el az adatbázishoz tartozó kiszolgálói szintű tűzfal beállítását az adatbázis **Áttekintés** lapján.

   ![kiszolgálói tűzfalszabály](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> Az adatbázis teljesítmény-tulajdonságainak konfigurálásához tekintse meg a [DTU-alapú vásárlási modellt](sql-database-service-tiers-dtu.md) és a [virtuális mag-alapú vásárlási modellt](sql-database-service-tiers-vcore.md).
> [!TIP]
> Egy Azure Portal rövid útmutató: [Azure SQL Database-adatbázis létrehozása a Azure Portal](sql-database-single-database-get-started.md).

## <a name="powershell-manage-sql-database-servers-and-single-databases"></a>PowerShell: SQL Database-kiszolgálók és önálló adatbázisok kezelése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

Azure SQL Database-kiszolgálók, önálló és készletezett adatbázisok létrehozásához és kezeléséhez, valamint a Azure PowerShell SQL Database kiszolgáló tűzfalakhoz használja a következő PowerShell-parancsmagokat. Ha telepítenie vagy frissítenie kell a PowerShellt, olvassa el a [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps)című témakört.

> [!TIP]
> A PowerShell-parancsfájlok esetében lásd: a [PowerShell használata Azure SQL önálló adatbázis létrehozásához és egy SQL Database kiszolgáló tűzfalszabály konfigurálásához](scripts/sql-database-create-and-configure-database-powershell.md) és [egy SQL-alapú önálló adatbázis figyeléséhez és méretezéséhez a PowerShell használatával](scripts/sql-database-monitor-and-scale-database-powershell.md).

| Parancsmag | Leírás |
| --- | --- |
|[Új – AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Létrehoz egy adatbázist |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Egy vagy több adatbázis beolvasása|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Beállítja egy adatbázis tulajdonságait, vagy áthelyezi egy meglévő adatbázist egy rugalmas készletbe.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Adatbázis eltávolítása|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Létrehoz egy erőforráscsoportot|
|[Új – AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Létrehoz egy kiszolgálót|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|A kiszolgálókkal kapcsolatos információkat ad vissza.|
|[Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver)|Egy kiszolgáló tulajdonságainak módosítása|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Kiszolgáló eltávolítása|
|[Új – AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Kiszolgálói szintű tűzfalszabály létrehozása |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Egy kiszolgáló tűzfalszabályok beolvasása|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Tűzfalszabály módosítása egy kiszolgálón|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Tűzfalszabály törlése egy kiszolgálóról.|
| New-AzSqlServerVirtualNetworkRule | Létrehoz egy [*virtuális hálózati szabályt*](sql-database-vnet-service-endpoint-rule-overview.md), amely egy Virtual Network szolgáltatási végponton alapuló alhálózat. |

## <a name="azure-cli-manage-sql-database-servers-and-single-databases"></a>Azure CLI: SQL Database-kiszolgálók és önálló adatbázisok kezelése

Azure SQL Server, adatbázisok és tűzfalak Azure [CLI](/cli/azure)-vel való létrehozásához és kezeléséhez használja az alábbi [Azure CLI SQL Database](/cli/azure/sql/db) parancsokat. A [Cloud Shell-lel](/azure/cloud-shell/overview) futtassa a parancssori felületet a böngészőben, vagy [telepítse](/cli/azure/install-azure-cli) macOS, Linux, illetve Windows rendszeren. Rugalmas készletek létrehozásához és kezeléséhez lásd: [rugalmas készletek](sql-database-elastic-pool.md).

> [!TIP]
> Azure CLI-gyors útmutató: Azure [SQL önálló adatbázis létrehozása az Azure CLI használatával](sql-database-cli-samples.md). Az Azure CLI példa parancsfájljai esetében lásd: a [CLI használata Azure SQL önálló adatbázis létrehozásához és egy SQL Database tűzfalszabály konfigurálásához](scripts/sql-database-create-and-configure-database-cli.md) , valamint a [CLI használatával egy Azure SQL-alapú önálló adatbázis figyeléséhez és méretezéséhez](scripts/sql-database-monitor-and-scale-database-cli.md).
>

| Parancsmag | Leírás |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Létrehoz egy adatbázist|
|[az SQL db List](/cli/azure/sql/db#az-sql-db-list)|Egy kiszolgáló összes adatbázisát és adattárházát, vagy egy rugalmas készletben lévő összes adatbázist listáz|
|[az SQL db List-Editions](/cli/azure/sql/db#az-sql-db-list-editions)|Az elérhető szolgáltatási célkitűzések és a tárolási korlátok listája|
|[az SQL db List-használat](/cli/azure/sql/db#az-sql-db-list-usages)|Adatbázis-használatot ad vissza|
|[az SQL db show](/cli/azure/sql/db#az-sql-db-show)|Adatbázis vagy adatraktár beolvasása|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Adatbázis frissítése|
|[az SQL db delete](/cli/azure/sql/db#az-sql-db-delete)|Adatbázis eltávolítása|
|[az group create](/cli/azure/group#az-group-create)|Létrehoz egy erőforráscsoportot|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Létrehoz egy kiszolgálót|
|[az SQL Server List](/cli/azure/sql/server#az-sql-server-list)|Kiszolgálók felsorolása|
|[az SQL Server List-használati adatok](/cli/azure/sql/server#az-sql-server-list-usages)|Kiszolgáló-használatot ad vissza|
|[az SQL Server show](/cli/azure/sql/server#az-sql-server-show)|Kiszolgáló beolvasása|
|[az SQL Server Update](/cli/azure/sql/server#az-sql-server-update)|Kiszolgáló frissítése|
|[az SQL Server delete](/cli/azure/sql/server#az-sql-server-delete)|Kiszolgáló törlése|
|[az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Létrehoz egy kiszolgálói tűzfalszabály-szabályt|
|[az SQL Server Firewall-Rule List](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|A kiszolgálón található tűzfalszabályok felsorolása|
|[az SQL Server Firewall-Rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Egy tűzfalszabály részleteit jeleníti meg|
|[az SQL Server Firewall-Rule Update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Tűzfalszabály frissítése|
|[az SQL Server Firewall-Rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Tűzfalszabály törlése|

## <a name="transact-sql-manage-sql-database-servers-and-single-databases"></a>Transact-SQL: SQL Database kiszolgálók és önálló adatbázisok kezelése

Az Azure SQL Server, adatbázisok és tűzfalak a Transact-SQL használatával történő létrehozásához és kezeléséhez használja a következő T-SQL-parancsokat. Ezeket a parancsokat a Azure Portal, a [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), a [Visual Studio Code](https://code.visualstudio.com/docs)vagy bármely más program használatával lehet kiadni, amely csatlakozhat egy Azure SQL Database-kiszolgálóhoz, és átadhatja a Transact-SQL-parancsokat. Rugalmas készletek kezeléséhez lásd: [rugalmas készletek](sql-database-elastic-pool.md).

> [!TIP]
> A Microsoft Windows SQL Server Management Studiot használó rövid útmutatókat a [Azure SQL Database: az SQL Server Management Studio használata a kapcsolódáshoz és az adatlekérdezéshez](sql-database-connect-query-ssms.md)című témakörben talál. A macOS, Linux vagy Windows rendszeren a Visual Studio Code használatával történő gyors útmutatóért lásd [Azure SQL Database: a Visual Studio Code használata a kapcsolódáshoz és az adatlekérdezéshez](sql-database-connect-query-vscode.md).
> [!IMPORTANT]
> A Transact-SQL használatával nem lehet kiszolgálót létrehozni vagy törölni.

| Parancs | Leírás |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)|Létrehoz egy új önálló adatbázist. Új adatbázis létrehozásához csatlakoznia kell a Master adatbázishoz.|
| [ALTER DATABASE (Azure SQL Database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Módosítja egy Azure SQL Database-adatbázist. |
|[ADATBÁZIS eldobása (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Töröl egy adatbázist.|
|[sys. database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|A kiadás (szolgáltatási csomag), a szolgáltatási cél (árképzési csomag) és a rugalmas készlet nevét adja vissza egy Azure SQL Database-adatbázishoz vagy egy Azure SQL Data Warehousehoz. Ha bejelentkezett a főadatbázisba egy Azure SQL Database-kiszolgálón, a visszaadja az összes adatbázisra vonatkozó információt. Azure SQL Data Warehouse esetében csatlakoznia kell a Master adatbázishoz.|
|[sys. dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Egy Azure SQL Database adatbázis CPU-, IO-és memória-felhasználását adja vissza. 15 másodpercenként egy sor létezik, még akkor is, ha nincs tevékenység az adatbázisban.|
|[sys. resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Egy Azure SQL Database CPU-használati és tárolási adatait adja vissza. Az adatok gyűjtése és összesítése öt percen belül történik.|
|[sys. database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|A SQL Database adatbázis-kapcsolati események statisztikáit tartalmazza, és áttekintést nyújt az adatbázis-kapcsolódási sikerekről és hibákról. |
|[sys. event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Sikeres Azure SQL Database adatbázis-kapcsolatok, csatlakozási hibák és holtpontok beolvasása. Ezekkel az információkkal nyomon követheti és elháríthatja az adatbázis-tevékenységeket SQL Database használatával.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Létrehozza vagy frissíti a SQL Database-kiszolgáló kiszolgálói szintű tűzfalbeállítások beállításait. Ez a tárolt eljárás csak a főadatbázisban érhető el a kiszolgálói szintű rendszerbiztonsági tag-bejelentkezéshez. A kiszolgálói szintű tűzfalszabályok csak akkor hozhatók létre a Transact-SQL használatával, ha az első kiszolgálói szintű tűzfalszabály az Azure-szintű engedélyekkel rendelkező felhasználó által lett létrehozva.|
|[sys. firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|A Microsoft Azure SQL Databasehoz társított kiszolgálói szintű tűzfalbeállítások adatait adja vissza.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|A kiszolgáló szintű tűzfalbeállítások eltávolítása a SQL Database-kiszolgálóról. Ez a tárolt eljárás csak a főadatbázisban érhető el a kiszolgálói szintű rendszerbiztonsági tag-bejelentkezéshez.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Létrehozza vagy frissíti a Azure SQL Database vagy SQL Data Warehouse adatbázis-szintű tűzfalszabály-szabályait. Az adatbázis-tűzfalszabályok konfigurálhatók a Master adatbázishoz, valamint SQL Database felhasználói adatbázisaihoz. Az adatbázis-tűzfalszabályok a tárolt adatbázis-felhasználók használata esetén hasznosak. |
|[sys. database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|A Microsoft Azure SQL Databasehoz társított adatbázis-szintű tűzfalbeállítások adatait adja vissza. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Az adatbázis-szintű tűzfal beállításának eltávolítása a Azure SQL Database vagy SQL Data Warehouse. |

## <a name="rest-api-manage-sql-database-servers-and-single-databases"></a>REST API: SQL Database-kiszolgálók és önálló adatbázisok kezelése

Az Azure SQL Server, adatbázisok és tűzfalak létrehozásához és kezeléséhez használja ezeket a REST API kérelmeket.

| Parancs | Leírás |
| --- | --- |
|[Kiszolgálók – létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Új kiszolgáló létrehozása vagy frissítése.|
|[Kiszolgálók – törlés](https://docs.microsoft.com/rest/api/sql/servers/delete)|Töröl egy SQL-kiszolgálót.|
|[Kiszolgálók – Get](https://docs.microsoft.com/rest/api/sql/servers/get)|Kiszolgáló beolvasása.|
|[Kiszolgálók – lista](https://docs.microsoft.com/rest/api/sql/servers/list)|Egy előfizetésben található kiszolgálók listáját adja vissza.|
|[Kiszolgálók – erőforráscsoport szerint listázva](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|Egy erőforráscsoport kiszolgálóinak listáját adja vissza.|
|[Kiszolgálók – frissítés](https://docs.microsoft.com/rest/api/sql/servers/update)|Frissít egy meglévő kiszolgálót.|
|[Adatbázisok – létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Létrehoz egy új adatbázist, vagy frissít egy meglévő adatbázist.|
|[Adatbázisok – törlés](https://docs.microsoft.com/rest/api/sql/databases/delete)|Töröl egy adatbázist.|
|[Adatbázisok – Get](https://docs.microsoft.com/rest/api/sql/databases/get)|Adatbázis beolvasása.|
|[Adatbázisok – lista rugalmas készlet alapján](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Egy rugalmas készletben lévő adatbázisok listáját adja vissza.|
|[Adatbázisok – kiszolgálók listázása](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Egy kiszolgáló adatbázisainak listáját adja vissza.|
|[Adatbázisok – frissítés](https://docs.microsoft.com/rest/api/sql/databases/update)|Frissít egy meglévő adatbázist.|
|[Tűzfalszabályok – létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|Tűzfalszabály létrehozása vagy frissítése.|
|[Tűzfalszabályok – törlés](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|Tűzfalszabály törlése.|
|[Tűzfalszabályok – Get](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|Tűzfalszabály beolvasása.|
|[Tűzfalszabályok – kiszolgálók listázása](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|A tűzfalszabályok listáját adja vissza.|

## <a name="next-steps"></a>Következő lépések

- A SQL Server-adatbázisok Azure-ba történő áttelepítésével kapcsolatos információkért lásd: [áttelepítés Azure SQL Databasere](sql-database-single-database-migrate.md).
- A támogatott funkciókkal kapcsolatos tudnivalókat lásd: [Funkciók](sql-database-features.md).
