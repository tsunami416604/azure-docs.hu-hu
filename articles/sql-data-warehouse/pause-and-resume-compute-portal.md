---
title: 'Gyors útmutató: & szüneteltetésének folytatása – Azure Portal '
description: A Azure Portal használatával szüneteltetheti az SQL-készlet számítási felszámítását a költségek megtakarítása érdekében. Ha készen áll az adattárház használatára, folytassa a számítást.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 49684e6844c2d9f58e9b750b12991428218e4426
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200618"
---
# <a name="quickstart-pause-and-resume-compute-in-azure-synapse-analytics-sql-pool-in-the-azure-portal"></a>Rövid útmutató: a számítási feladat szüneteltetése és folytatása az Azure szinapszis Analytics SQL-készletben a Azure Portal

A Azure Portal használatával szüneteltetheti az SQL-készlet számítási felszámítását a költségek megtakarítása érdekében. Ha készen áll az adattárház használatára, [folytassa a számítást](sql-data-warehouse-manage-compute-overview.md) .

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Előkészületek

A [create és a összekapcsolás-Portal](create-data-warehouse-portal.md) használatával hozzon létre egy **mySampleDataWarehouse**nevű SQL-készletet. 

## <a name="pause-compute"></a>Számítás szüneteltetése

A költségek megtakarítása érdekében szüneteltetheti és folytathatja a számítási erőforrások igény szerinti szüneteltetését. Ha például nem fogja használni az adatbázist az éjszaka és a hétvégén, szüneteltetheti ezeket az időpontokban, és a nap folyamán folytathatja. A számítási erőforrásokért nem számítunk fel díjat, amíg az adatbázis szüneteltetve van. Azonban továbbra is a tárterületért kell fizetnie. 

Az SQL-készlet szüneteltetéséhez kövesse az alábbi lépéseket.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).
2. Kattintson az **Azure szinapszis Analytics (korábban SQL DW)** elemre a Azure Portal bal oldali navigációs oldalán.
2. Az SQL-készlet megnyitásához válassza a **mySampleDataWarehouse** lehetőséget az **Azure szinapszis Analytics (korábban SQL DW)** lapon. 
3. A **mySampleDataWarehouse** lapon a figyelmeztetés állapota **online** **állapotú** .

    ![Számítás online](media/pause-and-resume-compute-portal/compute-online.png)

4. Az SQL-készlet szüneteltetéséhez kattintson a **szüneteltetés** gombra. 
5. A folytatáshoz egy megerősítést kérő kérdés jelenik meg. Kattintson a **Yes** (Igen) gombra.
6. Várjon néhány percet, és figyelje meg, hogy az állapot **szünetel**.

    ![Felfüggesztése](media/pause-and-resume-compute-portal/pausing.png)

7. A szüneteltetési művelet befejezésekor a rendszer **szünetelteti** az állapotot, és a beállítás gombja **folytatódik**.
8. Az SQL-készlet számítási erőforrásai mostantól offline állapotban vannak. A számítási feladatokért nem számítunk fel díjat, amíg újra nem folytatja a szolgáltatást.

    ![Számítás offline](media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Számítás folytatása

Az SQL-készlet folytatásához kövesse az alábbi lépéseket.

1. Az Azure Portal bal oldalán kattintson az **Azure szinapszis Analytics (korábban SQL DW)** elemre.
2. Az SQL-készlet lap megnyitásához válassza a **mySampleDataWarehouse** lehetőséget az **Azure szinapszis Analytics (korábban SQL DW)** lapon. 
3. A **mySampleDataWarehouse** lapon a figyelmeztetés **állapota** **szünetel**.

    ![Számítás offline](media/pause-and-resume-compute-portal/compute-offline.png)

4. Az SQL-készlet folytatásához kattintson a **Folytatás**gombra. 
5. Egy megerősítő kérdés jelenik meg, amely megkérdezi, hogy szeretné-e elindítani. Kattintson a **Yes** (Igen) gombra.
6. Figyelje meg, hogy az **állapot** **folytatása**folyamatban van.

    ![Folytatás](media/pause-and-resume-compute-portal/resuming.png)

7. Ha az SQL-készlet ismét online állapotba kerül, az állapota **online** állapotban van, a választókapcsoló pedig **szünetel**.
8. Az SQL-készlet számítási erőforrásai online állapotban vannak, és használhatja a szolgáltatást. A számítási költségek újraindultak.

    ![Számítás online](media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az adatraktár-egységek és az adattárházban tárolt adatforgalomért kell fizetnie. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva. 

- Ha meg szeretné őrizni az adatok tárolását, szüneteltetheti a számítást.
- Ha el szeretné távolítani a jövőbeli díjakat, törölheti az SQL-készletet. 

Kövesse az alábbi lépéseket a fölöslegessé vált erőforrások eltávolítására.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és kattintson az SQL-készletre.

    ![Az erőforrások eltávolítása](media/pause-and-resume-compute-portal/clean-up-resources.png)

1. A számítási erőforrások szüneteltetéshez kattintson a **Szüneteltetés** gombra. 

2. Ha el szeretné távolítani az SQL-készletet, hogy ne legyen kiszámítva a számítás vagy a tárterület, kattintson a **Törlés**gombra.

3. A létrehozott SQL-kiszolgáló eltávolításához kattintson a **sqlpoolservername.database.Windows.net**elemre, majd a **Törlés**elemre.  A törléssel bánjon óvatosan, mivel a kiszolgálóval együtt a hozzá rendelt összes adatbázis is törölve lesz.

4. Az erőforráscsoport törléséhez kattintson a **myResourceGroup** elemre, majd az **Erőforráscsoport törlése** parancsra.


## <a name="next-steps"></a>További lépések

Ezzel szüneteltette és folytatta a számítást az SQL-készlethez. A következő lépésekhez folytassa a betöltési oktatóanyagot.

> [!div class="nextstepaction"]
> [Betöltés az SQL-készletbe](load-data-from-azure-blob-storage-using-polybase.md)
