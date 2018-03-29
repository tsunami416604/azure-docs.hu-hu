---
title: Adatok másolása az Azure Adatok másolása eszközével | Microsoft Docs
description: Létrehozhat egy Azure-beli adat-előállítót, majd az Adatok másolása eszközzel másolhat adatokat egy Azure Blob Storage-ból egy SQL Database-be.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: d2f1d089c6a08a1dc90f82fd9d1c3cb2b6f6dc0a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Adatok másolása az Azure Blob Storage-ból egy SQL Database-be az Adatok másolása eszközzel
> [!div class="op_single_selector" title1="Select the version of the Data Factory service that you're using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [2. verzió – Előzetes verzió](tutorial-copy-data-tool.md)

Ebben az oktatóanyagban egy adat-előállítót hoz létre az Azure Portal használatával. Ezután az Adatok másolása eszközzel létrehoz egy folyamatot, amely adatokat másol át az Azure Blob Storage-ból egy SQL Database-be. 

> [!NOTE]
> Ha még csak ismerkedik az Azure Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](introduction.md) ismertető cikket.
>
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, tekintse át a [Data Factory 1. verzió használatának első lépéseit](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása az Adatok másolása eszközzel.
> * A folyamat és a tevékenységek futásának monitorozása

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* **Azure Storage-fiók**: Használja a Blob Storage-ot _forrásadattárként_. Ha még nem rendelkezik Azure Storage-fiókkal, a szükséges utasításokat a [Storage-fiók létrehozását](../storage/common/storage-create-storage-account.md#create-a-storage-account) ismertető cikkben találja.
* **Azure SQL Database**: Használjon egy SQL-adatbázist _fogadóadattárként_. Ha még nem rendelkezik SQL-adatbázissal, a szükséges utasításokat az [SQL-adatbázis létrehozását](../sql-database/sql-database-get-started-portal.md) ismertető cikkben találja.

### <a name="create-a-blob-and-a-sql-table"></a>Blob és SQL-tábla létrehozása

Készítse elő a Blob Storage-ot és az SQL-adatbázist az oktatóanyaghoz a következő lépésekkel.

#### <a name="create-a-source-blob"></a>Forrás blob létrehozása

1. Indítsa el a **Jegyzettömböt**. Másolja be a következő szöveget, és mentse a lemezen egy **inputEmp.txt** nevű fájlba:

    ```
    John|Doe
    Jane|Doe
    ```

2. Hozzon létre egy **adfv2tutorial** nevű tárolót és töltse fel az inputEmp.txt fájlt a tárolóba. Ennek elvégzéséhez különböző eszközök állnak rendelkezésére, például az [Azure Storage Explorer](http://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Fogadó SQL-tábla létrehozása

1. A következő SQL-szkripttel hozza létre a **dbo.emp** nevű táblát az SQL-adatbázisban:

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

2. Engedélyezze az SQL Server elérését az Azure-szolgáltatások számára. Ellenőrizze, hogy az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítás engedélyezve van-e az SQL Servert futtató kiszolgálón. Ezzel a beállítással engedélyezheti, hogy a Data Factory adatokat írjon az SQL Server-példányra. A beállítás ellenőrzéséhez és bekapcsolásához hajtsa végre a következő lépéseket:

    a. A bal oldalon válassza ki a **További szolgáltatások**, majd az **SQL-kiszolgálók** elemet.

    b. Válassza ki a kiszolgálót, majd kattintson **BEÁLLÍTÁSOK** > **Tűzfal** elemre.

    c. A **Tűzfalbeállítások** lapon állítsa az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítást **BE** értékre.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben kattintson az **+ Új** > **Adatok és analitika** > **Data Factory** elemre: 
   
   ![Új adat-előállító létrehozása](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket. 
      
     ![Új adat-előállító](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Az adat-előállító nevének _globálisan egyedinek_ kell lennie. A következő hibaüzenet jelenhet meg:
   
   ![Új adat-előállító hibaüzenete](./media/tutorial-copy-data-tool/name-not-available-error.png)

   Ha a név értékével kapcsolatos hibaüzenet kap, adjon meg másik nevet az adat-előállítóhoz. Például: _**sajátneve**_**ADFTutorialDataFactory**. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
3. Válassza ki az Azure-**előfizetést** az új adat-előállító létrehozásához. 
4. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:
     
    a. Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot.

    b. Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét. 
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/resource-group-overview.md) ismertető cikket.

5. A **Verzió** résznél válassza a **V2 (előzetes verzió)** értéket.
6. A **Hely** alatt válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (például Azure Storage és SQL Database) és számítási erőforrások (például Azure HDInsight) más helyeken/régiókban is lehetnek.
7. Válassza a **Rögzítés az irányítópulton** lehetőséget. 
8. Kattintson a **Létrehozás** gombra.
9. Az irányítópulton a folyamat állapotát az **Adat-előállító üzembe helyezése** csempe jelzi.

    ![Adat-előállító üzembe helyezése csempe](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. A létrehozás befejezése után a **Data Factory** kezdőlapja jelenik meg.
   
    ![Data factory kezdőlap](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Az Azure Data Factory felhasználói felületének (UI) külön lapon történő megnyitásához kattintson a **Tartalomkészítés és monitorozás** csempére. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Folyamat létrehozása az Adatok másolása eszközzel

1. Az Adatok másolása eszköz elindításához az **első lépéseket** ismertető oldalon kattintson az **Adatok másolása** csempére. 

   ![Az Adatok másolása eszköz csempéje](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
2. A **Tulajdonságok** lap **Feladat neve** részénél adja meg a következőt: **CopyFromBlobToSqlPipeline**. Ezután kattintson a **Tovább** gombra. A Data Factory felhasználói felülete létrehoz egy folyamatot a megadott feladatnéven. 

    ![Tulajdonságok lap](./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png)
3. A **Forrásadattár** lapon válassza ki az **Azure Blob Storage** elemet, majd kattintson a **Tovább** gombra. A forrásadatok a Blob Storage-ban találhatók. 

    ![Forrásadattár lap](./media/tutorial-copy-data-tool/source-data-store-page.png)
4. **Az Azure Blob Storage-fiók megadása** oldalon hajtsa végre a következő lépéseket:

    a. A **Kapcsolat neve** mezőben adja meg az **AzureStorageLinkedService** nevet.

    b. Válassza ki saját **tárfiókjának nevét** a legördülő listából.

    c. Kattintson a **Tovább** gombra. 

    ![A tárfiók megadása](./media/tutorial-copy-data-tool/specify-blob-storage-account.png)

    Egy társított szolgáltatás egy adattárat vagy számítási erőforrást kapcsol az adat-előállítóhoz. Ebben az esetben létrehoz egy Storage-beli társított szolgáltatást, amely a tárfiókot társítja az adat-előállítóhoz. A társított szolgáltatás azon kapcsolatadatokkal rendelkezik, amelyeket a Data Factory használ futtatáskor a Blob Storage-hoz való kapcsolódáshoz. Az adatkészlet megadja a forrásadatokat tartalmazó tárolót, mappát és fájlt (ez utóbbi nem kötelező). 

5. **A bemeneti fájl vagy mappa kiválasztása** oldalon kövesse az alábbi lépéseket:
    
    a. Keresse meg az **adfv2tutorial/input** mappát.

    b. Kattintson az **inputEmp.txt** fájlra.

    c. Kattintson a **Kiválasztás** elemre. Alternatív megoldásként kattinthat duplán is az **inputEmp.txt** fájlra.

    d. Kattintson a **Tovább** gombra. 

    ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-copy-data-tool/choose-input-file-folder.png)

6. A **Fájlformátum beállításai** lapon látható, hogy az eszköz automatikusan észleli az oszlopok és sorok határolókaraktereit. Kattintson a **Tovább** gombra. Ezen a lapon emellett megtekintheti az adatok előnézetét, valamint a bemeneti adatok sémáját is. 

    ![A Fájlformátum beállításai](./media/tutorial-copy-data-tool/file-format-settings-page.png)
7. A **Céladattár** lapon válassza ki az **Azure SQL Database** elemet, majd kattintson a **Tovább** gombra.

    ![Céladattár](./media/tutorial-copy-data-tool/destination-data-storage-page.png)
8. **Az Azure SQL Database megadása** oldalon tegye a következőket: 

    a. A **Kapcsolat neve** mezőben adja meg az **AzureSqlDatabaseLinkedService** nevet.

    b. A **Kiszolgáló neve** mezőben válassza ki az SQL Server-példányát.

    c. Az **Adatbázis neve** mezőben válassza ki az SQL Database-adatbázisát.

    d. A **Felhasználónév** mezőben adja meg a felhasználó nevét.

    e. A **Jelszó** mezőben adja meg a felhasználó jelszavát.

    f. Kattintson a **Tovább** gombra. 

    ![Az SQL-adatbázis megadása](./media/tutorial-copy-data-tool/specify-azure-sql-database.png)

    Az adatkészleteket mindig társítani kell egy társított szolgáltatáshoz. A társított szolgáltatás rendelkezik azzal a kapcsolati karakterlánccal, amely használatával a Data Factory a futtatás során kapcsolódik az SQL Database-hez. Az adatkészlet meghatározza azt a tárolót, mappát és fájlt (az utóbbi nem kötelező), ahova a rendszer az adatokat másolja.

9. A **Tábla hozzárendelése** oldalon válassza ki a **[dbo].[emp]** táblát, majd kattintson a **Tovább** gombra. 

    ![Tábla hozzárendelése](./media/tutorial-copy-data-tool/table-mapping-page.png)
10. A **Séma hozzárendelése** oldalon figyelje meg, hogy a bemeneti fájl első és második oszlopa az **emp** tábla **FirstName** és **LastName** oszlopában képeződik le.

    ![Séma hozzárendelése oldal](./media/tutorial-copy-data-tool/schema-mapping-page.png)
11. A **Beállítások** lapon kattintson a **Tovább** gombra. 

    ![Beállítások lap](./media/tutorial-copy-data-tool/settings-page.png)
12. Az **Összefoglalás** lapon tekintse át a beállításokat, majd kattintson a **Tovább** gombra.

    ![Összefoglaló lap](./media/tutorial-copy-data-tool/summary-page.png)
13. A folyamat (feladat) figyeléséhez az **Üzembe helyezés** lapon kattintson a **Monitorozás** elemre.

    ![Üzembe helyezés lap](./media/tutorial-copy-data-tool/deployment-page.png)
14. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. A tevékenységfuttatási részletek megtekintéséhez és a folyamat ismételt futtatásához használható hivatkozások a **Műveletek** oszlopban találhatók. A lista frissítéséhez kattintson a **Frissítés** gombra. 

    ![Folyamatfuttatások monitorozása](./media/tutorial-copy-data-tool/monitor-pipeline-runs.png)
15. A folyamat futtatásához kapcsolódó tevékenységfuttatások megtekintéséhez kattintson a **Tevékenységfuttatások megtekintése** hivatkozásra a **Műveletek** oszlopban. Csak egy (másolási) tevékenység található a folyamatban, ezért csak egy bejegyzést lát. A másolási művelet részleteinek megtekintéséhez válassza a **Műveletek** oszlop **Részletek** hivatkozását (szemüveg ikon). A **Folyamatfuttatások** nézetre való visszaváltáshoz kattintson a fenti **Folyamatok** hivatkozásra. A nézet frissítéséhez válassza a **Frissítés** parancsot. 

    ![Tevékenységfuttatások monitorozása](./media/tutorial-copy-data-tool/monitor-activity-runs.png)
16. A szerkesztő módra való váltáshoz kattintson a bal oldalon található **Szerkesztés** fülre. A szerkesztővel frissítheti a társított szolgáltatásokat, az adatkészleteket és az eszközzel létrehozott folyamatokat. A szerkesztőben megnyitott entitáshoz tartozó JSON-kód megtekintéséhez kattintson a **Kód** elemre. Az entitások Data Factory felhasználói felületen való szerkesztéséről [a jelen oktatóanyag Azure Portal-verziójában](tutorial-copy-data-portal.md) talál további információt.

    ![Szerkesztői lap](./media/tutorial-copy-data-tool/edit-tab.png)
17. Ellenőrizze, hogy a rendszer beszúrta-e az adatokat az SQL-adatbázis **emp** táblájába.

    ![SQL-kimenet ellenőrzése](./media/tutorial-copy-data-tool/verify-sql-output.png)

## <a name="next-steps"></a>További lépések
A példában szereplő folyamat adatokat másol a Blob Storage-ból egy SQL Database-be. Megismerte, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása az Adatok másolása eszközzel.
> * A folyamat és a tevékenységek futásának monitorozása

A következő oktatóanyagra lépve megismerheti az adatok helyszíni rendszerből felhőre való másolásának folyamatát: 

> [!div class="nextstepaction"]
>[Adatok másolása a helyszínről a felhőbe](tutorial-hybrid-copy-data-tool.md)
