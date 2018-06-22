---
title: Létrehozásához és kezeléséhez a rugalmas készletek - Azure SQL adatbázis |} Microsoft Docs
description: Létrehozása és kezelése az Azure SQL rugalmas készletek.
keywords: több adatbázisból, adatbázis-erőforrások, adatbázis-teljesítmény
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.date: 06/20/2018
ms.author: ninarn
ms.topic: conceptual
ms.reviewer: carlrab
ms.openlocfilehash: 3cdc82d71c05298aa5822b87c22edcc5d8e73385
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313324"
---
# <a name="create-and-manage-elastic-pools-in-azure-sql-database"></a>Létrehozása és kezelése az Azure SQL Database rugalmas készletek

A rugalmas készlethez meghatározhatja, hogy a rugalmas készlet a munkaterhelés-adatbázisa kezeléséhez szükséges erőforrások mennyiségét, és minden készletezett adatbázis erőforrások mennyisége. 

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Azure-portálon: rugalmas készletek és a készletezett adatbázisok kezelése

Egy helyen található összes alkalmazáskészlet beállításai: a **készlet beállítása** panelen. Ahhoz, hogy az itt, található a portálon, majd kattintson a rugalmas készlet **készlet beállítása** vagy a panel tetején vagy az erőforrás a bal oldali menüből.

Itt egy kötegben levő összes tetszőleges kombinációját, a következő módosításokat, majd mentse a módosításokat végezheti el:
1. A készlet szolgáltatási szintjének módosítása
2. A teljesítmény (DTU vagy vCores) és a tárolási méretek feljebb vagy lejjebb
3. És adatbázisok belőle a készlet törlése
4. Állítsa be a min (garantált), valamint a maximális teljesítményszint az adatbázisok a készletek
5. Ellenőrizze a költség módosításait a számlázási miatt az új beállítások megtekintéséhez

![A rugalmas készlet konfigurációs panel](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>PowerShell: Rugalmas készletek és a készletezett adatbázisok kezelése 

Hozzon létre, és SQL Database rugalmas készletek és az Azure PowerShell készletezett adatbázisok kezeléséhez, használja a következő PowerShell-parancsmagok. Ha szeretné telepíteni vagy frissíteni a PowerShell, lásd: [telepítése Azure PowerShell modul](/powershell/azure/install-azurerm-ps). Hozzon létre és az adatbázisok, a kiszolgálók és a tűzfal-szabályok kezelése [létrehozása és kezelése az Azure SQL Database-kiszolgálók és adatbázisok PowerShell-lel](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-powershell).

> [!TIP]
> PowerShell-példa parancsfájlok, lásd: [rugalmas készletek létrehozása és a készletek és a PowerShell-lel készlet kívül adatbázisok áthelyezése](scripts/sql-database-move-database-between-pools-powershell.md) és [a PowerShell szolgáltatás használatával figyelheti és a rugalmas SQL-készletet, az Azure SQL Databaseméretezése](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Parancsmag | Leírás |
| --- | --- |
|[New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool)|A rugalmas adatbáziskészlet létrehoz egy logikai SQL-kiszolgálón.|
|[Get-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/get-azurermsqlelasticpool)|Lekérdezi a rugalmas készletek és a tulajdonság értékek logikai SQL-kiszolgálón.|
|[Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)|A rugalmas adatbáziskészlet logikai SQL-kiszolgáló tulajdonságainak módosítása. Tegyük fel például, a **StorageMB** tulajdonság módosításához a rugalmas készlet maximális tárolási.|
|[Remove-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/remove-azurermsqlelasticpool)|Törli a rugalmas adatbáziskészlet logikai SQL-kiszolgálón.|
|[Get-AzureRmSqlElasticPoolActivity](/powershell/module/azurerm.sql/get-azurermsqlelasticpoolactivity)|Egy rugalmas készlet logikai SQL-kiszolgálón a műveletek állapotának beolvasása.|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Létrehoz egy új adatbázist, egy meglévő készlet vagy egy önálló adatbázis. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Egy vagy több adatbázist kér le.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Az adatbázis tulajdonságainak megadása, vagy helyezi át a meglévő adatbázis into, ki és rugalmas készletek között.|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Eltávolít egy adatbázis.|


> [!TIP]
> Sok adatbázisok rugalmas készlethez létrehozásának befejezése után a portál vagy PowerShell-parancsmagok egyszerre csak egy önálló adatbázis létrehozása időt vehet igénybe. Egy rugalmas készlet automatizálásához, lásd: [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).
>

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Az Azure CLI: Rugalmas készletek és a készletezett adatbázisok kezelése

Létrehozásához és kezeléséhez az SQL Database rugalmas készletek és a [Azure CLI](/cli/azure), használja a következő [Azure CLI SQL Database](/cli/azure/sql/db) parancsok. A [Cloud Shell-lel](/azure/cloud-shell/overview) futtassa a parancssori felületet a böngészőben, vagy [telepítse](/cli/azure/install-azure-cli) macOS, Linux, illetve Windows rendszeren.

> [!TIP]
> Az Azure parancssori felület parancsfájlpéldákat, lásd: [használata CLI Azure SQL-adatbázis áthelyezése rugalmas SQL-készletet a](scripts/sql-database-move-database-between-pools-cli.md) és [méretezése a rugalmas SQL-készletet, az Azure SQL-adatbázis használata az Azure parancssori felület](scripts/sql-database-scale-pool-cli.md).
>

| Parancsmag | Leírás |
| --- | --- |
|[az sql-rugalmas-készlet létrehozása](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create)|Létrehoz egy rugalmas készlet.|
|[az sql rugalmas-készlet listája](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list)|Kiszolgálók rugalmas készletek listáját adja vissza.|
|[az sql rugalmas-készlet lista-adatbázisok](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_dbs)|A rugalmas készletekben található adatbázisok listáját adja vissza.|
|[az sql rugalmas-készlet lista-verziók](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_editions)|Is elérhető készletből DTU beállításokat tartalmaz, tárolási korlátok és adatbázis-beállítások szerint. Ahhoz, hogy csökkentse a részletesség további tárolási korlátokat és az adatbázisonkénti beállítások alapértelmezés szerint rejtve vannak.|
|[az sql rugalmas-készlet frissítése](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)|Frissíti a rugalmas készletekben.|
|[az sql rugalmas-készlet törlése](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_delete)|A rugalmas készlet törlése.|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL: Készletezett adatbázisok kezelése

Létrehozásához és a meglévő rugalmas készleten belül adatbázisok áthelyezése vagy egy SQL-adatbázis a Transact-SQL rugalmas készlet információt használja a következő T-SQL-parancsokat. Ezek a parancsok használata az Azure-portálon kiadhatja [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), vagy bármely más programot, amely egy Azure SQL Database-kiszolgálóhoz csatlakozhat, és adja át a Transact-SQL-parancsokat. Hozzon létre és az adatbázisok, a kiszolgálók és a tűzfal-szabályok kezelése [létrehozása és kezelése az Azure SQL Database-kiszolgálók és adatbázisok Transact-SQL használatával](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-transact-sql).

> [!IMPORTANT]
> Nem lehet létrehozni, frissíteni vagy Transact-SQL használatával az Azure SQL Database rugalmas készlet törlése. Adja hozzá, vagy távolítsa el az adatbázisok rugalmas készlethez való, és dinamikus felügyeleti nézetek használatával meglévő rugalmas készletek vonatkozó adatokat ad vissza.
>

| Parancs | Leírás |
| --- | --- |
|[ADATBÁZIS (az Azure SQL Database) létrehozása](/sql/t-sql/statements/create-database-azure-sql-database)|Létrehoz egy új adatbázist, egy meglévő készlet vagy egy önálló adatbázis. Kell csatlakoznia a főadatbázison való futtatásával hozzon létre egy új adatbázist.|
| [Az ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Vagy helyez át egy adatbázist, ki, rugalmas készletek között.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Adatbázis törlése.|
|[sys.elastic_pool_resource_stats (az Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Egy logikai kiszolgáló erőforrás kihasználtságának statisztikai adatai összes rugalmas adatbáziskészletek adja vissza. Minden rugalmas adatbáziskészlet van egy olyan sor 15 másodpercenként jelentési időszak (négy sorok száma percenként). Tartalmazzák CPU, a IO, a napló, a tároló fogyasztása és a egyidejű kérelem/munkamenet kihasználtsági minden adatbázis-készletben.|
|[sys.database_service_objectives (az Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Egy Azure SQL database vagy az Azure SQL Data Warehouse esetében adja vissza a edition (szolgáltatási réteg), a szolgáltatási cél (IP-címek) és a rugalmas készlet nevét. Ha be van jelentkezve a főadatbázishoz egy Azure SQL adatbázis-kiszolgáló, az összes adatbázis ad vissza adatokat. Az Azure SQL Data Warehouse kell csatlakoznia a fő adatbázist.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>REST API: Rugalmas készletek és a készletezett adatbázisok kezelése

Hozzon létre, és SQL Database rugalmas készletek és a készletezett adatbázisok kezeléséhez, használja a REST API-kérés.

| Parancs | Leírás |
| --- | --- |
|[Rugalmas készletek - létrehozása vagy frissítése](/rest/api/sql/elasticpools/createorupdate)|Létrehoz egy új rugalmas készletet, vagy egy meglévő rugalmas készlet frissítése.|
|[Rugalmas készletek - törlése](/rest/api/sql/elasticpools/delete)|A rugalmas készlet törlése.|
|[Rugalmas készletek - beolvasása](/rest/api/sql/elasticpools/get)|Lekérdezi a rugalmas készletekben.|
|[Rugalmas készletek - kiszolgáló listája](/rest/api/sql/elasticpools/listbyserver)|Kiszolgálók rugalmas készletek listáját adja vissza.|
|[Rugalmas készletek - frissítés](/rest/api/sql/elasticpools/update)|Frissíti a meglévő rugalmas készlet.|
|[Ajánlott rugalmas készletek érhetők - beolvasása](/rest/api/sql/recommendedelasticpools/get)|Lekérdezi a javasolt rugalmas készlet.|
|[Ajánlott rugalmas készletek érhetők - kiszolgáló listája](/rest/api/sql/recommendedelasticpools/listbyserver)|A ajánlott rugalmas készletek érhetők értéket ad vissza.|
|[Ajánlott rugalmas készletek érhetők - lista metrikák](/rest/api/sql/recommendedelasticpools/listmetrics)|Értéket ad vissza a rugalmas készlet metrikák ajánlott.|
|[A rugalmas készlet tevékenységek](/rest/api/sql/elasticpoolactivities)|A rugalmas készlet tevékenységek adja vissza.|
|[A rugalmas készlet adatbázis tevékenységek](/rest/api/sql/elasticpooldatabaseactivities)|Adatbázisok rugalmas készlethez belül tevékenység adja vissza.|
|[-Adatbázis létrehozása vagy frissítése](/rest/api/sql/databases/createorupdate)|Létrehoz egy új adatbázist, vagy egy meglévő adatbázist frissíti.|
|[Adatbázis - Get](/rest/api/sql/databases/get)|Lekérdezi egy adatbázis.|
|[Adatbázis - hozza ki a rugalmas készlet](/rest/api/sql/databases/getbyelasticpool)|Egy adatbázis lekérdezi a rugalmas készletekben belül.|
|[Ajánlott rugalmas készlet hozza - adatbázisok](/rest/api/sql/databases/getbyrecommendedelasticpool)|Egy adatbázis lekérdezi a javasolt rugalmas készlet belül.|
|[Adatbázis - listát a rugalmas készlet](/rest/api/sql/databases/listbyelasticpool)|A rugalmas készletekben található adatbázisok listáját adja vissza.|
|[Adatbázis - lista által javasolt rugalmas készlet](/rest/api/sql/databases/listbyrecommendedelasticpool)|Ajánlott rugalmas készlet belül adatbázisok listáját adja vissza.|
|[Adatbázis - kiszolgáló listája](/rest/api/sql/databases/listbyserver)|A Server-adatbázisok listáját adja vissza.|
|[Adatbázis - frissítés](/rest/api/sql/databases/update)|Egy meglévő adatbázist frissíti.|

## <a name="next-steps"></a>További lépések

* A videót: [a Microsoft Virtual Academy videó működés során az Azure SQL Database rugalmas képességek](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt.
* A rugalmas készleteket használó SaaS-oktatóanyag, lásd: [bemutatása a Wingtip SaaS-alkalmazáshoz](sql-database-wtp-overview.md).
