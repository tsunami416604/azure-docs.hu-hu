---
title: 'Gyors üzembe helyezés: Szüneteltetése és folytatása számítási az Azure SQL Data Warehouse - PowerShell |} Microsoft Docs'
description: A PowerShell szolgáltatás használatával szünet számítási az Azure SQL Data Warehouse költségek csökkentése érdekében. Amikor készen áll az adatraktárat, folytathatja a számítást.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: ef341a1528bf759461abfb7cfc6d878fd8a44cb4
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31598897"
---
# <a name="quickstart-pause-and-resume-compute-in-azure-sql-data-warehouse-with-powershell"></a>Gyors üzembe helyezés: Szüneteltetése és folytatása számítási az Azure SQL Data Warehouse a PowerShell használatával
A PowerShell szolgáltatás használatával szünet számítási az Azure SQL Data Warehouse költségek csökkentése érdekében. [Folytathatja a számítást](sql-data-warehouse-manage-compute-overview.md) Ha készen áll az adatraktárat.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Az oktatóanyaghoz az Azure PowerShell-modul 5.1.1-es vagy újabb verziója szükséges. A jelenleg rendelkezésére álló verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps.md) ismertető cikket.

## <a name="before-you-begin"></a>Előkészületek

A gyors üzembe helyezés feltételezi, hogy már rendelkezik egy SQL data warehouse szüneteltetése és folytatása. Ha szeretne létrehozni egyet, akkor használhatja [létrehozása és a Connect - portál](create-data-warehouse-portal.md) nevű adatraktár létrehozásához **mySampleDataWarehouse**.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) parancs használatával, és kövesse a képernyőn megjelenő útmutatást.

```powershell
Connect-AzureRmAccount
```

A használt előfizetés megtekintéséhez futtassa a [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) parancsot.

```powershell
Get-AzureRmSubscription
```

Ha nem az alapértelmezett előfizetést kívánja használni, futtassa a [Select-AzureRmSubscription](/powershell/module/azurerm.profile/select-azurermsubscription) parancsot.

```powershell
Select-AzureRmSubscription -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Adattárház-információk megkeresése

Keresse meg a felfüggeszteni és folytatni tervezett adattárházhoz tartozó adatbázis nevét, a kiszolgáló nevét és az erőforráscsoportot.

Keresse meg adattárháza helyinformációit ezekkel lépésekkel.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Az Azure Portal bal oldali paneljén kattintson az **SQL-adatbázisok** elemre.
3. Az **SQL-adatbázisok** lapon jelölje ki a **mySampleDataWarehouse** elemet. Ekkor megnyílik az adattárház.

    ![Kiszolgálónév és erőforráscsoport](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Jegyezze fel az adatraktár-nevét, amely az adatbázis nevét. Írja fel a kiszolgáló nevét és az erőforráscsoportot is. Ön
5.  Ezek a szüneteltetése és folytatása parancsokban.
6. Ha a kiszolgáló valami.database.windows.net, csak az első részt használja kiszolgálónévként a PowerShell-parancsmagokban. A fenti ábrán a teljes kiszolgálónév newserver-20171113.database.windows.net. A utótag dobja el, és használja **newserver-20171113** a PowerShell-parancsmag a kiszolgálónevet.

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
