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
ms.openlocfilehash: 5f22de08c4eabd3f9a3d6ee29cad3f0a9e8509e0
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351390"
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
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Egyéb támogatott PowerShell-parancsmagok
Ezek a PowerShell-parancsmagok az Azure Synapse Analytics adattárház támogatja.

* [Get-AzSqlDatabase adatbázis](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase)
* [Get-AzSqlDeletedDatabaseBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup)
* [Get-AzSQLDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint)
* [Új-AzsqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
* [Remove-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase)
* [Visszaállítás-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)
* [Folytatás-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)
* [Select-AzSubscription](https://msdn.microsoft.com/library/dn722499.aspx)
* [Set-AzSqlDatabase Adatbázis](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
* [Felfüggesztés-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)

## <a name="next-steps"></a>További lépések
További PowerShell-példák:

* [Adattárház létrehozása a PowerShell használatával](create-data-warehouse-powershell.md)
* [Adatbázis-visszaállítás](sql-data-warehouse-restore-points.md)

A PowerShell használatával automatizálható egyéb feladatokról az [Azure SQL Database-parancsmagok című témakörben található.](https://docs.microsoft.com/powershell/module/az.sql) Nem minden Azure SQL Database-parancsmag támogatott az Azure Synapse Analytics adattárházban.  A REST szolgáltatással automatizálható feladatok listáját az Operations for Azure SQL Database című [témakörben tetszhet.](/rest/api/sql/)
