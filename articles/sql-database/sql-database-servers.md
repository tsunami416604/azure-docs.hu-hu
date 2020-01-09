---
title: Kiszolgálók
description: Ismerje meg Azure SQL Database-kiszolgálókat és azok felügyeletét.
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
ms.openlocfilehash: 7557ed43d9ecb8fc7a584e7e8239bc7ccb972e99
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647169"
---
# <a name="azure-sql-database-servers-and-their-management"></a>Azure SQL Database-kiszolgálók és azok kezelése

## <a name="what-is-an-azure-sql-database-server"></a>Mi az Azure SQL Database-kiszolgáló

A SQL Database-kiszolgálók olyan logikai szerkezetek, amelyek központi felügyeleti pontként működnek több önálló vagy [készletezett](sql-database-elastic-pool.md) adatbázis, [Bejelentkezés](sql-database-manage-logins.md), [Tűzfalszabályok](sql-database-firewall-configure.md), [naplózási szabályok](sql-database-auditing.md), [veszélyforrások észlelési szabályzatok](sql-database-threat-detection.md)és [feladatátvételi csoportok](sql-database-auto-failover-group.md) számára, a SQL Database-kiszolgáló lehet más régióban, mint az erőforráscsoport. Az Azure SQL Database létrehozása előtt a SQL Database-kiszolgálónak léteznie kell. SQL Database kiszolgáló által felügyelt összes adatbázis ugyanabban a régióban jön létre, mint a SQL Database-kiszolgáló.

A SQL Database-kiszolgáló különbözik egy olyan SQL Server-példánytól, amely a helyszíni világban is ismerős lehet. Pontosabban, a SQL Database szolgáltatás nem vállal garanciát az adatbázisok helyével kapcsolatban az azokat kezelő SQL Database-kiszolgálóval kapcsolatban, és nem tesz elérhetővé példány-szintű hozzáférést vagy szolgáltatást. Ezzel szemben a felügyelt példányban található példány-adatbázisok mind közösen helyezkednek el, ugyanúgy, mint a helyszíni világban SQL Server.

SQL Database kiszolgáló létrehozásakor meg kell adnia egy kiszolgálói bejelentkezési fiókot és jelszót, amely rendszergazdai jogosultságokkal rendelkezik a kiszolgálón található főadatbázishoz és a kiszolgálón létrehozott összes adatbázishoz. Ez a kezdeti fiók egy SQL-bejelentkezési fiók. Azure SQL Database támogatja az SQL-hitelesítést, és Azure Active Directory hitelesítést a hitelesítéshez. További információ a bejelentkezésekről és a hitelesítésről: [adatbázisok és bejelentkezések kezelése Azure SQL Databaseban](sql-database-manage-logins.md). A Windows-hitelesítés nem támogatott.

SQL Database kiszolgáló:

- Egy Azure-előfizetésen belül jön létre, de az erőforrásaival együtt áthelyezhető másik előfizetésre.
- Az adatbázisok, rugalmas készletek és adattárházak szülőerőforrása.
- Névteret biztosít az adatbázisok, a rugalmas készletek és az adattárházak számára
- Egy logikai tároló erős élettartamú szemantikagal – kiszolgáló törlése, valamint a tárolt adatbázisok, rugalmas készletek és adattárházak törlése
- Részt vesz az [Azure szerepköralapú hozzáférés-vezérlésében (RBAC)](/azure/role-based-access-control/overview) – a kiszolgálókon belüli adatbázisok, rugalmas készletek és adattárházak öröklik a hozzáférési jogosultságokat a kiszolgálóról
- A az adatbázisok, rugalmas készletek és adattárházak identitásának magas rendű eleme az Azure erőforrás-kezelési célokra (lásd: adatbázisok és készletek URL-sémája)
- Közösen helyezi el egy adott régió erőforrásait.
- Kapcsolódási végpontot biztosít az adatbázis-hozzáféréshez (`<serverName>`.database.windows.net)
- Egy master adatbázishoz való kapcsolódással hozzáférést biztosít a tárolt erőforrásokra vonatkozó metaadatokhoz a DMV-ken keresztül
- Hatókört biztosít a felügyeleti házirendek számára, amelyek az adatbázisaira vonatkoznak – bejelentkezések, tűzfal, naplózás, veszélyforrások észlelése és hasonlók
- A szülő-előfizetésben lévő kvóta korlátozza (alapértelmezés szerint hat kiszolgáló/előfizetés – [itt az előfizetési korlátok itt láthatók](../azure-resource-manager/management/azure-subscription-service-limits.md))
- Az adatbázis-kvóta-és DTU-vagy virtuális mag-kvóta hatókörét biztosítja a benne található erőforrások számára (például 45 000 DTU).
- A foglalt erőforrásokon engedélyezett funkciók verziószámozási hatóköre
- Kiszolgálószintű rendszerbiztonsági tagként bejelentkezve a kiszolgáló minden adatbázisa felügyelhető.
- Képes az olyan helyszíni SQL Server-példányokhoz hasonló bejelentkezések kezelésére, amelyek a kiszolgáló egy vagy több adatbázisához hozzáféréssel rendelkeznek, és korlátozott rendszergazdai jogosultságokkal ruházhatók fel. További információk: [Bejelentkezések](sql-database-manage-logins.md).
- A SQL Database-kiszolgálón létrehozott összes adatbázis alapértelmezett rendezése `SQL_LATIN1_GENERAL_CP1_CI_AS`, ahol a `LATIN1_GENERAL` angol (Egyesült Államok), `CP1` a kódlap 1252, `CI` a kis-és nagybetűket megkülönböztető, `AS` pedig ékezetes-érzékeny.

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-portal"></a>Azure SQL Server-kiszolgálók,-adatbázisok és-tűzfalak kezelése a Azure Portal használatával

Az Azure SQL Database-adatbázishoz tartozó erőforráscsoport idő előtt vagy maga a kiszolgáló létrehozásakor hozható létre. Az SQL Server új űrlapjának beolvasására több módszer is rendelkezésre áll egy új SQL Server létrehozásával vagy egy új adatbázis létrehozásának részeként.

### <a name="create-a-blank-sql-database-server"></a>Üres SQL Database-kiszolgáló létrehozása

Ha Azure SQL Database-kiszolgálót szeretne létrehozni (adatbázis nélkül) a [Azure Portal](https://portal.azure.com)használatával, navigáljon egy üres SQL Server-(logikai kiszolgáló) űrlapra.  

### <a name="create-a-blank-or-sample-sql-database"></a>Üres vagy mintául szolgáló SQL-adatbázis létrehozása

Ha az [Azure Portal](https://portal.azure.com)használatával szeretne Azure SQL Database-adatbázist létrehozni, navigáljon üres SQL Database űrlaphoz, és adja meg a kért információkat. Az Azure SQL Database-adatbázishoz tartozó erőforráscsoportot és SQL Database kiszolgálót az idő előtt vagy maga az adatbázis létrehozásakor is létrehozhatja. Létrehozhat egy üres adatbázist, vagy létrehozhat egy minta-adatbázist az Adventure Works LT alapján.

  ![adatbázis létrehozása-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Az adatbázis díjszabási rétegének kiválasztásával kapcsolatos információkért lásd: [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és [virtuális mag-alapú vásárlási modell](sql-database-service-tiers-vcore.md).

Felügyelt példány létrehozásához tekintse meg [a felügyelt példány létrehozása](sql-database-managed-instance-get-started.md) című témakört.

### <a name="manage-an-existing-sql-server"></a>Meglévő SQL Server-kiszolgáló kezelése

Egy meglévő kiszolgáló kezeléséhez navigáljon a kiszolgálóhoz számos módszer használatával – például egy adott SQL Database-oldalról, az SQL- **kiszolgálók** lapról vagy a **minden erőforrás** lapról.

Meglévő adatbázis kezeléséhez navigáljon az SQL- **adatbázisok** oldalra, és kattintson a felügyelni kívánt adatbázisra. Az alábbi képernyőfelvételen megtudhatja, hogyan kezdheti el az adatbázishoz tartozó kiszolgálói szintű tűzfal beállítását az adatbázis **Áttekintés** lapján.

   ![kiszolgálói tűzfalszabály](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> Az adatbázis teljesítmény-tulajdonságainak konfigurálásához tekintse meg a [DTU-alapú vásárlási modellt](sql-database-service-tiers-dtu.md) és a [virtuális mag-alapú vásárlási modellt](sql-database-service-tiers-vcore.md).
> [!TIP]
> Egy Azure Portal rövid útmutató: [Azure SQL Database-adatbázis létrehozása a Azure Portal](sql-database-single-database-get-started.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-powershell"></a>Azure SQL Server-kiszolgálók,-adatbázisok és-tűzfalak kezelése a PowerShell-lel

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

Az Azure SQL Server, adatbázisok és tűzfalak Azure PowerShell használatával történő létrehozásához és kezeléséhez használja a következő PowerShell-parancsmagokat. Ha telepítenie vagy frissítenie kell a PowerShellt, olvassa el a [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps)című témakört. Rugalmas készletek létrehozásához és kezeléséhez lásd: [rugalmas készletek](sql-database-elastic-pool.md).

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
| Új – AzSqlServerVirtualNetworkRule | Létrehoz egy [*virtuális hálózati szabályt*](sql-database-vnet-service-endpoint-rule-overview.md), amely egy Virtual Network szolgáltatási végponton alapuló alhálózat. |

> [!TIP]
> PowerShell-gyors útmutató: [Azure SQL-alapú önálló adatbázis létrehozása a PowerShell használatával](sql-database-single-database-get-started.md). A PowerShell-parancsfájlok esetében lásd: a [PowerShell használata Azure SQL önálló adatbázis létrehozásához és tűzfalszabály konfigurálásához](scripts/sql-database-create-and-configure-database-powershell.md) és egy [Azure SQL-alapú önálló adatbázis figyeléséhez és méretezéséhez a PowerShell használatával](scripts/sql-database-monitor-and-scale-database-powershell.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-cli"></a>Azure SQL-kiszolgálók,-adatbázisok és-tűzfalak kezelése az Azure CLI használatával

Azure SQL Server, adatbázisok és tűzfalak Azure [CLI](/cli/azure)-vel való létrehozásához és kezeléséhez használja az alábbi [Azure CLI SQL Database](/cli/azure/sql/db) parancsokat. A [Cloud Shell-lel](/azure/cloud-shell/overview) futtassa a parancssori felületet a böngészőben, vagy [telepítse](/cli/azure/install-azure-cli) macOS, Linux, illetve Windows rendszeren. Rugalmas készletek létrehozásához és kezeléséhez lásd: [rugalmas készletek](sql-database-elastic-pool.md).

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

> [!TIP]
> Azure CLI-gyors útmutató: Azure [SQL önálló adatbázis létrehozása az Azure CLI használatával](sql-database-cli-samples.md). Az Azure CLI példa parancsfájljai esetében lásd: a [CLI használata Azure SQL önálló adatbázis létrehozásához és egy tűzfalszabály konfigurálásához](scripts/sql-database-create-and-configure-database-cli.md) , valamint [egy Azure SQL-alapú önálló adatbázis figyeléséhez és méretezéséhez](scripts/sql-database-monitor-and-scale-database-cli.md)a CLI használatával.
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-transact-sql"></a>Azure SQL Server-kiszolgálók,-adatbázisok és-tűzfalak kezelése a Transact-SQL használatával

Az Azure SQL Server, adatbázisok és tűzfalak a Transact-SQL használatával történő létrehozásához és kezeléséhez használja a következő T-SQL-parancsokat. Ezeket a parancsokat a Azure Portal, a [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), a [Visual Studio Code](https://code.visualstudio.com/docs)vagy bármely más program használatával lehet kiadni, amely csatlakozhat egy Azure SQL Database-kiszolgálóhoz, és átadhatja a Transact-SQL-parancsokat. Rugalmas készletek kezeléséhez lásd: [rugalmas készletek](sql-database-elastic-pool.md).

> [!IMPORTANT]
> A Transact-SQL használatával nem lehet kiszolgálót létrehozni vagy törölni.
>

| Parancs | Leírás |
| --- | --- |
|[ADATBÁZIS létrehozása (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|létrehoz egy új adatbázist; Új adatbázis létrehozásához csatlakoznia kell a Master adatbázishoz.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Módosítja egy Azure SQL Database-adatbázist. |
|[ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Módosít egy Azure SQL Data Warehouse.|
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

> [!TIP]
> A Microsoft Windows SQL Server Management Studiot használó rövid útmutatókat a [Azure SQL Database: az SQL Server Management Studio használata a kapcsolódáshoz és az adatlekérdezéshez](sql-database-connect-query-ssms.md)című témakörben talál. A macOS, Linux vagy Windows rendszeren a Visual Studio Code használatával történő gyors útmutatóért lásd [Azure SQL Database: a Visual Studio Code használata a kapcsolódáshoz és az adatlekérdezéshez](sql-database-connect-query-vscode.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-rest-api"></a>Azure SQL Server-kiszolgálók,-adatbázisok és-tűzfalak kezelése a REST API használatával

Az Azure SQL Server, adatbázisok és tűzfalak létrehozásához és kezeléséhez használja ezeket a REST API kérelmeket.

| Parancs | Leírás |
| --- | --- |
|[Kiszolgálók – létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Új kiszolgáló létrehozása vagy frissítése.|
|[Kiszolgálók – törlés](https://docs.microsoft.com/rest/api/sql/servers/delete)|Töröl egy SQL-kiszolgálót.|
|[Kiszolgálók – Get](https://docs.microsoft.com/rest/api/sql/servers/get)|Kiszolgáló beolvasása.|
|[Kiszolgálók – lista](https://docs.microsoft.com/rest/api/sql/servers/list)|A kiszolgálók listáját adja vissza.|
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
