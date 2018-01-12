---
title: "PowerShell-példa - frissítés SQL adatszinkronizálás szinkronizálási séma |} Microsoft Docs"
description: "Az Azure PowerShell példaként megadott parancsfájlt a sync-séma frissítésére az SQL-adatok szinkronizálása"
services: sql-database
documentationcenter: sql-database
author: jognanay
manager: craigg
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: load & move data, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 01/10/2018
ms.author: jognanay
ms.reviewer: douglasl
ms.openlocfilehash: 66bf084f585b86979e6521321daf466c571de10c
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>A szinkronizálási sémát a meglévő szinkronizálási csoport frissítése a PowerShell használatával

A PowerShell-példakód frissíti a meglévő szinkronizálási csoport szinkronizálási séma. Ha több tábla szinkronizálás ezt a parancsfájlt segítségével hatékonyan a sync-séma frissítésére.

Ez a példa bemutatja, hogy a **UpdateSyncSchema** parancsfájl, amely elérhető a Githubon, [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

SQL adatszinkronizálás áttekintését lásd: [adatok szinkronizálásának több felhőalapú és helyszíni adatbázisokat az Azure SQL adatszinkronizálás (előzetes verzió)](../sql-database-sync-data.md).
## <a name="prerequisites"></a>Előfeltételek

Ez a minta az Azure PowerShell 4.2 vagy újabb verziója szükséges. Futtatás `Get-Module -ListAvailable AzureRM` telepített verziója található. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) ismertető cikket.
 
Futtatás `Login-AzureRmAccount` kapcsolat létrehozása az Azure-ral.

## <a name="examples"></a>Példák

### <a name="example-1---add-all-tables-to-the-sync-schema"></a>1 – példa a szinkronizálási sémát minden olyan táblát hozzáadása

Az alábbi példa frissíti az adatbázis-séma, és minden érvényes tábla hozzáadja a központ adatbázisban a szinkronizálási sémát.

```powershell
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="example-2---add-and-remove-tables-and-columns"></a>2 – példa hozzáadása és eltávolítása a táblákat és oszlopokat

A következő példakóddal felveheti `[dbo].[Table1]` és `[dbo].[Table2].[Column1]` a szinkronizálási sémát, és eltávolítja `[dbo].[Table3]`.

```powershell
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Parancsfájl-paraméterek

A **UpdateSyncSchema** parancsfájlt az alábbi paraméterekkel rendelkezik:

| Paraméter | Megjegyzések |
|---|---|
| $SubscriptionId | Az előfizetés, ahol a szinkronizálási csoport jön létre. |
| $ResourceGroupName | Az erőforráscsoport, ahol a szinkronizálási csoport jön létre.|
| $ServerName | A központ adatbázis kiszolgáló nevét.|
| $DatabaseName | A központ adatbázis nevét. |
| $SyncGroupName | A szinkronizálás csoport nevét. |
| $MemberName | Adja meg a tag nevét, ha azt szeretné, hogy az adatbázis-séma betöltése a szinkronizálási tagból ahelyett, hogy a hub-adatbázisból. Ha azt szeretné, az adatbázis-séma betöltése a központi, hagyja üresen ezt a paramétert. |
| $TimeoutInSeconds | A parancsfájl frissíti az adatbázis-séma időtúllépés történt. Alapértelmezett érték a 900 másodperc. |
| $RefreshDatabaseSchema | Adja meg, hogy a parancsfájl az adatbázisséma frissíteni kell-e. Ha az adatbázis-séma megváltozott a korábbi konfiguráció – például ha hozzáadott új tábla vagy annál oszlop), a séma frissítése előtt konfigurálja újra kell. Alapértelmezett értéke false. |
| $AddAllTables | Ha az értéke igaz, az összes érvényes táblákat és oszlopokat kerülnek a szinkronizálási sémát. A $TablesAndColumnsToAdd és $TablesAndColumnsToRemove figyelmen kívül lesznek hagyva. |
| $TablesAndColumnsToAdd | Adja meg a táblák vagy a szinkronizálási séma hozzáadni kívánt oszlopokat. Meg kell minden tábla vagy oszlop nevét kell teljes tagolt séma nevű. Például: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Több tábla vagy oszlop neve megadott, és vesszővel (,) elválasztva. |
| $TablesAndColumnsToRemove | Adja meg a táblák vagy a szinkronizálási séma eltávolítani kívánt oszlopokat. Meg kell minden tábla vagy oszlop nevét kell teljes tagolt séma nevű. Például: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Több tábla vagy oszlop neve megadott, és vesszővel (,) elválasztva. |
|||

## <a name="script-explanation"></a>Parancsfájl ismertetése

A **UpdateSyncSchema** parancsfájl használja a következő parancsokat. Minden egyes parancsa a tábla-parancs-specifikus dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureRmSqlSyncGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncgroup) | A szinkronizálás a csoportról vonatkozó adatokkal tér vissza. |
| [Frissítés-AzureRmSqlSyncGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/update-azurermsqlsyncgroup) | Frissíti a szinkronizálású csoport. |
| [Get-AzureRmSqlSyncMember](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncmember) | Szinkronizálási tagja kapcsolatos vonatkozó adatokkal tér vissza. |
| [Get-AzureRmSqlSyncSchema](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncschema) | A szinkronizálási séma kapcsolatos vonatkozó adatokkal tér vissza. |
| [Frissítés-AzureRmSqlSyncSchema](https://docs.microsoft.com/powershell/module/azurerm.sql/update-azurermsqlsyncschema) | Szoftverfrissítések szinkronizálása a séma. |
|||

## <a name="next-steps"></a>További lépések

Azure PowerShell kapcsolatos további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

További SQL Database PowerShell parancsfájl minták található [Azure SQL Database PowerShell-parancsfájlok](../sql-database-powershell-samples.md).

SQL adatszinkronizálás kapcsolatos további információkért lásd:

-   [Szinkronizálja az adatokat több felhőalapú és helyszíni adatbázisokat az Azure SQL adatszinkronizálás](../sql-database-sync-data.md)
-   [Azure SQL Data szinkronizálás beállítása](../sql-database-get-started-sql-data-sync.md)
-   [Ajánlott eljárások az Azure SQL-adatok szinkronizálása](../sql-database-best-practices-data-sync.md)
-   [A figyelő az Azure SQL adatszinkronizálás az OMS szolgáltatáshoz](../sql-database-sync-monitor-oms.md)
-   [Az Azure SQL adatszinkronizálás problémák elhárítása](../sql-database-troubleshoot-data-sync.md)

-   PowerShell-példák bemutatják, hogyan konfigurálja az SQL adatszinkronizálás befejezése:
    -   [A PowerShell szolgáltatás használatával több Azure SQL-adatbázisok közötti szinkronizálása](sql-database-sync-data-between-sql-databases.md)
    -   [Egy Azure SQL-adatbázis és a helyszíni SQL Server-adatbázisok közötti szinkronizálása a PowerShell használatával](sql-database-sync-data-between-azure-onprem.md)

-   [Töltse le az SQL Data szinkronizálási REST API dokumentációja](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

SQL-adatbázis kapcsolatos további információkért lásd:

-   [SQL-adatbázis – áttekintés](../sql-database-technical-overview.md)
-   [Adatbázis életciklusának kezelésére](https://msdn.microsoft.com/library/jj907294.aspx)
