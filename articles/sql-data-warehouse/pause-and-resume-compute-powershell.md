---
title: 'Gyors útmutató: & szüneteltetésének folytatása – PowerShell '
description: A PowerShell használatával szüneteltetheti a számítást az Azure szinapszis Analytics SQL-készletben a költségek megtakarítása érdekében. Ha készen áll az adattárház használatára, folytassa a számítást.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 03/20/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: ce183edef9e5895d7b3f702f5466c505956a869a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200566"
---
# <a name="quickstart-pause-and-resume-compute-in-azure-synapse-analytics-sql-pool-with-azure-powershell"></a>Rövid útmutató: a számítási feladat szüneteltetése és folytatása az Azure szinapszis Analytics SQL-készletben Azure PowerShell

A Azure PowerShell használatával szüneteltetheti az SQL-készlet számítási felszámítását a költségek megtakarítása érdekében. Ha készen áll az adattárház használatára, [folytassa a számítást](sql-data-warehouse-manage-compute-overview.md) .

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez a rövid útmutató azt feltételezi, hogy már rendelkezik egy olyan SQL-készlettel, amelyet szüneteltetni és folytatni tud. Ha létre kell hoznia egyet, a [create and összekapcsolás-Portal](create-data-warehouse-portal.md) használatával létrehozhat egy **mySampleDataWarehouse**nevű SQL-készletet.

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

Keresse meg a szüneteltetni és folytatni kívánt SQL-készlet adatbázisának nevét, a kiszolgáló nevét és az erőforráscsoportot.

Az alábbi lépéseket követve megkeresheti az SQL-készlet helyével kapcsolatos információkat.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).
1. Az Azure Portal bal oldalán kattintson az **Azure szinapszis Analytics (korábban SQL DW)** elemre.
1. Válassza a **mySampleDataWarehouse** lehetőséget az **Azure szinapszis Analytics (korábban SQL DW)** lapon. Ekkor megnyílik az adattárház.

    ![Kiszolgálónév és erőforráscsoport](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. Jegyezze fel az adatraktár nevét, amely az adatbázis neve. Írja fel a kiszolgáló nevét és az erőforráscsoportot is.
1. A PowerShell-parancsmagokban csak a kiszolgáló nevének első részét használja. Az előző képen a teljes kiszolgáló neve sqlpoolservername.database.windows.net. A PowerShell-parancsmagban a **sqlpoolservername** -t használjuk a kiszolgáló neveként.

## <a name="pause-compute"></a>Számítás szüneteltetése

A költségek megtakarítása érdekében szüneteltetheti és folytathatja a számítási erőforrások igény szerinti szüneteltetését. Ha például nem az adatbázist használja az éjszaka és a hétvégén, akkor szüneteltetheti az időt, és a nap folyamán folytathatja. A számítási erőforrásokért nem számítunk fel díjat, amíg az adatbázis szüneteltetve van. Azonban továbbra is a tárterületért kell fizetnie.

Egy adatbázis szüneteltetéséhez használja a [felfüggesztés-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase) parancsmagot. Az alábbi példa egy **mySampleDataWarehouse** nevű adattárházat szüneteltet egy **sqlpoolservername**nevű kiszolgálón. A kiszolgáló egy **myResourceGroup**nevű Azure-erőforráscsoport.


```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "nsqlpoolservername" –DatabaseName "mySampleDataWarehouse"
```

Egy változat, a következő példa lekéri az adatbázist a $database objektumba. Ezután átadja az objektumot a [felfüggesztéshez – AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase). Az eredményeket az objektum resultDatabase tárolja. Az utolsó parancs az eredményeket jeleníti meg.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="resume-compute"></a>Számítás folytatása

Az adatbázisok elindításához használja a [resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase) parancsmagot. A következő példa egy **mySampleDataWarehouse** nevű adatbázist indít el egy **sqlpoolservername**nevű kiszolgálón. A kiszolgáló egy **myResourceGroup**nevű Azure-erőforráscsoport.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

Egy változat, a következő példa lekéri az adatbázist a $database objektumba. Ezután a [AzSqlDatabase folytatja](/powershell/module/az.sql/resume-azsqldatabase) az objektumot, és az eredményeket $resultDatabase tárolja. Az utolsó parancs az eredményeket jeleníti meg.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-data-warehouse-operation"></a>Az adatraktár-művelet állapotának ellenõrzése

Az adatraktár állapotának megtekintéséhez használja a [Get-AzSqlDatabaseActivity](https://docs.microsoft.com/powershell/module/az.sql/Get-AzSqlDatabaseActivity#description) parancsmagot.

```Powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az adattárházegységek és az adattárházban tárolt adatok díjkötelesek. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva.

- Ha meg szeretné őrizni az adatok tárolását, szüneteltetheti a számítást.
- Ha el szeretné távolítani a jövőbeli díjakat, törölheti az SQL-készletet.

Kövesse az alábbi lépéseket a fölöslegessé vált erőforrások eltávolítására.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és kattintson az SQL-készletre.

    ![Az erőforrások eltávolítása](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. A számítási erőforrások szüneteltetéshez kattintson a **Szüneteltetés** gombra. Ha az SQL-készlet fel van függesztve, a **Start** gomb jelenik meg.  A számítási erőforrások újraindításához kattintson az **Indítás** gombra.

3. Ha el szeretné távolítani az SQL-készletet, hogy ne legyen kiszámítva a számítás vagy a tárterület, kattintson a **Törlés**gombra.

4. A létrehozott SQL-kiszolgáló eltávolításához kattintson a **sqlpoolservername.database.Windows.net**elemre, majd a **Törlés**elemre.  A törléssel bánjon óvatosan, mivel a kiszolgálóval együtt a hozzá rendelt összes adatbázis is törölve lesz.

5. Az erőforráscsoport törléséhez kattintson a **myResourceGroup** elemre, majd az **Erőforráscsoport törlése** parancsra.


## <a name="next-steps"></a>További lépések

Ezzel szüneteltette és folytatta a számítást az SQL-készlethez. Ha többet szeretne megtudni az SQL-készletről, folytassa a betöltési oktatóanyaggal.

> [!div class="nextstepaction"]
> [Betöltés az SQL-készletbe](load-data-from-azure-blob-storage-using-polybase.md)
