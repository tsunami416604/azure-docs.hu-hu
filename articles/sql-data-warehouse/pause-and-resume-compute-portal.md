---
title: 'Gyors útmutató: Szünet és folytatás compute az Azure SQL Data Warehouse – Azure portal |} A Microsoft Docs'
description: Az Azure SQL Data Warehouse szüneteltetési compute az Azure portal használatával költségeit. Ha az adattárház használatra kész, folytathatja a számítást.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 9c3ed6dd79d6225b38751c910253cfa1f0720d1c
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731976"
---
# <a name="quickstart-pause-and-resume-compute-for-an-azure-sql-data-warehouse-in-the-azure-portal"></a>Gyors útmutató: Az Azure Portalon az Azure SQL Data Warehouse a számítás szüneteltetése és folytatása

Az Azure SQL Data Warehouse szüneteltetési compute az Azure portal használatával költségeit. [Folytathatja a számítást](sql-data-warehouse-manage-compute-overview.md) Ha készen áll az adattárház használata.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="before-you-begin"></a>Előkészületek

Használat [létrehozás és csatlakozás – portál](create-data-warehouse-portal.md) nevű adattárház létrehozásához **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Számítás szüneteltetése

Költségek csökkentése érdekében is szüneteltetése és folytatása a számítási erőforrások igény szerinti. Például ha nem fogja használni az adatbázis az éjszaka és hétvégén, is szüneteltetheti, ilyen alkalmakkor, és folytathatja a nap folyamán. Nem kell fizetnie a számítási erőforrásokat, miközben az adatbázis fel van függesztve. Azonban továbbra is a tárolásért kell fizetnie. 

Kövesse az alábbi lépéseket egy SQL data warehouse felfüggesztése.

1. Az Azure Portal bal oldali paneljén kattintson az **SQL-adatbázisok** elemre.
2. Az **SQL-adatbázisok** lapon jelölje ki a **mySampleDataWarehouse** elemet. Ezzel megnyílik az adattárház. 
3. Az a **mySampleDataWarehouse** lapon látható, **állapot** van **Online**.

    ![Számítási online](media/pause-and-resume-compute-portal/compute-online.png)

4. Felfüggessze az adatraktárat, kattintson a **szüneteltetése** gombra. 
5. Megerősítő kérdés megkérdezi, folytatja-e. Kattintson a **Yes** (Igen) gombra.
6. Várjon néhány pillanatot, és figyelje meg, majd a **állapot** van **Pausing**.

    ![Szüneteltetés](media/pause-and-resume-compute-portal/pausing.png)

7. A pause művelet befejeződése után az állapot értéke **felfüggesztett** , és a választógomb **Start**.
8. A data warehouse a számítási erőforrások immár offline állapotban van. Nem kell fizetnie a számítási mindaddig, amíg a szolgáltatás folytatásához.

    ![Kapcsolat nélküli számítási](media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Számítási folytatása

Kövesse az alábbi lépéseket egy SQL data warehouse folytatásához.

1. Az Azure Portal bal oldali paneljén kattintson az **SQL-adatbázisok** elemre.
2. Az **SQL-adatbázisok** lapon jelölje ki a **mySampleDataWarehouse** elemet. Ezzel megnyílik az adattárház. 
3. Az a **mySampleDataWarehouse** lapon látható, **állapot** van **felfüggesztett**.

    ![Kapcsolat nélküli számítási](media/pause-and-resume-compute-portal/compute-offline.png)

4. Az adatraktár újraindításához kattintson **Start**. 
5. Megerősítő kérdés jelenik meg azzal a kérdéssel, ha el szeretné indítani. Kattintson a **Yes** (Igen) gombra.
6. Figyelje meg a **állapot** van **folytatása**.

    ![Folytatás](media/pause-and-resume-compute-portal/resuming.png)

7. Az adatraktár újra online állapotba kerül, ha az állapot értéke **Online** , és a választógomb **szüneteltetése**.
8. A data warehouse a számítási erőforrások már online állapotban, és használhatja a szolgáltatást. A számítási díjak folytatódik.

    ![Számítási online](media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az adattárházegységek és az adatraktárban tárolt adatok díjkötelesek. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva. 

- Ha szeretné megőrizni az adatokat a tárolás, számítás szüneteltetésére.
- Ha szeretné megelőzni a jövőbeli kiadásokat, az adattárházat törölheti is. 

Kövesse az alábbi lépéseket a fölöslegessé vált erőforrások eltávolítására.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és kattintson az adattárházra.

    ![Az erőforrások eltávolítása](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. A számítási erőforrások szüneteltetéshez kattintson a **Szüneteltetés** gombra. Ha az adattárház szüneteltetve van, az **Indítás** gomb látható.  A számítási erőforrások újraindításához kattintson az **Indítás** gombra.

2. Ha el szeretné távolítani az adattárházat, hogy a számítási és tárolási erőforrásokért se kelljen fizetnie, kattintson a **Törlés** parancsra.

3. A létrehozott SQL-kiszolgáló eltávolításához kattintson **mynewserver-20171113.database.windows.net**, és kattintson a **törlése**.  A törléssel bánjon óvatosan, mivel a kiszolgálóval együtt a hozzá rendelt összes adatbázis is törölve lesz.

4. Az erőforráscsoport törléséhez kattintson a **myResourceGroup** elemre, majd az **Erőforráscsoport törlése** parancsra.


## <a name="next-steps"></a>További lépések

Most fel van függesztve, és az adatraktár számítási folytatódik. Ha bővebb információra van szüksége az Azure SQL Data Warehouse-zal kapcsolatban, folytassa az adatok betöltésével foglalkozó oktatóanyaggal.

> [!div class="nextstepaction"]
> [Adatok betöltése az SQL Data Warehouse-okba](load-data-from-azure-blob-storage-using-polybase.md)
