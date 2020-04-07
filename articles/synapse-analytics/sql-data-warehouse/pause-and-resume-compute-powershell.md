---
title: A számítás szüneteltetése és folytatása a Synapse SQL-készletében az Azure PowerShell segítségével
description: Az Azure PowerShell segítségével szüneteltetheti és folytathatja a Synapse SQL-készlet (adattárház). erőforrások at.
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
ms.openlocfilehash: dc56fe8cffa72c61b3f29f8b0fb8108547395f9c
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742781"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-with-azure-powershell"></a>Rövid útmutató: A számítás felfüggesztése és folytatása a Synapse SQL-készletében az Azure PowerShell segítségével

Az Azure PowerShell segítségével szüneteltetheti és folytathatja a Synapse SQL-készlet (adattárház) számítási erőforrások.
Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a rövid útmutató feltételezi, hogy már rendelkezik egy SQL-készlet, amely szüneteltetheti és folytathatja. Ha létre kell hoznia egyet, a [Create and Connect - portal](create-data-warehouse-portal.md) segítségével létrehozhat egy **mySampleDataWarehouse**nevű SQL-készletet.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be Azure-előfizetésébe a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) paranccsal, és kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzAccount
```

A használt előfizetés megtekintéséhez futtassa a [Get-AzSubscription futtassa.](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

```powershell
Get-AzSubscription
```

Ha az alapértelmezettnél eltérő előfizetést kell használnia, futtassa a [Set-AzContext programot.](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-sql-pool-information"></a>SQL-készletadatainak lekérdezése

Keresse meg a szüneteltetni és folytatni kívánt SQL-készlet adatbázisnevét, kiszolgálónevét és erőforráscsoportját.

Az SQL-készlet helyadatainak megkereséséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
1. Kattintson az **Azure Synapse Analytics (korábbi SQL DW)** elemre az Azure Portal bal oldalán.
1. Válassza ki a **mySampleDataWarehouse** az **Azure Synapse Analytics (korábban SQL DW)** lap. Megnyílik az SQL-készlet.

    ![Kiszolgálónév és erőforráscsoport](./media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. Írja le az SQL-készlet nevét, amely az adatbázis neve. Írja fel a kiszolgáló nevét és az erőforráscsoportot is.
1. Csak a kiszolgálónév első részét használja a PowerShell-parancsmagokban. Az előző lemezképben a teljes kiszolgálónév sqlpoolservername.database.windows.net. Az **sqlpoolservername-t** használjuk a PowerShell-parancsmagban a kiszolgáló neveként.

## <a name="pause-compute"></a>A számítás szüneteltetése

A költségek csökkentése érdekében szüneteltetheti és folytathatja a számítási erőforrásokat igény szerint. Ha például nem használja az adatbázist éjszaka és hétvégén, szüneteltetheti azt ezekben az időszakokban, és a nap folyamán folytathatja.

>[!NOTE]
>Az adatbázis szüneteltetése alatt a számítási erőforrásokért nem számítunk fel díjat. Azonban továbbra is díjat számítunk fel a tárolásért.

Adatbázis szüneteltetéséhez használja a [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) parancsmagát. A következő példa egy **sqlpoolservername**nevű kiszolgálón tárolt **mySampleDataWarehouse** nevű SQL-készletszünetét állítja elő. A kiszolgáló egy **myResourceGroup**nevű Azure-erőforráscsoportban található.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "nsqlpoolservername" –DatabaseName "mySampleDataWarehouse"
```

A következő példa beolvassa az adatbázist a $database objektumba. Ezután leadja az objektumot [a Suspend-AzSqlDatabase elemre.](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Az eredmények az objektum eredményébentárolódnakDatabase. A végső parancs az eredményeket mutatja.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="resume-compute"></a>Számítás folytatása

Adatbázis indításához használja a [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) parancsmagát. A következő példa egy **sqlpoolservername**nevű kiszolgálón található **mySampleDataWarehouse** nevű adatbázist indít el. A kiszolgáló egy **myResourceGroup**nevű Azure-erőforráscsoportban található.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

A következő példa beolvassa az adatbázist a $database objektumba. Ezután leadja az objektumot a [Resume-AzSqlDatabase könyvtárba,](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) és az eredményeket $resultDatabase tárolja. A végső parancs az eredményeket mutatja.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-sql-pool-operation"></a>Az SQL-készlet művelet állapotának ellenőrzése

Az SQL-készlet állapotának ellenőrzéséhez használja a [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/Get-AzSqlDatabaseActivity#description?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) parancsmag.

```Powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az adattárház egységek és az SQL-készlet tárolt adatok után díjat számítunk fel. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva.

- Ha az adatokat a tárolóban szeretné tartani, szüneteltesse a számítást.
- Ha el szeretné távolítani a jövőbeli díjakat, törölheti az SQL-készletet.

Kövesse az alábbi lépéseket a fölöslegessé vált erőforrások eltávolítására.

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és kattintson az SQL-készletre.

    ![Az erőforrások eltávolítása](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. A számítási erőforrások szüneteltetéshez kattintson a **Szüneteltetés** gombra. Az SQL-készlet szüneteltetésekor megjelenik egy **Start** gomb.  A számítási erőforrások újraindításához kattintson az **Indítás** gombra.

3. Ha el szeretné távolítani az SQL-készletet, hogy ne kelljen fizetnie a számításért vagy a tárolásért, kattintson a **Törlés gombra.**

4. A létrehozott SQL-kiszolgáló eltávolításához kattintson **sqlpoolservername.database.windows.net**gombra, majd a **Törlés gombra.**  A törléssel bánjon óvatosan, mivel a kiszolgálóval együtt a hozzá rendelt összes adatbázis is törölve lesz.

5. Az erőforráscsoport törléséhez kattintson a **myResourceGroup** elemre, majd az **Erőforráscsoport törlése** parancsra.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az SQL-készletről, folytassa az [Adatok betöltése az SQL-készletbe](load-data-from-azure-blob-storage-using-polybase.md) cikkhez. A számítási képességek kezeléséről további információt a [Számítási áttekintés kezelése](sql-data-warehouse-manage-compute-overview.md) című cikkben talál.
