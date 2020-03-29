---
title: Rugalmas készletek kezelése
description: Azure SQL rugalmas készletek létrehozása és kezelése.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: d8dde76753e58c713763c16230e5461fef43be88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067337"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Rugalmas készletek kezelése az Azure SQL Database-ben

Egy rugalmas készlet, meghatározza az erőforrások mennyiségét, hogy a rugalmas készlet az adatbázisok munkaterhelésének kezeléséhez szükséges, és az egyes készletezett adatbázisok erőforrások mennyiségét.

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Azure Portal: Rugalmas készletek és készletes adatbázisok kezelése

Az összes készletbeállítás egy helyen található: a **Készlet konfigurálása** panel. Ha ide szeretne jutni, keressen egy rugalmas készletet a portálon, és kattintson a **Készlet konfigurálása** elemre a panel tetejéről vagy a bal oldali erőforrás menüből.

Itt a következő módosítások tetszőleges kombinációját elláthatja, és mindet egy kötegbe mentheti:

1. A készlet szolgáltatási szintjének módosítása
2. A teljesítmény (DTU vagy virtuális magok) és a tárolás méretezése fel- vagy leskálán
3. Adatbázisok hozzáadása vagy eltávolítása a készlethez vagy akészletből
4. Állítsa be a készletben lévő adatbázisok ra vonatkozó min (garantált) és maximális teljesítménykorlátot
5. Tekintse át a költségösszegzést, és tekintse meg a számla új beállítások ból eredő módosításait

![Rugalmas készletkonfigurációs panel](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>PowerShell: Rugalmas készletek és készletes adatbázisok kezelése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Ezekről a parancsmagokról az [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)című témakörben található. Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak.

Sql Database rugalmas készletek és az Azure PowerShell készletezésű adatbázisok létrehozásához és kezeléséhez használja a következő PowerShell-parancsmagokat. Ha telepítenie vagy frissítenie kell a PowerShellt, olvassa [el az Azure PowerShell-modul telepítése című témakört.](/powershell/azure/install-az-ps) Rugalmas készlet SQL adatbázis-kiszolgálóinak létrehozásáról és kezeléséről az [SQL Database-kiszolgálók létrehozása és kezelése](sql-database-servers.md)című témakörben található. Tűzfalszabályok létrehozásáról és kezeléséről a [Tűzfalszabályok létrehozása és kezelése a PowerShell használatával című témakörben található.](sql-database-firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules)

> [!TIP]
> A PowerShell-példaparancsfájlok esetében olvassa el a [Rugalmas készletek létrehozása és adatbázisok áthelyezése készletek között és a készletből a PowerShell használatával,](scripts/sql-database-move-database-between-pools-powershell.md) a [PowerShell használata az SQL rugalmas készlet figyeléséhez és méretezéséhez az Azure SQL Database-ben című témakört.](scripts/sql-database-monitor-and-scale-pool-powershell.md)
>

| Parancsmag | Leírás |
| --- | --- |
|[Új-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Rugalmas készletet hoz létre.|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Rugalmas készletek et és azok tulajdonságértékeit kapja meg.|
|[Set-AzSqlelasticpool](/powershell/module/az.sql/set-azsqlelasticpool)|Rugalmas készlet tulajdonságait módosítja Például a **StorageMB** tulajdonsággal módosíthatja a rugalmas készlet maximális tárolóját.|
|[Eltávolítás-AzSqlelasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|Rugalmas készlet törlése.|
|[Get-AzSqlelasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Leveszi a műveletek állapotát egy rugalmas készleten|
|[Új-AzsqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Új adatbázist hoz létre egy meglévő készletben vagy egyetlen adatbázisként. |
|[Get-AzSqlDatabase adatbázis](/powershell/module/az.sql/get-azsqldatabase)|Egy vagy több adatbázist kér le.|
|[Set-AzSqlDatabase Adatbázis](/powershell/module/az.sql/set-azsqldatabase)|Beállítja az adatbázis tulajdonságait, vagy áthelyez egy meglévő adatbázist a rugalmas készletekbe, kivagy a rugalmas készletek közé.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Adatbázis eltávolítása.|

> [!TIP]
> Számos adatbázis létrehozása egy rugalmas készletben időt vehet igénybe, ha a portál vagy a PowerShell-parancsmagok használatával történik, amelyek egyszerre csak egyetlen adatbázist hoznak létre. A létrehozás rugalmas készletté történő automatizálásához [lásd: CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Azure CLI: Rugalmas készletek és készletezésű adatbázisok kezelése

Sql Database rugalmas készletek létrehozása és kezelése az [Azure CLI](/cli/azure)használatával, használja a következő [Azure CLI SQL](/cli/azure/sql/db) Database-parancsokat. A [Cloud Shell-lel](/azure/cloud-shell/overview) futtassa a parancssori felületet a böngészőben, vagy [telepítse](/cli/azure/install-azure-cli) macOS, Linux, illetve Windows rendszeren.

> [!TIP]
> Az Azure CLI példaparancsfájljairól a [CLI használata SQL-adatbázis sql rugalmas készletbe való áthelyezéséhez](scripts/sql-database-move-database-between-pools-cli.md) és [az Azure CLI használatával az Azure SQL Database sql rugalmas készletének méretezéséhez című témakörben található.](scripts/sql-database-scale-pool-cli.md)
>

| Parancsmag | Leírás |
| --- | --- |
|[az sql elastic-pool létrehozása](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Rugalmas készletet hoz létre.|
|[az sql elastic-pool lista](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|A kiszolgáló rugalmas készleteinek listáját adja vissza.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Egy rugalmas készletben lévő adatbázisok listáját adja vissza.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|A rendelkezésre álló készlet DTU-beállításait, a tárolási korlátokat és az adatbázis-beállításokat is tartalmazza. A részletesség csökkentése érdekében alapértelmezés szerint további tárhelykorlátok és adatbázis-beállítások vannak elrejtve.|
|[az sql elastic-pool frissítése](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Egy rugalmas készlet frissítése.|
|[az sql elastic-pool törlése](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Törli a rugalmas készletet.|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL: Készletezésű adatbázisok kezelése

Ha adatbázisokat szeretne létrehozni és áthelyezni a meglévő rugalmas készleteken belül, vagy információt szeretne visszaadni egy SQL Database rugalmas készletről a Transact-SQL-rel, használja a következő T-SQL parancsokat. Ezeket a parancsokat az Azure Portal, az [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), a Visual Studio [Code](https://code.visualstudio.com/docs)vagy bármely más olyan program segítségével adhatja ki, amely csatlakozhat egy Azure SQL Database-kiszolgálóhoz, és átadhatja a Transact-SQL parancsokat. Ha tűzfalszabályokat szeretne létrehozni és kezelni a T-SQL használatával, olvassa el [a Tűzfalszabályok kezelése a Transact-SQL használatával című témakört.](sql-database-firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules)

> [!IMPORTANT]
> Az Azure SQL Database rugalmas készlete nem hozható létre, nem frissíthető és nem törölhető a Transact-SQL használatával. Adatbázisok at adhat hozzá vagy távolíthat el egy rugalmas készletből, és a dmv-k segítségével a meglévő rugalmas készletek adatait adhat vissza.
>

| Parancs | Leírás |
| --- | --- |
|[CREATE ADATBÁZIS (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Új adatbázist hoz létre egy meglévő készletben vagy egyetlen adatbázisként. Új adatbázis létrehozásához kapcsolódnia kell a fő adatbázishoz.|
| [ALTER ADATBÁZIS (Azure SQL-adatbázis)](/sql/t-sql/statements/alter-database-azure-sql-database) |Adatbázis áthelyezése rugalmas készletekbe, kifelé vagy között.|
|[DROP ADATBÁZIS (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Adatbázis törlése.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Erőforrás-használati statisztikát ad vissza az SQL Database-kiszolgáló összes rugalmas készletéhez. Minden rugalmas készlethez minden 15 másodperces jelentési ablakhoz egy sor tartozik (négy sor percenként). Ez magában foglalja a CPU, Io, Napló, tárolási felhasználás és egyidejű kérelem/munkamenet kihasználtsága a készlet összes adatbázisa.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Egy Azure SQL-adatbázis vagy egy Azure SQL Data Warehouse kiadását (szolgáltatási szint), szolgáltatási célt (tarifacsomag) és rugalmas készletnevét adja vissza. Ha bejelentkezett a fő adatbázis egy Azure SQL Database-kiszolgáló, az összes adatbázis adatait adja vissza. Az Azure SQL Data Warehouse, a fő adatbázishoz kell csatlakoztatni.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>REST API: Rugalmas készletek és készletezésű adatbázisok kezelése

Sql Database rugalmas készletek és készletező adatbázisok létrehozásához és kezeléséhez használja ezeket a REST API-kérelmeket.

| Parancs | Leírás |
| --- | --- |
|[Rugalmas készletek – Létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/sql/elasticpools/createorupdate)|Új rugalmas készletet hoz létre, vagy frissít egy meglévő rugalmas készletet.|
|[Elasztikus készletek - Törlés](https://docs.microsoft.com/rest/api/sql/elasticpools/delete)|Törli a rugalmas készletet.|
|[Elasztikus medencék - Get](https://docs.microsoft.com/rest/api/sql/elasticpools/get)|Kap egy rugalmas medencét.|
|[Rugalmas készletek - Lista kiszolgáló szerint](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|A kiszolgáló rugalmas készleteinek listáját adja vissza.|
|[Rugalmas készletek - Frissítés](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Egy meglévő rugalmas készlet frissítése.|
|[Rugalmas készlettevékenységek](https://docs.microsoft.com/rest/api/sql/elasticpoolactivities)|Rugalmas készlettevékenységeket ad vissza.|
|[Rugalmas készletadatbázis-tevékenységek](https://docs.microsoft.com/rest/api/sql/elasticpooldatabaseactivities)|A rugalmas készleten belüli adatbázisokon lévő tevékenységet adja vissza.|
|[Adatbázisok – Létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Új adatbázist hoz létre, vagy egy meglévőadatbázist frissít.|
|[Adatbázisok - Beszerezés](https://docs.microsoft.com/rest/api/sql/databases/get)|Beszerzi az adatbázist.|
|[Adatbázisok – Lista rugalmas készlet szerint](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Egy rugalmas készletben lévő adatbázisok listáját adja vissza.|
|[Adatbázisok – Lista kiszolgáló szerint](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|A kiszolgáló adatbázisainak listáját adja vissza.|
|[Adatbázisok - Frissítés](https://docs.microsoft.com/rest/api/sql/databases/update)|Meglévő adatbázis frissítése.|

## <a name="next-steps"></a>További lépések

* A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt.
* A rugalmas készleteket használó SaaS-oktatóanyagról [a Bevezetés a Wingtip SaaS alkalmazás bemutatása című témakörben](sql-database-wtp-overview.md)található.
