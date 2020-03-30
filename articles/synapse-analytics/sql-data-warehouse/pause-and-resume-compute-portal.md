---
title: A synapse-i SQL-készletben a számítás szüneteltetése és folytatása az Azure Portalon keresztül
description: Az Azure Portal használatával szüneteltetheti az SQL-készlet kiszámítását a költségek csökkentése érdekében. Folytassa a számítást, ha készen áll az adattárház használatára.
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
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350963"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-via-the-azure-portal"></a>Rövid útmutató: A synapse-i SQL-készletben a számítás felfüggesztése és folytatása az Azure Portalon keresztül

Az Azure Portal segítségével szüneteltetheti és folytathatja a Synapse SQL-készlet (adattárház) számítási erőforrások. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

## <a name="before-you-begin"></a>Előkészületek

A [Create and Connect - portal](create-data-warehouse-portal.md) használatával hozzon létre egy **mySampleDataWarehouse**nevű SQL-készletet. 

## <a name="pause-compute"></a>A számítás szüneteltetése

A költségek csökkentése érdekében szüneteltetheti és folytathatja a számítási erőforrásokat igény szerint. Ha például az adatbázist nem használja éjszaka és hétvégén, szüneteltetheti azt ezekben az időszakokban, és a nap folyamán folytathatja azt. 
>[!NOTE]
>Az adatbázis szüneteltetése alatt nem kell fizetnie a számítási erőforrásokért. Azonban továbbra is díjat számítunk fel a tárolásért. 

Az SQL-készlet szüneteltetéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Kattintson az **Azure Synapse Analytics (korábbi SQL DW)** elemre az Azure Portal bal oldali navigációs lapján.
2. Válassza ki a **mySampleDataWarehouse** az **Azure Synapse Analytics (korábban SQL DW)** lapon az SQL-készlet megnyitásához. 
3. A **mySampleDataWarehouse** lapon az **Állapot** **online**állapot .

    ![Online számítás](././media/pause-and-resume-compute-portal/compute-online.png)

4. Az SQL-készlet szüneteltetéséhez kattintson a **Szünet** gombra. 
5. Megjelenik egy megerősítő kérdés, amely megkérdezi, hogy folytatni kívánja-e. Kattintson **az Igen gombra.**
6. Várjon néhány percet, majd figyelje meg, hogy az **Állapot** **szüneteltetése**.

    ![Felfüggesztés](./media/pause-and-resume-compute-portal/pausing.png)

7. Amikor a szüneteltetési művelet befejeződött, az állapot **Szüneteltetve,** a választógomb pedig **Folytatás**.
8. Az SQL-készlet számítási erőforrásai most antól offline állapotban vannak. A szolgáltatás folytatásáig nem számítunk fel díjat a számításért.

    ![Számítás kapcsolat nélkül](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Számítás folytatása

Az SQL-készlet folytatásához kövesse az alábbi lépéseket.

1. Kattintson az **Azure Synapse Analytics (korábbi SQL DW)** elemre az Azure Portal bal oldalán.
2. Válassza ki a **mySampleDataWarehouse** az **Azure Synapse Analytics (korábban SQL DW)** lapon nyissa meg az SQL-készlet lap. 
3. A **mySampleDataWarehouse** lapon az **állapot** **szüneteltetve**jelenik meg.

    ![Számítás kapcsolat nélkül](././media/pause-and-resume-compute-portal/compute-offline.png)

4. Az SQL-készlet folytatásához kattintson a **Folytatás gombra.** 
5. Megjelenik egy megerősítő kérdés, amely azt kérdezi, hogy el szeretné-e kezdeni. Kattintson **az Igen gombra.**
6. Figyelje meg, hogy az **állapot** **folytatódik.**

    ![Folytatás](./media/pause-and-resume-compute-portal/resuming.png)

7. Amikor az SQL-készlet újra online állapotba kerül, az állapot **online** állapot, a választógomb pedig **Szünet**.
8. Az SQL-készlet számítási erőforrásai már online állapotban vannak, és használhatja a szolgáltatást. A számítási díjak újrakezdődtek.

    ![Online számítás](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az adattárház-egységekért és az SQL-készletben tárolt adatokért díjat számítunk fel. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva. 

- Ha az adatokat a tárolóban szeretné tartani, szüneteltesse a számítást.
- Ha el szeretné távolítani a jövőbeli díjakat, törölheti az SQL-készletet. 

Kövesse az alábbi lépéseket a fölöslegessé vált erőforrások eltávolítására.

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és kattintson az SQL-készletre.

    ![Az erőforrások eltávolítása](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. A számítási erőforrások szüneteltetéshez kattintson a **Szüneteltetés** gombra. 

2. Ha el szeretné távolítani az SQL-készletet, hogy ne kelljen fizetnie a számításért vagy a tárolásért, kattintson a **Törlés gombra.**

3. A létrehozott SQL-kiszolgáló eltávolításához kattintson **sqlpoolservername.database.windows.net**gombra, majd a **Törlés gombra.**  

   > [!CAUTION]
   > A törléssel bánjon óvatosan, mivel a kiszolgálóval együtt a hozzá rendelt összes adatbázis is törölve lesz.

5. Az erőforráscsoport törléséhez kattintson a **myResourceGroup** elemre, majd az **Erőforráscsoport törlése** parancsra.


## <a name="next-steps"></a>További lépések

Most már szüneteltette, és folytatta a számítást az SQL-készlethez. Folytassa a következő cikkel, hogy többet tudjon meg arról, hogyan [tölthet be adatokat az SQL-készletbe.](load-data-from-azure-blob-storage-using-polybase.md) A számítási képességek kezeléséről további információt a [Számítási áttekintés kezelése](sql-data-warehouse-manage-compute-overview.md) című cikkben talál. 

