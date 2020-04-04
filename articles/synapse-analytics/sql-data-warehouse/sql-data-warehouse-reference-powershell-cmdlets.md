---
title: A PowerShell & REST API-k
description: Keresse meg az Azure Synapse Analytics SQL-készletének felső PowerShell-parancsmagjait, beleértve az adatbázisok szüneteltetésének és folytatásának módját is.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 644ad931879c1953560abf13ac0eda58f3d814d8
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633062"
---
# <a name="powershell--rest-apis-for-azure-synapse-analytics-sql-pool"></a>A PowerShell & AZ Azure Synapse Analytics SQL-készletrest-API-kat

Számos Azure Synapse Analytics SQL-készlet felügyeleti feladatok azure PowerShell-parancsmagok vagy REST API-k használatával kezelhetők.  Az alábbiakban néhány példa, hogyan powershell-parancsokkal automatizálhatja a gyakori feladatokat az SQL-készletben.  Néhány jó REST-példát a [Méretezhetőség kezelése REST-el](sql-data-warehouse-manage-compute-rest-api.md)című témakörben talál.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Első lépések az Azure PowerShell-parancsmagok használatával

1. Nyissa meg a Windows PowerShellt.
2. A PowerShell-parancssorban futtassa ezeket a parancsokat az Azure Resource Managerbe való bejelentkezéshez, és válassza ki az előfizetést.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>Példa az adatraktár szüneteltetése

"Server01" nevű kiszolgálón található "Database02" nevű adatbázis szüneteltetése.  A kiszolgáló egy "ResourceGroup1" nevű Azure-erőforráscsoportban található.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

Ez a példa a beolvasott objektumot [Suspend-AzSqlDatabase -re adja](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)le.  Ennek eredményeképpen az adatbázis szünetel. A végső parancs az eredményeket mutatja.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>Például az adattárház indítása

A "Server01" nevű kiszolgálón található "Database02" nevű adatbázis folytatásának folytatása. A kiszolgáló egy "ResourceGroup1" nevű erőforráscsoportban található.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Ez a példa egy "Database02" nevű adatbázist olvas be egy "Server01" nevű kiszolgálóról, amely egy "ResourceGroup1" nevű erőforráscsoportban található. A beolvasott objektumot [a Resume-AzSqlDatabase elemre adja.](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Vegye figyelembe, hogy ha a kiszolgáló foo.database.windows.net, használja a "foo" a PowerShell-parancsmagok -ServerName.

## <a name="other-supported-powershell-cmdlets"></a>Egyéb támogatott PowerShell-parancsmagok

Ezek a PowerShell-parancsmagok az Azure Synapse Analytics adattárház támogatja.

* [Get-AzSqlDatabase adatbázis](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSQLDatabaseRestorePoint](/powershell/module/az.sql/get-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Új-AzsqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Restore-AzSqlDatabase]/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Folytatás-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Set-AzSqlDatabase Adatbázis](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Felfüggesztés-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="next-steps"></a>További lépések

További PowerShell-példák:

* [Adattárház létrehozása a PowerShell használatával](create-data-warehouse-powershell.md)
* [Adatbázis-visszaállítás](sql-data-warehouse-restore-points.md)

A PowerShell használatával automatizálható egyéb feladatokról lásd: [Azure SQL Database-parancsmagok]/powershell/module/az.sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Nem minden Azure SQL Database-parancsmag támogatott az Azure Synapse Analytics adattárházban. A REST szolgáltatással automatizálható feladatok listáját az Operations for Azure SQL Database című [témakörben tetszhet.](/rest/api/sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
