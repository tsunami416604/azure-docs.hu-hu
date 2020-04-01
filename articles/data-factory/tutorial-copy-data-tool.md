---
title: Adatok másolása az Azure Blob storage-ból az SQL-be az Adatok másolása eszközzel
description: Hozzon létre egy Azure-adat-előállítót, majd az Adatok másolása eszközzel másolja az adatokat az Azure Blob storage-ból egy SQL-adatbázisba.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 03/03/2020
ms.openlocfilehash: 52ed43277eef84de826d2f4fa41ba860211a1531
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78969905"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Adatok másolása az Azure Blob storage-ból egy SQL-adatbázisba az Adatok másolása eszközzel

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuális verzió](tutorial-copy-data-tool.md)

Ebben az oktatóanyagban egy adat-előállítót hoz létre az Azure Portal használatával. Ezután az Adatok másolása eszközzel hozzon létre egy folyamatot, amely adatokat másol az Azure Blob storage-ból egy SQL-adatbázisba.

> [!NOTE]
> Ha még csak ismerkedik az Azure Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](introduction.md) ismertető cikket.

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:
> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása az Adatok másolása eszközzel.
> * A folyamat és a tevékenységek futásának monitorozása

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* **Azure Storage-fiók:** Blob storage használata forrásadattárként. _source_ Ha nem rendelkezik Azure Storage-fiókkal, olvassa el a Tárfiók létrehozása című [témakörutasításait.](../storage/common/storage-account-create.md)
* **Azure SQL Database:** Sql-adatbázis használata _a fogadó_ adattárként. Ha nem rendelkezik SQL-adatbázissal, olvassa el az [SQL-adatbázis létrehozása](../sql-database/sql-database-get-started-portal.md)című témakör utasításait.

### <a name="create-a-blob-and-a-sql-table"></a>Blob és SQL-tábla létrehozása

Készítse elő a Blob storage és az SQL-adatbázis az oktatóanyag ezekkel a lépésekkel.

#### <a name="create-a-source-blob"></a>Forrás blob létrehozása

1. Indítsa el a **Jegyzettömböt**. Másolja be a következő szöveget, és mentse a lemezen egy **inputEmp.txt** nevű fájlba:

    ```
    FirstName|LastName
    John|Doe
    Jane|Doe
    ```

1. Hozzon létre egy **adfv2tutorial** nevű tárolót és töltse fel az inputEmp.txt fájlt a tárolóba. Használhatja az Azure Portalon, vagy különböző eszközök, például [az Azure Storage Explorer](https://storageexplorer.com/) ezeket a feladatokat.

#### <a name="create-a-sink-sql-table"></a>Fogadó SQL-tábla létrehozása

1. A következő SQL-parancsfájl segítségével hozzon létre egy **dbo.emp** nevű táblát az SQL-adatbázisban:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Engedélyezze az SQL Server elérését az Azure-szolgáltatások számára. Ellenőrizze, hogy engedélyezve van-e az **Azure-szolgáltatások és -erőforrások hozzáférése a kiszolgálóhoz** az SQL Database szolgáltatást futtató kiszolgálón. Ezzel a beállítással engedélyezheti, hogy a Data Factory adatokat írjon az adatbázispéldányra. A beállítás ellenőrzéséhez és bekapcsolásához nyissa meg az Azure SQL-kiszolgáló > biztonsági > tűzfalak és virtuális > hálózatok című állítsa be az **Azure-szolgáltatások és -erőforrások hozzáférésének engedélyezése a kiszolgálóhoz** beállítást **BE beállításra.**

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza az **Erőforrás-elemzési** > **Analytics** > **adatgyár**létrehozása lehetőséget:

    ![Új adat-előállító létrehozása](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket.

    Az adat-előállító nevének _globálisan egyedinek_ kell lennie. A következő hibaüzenet jelenhet meg:

    ![Új adat-előállító hibaüzenete](./media/doc-common-process/name-not-available-error.png)

    Ha a név értékével kapcsolatos hibaüzenet kap, adjon meg másik nevet az adat-előállítóhoz. Például: _**sajátneve**_**ADFTutorialDataFactory**. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
1. Válassza ki az Azure-**előfizetést** az új adat-előállító létrehozásához.
1. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:

    a. Válassza **a Meglévő használata**lehetőséget, és válasszon ki egy meglévő erőforráscsoportot a legördülő listából.

    b. Válassza **az Új létrehozása**lehetőséget, és írja be egy erőforráscsoport nevét.
    
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket.

1. A **Verzió** résznél válassza a **V2** értéket.
1. A **Hely** alatt válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (például Azure Storage és SQL Database) és számítási erőforrások (például Azure HDInsight) más helyeken/régiókban is lehetnek.
1. Kattintson a **Létrehozás** gombra.

1. A létrehozás befejezése után a **Data Factory** kezdőlapja jelenik meg.

    ![Data factory kezdőlap](./media/doc-common-process/data-factory-home-page.png)
1. Az Azure Data Factory felhasználói felületének (UI) külön lapon történő megnyitásához kattintson a **Tartalomkészítés és monitorozás** csempére.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Folyamat létrehozása az Adatok másolása eszközzel

1. Az Adatok másolása eszköz elindításához az **első lépéseket** ismertető oldalon kattintson az **Adatok másolása** csempére.

    ![Az Adatok másolása eszköz csempéje](./media/doc-common-process/get-started-page.png)
1. A **Tulajdonságok** lap **Feladat neve** részénél adja meg a következőt: **CopyFromBlobToSqlPipeline**. Ezután válassza a **Tovább**gombot. A Data Factory felhasználói felülete létrehoz egy folyamatot a megadott feladatnéven.
    ![Folyamat létrehozása](./media/tutorial-copy-data-tool/create-pipeline.png)

1. A **Forrásadattár** oldalon hajtsa végre az alábbi lépéseket:

    a. Kapcsolat hozzáadásához kattintson a **+ Új kapcsolat létrehozása** lehetőségre

    b. Válassza az **Azure Blob Storage** elemet a gyűjteményből, majd kattintson a Folytatás **gombra.**

    c. Az **új kapcsolt szolgáltatás** lapon válassza ki az Azure-előfizetést, és válassza ki a tárfiókot a **Storage-fiók** névlistájából. Tesztelje a kapcsolatot, és válassza a **Létrehozás lehetőséget.**

    d. Forrásnak válassza ki az újonnan létrehozott társított szolgáltatást, majd kattintson a **Tovább** gombra.

    ![Forráshoz társított szolgáltatás kiválasztása](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. **A bemeneti fájl vagy mappa kiválasztása** lapon kövesse az alábbi lépéseket:

    a. A **Tallózás** elemre kattintva lépjen az **adfv2tutorial/input** mappához, válassza az **inputEmp.txt** fájlt, majd kattintson a **Kiválasztás** lehetőségre.

    b. A következő lépéshez kattintson a **Tovább** gombra.

1. A **Fájlformátum beállításai** lapon engedélyezze az *Első sor fejlécként jelölőnégyzetét.* Figyelje meg, hogy az eszköz automatikusan észleli az oszlop- és sorhatárolókat. Válassza a **Tovább lehetőséget.** Megtekintheti az adatok előnézetét is, és megtekintheti a bemeneti adatok sémáját ezen az oldalon.

    ![A Fájlformátum beállításai](./media/tutorial-copy-data-tool/file-format-settings-page.png)
1. A **Céladattár** oldalon hajtsa végre a következő lépéseket:

    a. Kapcsolat hozzáadásához kattintson a **+ Új kapcsolat létrehozása** lehetőségre

    b. Válassza az **Azure SQL Database elemet** a gyűjteményből, majd kattintson a Folytatás **gombra.**

    c. Az **Új csatolt szolgáltatás** lapon válassza ki a kiszolgáló nevét és az adatbázis nevét a legördülő listából, majd adja meg a felhasználónevet és a jelszót, majd válassza a **Létrehozás gombot.**

    ![Azure SQL-adatbázis konfigurálása](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

    d. Válassza ki fogadóként az újonnan létrehozott társított szolgáltatást, majd kattintson a **Tovább** gombra.

1. A **Tábla hozzárendelése** oldalon válassza ki a **[dbo].[emp]** táblát, majd kattintson a **Tovább** gombra.

1. Az **Oszlopleképezés** lapon figyelje meg, hogy a bemeneti fájl második és harmadik oszlopa az **emp** tábla **Utónév** és **Vezetéknév** oszlopaihoz lesz leképezve. Állítsa be a leképezést, hogy megbizonyosodjon arról, hogy nincs hiba, majd válassza a **Tovább**gombot.

    ![Oszlopleképezési lap](./media/tutorial-copy-data-tool/column-mapping.png)

1. A **Beállítások** lapon kattintson a **Tovább** gombra.
1. Az **Összefoglalás** lapon tekintse át a beállításokat, majd kattintson a **Tovább** gombra.
1. A folyamat (feladat) figyeléséhez az **Üzembe helyezés** lapon kattintson a **Monitorozás** elemre.
 
    ![Folyamat figyelése](./media/tutorial-copy-data-tool/monitor-pipeline.png)

1. A Folyamat futtatása lapon válassza a **Frissítés** lehetőséget a lista frissítéséhez. Kattintson a **FOLYAMAT NEVE** csoporthivatkozásra a tevékenységfuttatás részleteinek megtekintéséhez vagy a folyamat újbóli futtatásához. 
    ![Folyamat futtatása](./media/tutorial-copy-data-tool/pipeline-run.png)

1. A Tevékenység futtatása lapon válassza a **Részletek** hivatkozást (szemüveg ikon) a **TEVÉKENYSÉG NEVE** oszlopban a másolási művelettel kapcsolatos további részletekért. Ha vissza szeretne lépni a Folyamatfuttatások nézetbe, válassza az **ALL folyamat futtatása** hivatkozást a navigációs menüben. A nézet frissítéséhez válassza a **Frissítés** parancsot.

    ![Tevékenységfuttatások monitorozása](./media/tutorial-copy-data-tool/activity-monitoring.png)


1. Ellenőrizze, hogy az adatok bekerülnek-e az SQL-adatbázis **dbo.emp** táblájába.


1. A szerkesztő módra való váltáshoz kattintson a bal oldalon található **Létrehozás** fülre. A szerkesztővel frissítheti a társított szolgáltatásokat, az adatkészleteket és az eszközzel létrehozott folyamatokat. Az entitások Data Factory felhasználói felületen való szerkesztéséről [a jelen oktatóanyag Azure Portal-verziójában](tutorial-copy-data-portal.md) talál további információt.

    ![Szerző kiválasztása lap](./media/tutorial-copy-data-tool/author-tab.png)

## <a name="next-steps"></a>További lépések
A minta folyamata adatokat másol a Blob storage-ból egy SQL-adatbázisba. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása az Adatok másolása eszközzel.
> * A folyamat és a tevékenységek futásának monitorozása

A következő oktatóanyagra lépve megismerheti az adatok helyszíni rendszerből felhőre való másolásának folyamatát:

>[!div class="nextstepaction"]
>[Adatok másolása a helyszínről a felhőbe](tutorial-hybrid-copy-data-tool.md)
