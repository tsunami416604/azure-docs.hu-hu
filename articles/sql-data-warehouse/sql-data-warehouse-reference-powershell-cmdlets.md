---
title: PowerShell-parancsmagok az Azure SQL Data warehouse-hoz
description: Az első PowerShell-parancsmagok az Azure SQL Data Warehouse például szüneteltetéséről és folytatásáról adatbázis található.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 222bc8ee15fdc8802dacd5a5b74cfd84961aa397
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43300753"
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>PowerShell-parancsmagok és a REST API-k, az SQL Data warehouse-hoz
Az SQL Data Warehouse számos feladat az Azure PowerShell-parancsmagok vagy a REST API-k használatával kezelhetők.  Az alábbiakban néhány példa bemutatja, hogyan automatizálhatja a gyakori feladatokat az SQL Data Warehouse a PowerShell-parancsok használatával.  Néhány hasznos REST példák: a cikk [kezelése a REST-tel méretezhetőség][Manage scalability with REST].

> [!NOTE]
> Azure PowerShell 1.0.3-as kell használni az Azure Powershellt az SQL Data Warehouse, vagy nagyobb.  A verzió ellenőrzéséhez futtassa **Get-Module - ListAvailable-Name Azure**.  A legújabb verzió telepíthető [Microsoft Webplatform-telepítő][Microsoft Web Platform Installer].  A legújabb verzió telepítésével kapcsolatban lásd: [How to install and configure Azure PowerShell][How to install and configure Azure PowerShell] (Az Azure PowerShell telepítése és konfigurálása).
> 
> 

## <a name="get-started-with-azure-powershell-cmdlets"></a>Azure PowerShell-parancsmagok használatának első lépései
1. Nyissa meg a Windows PowerShellt.
2. A PowerShell-parancssorba futtassa ezeket a parancsokat, az Azure Resource Managerrel való bejelentkezéshez, és válassza ki az előfizetését.
   
    ```PowerShell
    Connect-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Szüneteltetési SQL Data Warehouse példa
A "Kiszolgalo01." nevű kiszolgáló által üzemeltetett "Database02" nevű adatbázis szüneteltetése  A kiszolgáló része egy Azure-erőforráscsoportot "ResourceGroup1."

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Egy variációjával ebben a példában átadja a lekért objektum [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase].  Ennek eredményeképpen az adatbázis fel van függesztve. A végső parancs megjeleníti az eredményeket.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>Indítsa el az SQL Data Warehouse példa
"Kiszolgalo01." nevű kiszolgálón található "Database02" nevű adatbázis működésének folytatása A kiszolgáló szerepel egy "ResourceGroup1." nevű erőforráscsoportot

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Egy variációjával ebben a példában a kiszolgáló neve "Kiszolgalo01", "ResourceGroup1." nevű erőforráscsoportban található "Database02" nevű adatbázis kéri le Azt a lekért objektum csövek [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase].

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [!NOTE]
> Megjegyzés: Ha a kiszolgáló valami.Database.Windows.NET, a PowerShell-parancsmagok – kiszolgálónév használni "foo".
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Egyéb támogatott PowerShell-parancsmagok
Ezek a PowerShell-parancsmagok az Azure SQL Data Warehouse használata támogatott.

* [Get-azurermsqldatabase parancsmaghoz][Get-AzureRmSqlDatabase]
* [Get-AzureRmSqlDeletedDatabaseBackup][Get-AzureRmSqlDeletedDatabaseBackup]
* [Get-AzureRmSqlDatabaseRestorePoints][Get-AzureRmSqlDatabaseRestorePoints]
* [Új-azurermsqldatabase parancsmaghoz][New-AzureRmSqlDatabase]
* [Remove-azurermsqldatabase parancsmaghoz][Remove-AzureRmSqlDatabase]
* [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase]
* [RESUME-azurermsqldatabase parancsmaghoz][Resume-AzureRmSqlDatabase]
* [SELECT-AzureRmSubscription][Select-AzureRmSubscription]
* [Set-azurermsqldatabase parancsmaghoz][Set-AzureRmSqlDatabase]
* [Suspend-azurermsqldatabase parancsmaghoz][Suspend-AzureRmSqlDatabase]

## <a name="next-steps"></a>További lépések
PowerShell kapcsolatos további példákért lásd:

* [PowerShell-lel az SQL Data Warehouse létrehozása][Create a SQL Data Warehouse using PowerShell]
* [Adatbázis visszaállítása][Database restore]

Egyéb feladatok, amelyek a PowerShell használatával automatizálható, lásd: [Azure SQL Database-parancsmagok][Azure SQL Database Cmdlets]. Ne feledje, hogy nem minden Azure SQL Database-parancsmagok az Azure SQL Data Warehouse is támogatott.  A REST segítségével automatizálható feladatok listáját lásd: [műveleteket az Azure SQL Database][Operations for Azure SQL Databases].

<!--Image references-->

<!--Article references-->
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Create a SQL Data Warehouse using PowerShell]: ./create-data-warehouse-powershell.md
[Database restore]: ./sql-data-warehouse-restore-database-powershell.md
[Manage scalability with REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.sql
[Operations for Azure SQL Databases]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabase
[Get-AzureRmSqlDeletedDatabaseBackup]: https://docs.microsoft.com/powershell/module/azurerm.sql/get-AzureRmSqlDeletedDatabaseBackup
[Get-AzureRmSqlDatabaseRestorePoints]: https://docs.microsoft.com/powershell/module/azurerm.sql/get-AzureRmSqlDatabaseRestorePoints
[New-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/New-AzureRmSqlDatabase
[Remove-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Remove-AzureRmSqlDatabase
[Restore-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Restore-AzureRmSqlDatabase
[Resume-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Resume-AzureRmSqlDatabase
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Set-AzureRmSqlDatabase
[Suspend-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Suspend-AzureRmSqlDatabase

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
