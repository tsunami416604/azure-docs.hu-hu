---
title: "Az Azure SQL Data Warehouse (PowerShell) a számítási teljesítmény kezelése |} Microsoft Docs"
description: "PowerShell-feladatok kezelésére számítási teljesítményt. Skála dwu-k beállításával számítási erőforrásokat. Vagy, és sablonok felfüggesztése és folytatása a számítási erőforrásokat költségek csökkentése érdekében."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 8354a3c1-4e04-4809-933f-db414a8c74dc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 6a185d96447c2e1b0b463439dd062081e783da5f
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-powershell"></a>Az Azure SQL Data Warehouse (PowerShell) a számítási teljesítmény kezelése
> [!div class="op_single_selector"]
> * [Áttekintés](sql-data-warehouse-manage-compute-overview.md)
> * [Portál](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

## <a name="before-you-begin"></a>Előkészületek
### <a name="install-the-latest-version-of-azure-powershell"></a>Az Azure PowerShell legújabb verziójának telepítése
> [!NOTE]
> Az SQL Data Warehouse szolgáltatással Azure PowerShell használatához szüksége Azure PowerShell 1.0.3-as vagy újabb.  Futtassa a parancsot a meglévő ellenőrzése **Get-Module - ListAvailable-Name Azure**. Telepítheti a legújabb verziót [Microsoft Webplatform-telepítő][Microsoft Web Platform Installer].  További információkért lásd: [telepítése és konfigurálása az Azure PowerShell][How to install and configure Azure PowerShell].
>
> 

### <a name="get-started-with-azure-powershell-cmdlets"></a>Ismerkedés az Azure PowerShell-parancsmagok
Első lépések:

1. Nyissa meg az Azure PowerShell.
2. A PowerShell-parancssorba, futtassa az alábbi parancsokat az Azure Resource Managerrel történő bejelentkezéshez, és jelölje ki az előfizetését.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Skála számítási teljesítmény
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

A dwu-k módosításához használja a [Set-AzureRmSqlDatabase] [ Set-AzureRmSqlDatabase] PowerShell-parancsmagot. Az alábbi példa állítja be a szolgáltatási szint célkitűzés DW1000 az adatbázis MySQLDW, amely a MyServer kiszolgáló üzemelteti.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Felfüggesztés számítási
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Egy adatbázis felfüggesztése, használja a [Suspend-AzureRmSqlDatabase] [ Suspend-AzureRmSqlDatabase] parancsmag. A következő példa egy kiszolgalo01 nevű kiszolgálón található Database02 nevű adatbázis felfüggesztése. A kiszolgáló egy ResourceGroup1 nevű Azure-erőforrás-csoportban.

> [!NOTE]
> Figyelje meg, hogy ha a kiszolgáló foo.database.windows.net, "foo" használják a PowerShell-parancsmagok - kiszolgálónév.
>
> 

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
A módosítást, a következő példa olvassa be az adatbázis a $database objektumba. Ezt követően átadja a az objektum [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]. Az objektum resultDatabase tárolja az eredményeket. A záró parancs az eredményeit jeleníti meg.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Folytatás számítási
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Egy adatbázis indításához használja a [Resume-AzureRmSqlDatabase] [ Resume-AzureRmSqlDatabase] parancsmag. A következő példában elindul kiszolgalo01 nevű kiszolgálón található Database02 nevű adatbázis. A kiszolgáló egy ResourceGroup1 nevű Azure-erőforrás-csoportban.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

A módosítást, a következő példa olvassa be az adatbázis a $database objektumba. Ezt követően átadja a az objektum [Resume-AzureRmSqlDatabase] [ Resume-AzureRmSqlDatabase] , és az eredmények $resultDatabase tárolja. A záró parancs az eredményeit jeleníti meg.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="check-database-state-bk"></a>

## <a name="check-database-state"></a>Ellenőrizze az adatbázis állapota

A fenti példákban látható, egy használható [Get-AzureRmSqlDatabase] [ Get-AzureRmSqlDatabase] parancsmag segítségével információkat olvashat egy adatbázist, ezáltal a a állapotának ellenőrzése a, hanem argumentumként használatára. 

```powershell
Get-AzureRmSqlDatabase [-ResourceGroupName] <String> [-ServerName] <String> [[-DatabaseName] <String>]
 [-InformationAction <ActionPreference>] [-InformationVariable <String>] [-Confirm] [-WhatIf]
 [<CommonParameters>]
```

Amely eredményt valamit meg 

```powershell   
ResourceGroupName             : nytrg
ServerName                    : nytsvr
DatabaseName                  : nytdb
Location                      : West US
DatabaseId                    : 86461aae-8e3d-4ded-9389-ac9d4bc69bbb
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1General_CP1CI_AS
CatalogCollation              :
MaxSizeBytes                  : 32212254720
Status                        : Online
CreationDate                  : 10/26/2016 4:33:14 PM
CurrentServiceObjectiveId     : 620323bf-2879-4807-b30d-c2e6d7b3b3aa
CurrentServiceObjectiveName   : System2
RequestedServiceObjectiveId   : 620323bf-2879-4807-b30d-c2e6d7b3b3aa
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           : 1/1/0001 12:00:00 AM
```

Ahol majd ellenőrizheti, hogy a *állapot* az adatbázis. Ebben az esetben láthatja, hogy az adatbázis online állapotban. 

Ez a parancs futtatásakor vagy Online, felfüggesztése, folytatása, méretezés és felfüggesztett állapot értéket kell kapnia.

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Következő lépések
Más felügyeleti feladatokat [kezelése-áttekintés][Management overview].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Management overview]: ./sql-data-warehouse-overview-manage.md
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Manage compute overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Get-AzureRmSqlDatabase]: /powershell/servicemanagement/azure.sqldatabase/v1.6.1/get-azuresqldatabase

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[Azure portal]: http://portal.azure.com/
