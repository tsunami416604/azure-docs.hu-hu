---
title: PowerShell example - Update SQL Data Sync sync schema
description: Azure PowerShell-példaszkript az SQL Data Sync szinkronizálási sémájának frissítéséhez
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 0106b80259083c6e5e3e527063a18aae2e7c6cee
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74421605"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>PowerShell használata meglévő szinkronizálási csoport szinkronizálási sémájának frissítéséhez

Ez a PowerShell-példa egy SQL Data Syncbeli szinkronizálási csoport szinkronizálási sémáját frissíti. Ha több táblát szinkronizál, ez a szkript segít Önnek a szinkronizálási séma hatékony frissítésében. Ez a példa a bemutatja az **UpdateSyncSchema** szkript használatát, amely [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1) néven érhető el a GitHubon.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

If you choose to install and use the PowerShell locally, this tutorial requires AZ PowerShell 1.4.0 or later. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

Az SQL Data Sync áttekintéséhez tekintse meg a [több felhőalapú és helyszíni adatbázis közötti, az Azure SQL Data Sync segítségével végzett adatszinkronizálást](../sql-database-sync-data.md) ismertető cikket.

> [!IMPORTANT]
> Azure SQL Data Sync does not support Azure SQL database managed instance at this time.

## <a name="examples"></a>Példák

### <a name="add-all-tables-to-the-sync-schema"></a>Add all tables to the sync schema

Az alábbi példa frissíti az adatbázissémát, és a központi adatbázis összes érvényes tábláját hozzáadja a szinkronizálási sémához.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="add-and-remove-tables-and-columns"></a>Add and remove tables and columns

Az alábbi példa hozzáadja a szinkronizálási sémához a `[dbo].[Table1]` és a `[dbo].[Table2].[Column1]` elemet, valamint eltávolítja a `[dbo].[Table3]` elemet.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Szkriptparaméterek

Az **UpdateSyncSchema** szkript a következő paramétereket tartalmazza:

| Paraméter | Megjegyzések |
|---|---|
| $subscriptionId | Az előfizetés, amelyben a szinkronizálási csoport létrejön. |
| $resourceGroupName | Az erőforráscsoport, amelyben a szinkronizálási csoport létrejön.|
| $serverName | A központi adatbázis kiszolgálóneve.|
| $databaseName | A központi adatbázis neve. |
| $syncGroupName | A szinkronizálási csoport neve. |
| $memberName | Akkor adja meg a tag nevét, ha a központi adatbázis helyett a szinkronizálási tagból kívánja betölteni az adatbázissémát. Ha a központi adatbázisból kívánja betölteni az adatbázissémát, akkor hagyja üresen ezt a paramétert. |
| $timeoutInSeconds | Az időtúllépési érték, amelyet elérve a szkript frissíti az adatbázissémát. Az alapértelmezett érték 900 másodperc. |
| $refreshDatabaseSchema | Meghatározza, hogy a szkriptnek frissítenie kell-e az adatbázissémát. Ha az adatbázisséma megváltozott a korábbi konfigurációhoz képest – például ha új táblát vagy új oszlopot adott hozzá – akkor frissítenie kell a sémát, mielőtt újrakonfigurálja. Az alapértelmezett érték a false (hamis). |
| $addAllTables | Ha az érték true (igaz), akkor az összes érvényes tábla és oszlop hozzáadódik a szinkronizálási sémához. A $TablesAndColumnsToAdd és a $TablesAndColumnsToRemove értékeit a rendszer figyelmen kívül hagyja. |
| $tablesAndColumnsToAdd | Meghatározza a szinkronizálási sémához hozzáadandó táblákat vagy oszlopokat. Minden tábla- vagy oszlopnevet tagolni kell a séma nevével. Például: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Több tábla- vagy oszlopnév is megadható, ezeket vesszővel (,) kell egymástól elválasztani. |
| $tablesAndColumnsToRemove | Meghatározza a szinkronizálási sémából eltávolítandó táblákat vagy oszlopokat. Minden tábla- vagy oszlopnevet tagolni kell a séma nevével. Például: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Több tábla- vagy oszlopnév is megadható, ezeket vesszővel (,) kell egymástól elválasztani. |

## <a name="script-explanation"></a>Szkript ismertetése

Az **UpdateSyncSchema** szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncgroup) | Egy szinkronizálási csoportra vonatkozó adatokat ad vissza. |
| [Update-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncgroup) | Frissít egy szinkronizálási csoportot. |
| [Get-AzSqlSyncMember](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncmember) | Egy szinkronizálási tagra vonatkozó adatokat ad vissza. |
| [Get-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncschema) | Egy szinkronizálási sémára vonatkozó adatokat ad vissza. |
| [Update-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncschema) | Frissít egy szinkronizálási sémát. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről [az Azure PowerShell dokumentációjában](/powershell/azure/overview) talál további információt.

Additional SQL database PowerShell script samples can be found in [Azure SQL database PowerShell scripts](../sql-database-powershell-samples.md).

További információ az SQL Data Syncről:

- Overview - [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync](../sql-database-sync-data.md)
- Set up Data Sync
    - In the portal - [Tutorial: Set up SQL Data Sync to sync data between Azure SQL Database and SQL Server on-premises](../sql-database-get-started-sql-data-sync.md)
    - A PowerShell-lel
        - [A PowerShell használata több Azure SQL-adatbázis közötti szinkronizáláshoz](sql-database-sync-data-between-sql-databases.md)
        - [A PowerShell használata egy Azure SQL-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz](sql-database-sync-data-between-azure-onprem.md)
- Data Sync Agent - [Data Sync Agent for Azure SQL Data Sync](../sql-database-data-sync-agent.md)
- Best practices - [Best practices for Azure SQL Data Sync](../sql-database-best-practices-data-sync.md)
- Monitor - [Monitor SQL Data Sync with Azure Monitor logs](../sql-database-sync-monitor-oms.md)
- Troubleshoot - [Troubleshoot issues with Azure SQL Data Sync](../sql-database-troubleshoot-data-sync.md)
- Update the sync schema
    - With Transact-SQL - [Automate the replication of schema changes in Azure SQL Data Sync](../sql-database-update-sync-schema.md)

További információ az SQL Database-ről:

- [Az SQL Database áttekintése](../sql-database-technical-overview.md)
- [Az adatbázis életciklusának felügyelete](https://msdn.microsoft.com/library/jj907294.aspx)
