---
title: "Gyors üzembe helyezés: Horizontális felskálázás az Azure SQL Data Warehouse - PowerShell számítási |} Microsoft Docs"
description: "PowerShell feladatok horizontális adattárházegységek beállításával számítási erőforrásokat."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 01/31/2018
ms.author: elbutter;barbkess
ms.openlocfilehash: a3a435d6bdb0d35c96349540d5e9f9b5be61bd9b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-powershell"></a>Gyors üzembe helyezés: Az Azure SQL Data Warehouse a PowerShellben számítási méretezési

Skála számítási az Azure SQL Data Warehouse a PowerShellben. [Horizontális felskálázás számítási](sql-data-warehouse-manage-compute-overview.md) jobb teljesítményt vagy skálája biztonsági számítási költségek csökkentése érdekében. 

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ez az oktatóanyag van szükség az Azure PowerShell modul verziója 5.1.1-es vagy újabb. Futtatás `Get-Module -ListAvailable AzureRM` találnia, amelynek van jelenleg. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps.md) ismertető cikket. 

## <a name="before-you-begin"></a>Előkészületek

A gyors üzembe helyezés azt feltételezi, hogy már rendelkezik egy SQL data warehouse is méretezhető. Ha szeretne létrehozni egyet, [létrehozása és a Connect - portál](create-data-warehouse-portal.md) nevű data warehouse létrehozása az **mySampleDataWarehouse**. 

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe az [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) parancs használatával, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Add-AzureRmAccount
```

Melyik előfizetéssel használja megtekintéséhez futtassa [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

```powershell
Get-AzureRmSubscription
```

Ha egy másik előfizetésben található alapértelmezett használni kell, futtassa [Select-AzureRmSubscription](/powershell/module/azurerm.profile/select-azurermsubscription).

```powershell
Select-AzureRmSubscription -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Kereshet az adatraktár információi

Keresse meg az adatbázis neve, a kiszolgálónév és a szüneteltetése és folytatása tervezi az adatraktárhoz tartozó erőforráscsoport. 

Kövesse az alábbi lépéseket az adatraktár Tartózkodásihely-adatok kereséséhez.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a **SQL-adatbázisok** az Azure portál bal oldalán.
3. Válassza ki **mySampleDataWarehouse** a a **SQL-adatbázisok** lap. Ekkor megnyílik az adatraktárba. 

    ![Kiszolgáló nevét és az erőforrás-csoport](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Jegyezze fel az adatraktár nevét, amely az adatbázis neve lesz. Is írja be a kiszolgáló nevét, és az erőforráscsoportot. Először használja ezeket az szüneteltetése, folytatása parancsok.
5. Ha a kiszolgáló foo.database.windows.net, akkor csak az első rész a PowerShell-parancsmagok a kiszolgálónevet. Az előző ábrán a teljes kiszolgálónevet newserver-20171113.database.windows.net. Használjuk **newserver-20171113** a PowerShell-parancsmag a kiszolgálónevet.

## <a name="scale-compute"></a>Számítások méretezése

Az SQL Data Warehouse növelheti vagy csökkentheti a számítási erőforrásokat adattárházegységek beállításával. A [létrehozása és a Connect - portál](create-data-warehouse-portal.md) létrehozott **mySampleDataWarehouse** és 400 dwu-k inicializálása azt. Az alábbi lépéseket, állítsa be a dwu-k a **mySampleDataWarehouse**.

Adattárházegységek módosításához használja a [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) PowerShell-parancsmagot. Az alábbi példa DW300 az adattárházegységek állítja az adatbázis **mySampleDataWarehouse** erőforráscsoporthoz tartozik, amely található **myResourceGroup** kiszolgálón  **mynewserver-20171113**.

```Powershell
Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
```

## <a name="check-database-state"></a>Ellenőrizze az adatbázis állapota

Az adatraktár aktuális állapotának megtekintéséhez használja a [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) PowerShell-parancsmagot. Ez a állapotának beolvasása a **mySampleDataWarehouse** ResourceGroup adatbázis **myResourceGroup** és a kiszolgáló **mynewserver-20171113.database.windows.net**.

```powershell
Get-AzureRmSqlDatabase -ResourceGroupName myResourceGroup -ServerName mynewserver-20171113 -DatabaseName mySampleDataWarehouse
```

Emiatt, az alábbihoz hasonló:

```powershell   
ResourceGroupName             : myResourceGroup
ServerName                    : mynewserver-20171113
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
CurrentServiceObjectiveName   : DW300
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mynewserver-20171113/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

Majd ellenőrizze, hogy a **állapot** az adatbázis. Ebben az esetben láthatja, hogy az adatbázis online állapotban.  Ez a parancs futtatásakor Online, felfüggesztése, folytatása, méretezés vagy felfüggesztett állapot értéket kell kapnia.

## <a name="next-steps"></a>További lépések
Most megtanulhatta, az adatraktár számítási méretezése. Ha bővebb információra van szüksége az Azure SQL Data Warehouse-zal kapcsolatban, folytassa az adatok betöltésével foglalkozó oktatóanyaggal.

> [!div class="nextstepaction"]
>[Adatok betöltése az SQL Data Warehouse-okba](load-data-from-azure-blob-storage-using-polybase.md)
