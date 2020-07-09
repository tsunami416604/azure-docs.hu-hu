---
title: 'Rövid útmutató: a munkaterhelés elkülönítésének konfigurálása – portál'
description: A számítási feladatok elkülönítésének konfigurálásához használja a Azure Portal.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 05/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 30862a0c16995e143df72f2a243419819941f54e
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85213040"
---
# <a name="quickstart-configure-synapse-sql-pool-workload-isolation-using-a-workload-group-in-the-azure-portal"></a>Rövid útmutató: a szinapszis SQL-készlet számítási feladatainak elkülönítése munkaterhelési csoport használatával a Azure Portalban

Ebben a rövid útmutatóban a [munkaterhelés elkülönítését](sql-data-warehouse-workload-isolation.md) úgy konfigurálja, hogy létrehoz egy munkaterhelés-csoportot az erőforrások megőrzéséhez.  Ebben az oktatóanyagban a meghívott adatok betöltése munkaterhelési csoportot hozunk létre `DataLoads` . A munkaterhelés-csoport a rendszererőforrások 20%-át fogja lefoglalni.  Az adatterhelések 20%-os elkülönítése esetén azok garantált erőforrások, amelyek lehetővé teszik számukra, hogy kitalálják a SLA-kat.  A munkaterhelési csoport létrehozása után [hozzon létre egy](quickstart-create-a-workload-classifier-portal.md) számítási feladattal rendelkező számítási feladatot, amely lekérdezéseket rendel ehhez a munkaterhelés-csoporthoz


Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot.


## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

> [!NOTE]
> Ha SQL Pool-példányt hoz létre az Azure szinapszis Analytics szolgáltatásban, akkor egy új számlázható szolgáltatást eredményezhet.  További információ: az [Azure szinapszis Analytics díjszabása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató azt feltételezi, hogy már rendelkezik egy SQL Pool-példánnyal a szinapszis SQL-ben, és hogy rendelkezik az adatbázis VEZÉRLÉSéhez szükséges engedélyekkel. Ha létre kell hoznia egyet, kövesse a [Létrehozás és csatlakozás – portál](create-data-warehouse-portal.md) útmutatót egy **mySampleDataWarehouse** nevű adattárház létrehozásához.

>[!IMPORTANT] 
>A számítási feladatok kezelésének konfigurálásához az SQL-készletnek online állapotban kell lennie. 

## <a name="configure-workload-isolation"></a>Munkaterhelés elkülönítésének konfigurálása
Az SQL-készlet erőforrásai elkülöníthetők és meghatározott számítási feladatokhoz vannak lefoglalva, munkaterhelési csoportok létrehozásával.  Tekintse meg a munkaterhelés [elkülönítési](sql-data-warehouse-workload-isolation.md) koncepciójának dokumentációját, ahol további információt talál arról, hogy a munkaterhelés-csoportok hogyan segítik a számítási feladatok kezelését.  A [create and összekapcsolás-Portal](create-data-warehouse-portal.md) rövid útmutatója létrehozta a **mySampleDataWarehouse** , és inicializálta azt 400 DWU. A következő lépésekkel hozhat létre munkaterhelés-csoportot a **mySampleDataWarehouse**-ben.

Munkaterhelés-csoport létrehozása 20%-os elkülönítéssel:
1.  Az Azure Portal bal oldalán kattintson az **Azure szinapszis Analytics (korábban SQL DW)** elemre.
2.  Válassza a **mySampleDataWarehouse** lehetőséget az **Azure szinapszis Analytics (korábban SQL DW)** lapon. Megnyílik az SQL-készlet.
3.  Kattintson a **munkaterhelés-kezelés**elemre.
4.  Kattintson az **új munkaterhelés csoport**elemre.
5.  Kattintson az **Egyéni**elemre.

    ![Kattintson az egyéni elemre.](./media/quickstart-configure-workload-isolation-portal/create-wg.png)

6.  Adja meg `DataLoads` a **munkaterhelés csoportot**.
7.  Adja meg `20` a **(z)%. erőforrások minimális**értékeit.
8.  Adja meg `5` a **(z)%-os minimális erőforrások számát**.
9.  Adja meg `100` a **Cap-erőforrások%**-át.
10.   Kattintson a **Save** (Mentés) gombra.

   ![Kattintson a Save (Mentés) gombra.](./media/quickstart-configure-workload-isolation-portal/configure-wg.png)

A munkaterhelési csoport létrehozásakor megjelenik egy portál értesítése.  A munkaterhelési csoport erőforrásai a konfigurált értékek alatt jelennek meg a diagramokon.

   ![Kattintson a végleges gombra](./media/quickstart-configure-workload-isolation-portal/display-wg.png)

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Az `DataLoads` oktatóanyagban létrehozott munkaterhelés-csoport törlése:
1. Kattintson a **`...`** `DataLoads` munkaterhelés csoport jobb oldalán.
2. Kattintson a **munkaterhelési csoport törlése**elemre.
3. Kattintson az **Igen** gombra, amikor a rendszer felszólítja a munkaterhelés-csoport törlésének megerősítésére.
4. Kattintson a **Save (Mentés**) gombra.

   ![Kattintson a Törlés gombra](./media/quickstart-configure-workload-isolation-portal/delete-wg.png)



Az adattárház-egységek és az adattárházban tárolt adatforgalomért kell fizetnie. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva.

- Ha szeretné az adatokat megtartani a tárolóban, a számítási erőforrásokat szüneteltetheti, amíg nem használja az adattárházat. A számítás felfüggesztésével csak az adattárolás díját számítjuk fel. Ha készen áll az adatok feldolgozására, folytassa a számítást.
- Ha szeretné megelőzni a jövőbeli kiadásokat, az adattárházat törölheti is.

Az erőforrások tisztításához kövesse az alábbi lépéseket.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és válassza ki az adattárházat.

    ![Erőforrások felszabadítása](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. A számítás szüneteltetéséhez kattintson a **szüneteltetés** gombra. Ha az adattárház szüneteltetve van, az **Indítás** gomb látható.  A számítás folytatásához kattintson a **Start**gombra.

3. Ha el szeretné távolítani az adattárházat, hogy ne számítsa ki a számítási és a tárolási díjat, válassza a **Törlés**lehetőséget.

4. A létrehozott SQL-kiszolgáló eltávolításához válassza a **sqlpoolservername.database.Windows.net** lehetőséget az előző képen, majd válassza a **Törlés**lehetőséget.  A törléssel bánjon óvatosan, mivel a kiszolgálóval együtt a hozzá rendelt összes adatbázis is törölve lesz.

5. Az erőforráscsoport eltávolításához válassza a **myResourceGroup**lehetőséget, majd válassza az **erőforráscsoport törlése**lehetőséget.

## <a name="next-steps"></a>További lépések

A `DataLoads` munkaterhelés csoport használatához létre [kell](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) hoznia egy számítási feladatot, amely a kérelmeknek a munkaterhelés-csoportba való továbbításához szükséges.  Folytassa a számítási [feladatok besorolása](quickstart-create-a-workload-classifier-portal.md) oktatóanyagot a számítási feladatok besorolásának létrehozásához `DataLoads` .

## <a name="see-also"></a>Lásd még
A számítási feladatok kezelésével kapcsolatos további információkért tekintse meg a számítási feladatok kezelésével [és figyelésével](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) kapcsolatos cikket.
