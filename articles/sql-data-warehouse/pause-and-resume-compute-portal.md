---
title: "Gyors üzembe helyezés: Szüneteltetése és folytatása számítási az Azure SQL Data Warehouse - Azure-portál |} Microsoft Docs"
description: "Az Azure portál feladatok szüneteltetése számítási az Azure SQL Data Warehouse költségek csökkentése érdekében. Amikor készen áll az adatraktárat, folytathatja a számítást."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 01/23/2018
ms.author: barbkess
ms.openlocfilehash: f8b4e29595c6a71696cf2c4939ad4c6c096a28b5
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="quickstart-pause-and-resume-compute-for-an-azure-sql-data-warehouse-in-the-azure-portal"></a>Gyors üzembe helyezés: Felfüggesztése és folytatása számítási az Azure SQL Data Warehouse az Azure-portálon
Felfüggesztés számítási az Azure SQL Data Warehouse költségek csökkentése érdekében. Amikor készen áll az adatraktárat, folytathatja a számítást.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="before-you-begin"></a>Előkészületek

Használjon [létrehozása és a Connect - portál](create-data-warehouse-portal.md) nevű adatraktár létrehozásához **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Felfüggesztés számítási
Költségek csökkentése érdekében szüneteltetése, és folytassa a számítási erőforrások igény. Például nem fogja használni az adatbázis éjszaka és hétvégén, akkor is ilyen alkalmakkor szünetelteti, és folytatásához azt a nap folyamán. Ön nem számlázni számítási erőforrásokat, amíg az adatbázis fel van függesztve. Azonban Ön továbbra is tárolási számlázni. 

Kövesse az alábbi lépéseket egy SQL data warehouse felfüggesztését.

1. Kattintson a **SQL-adatbázisok** az Azure portál bal oldalán.
2. Válassza ki **mySampleDataWarehouse** a a **SQL-adatbázisok** lap. Ekkor megnyílik az adatraktárba. 
3. Az a **mySampleDataWarehouse** lapon, a közlemény **állapot** van **Online**.

    ![Online számítási](media/pause-and-resume-compute-portal/compute-online.png)

4. Az adatraktár szüneteltethető, kattintson a **szüneteltetése** gombra. 
5. Megerősítő kérdés megkérdezi, folytatja-e. Kattintson a **Yes** (Igen) gombra.
6. Várjon egy kis ideig, és figyelje meg, majd a **állapot** van **felfüggesztése**.

    ![Felfüggesztés](media/pause-and-resume-compute-portal/pausing.png)

7. Ha a szüneteltetési művelete befejeződött, az állapot értéke **felfüggesztve** , és a választógomb megjelenítését **Start**.
8. A számítási erőforrásokat, az adatraktár mostantól offline módban. Ön nem számlázni számítási mindaddig, amíg a szolgáltatás folytatása.

    ![Kapcsolat nélküli számítási](media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Folytatás számítási
Kövesse az alábbi lépéseket egy SQL data warehouse folytatni.

1. Kattintson a **SQL-adatbázisok** az Azure portál bal oldalán.
2. Válassza ki **mySampleDataWarehouse** a a **SQL-adatbázisok** lap. Ekkor megnyílik az adatraktárba. 
3. Az a **mySampleDataWarehouse** lapon, a közlemény **állapot** van **felfüggesztve**.

    ![Kapcsolat nélküli számítási](media/pause-and-resume-compute-portal/compute-offline.png)

4. Az adatraktár folytatásához kattintson **Start**. 
5. Megerősítő kérdés megkérdezi, elindítja-e. Kattintson a **Yes** (Igen) gombra.
6. Figyelje meg a **állapot** van **folytatása**.

    ![Folytatás](media/pause-and-resume-compute-portal/resuming.png)

7. Az adatraktár újra online állapotba kerül, ha az állapot értéke **Online** , és a választógomb megjelenítését **szünet**.
8. A számítási erőforrásokat, az adatraktár most online állapotban, és használhatja a szolgáltatást. A számítási díjakat folytatódik.

    ![Online számítási](media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Adattárházegységek és az adatraktárban tárolt adatok van folyamatban szó. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva. 

- Ha meg szeretné tartani az adatokat a tárhelyen, szüneteltesse a számítási.
- Ha szeretné megelőzni a jövőbeli kiadásokat, az adattárházat törölheti is. 

Kövesse az alábbi lépéseket a fölöslegessé vált erőforrások eltávolítására.

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com), majd kattintson az az adatraktár.

    ![Az erőforrások eltávolítása](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. A számítási erőforrások szüneteltetéshez kattintson a **Szüneteltetés** gombra. Ha az adattárház szüneteltetve van, az **Indítás** gomb látható.  A számítási erőforrások újraindításához kattintson az **Indítás** gombra.

2. Ha el szeretné távolítani az adattárházat, hogy a számítási és tárolási erőforrásokért se kelljen fizetnie, kattintson a **Törlés** parancsra.

3. Kattintson a létrehozott SQL server **mynewserver-20171113.database.windows.net**, és kattintson a **törlése**.  A törléssel bánjon óvatosan, mivel a kiszolgálóval együtt a hozzá rendelt összes adatbázis is törölve lesz.

4. Az erőforráscsoport törléséhez kattintson a **myResourceGroup** elemre, majd az **Erőforráscsoport törlése** parancsra.


## <a name="next-steps"></a>További lépések
Most szünetel, és az adatraktár számítási folytatódik. Ha bővebb információra van szüksége az Azure SQL Data Warehouse-zal kapcsolatban, folytassa az adatok betöltésével foglalkozó oktatóanyaggal.

> [!div class="nextstepaction"]
>[Adatok betöltése az SQL Data Warehouse-okba](load-data-from-azure-blob-storage-using-polybase.md)
