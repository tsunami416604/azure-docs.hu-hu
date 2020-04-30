---
title: A Azure Portal használatával szüneteltetheti és folytathatja a számítást a szinapszis SQL-készletben
description: A Azure Portal használatával szüneteltetheti az SQL-készlet számítási felszámítását a költségek megtakarítása érdekében. Ha készen áll az adattárház használatára, folytassa a számítást.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 55e3d5bf4fb63c35d484e4a764c7eeb2e2484fcf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80350963"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-via-the-azure-portal"></a>Rövid útmutató: a számítások szüneteltetése és folytatása a szinapszis SQL-készletben a Azure Portal használatával

A Azure Portal használatával szüneteltetheti és folytathatja a szinapszis SQL-készlet (adatraktár) számítási erőforrásait. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="before-you-begin"></a>Előkészületek

A [create és a összekapcsolás-Portal](create-data-warehouse-portal.md) használatával hozzon létre egy **mySampleDataWarehouse**nevű SQL-készletet. 

## <a name="pause-compute"></a>Számítás szüneteltetése

A költségek csökkentése érdekében igény szerint szüneteltetheti és folytathatja a számítási erőforrásokat. Ha például nem fogja használni az adatbázist az éjszaka és a hétvégén, szüneteltetheti ezeket az időpontokban, és a nap folyamán folytathatja. 
>[!NOTE]
>A számítási erőforrásokért nem számítunk fel díjat, amíg az adatbázis szüneteltetve van. Azonban továbbra is a tárterületért kell fizetnie. 

Egy SQL-készlet szüneteltetéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson az **Azure szinapszis Analytics (korábban SQL DW)** elemre a Azure Portal bal oldali navigációs oldalán.
2. Az SQL-készlet megnyitásához válassza a **mySampleDataWarehouse** lehetőséget az **Azure szinapszis Analytics (korábban SQL DW)** lapon. 
3. A **mySampleDataWarehouse** lapon a figyelmeztetés állapota **online** **állapotú** .

    ![Számítás online](././media/pause-and-resume-compute-portal/compute-online.png)

4. Az SQL-készlet szüneteltetéséhez kattintson a **szüneteltetés** gombra. 
5. A folytatáshoz egy megerősítést kérő kérdés jelenik meg. Kattintson az **Igen**gombra.
6. Várjon néhány percet, és figyelje meg, hogy **Status** az állapot **szünetel**.

    ![Felfüggesztés](./media/pause-and-resume-compute-portal/pausing.png)

7. A szüneteltetési művelet befejezésekor a rendszer **szünetelteti** az állapotot, és a beállítás gombja **folytatódik**.
8. Az SQL-készlet számítási erőforrásai mostantól offline állapotban vannak. A számítási feladatokért nem számítunk fel díjat, amíg újra nem folytatja a szolgáltatást.

    ![Számítás offline](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Számítás folytatása

Az SQL-készlet folytatásához kövesse az alábbi lépéseket.

1. Az Azure Portal bal oldalán kattintson az **Azure szinapszis Analytics (korábban SQL DW)** elemre.
2. Az SQL-készlet lap megnyitásához válassza a **mySampleDataWarehouse** lehetőséget az **Azure szinapszis Analytics (korábban SQL DW)** lapon. 
3. A **mySampleDataWarehouse** lapon a figyelmeztetés **állapota** **szünetel**.

    ![Számítás offline](././media/pause-and-resume-compute-portal/compute-offline.png)

4. Az SQL-készlet folytatásához kattintson a **Folytatás**gombra. 
5. Egy megerősítő kérdés jelenik meg, amely megkérdezi, hogy szeretné-e elindítani. Kattintson az **Igen**gombra.
6. Figyelje meg, hogy az **állapot** **folytatása**folyamatban van.

    ![Folytatás](./media/pause-and-resume-compute-portal/resuming.png)

7. Ha az SQL-készlet ismét online állapotba kerül, az állapota **online** állapotban van, a választókapcsoló pedig **szünetel**.
8. Az SQL-készlet számítási erőforrásai online állapotban vannak, és használhatja a szolgáltatást. A számítási költségek újraindultak.

    ![Számítás online](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az adatraktár-egységekért és az SQL-készletben tárolt adatforgalomért kell fizetnie. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva. 

- Ha meg szeretné őrizni az adatok tárolását, szüneteltetheti a számítást.
- Ha el szeretné távolítani a jövőbeli díjakat, törölheti az SQL-készletet. 

Kövesse az alábbi lépéseket a fölöslegessé vált erőforrások eltávolítására.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és kattintson az SQL-készletre.

    ![Az erőforrások eltávolítása](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. A számítási erőforrások szüneteltetéshez kattintson a **Szüneteltetés** gombra. 

2. Ha el szeretné távolítani az SQL-készletet, hogy ne legyen kiszámítva a számítás vagy a tárterület, kattintson a **Törlés**gombra.

3. A létrehozott SQL-kiszolgáló eltávolításához kattintson a **sqlpoolservername.database.Windows.net**elemre, majd a **Törlés**elemre.  

   > [!CAUTION]
   > A törléssel bánjon óvatosan, mivel a kiszolgálóval együtt a hozzá rendelt összes adatbázis is törölve lesz.

5. Az erőforráscsoport törléséhez kattintson a **myResourceGroup** elemre, majd az **Erőforráscsoport törlése** parancsra.


## <a name="next-steps"></a>További lépések

Ezzel szüneteltette és folytatta a számítást az SQL-készlethez. A következő cikkből megtudhatja, hogyan [tölthetők be](load-data-from-azure-blob-storage-using-polybase.md)az információk az SQL-készletbe. A számítási képességek kezelésével kapcsolatos további információkért tekintse meg a [számítás áttekintését](sql-data-warehouse-manage-compute-overview.md) ismertető cikket. 

