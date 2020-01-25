---
title: PowerShell-parancsok
description: Keresse meg a Azure SQL Data Warehouse legfontosabb PowerShell-parancsmagokat, beleértve az adatbázisok szüneteltetését és folytatását.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c5f85f102d72ac2e4a0315109748d48573f49407
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721183"
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>PowerShell-parancsmagok és REST API-k SQL Data Warehouse
Számos SQL Data Warehouse felügyeleti feladat felügyelhető Azure PowerShell parancsmagok vagy REST API-k használatával.  Az alábbiakban néhány példát láthat arra, hogyan használhatja a PowerShell-parancsokat a SQL Data Warehouse gyakori feladatainak automatizálására.  Néhány jó REST-példákért tekintse meg a [méretezhetőség és a REST kezelése](sql-data-warehouse-manage-compute-rest-api.md)című cikket.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Ismerkedés a Azure PowerShell-parancsmagokkal
1. Nyissa meg a Windows PowerShellt.
2. A PowerShell parancssorába futtassa ezeket a parancsokat, hogy bejelentkezzen a Azure Resource Managerba, és válassza ki az előfizetését.
   
    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>SQL Data Warehouse szüneteltetése – példa
Szüneteltesse a "Database02" nevű adatbázist egy "Kiszolgalo01" nevű kiszolgálón.  A kiszolgáló egy "ResourceGroup1" nevű Azure-erőforráscsoport.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Egy változat, ez a példa a beolvasott objektumot [felfüggeszti a AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase).  Ennek eredményeképpen a rendszer szünetelteti az adatbázist. Az utolsó parancs az eredményeket jeleníti meg.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>SQL Data Warehouse-példa indítása
A "Database02" nevű, "Kiszolgalo01" nevű kiszolgálón futtatott adatbázis folytatásának folytatása A kiszolgálót egy "ResourceGroup1" nevű erőforráscsoport tárolja.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Egy változat, ez a példa egy "Database02" nevű adatbázist kér le egy "Kiszolgalo01" nevű kiszolgálóról, amelyet egy "ResourceGroup1" nevű erőforráscsoport tárol. A lekért objektumot a [folytatáshoz AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase).

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Vegye figyelembe, hogy ha a kiszolgáló foo.database.windows.net, a PowerShell-parancsmagokban használja az "ize" kifejezést a-ServerName értékre.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Egyéb támogatott PowerShell-parancsmagok
Ezeket a PowerShell-parancsmagokat a Azure SQL Data Warehouse támogatja.

* [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase)
* [Get-AzSqlDeletedDatabaseBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup)
* [Get-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint)
* [Új – AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
* [Remove-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase)
* [Visszaállítás – AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)
* [Folytatás – AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)
* [Select-AzSubscription](https://msdn.microsoft.com/library/dn722499.aspx)
* [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
* [Felfüggesztés – AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)

## <a name="next-steps"></a>Következő lépések
További PowerShell-példákat a következő témakörben talál:

* [SQL Data Warehouse létrehozása a PowerShell használatával](create-data-warehouse-powershell.md)
* [Adatbázis-visszaállítás](sql-data-warehouse-restore-database-powershell.md)

A PowerShell-lel automatizálható egyéb feladatokért lásd: [Azure SQL Database parancsmagok](https://docs.microsoft.com/powershell/module/az.sql). Nem minden Azure SQL Database parancsmag támogatott a Azure SQL Data Warehouse.  A REST-sel automatizálható feladatok listáját a [Azure SQL Database műveleteit](https://msdn.microsoft.com/library/azure/dn505719.aspx)ismertető témakörben tekintheti meg.
