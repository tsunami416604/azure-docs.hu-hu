---
title: PowerShell-parancsmagok az Azure SQL Data warehouse-hoz
description: Az első PowerShell-parancsmagok az Azure SQL Data Warehouse például szüneteltetéséről és folytatásáról adatbázis található.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: ec1f9a8a10f346c4b8e9e8dac364b2687637c204
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317900"
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>PowerShell-parancsmagok és a REST API-k, az SQL Data warehouse-hoz
Az SQL Data Warehouse számos feladat az Azure PowerShell-parancsmagok vagy a REST API-k használatával kezelhetők.  Az alábbiakban néhány példa bemutatja, hogyan automatizálhatja a gyakori feladatokat az SQL Data Warehouse a PowerShell-parancsok használatával.  Néhány hasznos REST példák: a cikk [kezelése a REST-tel méretezhetőség][Manage scalability with REST].

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Azure PowerShell-parancsmagok használatának első lépései
1. Nyissa meg a Windows PowerShellt.
2. A PowerShell-parancssorba futtassa ezeket a parancsokat, az Azure Resource Managerrel való bejelentkezéshez, és válassza ki az előfizetését.
   
    ```PowerShell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Szüneteltetési SQL Data Warehouse példa
A "Kiszolgalo01." nevű kiszolgáló által üzemeltetett "Database02" nevű adatbázis szüneteltetése  A kiszolgáló része egy Azure-erőforráscsoportot "ResourceGroup1."

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Egy variációjával ebben a példában átadja a lekért objektum [Suspend-AzSqlDatabase][Suspend-AzSqlDatabase].  Ennek eredményeképpen az adatbázis fel van függesztve. A végső parancs megjeleníti az eredményeket.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>Indítsa el az SQL Data Warehouse példa
"Kiszolgalo01." nevű kiszolgálón található "Database02" nevű adatbázis működésének folytatása A kiszolgáló szerepel egy "ResourceGroup1." nevű erőforráscsoportot

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Egy variációjával ebben a példában a kiszolgáló neve "Kiszolgalo01", "ResourceGroup1." nevű erőforráscsoportban található "Database02" nevű adatbázis kéri le Azt a lekért objektum csövek [Resume-AzSqlDatabase][Resume-AzSqlDatabase].

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Megjegyzés: Ha a kiszolgáló valami.Database.Windows.NET, a PowerShell-parancsmagok – kiszolgálónév használni "foo".
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Egyéb támogatott PowerShell-parancsmagok
Ezek a PowerShell-parancsmagok az Azure SQL Data Warehouse használata támogatott.

* [Get-AzSqlDatabase][Get-AzSqlDatabase]
* [Get-AzSqlDeletedDatabaseBackup][Get-AzSqlDeletedDatabaseBackup]
* [Get-AzSqlDatabaseRestorePoints][Get-AzSqlDatabaseRestorePoints]
* [New-AzSqlDatabase][New-AzSqlDatabase]
* [Remove-AzSqlDatabase][Remove-AzSqlDatabase]
* [Restore-AzSqlDatabase][Restore-AzSqlDatabase]
* [Resume-AzSqlDatabase][Resume-AzSqlDatabase]
* [Select-AzSubscription][Select-AzSubscription]
* [Set-AzSqlDatabase][Set-AzSqlDatabase]
* [Suspend-AzSqlDatabase][Suspend-AzSqlDatabase]

## <a name="next-steps"></a>További lépések
PowerShell kapcsolatos további példákért lásd:

* [PowerShell-lel az SQL Data Warehouse létrehozása][Create a SQL Data Warehouse using PowerShell]
* [Adatbázis visszaállítása][Database restore]

Egyéb feladatok, amelyek a PowerShell használatával automatizálható, lásd: [Azure SQL Database-parancsmagok][Azure SQL Database Cmdlets]. Ne feledje, hogy nem minden Azure SQL Database-parancsmagok az Azure SQL Data Warehouse is támogatott.  A REST segítségével automatizálható feladatok listáját lásd: [műveleteket az Azure SQL Database][Operations for Azure SQL Database].

<!--Image references-->

<!--Article references-->
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Create a SQL Data Warehouse using PowerShell]: ./create-data-warehouse-powershell.md
[Database restore]: ./sql-data-warehouse-restore-database-powershell.md
[Manage scalability with REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.sql
[Operations for Azure SQL Database]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/get-Azsqldatabase
[Get-AzSqlDeletedDatabaseBackup]: https://docs.microsoft.com/powershell/module/azurerm.sql/get-AzSqlDeletedDatabaseBackup
[Get-AzSqlDatabaseRestorePoints]: https://docs.microsoft.com/powershell/module/azurerm.sql/get-AzSqlDatabaseRestorePoints
[New-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/New-AzSqlDatabase
[Remove-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Remove-AzSqlDatabase
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Restore-AzSqlDatabase
[Resume-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Resume-AzSqlDatabase
<!-- It appears that Select-AzSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Set-AzSqlDatabase
[Suspend-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Suspend-AzSqlDatabase

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
