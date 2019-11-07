---
title: 'Gyors útmutató: & szüneteltetésének folytatása – Azure Portal '
description: A Azure Portal használatával szüneteltetheti a számítást a Azure SQL Data Warehouse a költségek megtakarítása érdekében. Ha készen áll az adattárház használatára, folytassa a számítást.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 14f66f71948f75a723c9fdbed7490d54c2c3e2b2
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693003"
---
# <a name="quickstart-pause-and-resume-compute-for-an-azure-sql-data-warehouse-in-the-azure-portal"></a>Rövid útmutató: a Azure SQL Data Warehouse szüneteltetése és folytatása a Azure Portal

A Azure Portal használatával szüneteltetheti a számítást a Azure SQL Data Warehouse a költségek megtakarítása érdekében. Ha készen áll az adattárház használatára, [folytassa a számítást](sql-data-warehouse-manage-compute-overview.md) .

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="before-you-begin"></a>Előzetes teendők

Hozzon létre egy **mySampleDataWarehouse**nevű adattárházat a [create és a összekapcsolás-Portal](create-data-warehouse-portal.md) használatával. 

## <a name="pause-compute"></a>Számítás szüneteltetése

A költségek megtakarítása érdekében szüneteltetheti és folytathatja a számítási erőforrások igény szerinti szüneteltetését. Ha például nem fogja használni az adatbázist az éjszaka és a hétvégén, szüneteltetheti ezeket az időpontokban, és a nap folyamán folytathatja. A számítási erőforrásokért nem számítunk fel díjat, amíg az adatbázis szüneteltetve van. Azonban továbbra is a tárterületért kell fizetnie. 

A SQL Data Warehouse szüneteltetéséhez kövesse az alábbi lépéseket.

1. Az Azure Portal bal oldali paneljén kattintson az **SQL-adatbázisok** elemre.
2. Az **SQL-adatbázisok** lapon jelölje ki a **mySampleDataWarehouse** elemet. Ezzel megnyílik az adattárház. 
3. A **mySampleDataWarehouse** lapon a figyelmeztetés állapota **online** **állapotú** .

    ![Számítás online](media/pause-and-resume-compute-portal/compute-online.png)

4. Az adatraktár szüneteltetéséhez kattintson a **szüneteltetés** gombra. 
5. A folytatáshoz egy megerősítést kérő kérdés jelenik meg. Kattintson a **Yes** (Igen) gombra.
6. Várjon néhány percet, és figyelje meg, hogy az állapot **szünetel**.

    ![Felfüggesztése](media/pause-and-resume-compute-portal/pausing.png)

7. A szüneteltetési művelet befejezésekor a rendszer **szünetelteti** az állapotot, és **megkezdi**a beállítás gombját.
8. Az adattárház számítási erőforrásai mostantól offline állapotban vannak. A számítási feladatokért nem számítunk fel díjat, amíg újra nem folytatja a szolgáltatást.

    ![Számítás offline](media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Számítás folytatása

SQL Data Warehouse folytatásához kövesse az alábbi lépéseket.

1. Az Azure Portal bal oldali paneljén kattintson az **SQL-adatbázisok** elemre.
2. Az **SQL-adatbázisok** lapon jelölje ki a **mySampleDataWarehouse** elemet. Ezzel megnyílik az adattárház. 
3. A **mySampleDataWarehouse** lapon a figyelmeztetés **állapota** **szünetel**.

    ![Számítás offline](media/pause-and-resume-compute-portal/compute-offline.png)

4. Az adatraktár folytatásához kattintson a **Start**gombra. 
5. Egy megerősítő kérdés jelenik meg, amely megkérdezi, hogy szeretné-e elindítani. Kattintson a **Yes** (Igen) gombra.
6. Figyelje meg, hogy az **állapot** **folytatása**folyamatban van.

    ![Újrakezd](media/pause-and-resume-compute-portal/resuming.png)

7. Amikor az adatraktár online állapotba kerül, az állapota **online** állapotban van, a választókapcsoló pedig **szünetel**.
8. Az adattárház számítási erőforrásai online állapotban vannak, és használhatja a szolgáltatást. A számítási költségek újraindultak.

    ![Számítás online](media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az adatraktár-egységek és az adattárházban tárolt adatforgalomért kell fizetnie. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva. 

- Ha meg szeretné őrizni az adatok tárolását, szüneteltetheti a számítást.
- Ha szeretné megelőzni a jövőbeli kiadásokat, az adattárházat törölheti is. 

Kövesse az alábbi lépéseket a fölöslegessé vált erőforrások eltávolítására.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és kattintson az adattárházra.

    ![Az erőforrások eltávolítása](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. A számítási erőforrások szüneteltetéshez kattintson a **Szüneteltetés** gombra. Ha az adattárház szüneteltetve van, az **Indítás** gomb látható.  A számítási erőforrások újraindításához kattintson az **Indítás** gombra.

2. Ha el szeretné távolítani az adattárházat, hogy a számítási és tárolási erőforrásokért se kelljen fizetnie, kattintson a **Törlés** parancsra.

3. A létrehozott SQL-kiszolgáló eltávolításához kattintson a **mynewserver-20171113.database.Windows.net**elemre, majd a **Törlés**elemre.  A törléssel bánjon óvatosan, mivel a kiszolgálóval együtt a hozzá rendelt összes adatbázis is törölve lesz.

4. Az erőforráscsoport törléséhez kattintson a **myResourceGroup** elemre, majd az **Erőforráscsoport törlése** parancsra.


## <a name="next-steps"></a>További lépések

Ezzel szüneteltette és folytatta a számítást az adattárházban. Ha bővebb információra van szüksége az Azure SQL Data Warehouse-zal kapcsolatban, folytassa az adatok betöltésével foglalkozó oktatóanyaggal.

> [!div class="nextstepaction"]
> [Az adatSQL Data Warehouseba való betöltés](load-data-from-azure-blob-storage-using-polybase.md)
