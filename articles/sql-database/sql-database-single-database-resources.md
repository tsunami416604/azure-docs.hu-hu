---
title: Az Azure SQL-adatbázis egy adatbázis |} Microsoft Docs
description: A szolgáltatási rétegben, teljesítményszintet és egyetlen Azure SQL-adatbázis storagea mennyisége kezelésére.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/04/2018
ms.author: carlrab
ms.openlocfilehash: c1fe162beca258fe8ec3d03ce2844c1abe3176dc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="manage-resources-for-a-single-database-in-azure-sql-database"></a>Az Azure SQL-adatbázis egy adatbázis-erőforrások kezelése

Az adatbázist az adatbázis számára a szükséges kezeli az alkalmazások és szolgáltatások a szolgáltatási rétegben, a teljesítményszintet és a szükséges tárolókapacitást erőforrások mennyisége határozza meg. 

## <a name="manage-single-database-resources-using-the-azure-portal"></a>Az Azure portál használatával egyetlen adatbázis-erőforrások kezelése

Állítsa be, vagy módosítsa a szolgáltatási rétegben, teljesítményszintet vagy tárterület új vagy meglévő Azure SQL-adatbázis az Azure portál használatával, nyissa meg a **konfigurálása a** ablakban kattintson az adatbázis **árképzési szint () méretezhető dtu-k)** – az alábbi képernyőfelvételen látható módon. 

- Vagy módosítsa a szolgáltatási rétegben a szolgáltatási rétegben, a munkaterhelés kiválasztásával. 
- Állítsa be vagy változtassa meg a teljesítményszintet (**dtu-k**) belül egy szolgáltatási réteg használatával a **DTU** csúszkát.
- Állítsa be, vagy módosítsa a teljesítmény szintű használó mennyisége a **tárolási** csúszkát. 

![Szolgáltatási szint és a teljesítmény szint konfigurálása](./media/sql-database-single-database-resources/change-service-tier.png)

Kattintson a **áttekintése** figyelése és/vagy egy folyamatban lévő művelet megszakításához.

![A művelet megszakítása](./media/sql-database-single-database-resources/cancel-operation.png)

> [!IMPORTANT]
> Felülvizsgálati [P11 és P15 adatbázisok maximális mérete 4 TB méretű a jelenlegi korlátozások](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb) P11 vagy P15 szolgáltatásréteg kiválasztásakor.
>

## <a name="manage-single-database-resources-using-powershell"></a>PowerShell-lel egy adatbázis-erőforrások kezelése

PowerShell-parancsmagok segítségével, vagy módosítsa a Azure SQL-adatbázisok szolgáltatásszintek, teljesítményszintjeivel és PowerShell használatával mennyisége. Ha szeretné telepíteni vagy frissíteni a PowerShell, lásd: [telepítése Azure PowerShell modul](/powershell/azure/install-azurerm-ps). 

| Parancsmag | Leírás |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Létrehoz egy adatbázis |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Egy vagy több adatbázis beolvasása|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Az adatbázis tulajdonságainak megadása, vagy a meglévő adatbázis áthelyezése rugalmas készletbe. Tegyük fel például, a **MaxSizeBytes** tulajdonság beállítása az adatbázis maximális méretét.|
|[Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity)|Adatbázis-művelet állapotát olvassa be. |
|[Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity)|Az aszinkron frissítése a művelet visszavonja.|


> [!TIP]
> Egy PowerShell-példa parancsfájlt, amely figyeli a metrikák egy adatbázis méretezi a magasabb teljesítményszintre, és a metrikák egyik egy riasztási szabályt hoz létre a következő témakörben: [figyelő és a skála egyetlen SQL adatbázis-PowerShellhasználatával](scripts/sql-database-monitor-and-scale-database-powershell.md).

## <a name="manage-single-database-resources-using-the-azure-cli"></a>Az Azure parancssori felület használatával egyetlen adatbázis-erőforrások kezelése

Beállítása vagy módosítása az Azure SQL-adatbázisok szolgáltatási szinteket, teljesítményszintjeivel és tárterület az Azure parancssori felület használatával ezeket használni [Azure CLI SQL Database](/cli/azure/sql/db) parancsok. A [Cloud Shell-lel](/azure/cloud-shell/overview) futtassa a parancssori felületet a böngészőben, vagy [telepítse](/cli/azure/install-azure-cli) macOS, Linux, illetve Windows rendszeren. Létrehozása és kezelése SQL rugalmas készletek: [rugalmas készletek](sql-database-elastic-pool.md).

| Parancsmag | Leírás |
| --- | --- |
|[az sql server-tűzfalszabály létrehozása](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Létrehoz egy tűzfalszabály létrehozása|
|[az sql server tűzfal-szabályok listája](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|A kiszolgálón a tűzfalszabályok listája|
|[az sql server-tűzfalszabály megjelenítése](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|A tűzfalszabályok részleteit tartalmazza|
|[az sql server tűzfal-szabály frissítése](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|A tűzfal szabály frissítése|
|[az sql server-tűzfalszabály törlése](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Egy tűzfalszabály törlése|
|[az sql db op listája](/cli/azure/sql/db/op?#az_sql_db_op_list)|Az adatbázison végrehajtott műveletek listájának lekérése.|
|[az sql db op Mégse](/cli/azure/sql/db/op#az_sql_db_op_cancel)|Az adatbázis aszinkron művelet visszavonása.|

> [!TIP]
> Az Azure parancssori felület példa parancsfájl, amely egy Azure SQL-adatbázis különböző teljesítményszintet is méretezi a méretre vonatkozó adatok az adatbázis lekérdezése után, lásd: [használata CLI figyelését, valamint egy SQL-adatbázis méretezése](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-single-database-resources-using-transact-sql"></a>Transact-SQL használatával egyetlen adatbázis-erőforrások kezelése

Beállítása vagy módosítása az Azure SQL-adatbázisok a szolgáltatási szinteket, teljesítményszintjeivel és tárterület a Transact-SQL, ezen T-SQL-parancsok használatával. Ezek a parancsok használata az Azure-portálon kiadhatja [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), vagy bármely más programot, amely egy Azure SQL Database-kiszolgálóhoz csatlakozhat, és adja át a Transact-SQL-parancsokat. 

| Parancs | Leírás |
| --- | --- |
|[ADATBÁZIS (az Azure SQL Database) létrehozása](/sql/t-sql/statements/create-database-azure-sql-database)|Egy új adatbázist hoz létre. Kell csatlakoznia a főadatbázison való futtatásával hozzon létre egy új adatbázist.|
| [Az ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Azure SQL-adatbázis módosítása. |
|[sys.database_service_objectives (az Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Egy Azure SQL database vagy az Azure SQL Data Warehouse esetében adja vissza a edition (szolgáltatási réteg), a szolgáltatási cél (IP-címek) és a rugalmas készlet nevét. Ha be van jelentkezve a főadatbázishoz egy Azure SQL adatbázis-kiszolgáló, az összes adatbázis ad vissza adatokat. Az Azure SQL Data Warehouse kell csatlakoznia a fő adatbázist.|
|[sys.database_usage (az Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|Felsorolja a száma, típusa és az Azure SQL Database-kiszolgálón lévő adatbázis időtartama.|

A következő példa bemutatja az adatbázis maximális méretét az ALTER DATABASE parancs használatával a következőre változik:

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

## <a name="manage-single-database-resources-using-the-rest-api"></a>A REST API használatával egyetlen adatbázis-erőforrások kezelése

Beállítása vagy módosítása az Azure SQL-adatbázisok szolgáltatási szinteket, teljesítményszintjeivel és tárterület a REST API-kérés használja.

| Parancs | Leírás |
| --- | --- |
|[-Adatbázis létrehozása vagy frissítése](/rest/api/sql/databases/createorupdate)|Létrehoz egy új adatbázist, vagy egy meglévő adatbázist frissíti.|
|[Adatbázis - Get](/rest/api/sql/databases/get)|Lekérdezi egy adatbázis.|
|[Adatbázis - hozza ki a rugalmas készlet](/rest/api/sql/databases/getbyelasticpool)|Egy adatbázis lekérdezi a rugalmas készletekben belül.|
|[Ajánlott rugalmas készlet hozza - adatbázisok](/rest/api/sql/databases/getbyrecommendedelasticpool)|Lekérdezi egy adatbázist egy recommented rugalmas készletet belül.|
|[Adatbázis - listát a rugalmas készlet](/rest/api/sql/databases/listbyelasticpool)|A rugalmas készletekben található adatbázisok listáját adja vissza.|
|[Adatbázis - lista által javasolt rugalmas készlet](/rest/api/sql/databases/listbyrecommendedelasticpool)|Adatbázisok belül egy recommented rugalmas készletet listáját adja vissza.|
|[Adatbázis - kiszolgáló listája](/rest/api/sql/databases/listbyserver)|A Server-adatbázisok listáját adja vissza.|
|[Adatbázis - frissítés](/rest/api/sql/databases/update)|Egy meglévő adatbázist frissíti.|
|[Műveletek – lista](/rest/api/sql/Operations/List)|Megjeleníti az elérhető SQL Rest API-műveleteket.|



## <a name="next-steps"></a>További lépések

- További tudnivalók a szolgáltatási szinteket, teljesítményszintjeivel és tárolási összegek, lásd: [[alapjául szolgáló vásárlási modell DTU-alapú](sql-database-service-tiers-dtu.md) és [vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió)](sql-database-service-tiers-vcore.md).
- További tudnivalók a rugalmas készletek című [rugalmas készletek](sql-database-elastic-pool.md).
- További tudnivalók [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../azure-subscription-service-limits.md)
