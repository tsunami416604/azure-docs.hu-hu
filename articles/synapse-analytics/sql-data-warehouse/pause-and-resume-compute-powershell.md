---
title: A számítás szüneteltetése és folytatása a szinapszis SQL-készletben Azure PowerShell
description: Azure PowerShell használatával szüneteltetheti és folytathatja a szinapszis SQL-készletet (adattárház). számítási erőforrások.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 03/20/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 44ce56238140de90145f69e966c94be8572c7749
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81252302"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-with-azure-powershell"></a>Rövid útmutató: a számítások szüneteltetése és folytatása a szinapszis SQL-készletben Azure PowerShell

A Azure PowerShell használatával szüneteltetheti és folytathatja a szinapszis SQL-készlet (adatraktár) számítási erőforrásait.
Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a rövid útmutató azt feltételezi, hogy már rendelkezik egy olyan SQL-készlettel, amelyet szüneteltetni és folytatni tud. Ha létre kell hoznia egyet, a [create and összekapcsolás-Portal](create-data-warehouse-portal.md) használatával létrehozhat egy **mySampleDataWarehouse**nevű SQL-készletet.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a [AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) parancs használatával, és kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzAccount
```

A [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)futtatásával megtekintheti, hogy melyik előfizetést használja.

```powershell
Get-AzSubscription
```

Ha az alapértelmezettnél eltérő előfizetést kell használnia, futtassa a [set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)parancsot.

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-sql-pool-information"></a>SQL-készlet adatainak megkeresése

Keresse meg a szüneteltetni és folytatni kívánt SQL-készlet adatbázisának nevét, a kiszolgáló nevét és az erőforráscsoportot.

Az alábbi lépéseket követve megkeresheti az SQL-készlet helyének adatait:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Az Azure Portal bal oldalán kattintson az **Azure szinapszis Analytics (korábban SQL DW)** elemre.
1. Válassza a **mySampleDataWarehouse** lehetőséget az **Azure szinapszis Analytics (korábban SQL DW)** lapon. Megnyílik az SQL-készlet.

    ![Kiszolgálónév és erőforráscsoport](./media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. Jegyezze fel az SQL-készlet nevét, amely az adatbázis neve. Írja fel a kiszolgáló nevét és az erőforráscsoportot is.
1. A PowerShell-parancsmagokban csak a kiszolgáló nevének első részét használja. Az előző képen a teljes kiszolgáló neve sqlpoolservername.database.windows.net. A PowerShell-parancsmagban a **sqlpoolservername** -t használjuk a kiszolgáló neveként.

## <a name="pause-compute"></a>Számítás szüneteltetése

A költségek megtakarítása érdekében szüneteltetheti és folytathatja a számítási erőforrások igény szerinti szüneteltetését. Ha például nem az adatbázist használja az éjszaka és a hétvégén, akkor szüneteltetheti az időt, és a nap folyamán folytathatja.

>[!NOTE]
>A számítási erőforrásokért nem számítunk fel díjat, amíg az adatbázis szüneteltetve van. Azonban továbbra is a tárterületért kell fizetnie.

Egy adatbázis szüneteltetéséhez használja a [felfüggesztés-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) parancsmagot. Az alábbi példa egy **mySampleDataWarehouse** nevű SQL-készlet szüneteltetését futtatja egy **sqlpoolservername**nevű kiszolgálón. A kiszolgáló egy **myResourceGroup**nevű Azure-erőforráscsoport.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "nsqlpoolservername" –DatabaseName "mySampleDataWarehouse"
```

A következő példa lekéri az adatbázist a $database objektumba. Ezután átadja az objektumot a [felfüggesztéshez – AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Az eredményeket az objektum resultDatabase tárolja. Az utolsó parancs az eredményeket jeleníti meg.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="resume-compute"></a>Számítás folytatása

Az adatbázisok elindításához használja a [resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) parancsmagot. A következő példa egy **mySampleDataWarehouse** nevű adatbázist indít el egy **sqlpoolservername**nevű kiszolgálón. A kiszolgáló egy **myResourceGroup**nevű Azure-erőforráscsoport.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

A következő példa lekéri az adatbázist a $database objektumba. Ezután a [AzSqlDatabase folytatja](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) az objektumot, és az eredményeket $resultDatabase tárolja. Az utolsó parancs az eredményeket jeleníti meg.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-sql-pool-operation"></a>Az SQL-készlet művelet állapotának ellenõrzése

Az SQL-készlet állapotának megtekintéséhez használja a [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/Get-AzSqlDatabaseActivity?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) parancsmagot.

```Powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az adatraktár-egységek és az SQL-készletben tárolt adatmennyiség után kell fizetnie. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva.

- Ha meg szeretné őrizni az adatok tárolását, szüneteltetheti a számítást.
- Ha el szeretné távolítani a jövőbeli díjakat, törölheti az SQL-készletet.

Kövesse az alábbi lépéseket a fölöslegessé vált erőforrások eltávolítására.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és kattintson az SQL-készletre.

    ![Az erőforrások eltávolítása](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. A számítási erőforrások szüneteltetéshez kattintson a **Szüneteltetés** gombra. Ha az SQL-készlet fel van függesztve, a **Start** gomb jelenik meg.  A számítási erőforrások újraindításához kattintson az **Indítás** gombra.

3. Ha el szeretné távolítani az SQL-készletet, hogy ne legyen kiszámítva a számítás vagy a tárterület, kattintson a **Törlés**gombra.

4. A létrehozott SQL-kiszolgáló eltávolításához kattintson a **sqlpoolservername.database.Windows.net**elemre, majd a **Törlés**elemre.  A törléssel bánjon óvatosan, mivel a kiszolgálóval együtt a hozzá rendelt összes adatbázis is törölve lesz.

5. Az erőforráscsoport törléséhez kattintson a **myResourceGroup** elemre, majd az **Erőforráscsoport törlése** parancsra.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az SQL-készletről, folytassa az [adatgyűjtés az SQL-készletbe](load-data-from-azure-blob-storage-using-polybase.md) című cikket. A számítási képességek kezelésével kapcsolatos további információkért tekintse meg a [számítás áttekintését](sql-data-warehouse-manage-compute-overview.md) ismertető cikket.
