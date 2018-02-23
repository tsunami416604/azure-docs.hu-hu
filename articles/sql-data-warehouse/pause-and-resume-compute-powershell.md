---
title: "Gyors üzembe helyezés: Szüneteltetése és folytatása számítási az Azure SQL Data Warehouse - PowerShell |} Microsoft Docs"
description: "PowerShell-feladatok szüneteltetése számítási az Azure SQL Data Warehouse költségek csökkentése érdekében. Amikor készen áll az adatraktárat, folytathatja a számítást."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 01/25/2018
ms.author: barbkess
ms.openlocfilehash: b1f5c10fe294b44a9853f16e1866b77cf74a1479
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="quickstart-pause-and-resume-compute-for-an-azure-sql-data-warehouse-in-powershell"></a>Gyors üzembe helyezés: Felfüggesztése és folytatása számítási az Azure SQL Data Warehouse a PowerShellben
Az Azure SQL Data Warehouse költségek csökkentése érdekében számítási szüneteltetése a PowerShell használatával. [Folytathatja a számítást](sql-data-warehouse-manage-compute-overview.md) Ha készen áll az adatraktárat.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ez az oktatóanyag van szükség az Azure PowerShell modul verziója 5.1.1-es vagy újabb. Futtatás ` Get-Module -ListAvailable AzureRM` találnia, amelynek van jelenleg. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps.md) ismertető cikket. 

## <a name="before-you-begin"></a>Előkészületek

A gyors üzembe helyezés feltételezi, hogy már rendelkezik egy SQL data warehouse szüneteltetése és folytatása. Ha szeretne létrehozni egyet, akkor használhatja [létrehozása és a Connect - portál](create-data-warehouse-portal.md) nevű adatraktár létrehozásához **mySampleDataWarehouse**. 

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
3. Válassza ki **mySampleDataWarehouse** a a **SQL-adatbázisok** lap. Megnyitja az adatraktárba.

    ![Kiszolgáló nevét és az erőforrás-csoport](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Jegyezze fel az adatraktár-nevét, amely az adatbázis nevét. Is írja be a kiszolgáló nevét, és az erőforráscsoportot. Ön 
5.  Ezek a szüneteltetése és folytatása parancsokban.
6. Ha a kiszolgáló foo.database.windows.net, akkor csak az első rész a PowerShell-parancsmagok a kiszolgálónevet. Az előző ábrán a teljes kiszolgálónevet newserver-20171113.database.windows.net. A utótag dobja el, és használja **newserver-20171113** a PowerShell-parancsmag a kiszolgálónevet.

## <a name="pause-compute"></a>Felfüggesztés számítási
Költségek csökkentése érdekében szüneteltetése, és folytassa a számítási erőforrások igény. Például ha az adatbázis éjszaka és hétvégén nem használ, után ilyen alkalmakkor szünetelteti, is fogják folytatni azt a nap folyamán. Amíg az adatbázis fel van függesztve, nincs díjmentes számítási erőforrásokat. Azonban továbbra is tárolási számlázni. 

Egy adatbázis felfüggesztése, használja a [Suspend-AzureRmSqlDatabase](/powershell/module/azurerm.sql/suspend-azurermsqldatabase.md) parancsmag. A következő példa egy adatraktár nevű felfüggesztése **mySampleDataWarehouse** nevű kiszolgálón található **newserver-20171113**. A kiszolgáló van egy Azure erőforráscsoport nevű **myResourceGroup**.


```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
```

A módosítást, a következő példa olvassa be az adatbázis a $database objektumba. Ezt követően átadja a az objektum [Suspend-AzureRmSqlDatabase](/powershell/module/azurerm.sql/suspend-azurermsqldatabase). Az objektum resultDatabase tárolja az eredményeket. A záró parancs az eredményeit jeleníti meg.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```


## <a name="resume-compute"></a>Folytatás számítási
Egy adatbázis indításához használja a [Resume-AzureRmSqlDatabase](/powershell/module/azurerm.sql/resume-azurermsqldatabase) parancsmag. A következő példában elindul newserver-20171113 nevű kiszolgálón található mySampleDataWarehouse nevű adatbázis. A kiszolgáló van egy contoso.com nevű Azure-erőforráscsoportot.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" -DatabaseName "mySampleDataWarehouse"
```

A módosítást, a következő példa olvassa be az adatbázis a $database objektumba. Ezt követően átadja a az objektum [Resume-AzureRmSqlDatabase](/powershell/module/azurerm.sql/resume-azurermsqldatabase.md) , és az eredmények $resultDatabase tárolja. A záró parancs az eredményeit jeleníti meg.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az adattárházegységek és az adattárházban tárolt adatok díjkötelesek. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva. 

- Ha meg szeretné tartani az adatokat a tárhelyen, szüneteltesse a számítási.
- Ha szeretné megelőzni a jövőbeli kiadásokat, az adattárházat törölheti is. 

Kövesse az alábbi lépéseket a fölöslegessé vált erőforrások eltávolítására.

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com), majd kattintson az az adatraktár.

    ![Az erőforrások eltávolítása](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. A számítási erőforrások szüneteltetéshez kattintson a **Szüneteltetés** gombra. Ha az adattárház szüneteltetve van, az **Indítás** gomb látható.  A számítási erőforrások újraindításához kattintson az **Indítás** gombra.

2. Ha el szeretné távolítani az adattárházat, hogy a számítási és tárolási erőforrásokért se kelljen fizetnie, kattintson a **Törlés** parancsra.

3. Kattintson a létrehozott SQL server **mynewserver-20171113.database.windows.net**, és kattintson a **törlése**.  A törléssel bánjon óvatosan, mivel a kiszolgálóval együtt a hozzá rendelt összes adatbázis is törölve lesz.

4. Az erőforráscsoport törléséhez kattintson a **myResourceGroup** elemre, majd az **Erőforráscsoport törlése** parancsra.


## <a name="next-steps"></a>További lépések
Most szünetel, és az adatraktár számítási folytatódik. Ha bővebb információra van szüksége az Azure SQL Data Warehouse-zal kapcsolatban, folytassa az adatok betöltésével foglalkozó oktatóanyaggal.

> [!div class="nextstepaction"]
>[Adatok betöltése az SQL Data Warehouse-okba](load-data-from-azure-blob-storage-using-polybase.md)