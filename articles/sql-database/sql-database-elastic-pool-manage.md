---
title: Hozzon létre és kezelhetők rugalmas készletek – Azure SQL database |} A Microsoft Docs
description: Hozzon létre, és rugalmas Azure SQL-készletek kezelése.
keywords: több adatbázis, adatbázis-erőforrások, adatbázis-teljesítmény
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.date: 08/01/2018
ms.author: ninarn
ms.topic: conceptual
ms.reviewer: carlrab
ms.openlocfilehash: 96ea965ac383ae449afffa62c5e9950c6fd4e4da
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39411933"
---
# <a name="create-and-manage-elastic-pools-in-azure-sql-database"></a>Az Azure SQL Database rugalmas készletek létrehozása és kezelése

A rugalmas készlet határozza meg a rugalmas készlet-adatbázisa a számítási feladatok kezeléséhez szükséges erőforrások mennyiségét, és minden készletezett adatbázis erőforrás-mennyiséget. 

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Az Azure portal: rugalmas készletek és a készletezett adatbázisok kezelése

Egy helyen található összes tárolókészlet beállításainak: a **készlet beállítása** panelen. Itt lekéréséhez találja a portálon, majd kattintson a rugalmas készlet **készlet beállítása** vagy a panel tetején vagy az erőforrás a bal oldali menüben.

Itt választhat bármilyen kombinációja a következő módosításokat, és mentse őket egy kötegben szereplő összes:
1. A készlet szolgáltatási szintjének módosítása
2. A teljesítmény (dtu-k vagy virtuális magok) és a tárolási kisebbre vagy nagyobbra méretezhetők
3. Adja hozzá, vagy távolítsa el az adatbázisok és-tárolókról a készlet
4. Állítsa be a minimális (garantált) és a teljesítményszint, az a készletekben található adatbázisok maximális száma
5. Ellenőrizze a költségek módosítások eredményeképpen az új beállításokat a számla megtekintése

![Rugalmas készlet konfigurálása panel](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>PowerShell: Rugalmas készletek és a készletezett adatbázisok kezelése 

Hozzon létre és kezeli az SQL Database rugalmas készletek és a készletezett adatbázisok az Azure PowerShell-lel, használja a következő PowerShell-parancsmagok. Ha telepíteni vagy frissíteni a PowerShell, lásd: kell [Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps). Hozhat létre, és a logikai kiszolgálók, rugalmas készletek kezelése, tekintse meg a [logikai kiszolgáló létrehozása és a felügyelt](sql-database-logical-servers.md). Hozzon létre és tűzfalszabályok kezelése: [létrehozása és a tűzfalszabályok kezelése PowerShell használatával](sql-database-firewall-configure.md#manage-firewall-rules-using-azure-powershell).

> [!TIP]
> PowerShell-példa szkriptek, lásd: [rugalmas készletek létrehozása és adatbázisok áthelyezése a készletek között, és a egy PowerShell-lel készletből](scripts/sql-database-move-database-between-pools-powershell.md) és [a PowerShell szolgáltatás használatával az Azure SQL DatabaseegySQLrugalmaskészletmonitorozásaésskálázása](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Parancsmag | Leírás |
| --- | --- |
|[New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool)|Rugalmas adatbáziskészlet hoz logikai SQL Server-kiszolgálón.|
|[Get-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/get-azurermsqlelasticpool)|Lekérdezi a rugalmas készletek és a tulajdonságértékek logikai SQL Server-kiszolgálón.|
|[Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)|Egy rugalmas adatbáziskészletet a logikai SQL-kiszolgáló tulajdonságainak módosítása. Például a **StorageMB** tulajdonság az egy rugalmas készlet adatbázisonkénti maximális tárolási módosításához.|
|[Remove-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/remove-azurermsqlelasticpool)|Törli a rugalmas adatbáziskészlet logikai SQL Server-kiszolgálón.|
|[Get-AzureRmSqlElasticPoolActivity](/powershell/module/azurerm.sql/get-azurermsqlelasticpoolactivity)|Műveletek a rugalmas készlet a logikai SQL-kiszolgáló állapotát olvassa be.|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Létrehoz egy új adatbázist egy meglévő készlet vagy önálló adatbázisként. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Egy vagy több adatbázist kér le.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Beállítja egy adatbázis tulajdonságait, vagy be, összesen: vagy rugalmas készletek között helyezi át a létező adatbázis.|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Eltávolít egy adatbázist.|


> [!TIP]
> A rugalmas készletek több adatbázis létrehozásának befejezése után a portál vagy az egyszerre csak egy önálló adatbázis létrehozása PowerShell-parancsmagok használatával időt vehet igénybe. Rugalmas készlet automatizálásához, lásd: [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).
>

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Az Azure CLI: Rugalmas készletek és a készletezett adatbázisok kezelése

Létrehozásához és kezeléséhez az SQL Database rugalmas készletei az [Azure CLI-vel](/cli/azure), használja a következő [Azure CLI az SQL Database](/cli/azure/sql/db) parancsokat. A [Cloud Shell-lel](/azure/cloud-shell/overview) futtassa a parancssori felületet a böngészőben, vagy [telepítse](/cli/azure/install-azure-cli) macOS, Linux, illetve Windows rendszeren.

> [!TIP]
> Azure CLI-példa szkriptek, lásd: [használható CLI, Azure SQL-adatbázis áthelyezése egy rugalmas SQL-készletben](scripts/sql-database-move-database-between-pools-cli.md) és [használható Azure CLI az Azure SQL Database egy SQL rugalmas készlet méretezése](scripts/sql-database-scale-pool-cli.md).
>

| Parancsmag | Leírás |
| --- | --- |
|[az sql elastic-pool létrehozása](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create)|Egy rugalmas készletet hoz létre.|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list)|Rugalmas készletek listáját adja vissza a kiszolgálón.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_dbs)|Rugalmas készletben található adatbázisok listáját adja vissza.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_editions)|Emellett tartalmazza a rendelkezésre álló készlet dtu-k beállításait, tárhelykorlátait, és az adatbázis-beállításokként. Annak érdekében, hogy csökkentse a részletesség, további tárhelyet és az adatbázisonkénti beállítások alapértelmezés szerint rejtettek.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)|Rugalmas készlet frissíti.|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_delete)|A rugalmas készlet törlése.|

## <a name="transact-sql-manage-pooled-databases"></a>A Transact-SQL: Készletezett adatbázisok kezelése

Hozhat létre és adatbázisok áthelyezése a meglévő rugalmas készletekben, vagy a Transact-SQL az SQL Database rugalmas készlet információt ad vissza, használja a következő T-SQL-parancsokat. Ezek a parancsok az Azure portal használatával adhat ki [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), vagy bármely más programot, amely képes csatlakozni egy Azure SQL Database-kiszolgálóhoz, és adja át a Transact-SQL parancsok. Hozzon létre és kezelheti a tűzfalszabályok a T-SQL használatával: [tűzfalszabályok kezelése a Transact-SQL használatával](sql-database-firewall-configure.md#manage-firewall-rules-using-transact-sql).

> [!IMPORTANT]
> Nem létrehozása, frissítése vagy törlése az Azure SQL Database rugalmas készlet Transact-SQL használatával. Hozzáadhat vagy eltávolíthat adatbázisokat rugalmas készletben, és dinamikus felügyeleti nézetek használatával meglévő rugalmas készletek kapcsolatos információkat ad vissza.
>

| Parancs | Leírás |
| --- | --- |
|[ADATBÁZIS (az Azure SQL Database) létrehozása](/sql/t-sql/statements/create-database-azure-sql-database)|Létrehoz egy új adatbázist egy meglévő készlet vagy önálló adatbázisként. Új adatbázis létrehozásához a master adatbázishoz internetkapcsolatra.|
| [Az ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Vagy helyez át egy adatbázisban, összesen:, rugalmas készletek között.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Töröl egy adatbázist.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Egy logikai kiszolgáló erőforrás-használati statisztikáit minden rugalmas adatbáziskészletek adja vissza. Az egyes rugalmas adatbáziskészletet, egy sor van jelentéskészítési (négy sorok száma percenként) ablakban 15 másodpercenként. A készletben található összes adatbázis által Ebbe beletartoznak CPU, i/o, Log, tárhelyhasználat és egyidejű kérelem/munkamenet kihasználtságát.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Adja vissza az edition (szolgáltatási réteg), a szolgáltatási cél (tarifacsomag) és a rugalmas készlet nevét, egy Azure SQL database vagy az Azure SQL Data Warehouse esetében. Ha be van jelentkezve a master adatbázishoz az Azure SQL Database kiszolgáló, információkat az összes adatbázis adja vissza. Az Azure SQL Data Warehouse akkor kapcsolódnia kell a master adatbázisban.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>REST API-val: A rugalmas készletek és a készletezett adatbázisok kezelése

Hozhat létre és kezelheti az SQL Database rugalmas készletek és a készletezett adatbázisok, használja ezeket a REST API-kéréseket.

| Parancs | Leírás |
| --- | --- |
|[Rugalmas készletek – létrehozása vagy frissítése](/rest/api/sql/elasticpools/createorupdate)|Új rugalmas készlet létrehozása vagy meglévő rugalmas készlet frissítése.|
|[Rugalmas készletek – törlés](/rest/api/sql/elasticpools/delete)|A rugalmas készlet törlése.|
|[Rugalmas készletek – Get](/rest/api/sql/elasticpools/get)|Rugalmas készlet beolvasása.|
|[Rugalmas készletek – kiszolgáló listája](/rest/api/sql/elasticpools/listbyserver)|Rugalmas készletek listáját adja vissza a kiszolgálón.|
|[Rugalmas készletek – frissítés](/rest/api/sql/elasticpools/update)|Frissíti egy meglévő rugalmas készlet.|
|[Ajánlott a rugalmas készletek – Get](/rest/api/sql/recommendedelasticpools/get)|Lekérdezi egy ajánlott rugalmas készletet.|
|[Ajánlott a rugalmas készletek – kiszolgáló listája](/rest/api/sql/recommendedelasticpools/listbyserver)|A javasolt rugalmas készletek értéket ad vissza.|
|[Ajánlott a rugalmas készletek – mérőszámok listája](/rest/api/sql/recommendedelasticpools/listmetrics)|Vissza a rugalmas készletekkel kapcsolatos metrikák ajánlott.|
|[Rugalmas készlet, tevékenységek](/rest/api/sql/elasticpoolactivities)|Rugalmas készlet, tevékenységek adja vissza.|
|[Rugalmas készlet adatbázis-tevékenységek](/rest/api/sql/elasticpooldatabaseactivities)|Az adatbázisok rugalmas készlet belül adja vissza a tevékenység.|
|[-Adatbázis létrehozása vagy frissítése](/rest/api/sql/databases/createorupdate)|Új adatbázis létrehozása vagy frissítése egy meglévő adatbázist.|
|[Adatbázisok – Get](/rest/api/sql/databases/get)|Egy adatbázis beolvasása.|
|[Rugalmas készlet adatbázisok – lekéréséhez](/rest/api/sql/databases/getbyelasticpool)|Lekéri egy adatbázis egy rugalmas készlet belül.|
|[Adatbázisok – ajánlott a rugalmas készlet lekéréséhez](/rest/api/sql/databases/getbyrecommendedelasticpool)|Lekéri egy adatbázis ajánlott a rugalmas készlet belül.|
|[Adatbázis - listát a rugalmas készlet](/rest/api/sql/databases/listbyelasticpool)|Rugalmas készletben található adatbázisok listáját adja vissza.|
|[Adatbázis - lista alapján ajánlott a rugalmas készlet](/rest/api/sql/databases/listbyrecommendedelasticpool)|Egy ajánlott rugalmas készletben található adatbázisok listáját adja vissza.|
|[Adatbázis - kiszolgáló által lista](/rest/api/sql/databases/listbyserver)|A kiszolgálón az adatbázisok listáját adja vissza.|
|[Adatbázis - frissítés](/rest/api/sql/databases/update)|Frissíti egy meglévő adatbázist.|

## <a name="next-steps"></a>További lépések

* Egy videót [Microsoft Virtual Academy videotanfolyam az Azure SQL Database rugalmas funkcióiról](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt.
* Rugalmas készleteket használó SaaS oktatóanyagért lásd: [– a Wingtip SaaS-alkalmazás bevezetés](sql-database-wtp-overview.md).
