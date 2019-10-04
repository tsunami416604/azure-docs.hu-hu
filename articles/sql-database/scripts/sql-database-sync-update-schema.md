---
title: PowerShell-példa – Az SQL Data Sync szinkronizálási sémájának frissítése | Microsoft Docs
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
ms.openlocfilehash: 2bf782e1241f4d88beb3d52eefe80511375e800a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569754"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>PowerShell használata meglévő szinkronizálási csoport szinkronizálási sémájának frissítéséhez

Ez a PowerShell-példa egy SQL Data Syncbeli szinkronizálási csoport szinkronizálási sémáját frissíti. Ha több táblát szinkronizál, ez a szkript segít Önnek a szinkronizálási séma hatékony frissítésében. Ez a példa a bemutatja az **UpdateSyncSchema** szkript használatát, amely [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1) néven érhető el a GitHubon.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az AZ PowerShell 1.4.0 vagy újabb verzió szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

Az SQL Data Sync áttekintéséhez tekintse meg a [több felhőalapú és helyszíni adatbázis közötti, az Azure SQL Data Sync segítségével végzett adatszinkronizálást](../sql-database-sync-data.md) ismertető cikket.

> [!IMPORTANT]
> Az Azure SQL-adatszinkronizálás jelenleg **nem** támogatja Azure SQL Database felügyelt példányok használatát.

## <a name="sample-script"></a>Példaszkript

### <a name="example-1---add-all-tables-to-the-sync-schema"></a>1\. példa – Az összes tábla hozzáadása a szinkronizálási sémához

Az alábbi példa frissíti az adatbázissémát, és a központi adatbázis összes érvényes tábláját hozzáadja a szinkronizálási sémához.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="example-2---add-and-remove-tables-and-columns"></a>2\. példa – Táblák és oszlopok hozzáadása és eltávolítása

Az alábbi példa hozzáadja a szinkronizálási sémához a `[dbo].[Table1]` és a `[dbo].[Table2].[Column1]` elemet, valamint eltávolítja a `[dbo].[Table3]` elemet.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Szkriptparaméterek

Az **UpdateSyncSchema** szkript a következő paramétereket tartalmazza:

| Paraméter | Megjegyzések |
|---|---|
| $SubscriptionId | Az előfizetés, amelyben a szinkronizálási csoport létrejön. |
| $ResourceGroupName | Az erőforráscsoport, amelyben a szinkronizálási csoport létrejön.|
| $ServerName | A központi adatbázis kiszolgálóneve.|
| $DatabaseName | A központi adatbázis neve. |
| $SyncGroupName | A szinkronizálási csoport neve. |
| $MemberName | Akkor adja meg a tag nevét, ha a központi adatbázis helyett a szinkronizálási tagból kívánja betölteni az adatbázissémát. Ha a központi adatbázisból kívánja betölteni az adatbázissémát, akkor hagyja üresen ezt a paramétert. |
| $TimeoutInSeconds | Az időtúllépési érték, amelyet elérve a szkript frissíti az adatbázissémát. Az alapértelmezett érték 900 másodperc. |
| $RefreshDatabaseSchema | Meghatározza, hogy a szkriptnek frissítenie kell-e az adatbázissémát. Ha az adatbázisséma megváltozott a korábbi konfigurációhoz képest – például ha új táblát vagy új oszlopot adott hozzá – akkor frissítenie kell a sémát, mielőtt újrakonfigurálja. Az alapértelmezett érték a false (hamis). |
| $AddAllTables | Ha az érték true (igaz), akkor az összes érvényes tábla és oszlop hozzáadódik a szinkronizálási sémához. A $TablesAndColumnsToAdd és a $TablesAndColumnsToRemove értékeit a rendszer figyelmen kívül hagyja. |
| $TablesAndColumnsToAdd | Meghatározza a szinkronizálási sémához hozzáadandó táblákat vagy oszlopokat. Minden tábla- vagy oszlopnevet tagolni kell a séma nevével. Például: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Több tábla- vagy oszlopnév is megadható, ezeket vesszővel (,) kell egymástól elválasztani. |
| $TablesAndColumnsToRemove | Meghatározza a szinkronizálási sémából eltávolítandó táblákat vagy oszlopokat. Minden tábla- vagy oszlopnevet tagolni kell a séma nevével. Például: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Több tábla- vagy oszlopnév is megadható, ezeket vesszővel (,) kell egymástól elválasztani. |
|||

## <a name="script-explanation"></a>Szkript ismertetése

Az **UpdateSyncSchema** szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncgroup) | Egy szinkronizálási csoportra vonatkozó adatokat ad vissza. |
| [Update-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncgroup) | Frissít egy szinkronizálási csoportot. |
| [Get-AzSqlSyncMember](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncmember) | Egy szinkronizálási tagra vonatkozó adatokat ad vissza. |
| [Get-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncschema) | Egy szinkronizálási sémára vonatkozó adatokat ad vissza. |
| [Update-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncschema) | Frissít egy szinkronizálási sémát. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről [az Azure PowerShell dokumentációjában](/powershell/azure/overview) talál további információt.

Az [Azure SQL Database PowerShell-szkriptekben](../sql-database-powershell-samples.md) további SQL Database PowerShell-szkriptminták találhatók.

További információ az SQL Data Syncről:

-   Áttekintés – az [adatszinkronizálás több felhőalapú és helyszíni adatbázis között az Azure SQL-adatszinkronizálás](../sql-database-sync-data.md)
-   Adatszinkronizálás beállítása
    - A portálon – [oktatóanyag: Az Azure SQL Database és a helyszíni SQL Server közötti adatszinkronizálás SQL-adatszinkronizálás beállítása](../sql-database-get-started-sql-data-sync.md)
    - A PowerShell-lel
        -  [A PowerShell használata több Azure SQL Database-adatbázis közötti szinkronizáláshoz](sql-database-sync-data-between-sql-databases.md)
        -  [A PowerShell használata egy Azure-beli SQL Database-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz](sql-database-sync-data-between-azure-onprem.md)
-   Adatok szinkronizálása az ügynök - [adatok szinkronizálása az Azure SQL Data Sync ügynök](../sql-database-data-sync-agent.md)
-   Ajánlott eljárások – [ajánlott eljárások az Azure SQL-adatszinkronizálás](../sql-database-best-practices-data-sync.md)
-   Figyelő – [SQL-adatszinkronizálás figyelése Azure monitor naplókkal](../sql-database-sync-monitor-oms.md)
-   Hibaelhárítás – [Az Azure SQL-adatszinkronizálás](../sql-database-troubleshoot-data-sync.md) hibáinak elhárítása
-   A szinkronizálási séma frissítése
    -   A Transact-SQL- [ben – automatizálja a séma változásainak az Azure-ban való replikálását SQL-adatszinkronizálás](../sql-database-update-sync-schema.md)

További információ az SQL Database-ről:

-   [Az SQL Database áttekintése](../sql-database-technical-overview.md)
-   [Az adatbázis életciklusának felügyelete](https://msdn.microsoft.com/library/jj907294.aspx)
