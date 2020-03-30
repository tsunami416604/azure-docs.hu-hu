---
title: Kiszolgálók és adatbázisok létrehozása, kezelése
description: Információ az SQL Database-kiszolgálók és az egyes adatbázisok létrehozásáról és kezeléséről.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268547"
---
# <a name="create-and-manage-sql-database-servers-and-single-databases-in-azure-sql-database"></a>SQL Database-kiszolgálók és egyetlen adatbázisok létrehozása és kezelése az Azure SQL Database-ben

Sql Database-kiszolgálókat és egyetlen adatbázisokat hozhat létre és kezelhet az Azure Portal, a PowerShell, az Azure CLI, a REST API és a Transact-SQL használatával.

## <a name="azure-portal-manage-sql-database-servers-and-single-databases"></a>Azure portal: SQL Database-kiszolgálók és egyetlen adatbázisok kezelése

Az Azure SQL-adatbázis erőforráscsoportot idő előtt vagy a kiszolgáló létrehozása közben hozhat létre. Többféle módszer létezik egy új SQL-kiszolgálóűrlap elhívására, akár egy új SQL-kiszolgáló létrehozásával, akár egy új adatbázis létrehozásának részeként.

### <a name="create-a-blank-sql-database-server"></a>Üres SQL-adatbázis-kiszolgáló létrehozása

SQL Database-kiszolgáló létrehozásához az [Azure Portal](https://portal.azure.com)használatával keresse meg egy üres SQL-kiszolgáló (logikai kiszolgáló) űrlapot.  

### <a name="create-a-blank-or-sample-sql-single-database"></a>Üres vagy minta SQL egyetlen adatbázis létrehozása

Ha az [Azure](https://portal.azure.com)Portal használatával szeretne létrehozni egy Azure SQL-adatbázist, keresse meg az üres SQL-adatbázis űrlapot, és adja meg a kért információkat. Az Azure SQL-adatbázis erőforráscsoportját és az SQL Database-kiszolgálót előre vagy az egyetlen adatbázis létrehozásakor hozhatja létre. Létrehozhat egy üres adatbázist, vagy létrehozhat egy mintaadatbázist a Kalandorbolt LT alapján.

  ![adatbázis létrehozása-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Az adatbázis tarifacsomagjának kiválasztásáról a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és [a virtuálismag-alapú vásárlási modell című témakörben](sql-database-service-tiers-vcore.md)talál további információt.

Felügyelt példány létrehozásához olvassa el a [Felügyelt példány létrehozása című témakört.](sql-database-managed-instance-get-started.md)

## <a name="manage-an-existing-sql-database-server"></a>Meglévő SQL-adatbázis-kiszolgáló kezelése

Meglévő SQL Database-kiszolgáló kezeléséhez keresse meg a kiszolgálót számos módszerrel – például egy adott SQL adatbázislapról, az **SQL-kiszolgálók** lapról vagy a **Minden erőforrás** lapról.

Meglévő adatbázis kezeléséhez keresse meg az **SQL-adatbázisok** lapot, és kattintson a kezelni kívánt adatbázisra. Az alábbi képernyőkép bemutatja, hogyan kezdheti el egy adatbázis kiszolgálószintű tűzfalát az **Adatbázis Áttekintés lapján.**

   ![kiszolgálói tűzfalszabály](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> Az adatbázis teljesítménytulajdonságainak konfigurálása a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és [a virtuálismag-alapú vásárlási modell című témakörben található.](sql-database-service-tiers-vcore.md)
> [!TIP]
> Az Azure Portal gyorsútmutatóról az [Azure SQL-adatbázis létrehozása az Azure Portalon című témakörben](sql-database-single-database-get-started.md)található.

## <a name="powershell-manage-sql-database-servers-and-single-databases"></a>PowerShell: SQL Database-kiszolgálók és egyetlen adatbázisok kezelése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Ezekről a parancsmagokról az [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)című témakörben található. Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak.

Azure SQL Database-kiszolgálók, egy- és készletalapú adatbázisok, valamint SQL Database-kiszolgálótűzfalak azure PowerShell használatával történő létrehozásához és kezeléséhez használja a következő PowerShell-parancsmagokat. Ha telepítenie vagy frissítenie kell a PowerShellt, olvassa [el az Azure PowerShell-modul telepítése című témakört.](/powershell/azure/install-az-ps)

> [!TIP]
> A PowerShell példaparancsfájljairól a [PowerShell használata egyetlen Azure-adatbázis létrehozásához és az SQL Database server tűzfalszabályának konfigurálásához,](scripts/sql-database-create-and-configure-database-powershell.md) valamint [egy SQL-adatbázis PowerShell használatával történő méretezése című](scripts/sql-database-monitor-and-scale-database-powershell.md)témakörben található.

| Parancsmag | Leírás |
| --- | --- |
|[Új-AzsqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Adatbázis létrehozása |
|[Get-AzSqlDatabase adatbázis](/powershell/module/az.sql/get-azsqldatabase)|Egy vagy több adatbázis beírása|
|[Set-AzSqlDatabase Adatbázis](/powershell/module/az.sql/set-azsqldatabase)|Adatbázis tulajdonságainak beállítása vagy meglévő adatbázis áthelyezése rugalmas készletbe|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Adatbázis eltávolítása|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Erőforráscsoport létrehozása|
|[Új-AzsqlServer](/powershell/module/az.sql/new-azsqlserver)|Kiszolgáló létrehozása|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Információt ad vissza a kiszolgálókról|
|[Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver)|A kiszolgáló tulajdonságainak módosítja|
|[Eltávolítás-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Kiszolgáló eltávolítása|
|[Új-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Kiszolgálószintű tűzfalszabályt hoz létre |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Tűzfalszabályok bekerülése kiszolgálóhoz|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Tűzfalszabály módosítja a kiszolgálón|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Tűzfalszabály törlése a kiszolgálóról.|
| New-AzSqlServerVirtualNetworkRule | Virtuális [*hálózati szabályt*](sql-database-vnet-service-endpoint-rule-overview.md)hoz létre egy virtuális hálózati szolgáltatásvégpontal kapcsolatos alhálózat alapján. |

## <a name="azure-cli-manage-sql-database-servers-and-single-databases"></a>Azure CLI: SQL Database-kiszolgálók és egyetlen adatbázisok kezelése

Az Azure SQL-kiszolgáló, adatbázisok és tűzfalak azure [CLI](/cli/azure)használatával történő létrehozásához és kezeléséhez használja a következő [Azure CLI SQL Database](/cli/azure/sql/db) parancsokat. A [Cloud Shell-lel](/azure/cloud-shell/overview) futtassa a parancssori felületet a böngészőben, vagy [telepítse](/cli/azure/install-azure-cli) macOS, Linux, illetve Windows rendszeren. Rugalmas készletek létrehozásáról és kezeléséről [a Rugalmas készletek](sql-database-elastic-pool.md)ben.

> [!TIP]
> Az Azure CLI rövid útmutatója: [Azure SQL egyetlen adatbázis létrehozása az Azure CLI használatával](sql-database-cli-samples.md)című témakörben. Az Azure CLI példaparancsfájljairól a [Cli használata egyetlen Azure-adatbázis létrehozásához és az SQL Database tűzfalszabály konfigurálásához,](scripts/sql-database-create-and-configure-database-cli.md) valamint [a CLI használata az Azure SQL-adatbázis figyeléséhez és méretezéséhez című](scripts/sql-database-monitor-and-scale-database-cli.md)témakörben található.
>

| Parancsmag | Leírás |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Adatbázis létrehozása|
|[az sql db lista](/cli/azure/sql/db#az-sql-db-list)|A kiszolgáló összes adatbázisának és adattárházának, illetve a rugalmas készlet összes adatbázisának listázása|
|[az sql db list-kiadások](/cli/azure/sql/db#az-sql-db-list-editions)|Az elérhető szolgáltatási célkitűzések és tárolási korlátok listázása|
|[az sql db listahasználat](/cli/azure/sql/db#az-sql-db-list-usages)|Adatbázis-használatot ad eredményül.|
|[az sql db show](/cli/azure/sql/db#az-sql-db-show)|Adatbázis vagy adattárház bementése|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Adatbázis frissítése|
|[az sql db törlés](/cli/azure/sql/db#az-sql-db-delete)|Adatbázis eltávolítása|
|[az group create](/cli/azure/group#az-group-create)|Erőforráscsoport létrehozása|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Kiszolgáló létrehozása|
|[az sql-kiszolgálólista](/cli/azure/sql/server#az-sql-server-list)|Kiszolgálók listázása|
|[az sql server listahasználat](/cli/azure/sql/server#az-sql-server-list-usages)|Kiszolgálóhasználatot ad vissza.|
|[az sql server show](/cli/azure/sql/server#az-sql-server-show)|Beszerzi a kiszolgálót|
|[az sql server frissítés](/cli/azure/sql/server#az-sql-server-update)|Kiszolgáló frissítése|
|[az sql server törlés](/cli/azure/sql/server#az-sql-server-delete)|Kiszolgáló törlése|
|[az sql server tűzfal-szabály létrehozása](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Kiszolgálói tűzfalszabály létrehozása|
|[az sql server tűzfalszabálylistája](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|A kiszolgálótűzfal-szabályok listája|
|[az sql server tűzfal-szabály megjelenítése](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Tűzfalszabály részleteinek megjelenítése|
|[az sql server tűzfalszabály frissítése](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Tűzfalszabály frissítése|
|[az sql server tűzfal-szabály törlése](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Tűzfalszabály törlése|

## <a name="transact-sql-manage-sql-database-servers-and-single-databases"></a>Transact-SQL: SQL Database-kiszolgálók és egyetlen adatbázisok kezelése

Azure SQL-kiszolgáló, adatbázisok és tűzfalak létrehozásához és kezeléséhez a Transact-SQL segítségével a következő T-SQL-parancsokat használhatja. Ezeket a parancsokat az Azure Portal, az [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), a Visual Studio [Code](https://code.visualstudio.com/docs)vagy bármely más olyan program segítségével adhatja ki, amely csatlakozhat egy Azure SQL Database-kiszolgálóhoz, és átadhatja a Transact-SQL parancsokat. Rugalmas készletek kezelésére, [lásd: rugalmas készletek.](sql-database-elastic-pool.md)

> [!TIP]
> Az SQL Server Management Studio microsoft windowsos használatának gyors használatáról az [Azure SQL Database: Az SQL Server Management Studio használatával adatok csatlakoztatása és lekérdezése](sql-database-connect-query-ssms.md)című témakörben található. A Visual Studio Code macOS, Linux vagy Windows rendszeren való használatának rövid útmutatóját az [Azure SQL Database: A Visual Studio-kód használatával az adatok csatlakoztatásához és lekérdezéséhez](sql-database-connect-query-vscode.md)című témakörben találja.
> [!IMPORTANT]
> A Transact-SQL használatával nem hozható létre és nem törölhet kiszolgálót.

| Parancs | Leírás |
| --- | --- |
|[ADATBÁZIS LÉTREHOZÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)|Új, egységes adatbázist hoz létre. Új adatbázis létrehozásához kapcsolódnia kell a fő adatbázishoz.|
| [ALTER ADATBÁZIS (Azure SQL-adatbázis)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Módosítja az Azure SQL-adatbázist. |
|[DROP ADATBÁZIS (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Adatbázis törlése.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Egy Azure SQL-adatbázis vagy egy Azure SQL Data Warehouse kiadását (szolgáltatási szint), szolgáltatási célt (tarifacsomag) és rugalmas készletnevét adja vissza. Ha bejelentkezett a fő adatbázis egy Azure SQL Database-kiszolgáló, az összes adatbázis adatait adja vissza. Az Azure SQL Data Warehouse, a fő adatbázishoz kell csatlakoztatni.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Processzor-, I//-és memóriafelhasználást ad vissza egy Azure SQL Database-adatbázishoz. 15 másodpercenként egy sor létezik, még akkor is, ha nincs tevékenység az adatbázisban.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Az Azure SQL-adatbázis processzorhasználati és tárolási adatait adja vissza. Az adatokat öt perces időközönként gyűjtjük és összesítjük.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Az SQL Database adatbázis-kapcsolati eseményeinek statisztikáit tartalmazza, amelyek áttekintést nyújtanak az adatbázis-kapcsolatok sikerességéről és hibáiról. |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Sikeres Azure SQL Database adatbázis-kapcsolatokat, kapcsolathibákat és holtpontokat ad vissza. Ezeket az információkat az SQL Database segítségével nyomon követheti vagy elháríthatja az adatbázis-tevékenységgel.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Létrehozza vagy frissíti az SQL Database-kiszolgáló kiszolgálószintű tűzfalbeállításait. Ez a tárolt eljárás csak a főadatbázisban érhető el a kiszolgálószintű egyszerű bejelentkezéshez. Kiszolgálószintű tűzfalszabály csak a Transact-SQL használatával hozható létre, miután az első kiszolgálószintű tűzfalszabályt egy Azure-szintű engedélyekkel rendelkező felhasználó hozta létre|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|A Microsoft Azure SQL-adatbázishoz társított kiszolgálószintű tűzfalbeállításokkal kapcsolatos információkat adja vissza.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Eltávolítja a kiszolgálószintű tűzfalbeállításokat az SQL Database-kiszolgálóról. Ez a tárolt eljárás csak a főadatbázisban érhető el a kiszolgálószintű egyszerű bejelentkezéshez.|
|[sp_set_database_firewall_rule (Azure SQL-adatbázis)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Létrehozza vagy frissíti az Azure SQL Database vagy az SQL Data Warehouse adatbázis-szintű tűzfalszabályait. Az adatbázis tűzfalszabályai konfigurálhatók a fő adatbázishoz és az SQL Database felhasználói adatbázisaihoz. Az adatbázis tűzfalszabályai akkor hasznosak, ha tartalmazott adatbázis-felhasználókat használ. |
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|A Microsoft Azure SQL-adatbázishoz társított adatbázisszintű tűzfalbeállításokkal kapcsolatos információkat adja vissza. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Eltávolítja az adatbázis-szintű tűzfal-beállítást az Azure SQL Database vagy az SQL Data Warehouse. |

## <a name="rest-api-manage-sql-database-servers-and-single-databases"></a>REST API: SQL Database-kiszolgálók és egyetlen adatbázisok kezelése

Az Azure SQL-kiszolgáló, adatbázisok és tűzfalak létrehozásához és kezeléséhez használja ezeket a REST API-kérelmeket.

| Parancs | Leírás |
| --- | --- |
|[Kiszolgálók – Létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Új kiszolgálót hoz létre vagy frissít.|
|[Kiszolgálók - Törlés](https://docs.microsoft.com/rest/api/sql/servers/delete)|SQL-kiszolgáló törlése.|
|[Szerverek - Bekés](https://docs.microsoft.com/rest/api/sql/servers/get)|Kap egy szervert.|
|[Kiszolgálók - Lista](https://docs.microsoft.com/rest/api/sql/servers/list)|Egy előfizetéskiszolgálóinak listáját adja vissza.|
|[Kiszolgálók – Lista erőforráscsoport szerint](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|Erőforráscsoport kiszolgálóinak listáját adja vissza.|
|[Kiszolgálók - Frissítés](https://docs.microsoft.com/rest/api/sql/servers/update)|Meglévő kiszolgáló frissítése.|
|[Adatbázisok – Létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Új adatbázist hoz létre, vagy egy meglévőadatbázist frissít.|
|[Adatbázisok - Törlés](https://docs.microsoft.com/rest/api/sql/databases/delete)|Adatbázis törlése.|
|[Adatbázisok - Beszerezés](https://docs.microsoft.com/rest/api/sql/databases/get)|Beszerzi az adatbázist.|
|[Adatbázisok – Lista rugalmas készlet szerint](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Egy rugalmas készletben lévő adatbázisok listáját adja vissza.|
|[Adatbázisok – Lista kiszolgáló szerint](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|A kiszolgáló adatbázisainak listáját adja vissza.|
|[Adatbázisok - Frissítés](https://docs.microsoft.com/rest/api/sql/databases/update)|Meglévő adatbázis frissítése.|
|[Tűzfalszabályok – Létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|Tűzfalszabályt hoz létre vagy frissít.|
|[Tűzfalszabályok - Törlés](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|Tűzfalszabály törlése.|
|[Tűzfalszabályok - Get](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|Tűzfalszabályt kap.|
|[Tűzfalszabályok – Lista kiszolgáló szerint](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|Tűzfalszabályok listáját adja vissza.|

## <a name="next-steps"></a>További lépések

- Az SQL Server-adatbázisok Azure-ba való áttelepítéséről az [Áttelepítés az Azure SQL-adatbázisba](sql-database-single-database-migrate.md)című témakörben olvashat.
- A támogatott funkciókkal kapcsolatos tudnivalókat lásd: [Funkciók](sql-database-features.md).
