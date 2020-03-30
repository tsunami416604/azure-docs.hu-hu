---
title: A Synapse SQL-készlet számítási méretezése (Azure PowerShell)
description: Az Azure PowerShell használatával skálázhatja a Synapse SQL-készlet (adattárház) számításait.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c7ec8db212a24f1f23f393e4cb0e7f4150605a56
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350796"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-azure-powershell"></a>Rövid útmutató: A Synapse SQL-készlet számításainak méretezése az Azure PowerShell segítségével

Az Azure PowerShell használatával skálázhatja a Synapse SQL-készlet (adattárház) számításait. [Felskálázással](sql-data-warehouse-manage-compute-overview.md) a számítások teljesítménye növelhető, leskálázással a költségek csökkenthetők. 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a rövid útmutató feltételezi, hogy már rendelkezik egy SQL-készlet, amely skálázható. Ha létre kell hoznia egyet, a [Create and Connect - portal (Létrehozás és csatlakozás - portál)](create-data-warehouse-portal.md) segítségével hozzon létre egy **mySampleDataWarehouse**nevű SQL-készletet.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be Azure-előfizetésébe a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) paranccsal, és kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzAccount
```

A használt előfizetés megtekintéséhez futtassa a [Get-AzSubscription futtassa.](/powershell/module/az.accounts/get-azsubscription)

```powershell
Get-AzSubscription
```

Ha az alapértelmezettnél eltérő előfizetést kell használnia, futtassa a [Set-AzContext programot.](/powershell/module/az.accounts/set-azcontext)

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Adattárház-információk megkeresése

Keresse meg a felfüggeszteni és folytatni tervezett adattárházhoz tartozó adatbázis nevét, a kiszolgáló nevét és az erőforráscsoportot.

Keresse meg adattárháza helyinformációit ezekkel lépésekkel.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Kattintson az **Azure Synapse Analytics (korábbi SQL DW)** elemre az Azure Portal bal oldali navigációs lapján.
3. Válassza ki a **mySampleDataWarehouse** az **Azure Synapse Analytics (korábban SQL DW)** lapon az adattárház megnyitásához.

    ![Kiszolgálónév és erőforráscsoport](./media/quickstart-scale-compute-powershell/locate-data-warehouse-information.png)

4. Írja fel az adattárház nevét. Ezt fogja használni az adatbázis neveként. Ne feledje, hogy az adattárház az adatbázisok egy típusa. Írja fel a kiszolgáló nevét és az erőforráscsoportot is. A szüneteltetési és folytatási parancsokban a kiszolgáló nevet és az erőforráscsoport nevét fogja használni.
5. Csak a kiszolgálónév első részét használja a PowerShell-parancsmagokban. Az előző lemezképben a teljes kiszolgálónév sqlpoolservername.database.windows.net. Az **sqlpoolservername-t** használjuk a PowerShell-parancsmagban a kiszolgáló neveként.

## <a name="scale-compute"></a>Számítások méretezése

Az SQL készletben növelheti vagy csökkentheti a számítási erőforrásokat az adattárház egységek módosításával. A [Létrehozás és csatlakozás – portál](create-data-warehouse-portal.md) gyorsútmutató létrehozta a **mySampleDataWarehouse** adattárházat, és inicializálta azt 400 adattárházegységgel. Az alábbi lépésekkel módosíthatja a **mySampleDataWarehouse** adattárházban az adattárházegységek számát.

Az adatraktári egységek módosításához használja a [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) PowerShell parancsmag. A következő példa az adattárház egységeket DW300c-re állítja a **mySampleDataWarehouse**adatbázishoz, amely a kiszolgáló **sqlpoolservername**erőforráscsoporterőforrás-csoportjának **nevében** található.

```Powershell
Set-AzSqlDatabase -ResourceGroupName "resourcegroupname" -DatabaseName "mySampleDataWarehouse" -ServerName "sqlpoolservername" -RequestedServiceObjectiveName "DW300c"
```

## <a name="check-data-warehouse-state"></a>Az adattárház állapotának ellenőrzése

Az adatraktár aktuális állapotának megtekintéséhez használja a [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) PowerShell-parancsmag. Ez a parancsmag a datagroup **resourcegroupname** és a kiszolgálói **sqlpoolservername.database.windows.net** **mySampleDataWarehouse** adatbázisállapotát mutatja.

```powershell
$database = Get-AzSqlDatabase -ResourceGroupName resourcegroupname -ServerName sqlpoolservername -DatabaseName mySampleDataWarehouse
```

Az eredmény a következőhöz lesz hasonló:

```powershell
ResourceGroupName             : resourcegroupname
ServerName                    : sqlpoolservername
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300c
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/resourcegroupname/providers/Microsoft.Sql/servers/sqlpoolservername/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

A kimenetben megjelenik az adatbázis **Állapota**. Ebben az esetben az látható, hogy az adatbázis online állapotban van.  Ennek a parancsnak a futtatáskor az Online, Pausing (felfüggesztés), Resuming (folytatás), Scaling (méretezés) vagy Paused (felfüggesztve) állapotértéket kell visszakapnia.

Az állapot önmagában való megjelenítéséhez használja a következő parancsot:

```powershell
$database | Select-Object DatabaseName,Status
```

## <a name="next-steps"></a>További lépések
Most már megtanulta, hogyan skálázhatja a számítási SQL-készlet. Ha többet szeretne megtudni az SQL-készletről, folytassa az adatok betöltésével kapcsolatos oktatóanyagmal.

> [!div class="nextstepaction"]
>[Adatok betöltése SQL-készletbe](load-data-from-azure-blob-storage-using-polybase.md)
