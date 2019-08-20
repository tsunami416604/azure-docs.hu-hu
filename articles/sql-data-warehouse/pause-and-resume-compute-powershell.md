---
title: 'Gyors útmutató: A számítási feladat szüneteltetése és folytatása a Azure SQL Data Warehouse-PowerShellben | Microsoft Docs'
description: A PowerShell használatával szüneteltetheti a számítást Azure SQL Data Warehouse a költségek megtakarítása érdekében. Ha készen áll az adattárház használatára, folytassa a számítást.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 03/20/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 220d2aefd714cd3546fa4d5c2ac8852d2786b8ac
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575408"
---
# <a name="quickstart-pause-and-resume-compute-in-azure-sql-data-warehouse-with-powershell"></a>Gyors útmutató: A Azure SQL Data Warehouse a PowerShell használatával szüneteltetheti és folytathatja a számítást

A PowerShell használatával szüneteltetheti a számítást Azure SQL Data Warehouse a költségek megtakarítása érdekében. Ha készen áll az adattárház használatára, [folytassa](sql-data-warehouse-manage-compute-overview.md) a számítást.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez a rövid útmutató azt feltételezi, hogy már rendelkezik egy SQL Data Warehouse, amelyet szüneteltetni és folytatni tud. Ha létre kell hoznia egyet, a [create és a összekapcsolás-Portal](create-data-warehouse-portal.md) használatával létrehozhat egy **mySampleDataWarehouse**nevű adattárházat.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancs használatával, és kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzAccount
```

A [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription)futtatásával megtekintheti, hogy melyik előfizetést használja.

```powershell
Get-AzSubscription
```

Ha az alapértelmezettnél eltérő előfizetést kell használnia, futtassa a [set-AzContext](/powershell/module/az.accounts/set-azcontext)parancsot.

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Adattárház-információk megkeresése

Keresse meg a felfüggeszteni és folytatni tervezett adattárházhoz tartozó adatbázis nevét, a kiszolgáló nevét és az erőforráscsoportot.

Keresse meg adattárháza helyinformációit ezekkel lépésekkel.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Az Azure Portal bal oldali paneljén kattintson az **SQL-adatbázisok** elemre.
3. Az **SQL-adatbázisok** lapon jelölje ki a **mySampleDataWarehouse** elemet. Ekkor megnyílik az adattárház.

    ![Kiszolgálónév és erőforráscsoport](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Jegyezze fel az adatraktár nevét, amely az adatbázis neve. Írja fel a kiszolgáló nevét és az erőforráscsoportot is.
6. Ha a kiszolgáló valami.database.windows.net, csak az első részt használja kiszolgálónévként a PowerShell-parancsmagokban. A fenti ábrán a teljes kiszolgálónév newserver-20171113.database.windows.net. Dobja el az utótagot, és használja a **NewServer-20171113** nevet a PowerShell-parancsmagban.

## <a name="pause-compute"></a>Számítás szüneteltetése

A költségek megtakarítása érdekében szüneteltetheti és folytathatja a számítási erőforrások igény szerinti szüneteltetését. Ha például nem az adatbázist használja az éjszaka és a hétvégén, akkor szüneteltetheti az időt, és a nap folyamán folytathatja. A számítási erőforrásokért nem számítunk fel díjat, amíg az adatbázis szüneteltetve van. Azonban továbbra is a tárterületért kell fizetnie.

Egy adatbázis szüneteltetéséhez használja a [felfüggesztés-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase) parancsmagot. Az alábbi példa egy **mySampleDataWarehouse** nevű adattárház felfüggesztését futtatja egy **NewServer-20171113**nevű kiszolgálón. A kiszolgáló egy **myResourceGroup**nevű Azure-erőforráscsoport.


```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
```

Egy változat, a következő példa lekéri az adatbázist a $database objektumba. Ezután átadja az objektumot a [felfüggesztéshez – AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase). Az eredményeket az objektum resultDatabase tárolja. Az utolsó parancs az eredményeket jeleníti meg.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```


## <a name="resume-compute"></a>Számítás folytatása

Az adatbázisok elindításához használja a [resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase) parancsmagot. A következő példa egy mySampleDataWarehouse nevű adatbázist indít el egy NewServer-20171113 nevű kiszolgálón. A kiszolgáló egy myResourceGroup nevű Azure-erőforráscsoport.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" -DatabaseName "mySampleDataWarehouse"
```

Egy változat, a következő példa lekéri az adatbázist a $database objektumba. Ezután a [AzSqlDatabase folytatja](/powershell/module/az.sql/resume-azsqldatabase) az objektumot, és az eredményeket $resultDatabase tárolja. Az utolsó parancs az eredményeket jeleníti meg.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-data-warehouse-operation"></a>Az adatraktár-művelet állapotának ellenõrzése

Az adatraktár állapotának megtekintéséhez használja a [Get-AzSqlDatabaseActivity](https://docs.microsoft.com/powershell/module/az.sql/Get-AzSqlDatabaseActivity#description) parancsmagot.

```
Get-AzSqlDatabaseActivity -ResourceGroupName "ResourceGroup01" -ServerName "Server01" -DatabaseName "Database02"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az adattárházegységek és az adattárházban tárolt adatok díjkötelesek. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva.

- Ha meg szeretné őrizni az adatok tárolását, szüneteltetheti a számítást.
- Ha szeretné megelőzni a jövőbeli kiadásokat, az adattárházat törölheti is.

Kövesse az alábbi lépéseket a fölöslegessé vált erőforrások eltávolítására.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és kattintson az adattárházra.

    ![Az erőforrások eltávolítása](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. A számítási erőforrások szüneteltetéshez kattintson a **Szüneteltetés** gombra. Ha az adattárház szüneteltetve van, az **Indítás** gomb látható.  A számítási erőforrások újraindításához kattintson az **Indítás** gombra.

3. Ha el szeretné távolítani az adattárházat, hogy a számítási és tárolási erőforrásokért se kelljen fizetnie, kattintson a **Törlés** parancsra.

4. A létrehozott SQL-kiszolgáló eltávolításához kattintson a **mynewserver-20171113.database.Windows.net**elemre, majd a **Törlés**elemre.  A törléssel bánjon óvatosan, mivel a kiszolgálóval együtt a hozzá rendelt összes adatbázis is törölve lesz.

5. Az erőforráscsoport törléséhez kattintson a **myResourceGroup** elemre, majd az **Erőforráscsoport törlése** parancsra.


## <a name="next-steps"></a>További lépések

Ezzel szüneteltette és folytatta a számítást az adattárházban. Ha bővebb információra van szüksége az Azure SQL Data Warehouse-zal kapcsolatban, folytassa az adatok betöltésével foglalkozó oktatóanyaggal.

> [!div class="nextstepaction"]
> [Az adatSQL Data Warehouseba való betöltés](load-data-from-azure-blob-storage-using-polybase.md)
