---
title: "Adatok másolása az Azure Adatok másolása eszközével | Microsoft Docs"
description: "Létrehozhat egy Azure-beli adat-előállítót, majd az Adatok másolása eszközzel másolhat adatokat egy Azure Blob Storage-ból egy Azure SQL Database-be."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: a8c7035ebf462bb168147e9d8eb60742333ce8b8
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-copy-data-tool"></a>Adatok másolása az Azure Blobból az Azure SQL Database-be az Adatok másolása eszköz használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [2. verzió – Előzetes verzió](tutorial-copy-data-tool.md)

Ebben az oktatóanyagban egy adat-előállítót hoz létre az Azure Portal használatával. Ezután az Adatok másolása eszközzel létrehoz egy folyamatot, amely adatokat másol át egy Azure Blob Storage-ból egy Azure SQL Database-be. 

> [!NOTE]
> - Ha még csak ismerkedik az Azure Data Factory szolgáltatással: [Bevezetés az Azure Data Factory használatába](introduction.md).
>
> - Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd a [Data Factory 1. verzió használatának első lépéseit](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása az Adatok másolása eszközzel
> * A folyamat és a tevékenységek futásának monitorozása

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.
* **Azure Storage-fiók** A blobtárolót használjuk **forrás** adattárként. Ha még nem rendelkezik Azure Storage-fiókkal, a létrehozás folyamatáért lásd a [tárfiók létrehozását](../storage/common/storage-create-storage-account.md#create-a-storage-account) ismertető cikket.
* **Azure SQL Database** Ezt az adatbázist használjuk **fogadó** adattárként. Ha még nem rendelkezik Azure SQL Database-adatbázissal, a létrehozás folyamatáért lásd az [Azure SQL-adatbázis létrehozását](../sql-database/sql-database-get-started-portal.md) ismertető cikket.

### <a name="create-a-blob-and-a-sql-table"></a>Blob és SQL-tábla létrehozása

Készítse elő az Azure Blobot és az Azure SQL Database-t az oktatóanyaghoz a következő lépésekkel:

#### <a name="create-a-source-blob"></a>Forrás blob létrehozása

1. Indítsa el a Jegyzettömböt. Másolja be a következő szöveget, és mentse **inputEmp.txt** néven a lemezen.

    ```
    John|Doe
    Jane|Doe
    ```

2. Az [Azure Storage Explorerrel](http://storageexplorer.com/) vagy egy hasonló eszközzel hozza létre az **adfv2tutorial** tárolót, és töltse fel az **inputEmp.txt** fájlt a tárolóba.

#### <a name="create-a-sink-sql-table"></a>Fogadó SQL-tábla létrehozása

1. A következő SQL-szkripttel hozza létre a **dbo.emp** táblát az Azure SQL Database-ben.

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

2. Engedélyezze az SQL Server elérését az Azure-szolgáltatások számára. Győződjön meg arról, hogy az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítás engedélyezve van az Azure SQL Serverhez, hogy a Data Factory szolgáltatás adatokat tudjon írni az Azure SQL Serverre. A beállítás ellenőrzéséhez és bekapcsolásához hajtsa végre a következő lépéseket:

    1. Kattintson a **További szolgáltatások** hubra a bal oldalon, majd az **SQL-kiszolgálók** elemre.
    2. Válassza ki a kiszolgálót, és kattintson a **BEÁLLÍTÁSOK** területen a **Tűzfal** elemre.
    3. A **Tűzfalbeállítások** lapon kattintson a **BE** kapcsolóra az **Azure-szolgáltatások hozzáférésének engedélyezése** beállítás mellett.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. Az **Új adat-előállító** lapon, a **Név** mezőben adja meg a következőt: **ADFTutorialDataFactory**. 
      
     ![Új adat-előállító lap](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Az Azure data factory nevének **globálisan egyedinek** kell lennie. Ha a Név mezőnél az alábbi hiba jelenik meg, módosítsa az adat-előállító nevét (például a következőre: sajátneveADFTutorialDataFactory). A Data Factory-összetevők részleteit a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
     ![Új adat-előállító lap](./media/tutorial-copy-data-tool/name-not-available-error.png)
3. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
4. Az **erőforráscsoportban** hajtsa végre a következő lépések egyikét:
     
      - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot. 
      - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.   
         
      Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
4. Válassza a **V2 (előzetes verzió)** értéket a **verzió** esetén.
5. Válassza ki a Data Factory **helyét**. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más helyeken/régiókban is lehetnek.
6. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
7. Kattintson a **Create** (Létrehozás) gombra.
8. Az irányítópulton megjelenő csempén a következő állapotleírás látható: **Adat-előállító üzembe helyezése**. 

    ![adat-előállító üzembe helyezése csempe](media/tutorial-copy-data-tool/deploying-data-factory.png)
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
   ![Data factory kezdőlap](./media/tutorial-copy-data-tool/data-factory-home-page.png)
10. Az Azure Data Factory felhasználói felületének (UI) külön lapon történő elindításához kattintson a **Létrehozás és monitorozás** csempére. 

## <a name="use-copy-data-tool-to-create-a-pipeline"></a>Folyamat létrehozása az Adatok másolása eszközzel

1. Az Adatok másolása eszköz elindításához az első lépéseket ismertető oldalon kattintson az **Adatok másolása** csempére. 

   ![Az Adatok másolása eszköz csempéje](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
2. Az Adatok másolása eszköz **Tulajdonságok** oldalán a **Feladat neve** mezőben adja meg a **CopyFromBlobToSqlPipeline** értéket, majd kattintson a **Tovább** elemre. A Data Factory felhasználói felülete létrehoz egy folyamatot a megadott feladatnéven. 

    ![Adatok másolása eszköz – Tulajdonságok lap](./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png)
3. A **Forrásadattár** lapon válassza ki az **Azure Blob Storage** elemet, majd kattintson a **Tovább** gombra. A forrásadatok egy Azure Blob Storage-ban találhatók. 

    ![Forrásadattár lap](./media/tutorial-copy-data-tool/source-data-store-page.png)
4. **Az Azure Blob Storage-fiók megadása** lapon tegye a következőket: 
    1. A **kapcsolat nevénél** adja meg az **AzureStorageLinkedService** nevet.
    2. Válassza ki saját **tárfiókjának nevét** a legördülő listából.
    3. Kattintson a **Tovább** gombra. 

        ![Blob Storage-fiók megadása](./media/tutorial-copy-data-tool/specify-blob-storage-account.png)

        Egy társított szolgáltatás egy adattárat vagy számítási erőforrást kapcsol az adat-előállítóhoz. Ebben az esetben létrehoz egy Azure Storage-beli társított szolgáltatást, amely az Azure-tárfiókot társítja az adat-előállítóhoz. A társított szolgáltatás azon kapcsolatadatokkal rendelkezik, amelyeket a Data Factory-szolgáltatások használnak futtatáskor a Blob Storage-hoz való kapcsolódáshoz. Az adatkészlet megadja a forrásadatokat tartalmazó tárolót, mappát és fájlt (ez utóbbi nem kötelező). 
5. **A bemeneti fájl vagy mappa kiválasztása** lapon kövesse az alábbi lépéseket:
    
    1. Nyissa meg az **adfv2tutorial/input** mappát.
    2. Válassza ki az **inputEmp.txt** fájlt.
    3. Kattintson a **Kiválasztás** elemre. Alternatív megoldásként kattinthat duplán is az **inputEmp.txt** fájlra. 
    4. Kattintson a **Tovább** gombra. 

    ![Bemeneti fájl vagy mappa kiválasztása](./media/tutorial-copy-data-tool/choose-input-file-folder.png)
6. A **Fájlformátum beállításai** lapon látható, hogy az eszköz automatikusan észleli az oszlopok és sorok határolókaraktereit. Ezután kattintson a **Tovább** gombra. Ezen a lapon emellett megtekintheti az adatok előnézetét, valamint a bemeneti adatok sémáját is. 

    ![Fájlformátum beállításai lap](./media/tutorial-copy-data-tool/file-format-settings-page.png)
7. A **Céladattár** lapon válassza ki az **Azure SQL Database** csempét, majd kattintson a **Tovább** gombra. 

    ![Céladattár lap](./media/tutorial-copy-data-tool/destination-data-storage-page.png)
8. **Az Azure SQL Database megadása** oldalon tegye a következőket: 

    1. Adja meg az **AzureSqlDatabaseLinkedService** nevet a **Kapcsolat neve** mezőben. 
    2. A **Kiszolgáló neve** mezőnél válassza ki az Azure SQL Server-kiszolgálót.
    3. Az **Adatbázis neve** mezőnél válassza ki az Azure SQL Database-adatbázist.
    4. A **Felhasználónév** mezőben adja meg a felhasználó nevét.
    5. A **Jelszó** mezőben adja meg a felhasználó jelszavát.
    6. Kattintson a **Tovább** gombra. 

        ![Az Azure SQL Database megadása](./media/tutorial-copy-data-tool/specify-azure-sql-database.png)

        Az adatkészleteket mindig társítani kell egy társított szolgáltatáshoz. A társított szolgáltatás rendelkezik azzal a kapcsolati karakterlánccal, amely használatával a Data Factory szolgáltatás a futtatás során kapcsolódik az Azure SQL Database-hez. Az adatkészlet meghatározza azt a tárolót, mappát és fájlt (az utóbbi nem kötelező), ahova a rendszer az adatokat másolja.
9.  A **Tábla hozzárendelése** oldalon válassza a **[dbo].[emp]** elemet, majd kattintson a **Tovább** gombra. 

    ![Tábla hozzárendelése oldal](./media/tutorial-copy-data-tool/table-mapping-page.png)
10. A **Séma hozzárendelése** oldalon figyelje meg, hogy a bemeneti fájl első és második oszlopa az **emp** tábla **FirstName** és **LastName** oszlopában képeződik le. 

    ![Séma hozzárendelése oldal](./media/tutorial-copy-data-tool/schema-mapping-page.png)
11. A **Beállítások** lapon kattintson a **Tovább** gombra. 

    ![Beállítások lap](./media/tutorial-copy-data-tool/settings-page.png)
12. Az **Összefoglalás** lapon tekintse át a beállításokat, és kattintson a **Tovább** elemre.

    ![Összefoglaló lap](./media/tutorial-copy-data-tool/summary-page.png)
13. A folyamat (feladat) figyeléséhez az **Üzembe helyezés** lapon kattintson a **Figyelés** elemre.

    ![Üzembe helyezés lap](./media/tutorial-copy-data-tool/deployment-page.png)
14. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. A tevékenységfuttatási részletek megtekintéséhez és a folyamat ismételt futtatásához használható hivatkozások a **Műveletek** oszlopban találhatók. A lista frissítéséhez kattintson a **Frissítés** elemre. 

    ![Folyamatfuttatások monitorozása](./media/tutorial-copy-data-tool/monitor-pipeline-runs.png)
15. A folyamat futásához társított tevékenységfuttatások megtekintéséhez kattintson a **Tevékenységfuttatások megtekintése** hivatkozásra a **Műveletek** oszlopban. Csak egy (másolási) tevékenység található a folyamatban, ezért csak egy bejegyzést lát. A másolási művelet részleteinek megtekintéséhez kattintson a **Műveletek** oszlop **Részletek** hivatkozására (szemüveg ikon). A Folyamatfuttatások nézetre való visszaváltáshoz kattintson a fent található **Folyamatok** hivatkozásra. A nézet frissítéséhez kattintson a **Frissítés** parancsra. 

    ![Tevékenységfuttatások monitorozása](./media/tutorial-copy-data-tool/monitor-activity-runs.png)
16. A szerkesztő módra való váltáshoz kattintson a bal oldalon található **Szerkesztés** lapra. A szerkesztővel frissítheti a társított szolgáltatásokat, az adatkészleteket és az eszközzel létrehozott folyamatokat. A szerkesztőben megnyitott entitáshoz társított JSON-kód megtekintéséhez kattintson a **Kód** elemre. Az entitások Data Factory felhasználói felületen való szerkesztéséről [a jelen oktatóanyag Azure Portal-verziójában](tutorial-copy-data-portal.md) talál további információt.

    ![Szerkesztői lap](./media/tutorial-copy-data-tool/edit-tab.png)
17. Ellenőrizze, hogy a rendszer beszúrta-e az adatokat az Azure SQL Database **emp** táblájába. 

    ![SQL-kimenet ellenőrzése](./media/tutorial-copy-data-tool/verify-sql-output.png)

## <a name="next-steps"></a>További lépések
Az ebben a mintában található folyamat adatokat másol egy Azure Blob Storage-ból egy Azure SQL Database-be. Megismerte, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása az Adatok másolása eszközzel
> * A folyamat és a tevékenységek futásának monitorozása

Folytassa a következő oktatóanyaggal, amelyben azzal ismerkedhet meg, hogyan másolhat adatokat a helyszíni rendszerből a felhőbe: 

> [!div class="nextstepaction"]
>[Adatok másolása a helyszínről a felhőbe](tutorial-hybrid-copy-data-tool.md)