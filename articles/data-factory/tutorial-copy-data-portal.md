---
title: "Az Azure Portal használata Data Factory-folyamatok létrehozására | Microsoft Docs"
description: "Az oktatóanyag részletes útmutatását követve létrehozhat egy adat-előállítót egy folyamat és az Azure Portal használatával. A folyamat egy másolási tevékenységgel másol adatokat egy Azure Blob Storage-ból egy SQL Database-be."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: 116832175a4b7e4497c9005be7841cb56c1d235b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Adatok másolása az Azure Blob Storage-ból egy SQL Database-be az Azure Data Factory segítségével
Ebben az oktatóanyagban az Azure Data Factory felhasználói felületének használatával hoz létre egy adat-előállítót. Az adat-előállító folyamata adatokat másol az Azure Blob Storage-ból egy SQL Database-be. Az oktatóanyagban szereplő konfigurációs minta fájlalapú adattárból relációs adattárba való másolásra vonatkozik. A forrásként és fogadóként támogatott adattárak listája a [támogatott adattárakat tartalmazó](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban található.

> [!NOTE]
> - Ha még csak ismerkedik a Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](introduction.md) ismertető cikket.
>
> - Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory általánosan elérhető 1. verzióját használja, tekintse meg a [Data Factory 1. verzió használatának első lépéseit](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Másolási tevékenységgel rendelkező folyamat létrehozása.
> * A folyamat próbafuttatása
> * A folyamat manuális aktiválása
> * A folyamat aktiválása ütemezés szerint
> * A folyamat és a tevékenységek futásának monitorozása

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.
* **Egy Azure Storage-fiók**. A Blob Storage lesz használatban *forrás*adattárként. Ha még nem rendelkezik tárfiókkal, tekintse meg az [Azure Storage-fiók létrehozásának](../storage/common/storage-create-storage-account.md#create-a-storage-account) lépéseit ismertető cikket.
* **Azure SQL Database** Ezt az adatbázist használjuk *fogadó*adattárként. Ha még nem rendelkezik SQL-adatbázissal, a létrehozás folyamatáért lásd az [SQL-adatbázis létrehozását](../sql-database/sql-database-get-started-portal.md) ismertető cikket.

### <a name="create-a-blob-and-a-sql-table"></a>Blob és SQL-tábla létrehozása

Készítse elő a Blob Storage-et és az SQL Database-t az oktatóanyaghoz a következő lépésekkel.

#### <a name="create-a-source-blob"></a>Forrás blob létrehozása

1. Indítsa el a Jegyzettömböt. Másolja be a következő szöveget, és mentse **emp.txt** néven egy fájlba a lemezen.

    ```
    John,Doe
    Jane,Doe
    ```

2. Hozzon létre egy **adftutorial** nevű tárolót a Blob Storage-ban. Ebben a tárolóban hozzon létre egy **input** nevű mappát. Ezután töltse fel az **emp.txt** fájlt az **input** mappába. Ezekhez a feladatokhoz használja az Azure Portalt vagy olyan eszközöket, mint az [Azure Storage Explorer](http://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Fogadó SQL-tábla létrehozása

1. A következő SQL-szkripttel hozza létre a **dbo.emp** táblát az SQL Database-ben.

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

2. Engedélyezze az SQL Server elérését az Azure-szolgáltatások számára. Győződjön meg arról, hogy az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** **BE** van kapcsolva az SQL Serverhez, hogy a Data Factory tudjon adatokat írni az SQL Serverre. A beállítás ellenőrzéséhez és bekapcsolásához hajtsa végre a következő lépéseket:

    a. A bal oldalon válassza a **További szolgáltatások** > **SQL-kiszolgálók** elemet.

    b. Válassza ki a kiszolgálóját, majd a **BEÁLLÍTÁSOK** alatt válassza a **Tűzfal** lehetőséget.

    c. A **Tűzfalbeállítások** lapon válassza a **BE** kapcsolót az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítás mellett.

## <a name="create-a-data-factory"></a>Data factory létrehozása
Ebben a lépésben létrehoz egy adat-előállítót, és elindítja a Data Factory felhasználói felületét, hogy létrehozzon egy folyamatot az adat-előállítóban. 

1. Nyissa meg a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
2. A bal oldali menüben válassza az **Új** > **Adatok + analitika** > **Adat-előállító** elemet. 
  
   ![Új adat-előállító létrehozása](./media/tutorial-copy-data-portal/new-azure-data-factory-menu.png)
3. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket. 
      
     ![Új adat-előállító](./media/tutorial-copy-data-portal/new-azure-data-factory.png)
 
   Az Azure data factory nevének *globálisan egyedinek* kell lennie. Ha a Név mezőnél az alábbi hibaüzenet jelenik meg, módosítsa az adat-előállító nevét (például a következőre: sajátneveADFTutorialDataFactory). A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
   ![Hibaüzenet](./media/tutorial-copy-data-portal/name-not-available-error.png)
4. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
5. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:
     
    a. Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot.

    b. Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét. 
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/resource-group-overview.md) ismertető cikket. 
6. A **Verzió** alatt válassza a **V2 (előzetes verzió)** értéket.
7. A **Hely** területen válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (például az Azure Storage és az SQL Database) és számítási erőforrások (például az Azure HDInsight) más régiókban is lehetnek.
8. Válassza a **Rögzítés az irányítópulton** lehetőséget. 
9. Kattintson a **Létrehozás** gombra. 
10. Az irányítópulton megjelenő csempén a **Adat-előállító üzembe helyezése** állapotleírás látható: 

    ![Adat-előállító üzembe helyezése csempe](media/tutorial-copy-data-portal/deploying-data-factory.png)
11. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
    ![Data factory kezdőlap](./media/tutorial-copy-data-portal/data-factory-home-page.png)
12. A Data Factory felhasználói felületének külön lapon történő elindításához válassza a **Létrehozás és figyelés** csempét.

## <a name="create-a-pipeline"></a>Folyamat létrehozása
Ebben a lépésben létrehoz egy másolási tevékenységgel rendelkező folyamatot az adat-előállítóban. A másolási tevékenység adatokat másol egy Blob Storage-ból egy SQL Database-be. A [rövid útmutatóban](quickstart-create-data-factory-portal.md) létrehozott egy folyamatot az alábbi lépéseket követve:

1. Hozza létre a társított szolgáltatást. 
2. Hozzon létre bemeneti és kimeneti adatkészleteket.
3. Folyamat létrehozása.

Ebben az oktatóanyagban először létrehozzuk a folyamatot. Ezután társított szolgáltatásokat és adatkészleteket hozunk létre, amikor szükség van rájuk a folyamat konfigurálásához. 

1. Az **Első lépések** lapon válassza a **Folyamat létrehozása** lehetőséget. 

   ![Folyamat létrehozása](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
2. A folyamat **Tulajdonságok** ablakában a **Név** alatt adja a **CopyPipeline** nevet a folyamatnak.

    ![Folyamat neve](./media/tutorial-copy-data-portal/pipeline-name.png)
3. A **Tevékenységek** eszközkészletben bontsa ki az **Adatfolyam** kategóriát, majd húzza a **másolási** tevékenységet az eszközkészletből a folyamat tervezőfelületére. 

    ![Másolási tevékenység](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)
4. A **Tulajdonságok** ablak **Általános** lapján adja a **CopyFromBlobToSql** nevet a tevékenységnek.

    ![Tevékenység neve](./media/tutorial-copy-data-portal/activity-name.png)
5. Váltson a **Forrás** lapra. Forrásadatkészlet létrehozásához válassza az **+ Új** elemet. 

    ![Forrás lap](./media/tutorial-copy-data-portal/new-source-dataset-button.png)
6. Az **Új adatkészlet** lapon válassza az **Azure Blob Storage** lehetőséget, majd válassza a **Befejezés** gombot. A forrásadatok egy Blob Storage-ban vannak, tehát forrásadatkészletként az **Azure Blob Storage**-ot válassza. 

    ![Tároló kiválasztása](./media/tutorial-copy-data-portal/select-azure-storage.png)
7. Megnyílik egy új, **AzureBlob1** nevű lap az alkalmazásban.

    ![AzureBlob1 lap ](./media/tutorial-copy-data-portal/new-tab-azure-blob1.png)        
8. Az **Általános** lap **Tulajdonságok** ablakának alján a **Név** alatt írja be a **SourceBlobDataset** nevet.

    ![Adatkészlet neve](./media/tutorial-copy-data-portal/dataset-name.png)
9. A **Tulajdonságok** ablakban váltson a **Kapcsolat** lapra. A **Társított szolgáltatások** szövegbeviteli mező mellett válassza az **+ Új** lehetőséget. 

    Egy társított szolgáltatás egy adattárat vagy számítási erőforrást kapcsol az adat-előállítóhoz. Ebben az esetben létrehoz egy Storage-beli társított szolgáltatást, amely a tárfiókot társítja az adat-előállítóhoz. A társított szolgáltatás azon kapcsolatadatokkal rendelkezik, amelyeket a Data Factory használ futtatáskor a Blob Storage-hoz való kapcsolódáshoz. Az adatkészlet megadja a forrásadatokat tartalmazó tárolót, mappát és fájlt (ez utóbbi nem kötelező). 

    ![Új társított szolgáltatás gomb](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)
10. Az **Új társított szolgáltatás** ablakban végezze el az alábbi lépéseket: 

    a. A **Név** mezőbe írja be az **AzureStorageLinkedService** nevet. 

    b. A **Tárfiók neve** alatt válassza ki a tárfiókja nevét.

    c. A tárfiók kapcsolatának teszteléséhez válassza a **Kapcsolat tesztelése** elemet.

    d. A társított szolgáltatás mentéséhez kattintson a **Mentés** gombra.

    ![Új társított szolgáltatás](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)
11. A **Fájl elérési útja** mellett válassza a **Tallózás** lehetőséget.

    ![Tallózás gomb a fájl elérési útjához](./media/tutorial-copy-data-portal/file-browse-button.png)
12. Lépjen az **adftutorial/input** mappára, válassza ki az **emp.txt** fájlt, és válassza a **Befejezés** lehetőséget. Alternatív megoldásként kattinthat duplán is az **emp.txt** fájlra. 

    ![Bemeneti fájl kiválasztása](./media/tutorial-copy-data-portal/select-input-file.png)
13. Győződjön meg arról, hogy a **Fájlformátum** beállítása **Szöveges formátum**, és az **Oszlophatároló** beállítása **Vessző (`,`)**. Ha a forrásfájl különböző sor- és oszlophatárolókat használ, választhatja a **Szövegformátum észlelése** lehetőséget a **Fájlformátum** mezőben. Az Adatok másolása eszköz automatikusan észleli a fájlformátumot és a határolókat. Ezeket az értékeket a későbbiekben még felülbírálhatja. A lapon lévő adatok előnézetének megtekintéséhez válassza az **Adatok előnézete** elemet.

    ![Szövegformátum észlelése](./media/tutorial-copy-data-portal/detect-text-format.png)
14. Lépjen a **Séma** lapra a **Tulajdonságok** ablakban, és válassza a **Séma importálása** lehetőséget. Figyelje meg, hogy az alkalmazás két oszlopot észlelt a forrásfájlban. Azért importálja ide a sémát, hogy leképezhesse a forrásadattár oszlopait a fogadóadattárban. Ha nincs szüksége oszlopok leképezésére, ezt a lépést kihagyhatja. A jelen oktatóanyag keretében importálja a sémát.

    ![Forrás sémájának észlelése](./media/tutorial-copy-data-portal/detect-source-schema.png)  
15. Most lépjen a folyamatot tartalmazó lapra, vagy válassza ki a folyamatot a bal oldalon.

    ![Folyamat lap](./media/tutorial-copy-data-portal/pipeline-tab.png)
16. Győződjön meg arról, hogy a **SourceBlobDataset** lehetőség van kiválasztva a **Tulajdonságok** ablak **Forrásadatkészlet** mezőjében. A lapon lévő adatok előnézetének megtekintéséhez válassza az **Adatok előnézete** elemet. 
    
    ![Forrásadatkészlet](./media/tutorial-copy-data-portal/source-dataset-selected.png)
17. Váltson a **Fogadó** lapra, és válassza az **+ Új** elemet egy fogadó-adatkészlet létrehozásához. 

    ![Fogadó-adatkészlet](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
18. Az **Új adatkészlet** lapon válassza az **Azure SQL Database** lehetőséget, majd válassza a **Befejezés** gombot. Ebben az oktatóanyagban adatokat másol egy SQL Database-be. 

    ![SQL Database kiválasztása](./media/tutorial-copy-data-portal/select-azure-sql-database.png)
19. Az **Általános** lap **Tulajdonságok** ablakában a **Név** alatt írja be az **OutputSqlDataset** nevet. 
    
    ![Kimeneti adatkészlet neve](./media/tutorial-copy-data-portal/output-dataset-name.png)
20. Lépjen a **Kapcsolat** lapra, és a **Társított szolgáltatás** mellett válassza az **+ Új** lehetőséget. Az adatkészleteket mindig társítani kell egy társított szolgáltatáshoz. A társított szolgáltatás rendelkezik azzal a kapcsolati karakterlánccal, amely használatával a Data Factory a futtatás során kapcsolódik az SQL Database-hez. Az adatkészlet meghatározza azt a tárolót, mappát és fájlt (az utóbbi nem kötelező), ahova a rendszer az adatokat másolja. 
    
    ![Társított szolgáltatások](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
21. Az **Új társított szolgáltatás** ablakban végezze el az alábbi lépéseket: 

    a. A **Név** mezőbe írja az **AzureSqlDatabaseLinkedService** nevet.

    b. A **Kiszolgáló neve** mezőben válassza ki az SQL Server-példányát.

    c. Az **Adatbázis neve** mezőben válassza ki az SQL Database-adatbázisát.

    d. A **Felhasználónév** mezőben adja meg a felhasználó nevét.

    e. A **Jelszó** mezőben adja meg a felhasználó jelszavát.

    f. A kapcsolat teszteléséhez válassza a **Kapcsolat tesztelése** elemet.

    g. A társított szolgáltatás mentéséhez kattintson a **Mentés** gombra. 
    
    ![Új társított szolgáltatás mentése](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

22. A **Tábla** területen válassza a **[dbo].[emp]** elemet. 

    ![Tábla](./media/tutorial-copy-data-portal/select-emp-table.png)
23. Lépjen a **Séma** lapra, és válassza a **Séma importálása** lehetőséget. 

    ![Séma importálása lehetőség kiválasztása](./media/tutorial-copy-data-portal/import-destination-schema.png)
24. Válassza ki az **Azonosító** oszlopot, majd válassza a **Törlés** lehetőséget. Az **Azonosító** oszlop egy identitásoszlop az SQL-adatbázisban, ezért a másolási tevékenységnek semmit nem kell ebbe az oszlopba beszúrnia.

    ![Azonosító oszlop törlése](./media/tutorial-copy-data-portal/delete-id-column.png)
25. Lépjen a folyamatot tartalmazó lapra, és győződjön meg arról, hogy a **Fogadóadattár** mezőben az **OutputSqlDataset** érték van kiválasztva.

    ![Folyamat lap](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        
26. Lépjen a **Leképezés** lapra a **Tulajdonságok** ablak alján, és válassza a **Sémák importálása** elemet. Figyelje meg, hogy a forrásfájl első és második oszlopa az SQL-adatbázis **FirstName** és **LastName** mezőjében képeződik le.

    ![Sémák leképezése](./media/tutorial-copy-data-portal/map-schemas.png)
27. A folyamat érvényesítéséhez kattintson az **Érvényesítés** elemre. Az érvényesítési ablak bezárásához a jobb felső sarokban válassza a jobbra mutató nyilat.

    ![Folyamatérvényesítési kimenet](./media/tutorial-copy-data-portal/pipeline-validation-output.png)   
28. A jobb felső sarokban válassza a **Kód** elemet. Itt a folyamat futtatásához kapcsolódó JSON-kód látható. 

    ![Kód gomb](./media/tutorial-copy-data-portal/code-button.png)
29. Egy, a következő részlethez hasonló JSON-kód jelenik meg: 

    ```json
    {
        "name": "CopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToSql",
                    "type": "Copy",
                    "dependsOn": [],
                    "policy": {
                        "timeout": "7.00:00:00",
                        "retry": 0,
                        "retryIntervalInSeconds": 20
                    },
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "recursive": true
                        },
                        "sink": {
                            "type": "SqlSink",
                            "writeBatchSize": 10000
                        },
                        "enableStaging": false,
                        "parallelCopies": 0,
                        "cloudDataMovementUnits": 0,
                        "translator": {
                            "type": "TabularTranslator",
                            "columnMappings": "Prop_0: FirstName, Prop_1: LastName"
                        }
                    },
                    "inputs": [
                        {
                            "referenceName": "SourceBlobDataset",
                            "type": "DatasetReference",
                            "parameters": {}
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "OutputSqlDataset",
                            "type": "DatasetReference",
                            "parameters": {}
                        }
                    ]
                }
            ]
        }
    }
    ```

## <a name="test-run-the-pipeline"></a>A folyamat próbafuttatása
Elvégezheti a folyamat próbafuttatását, mielőtt összetevőket (társított szolgáltatások, adatkészletek és folyamat) tenne közzé az adat-előállítóban vagy a saját Visual Studio Team Services Git-adattárában. 

1. A folyamat próbafuttatásához válassza az eszköztár **Tesztfuttatás** elemét. A folyamat futtatási állapotát az ablak alján található **Kimenet** lapon tekintheti meg. 

    ![Folyamat tesztelése](./media/tutorial-copy-data-portal/test-run-output.png)
2. Ellenőrizze, hogy a forrásfájl adatai bekerültek-e a cél SQL-adatbázisba. 

    ![SQL-kimenet ellenőrzése](./media/tutorial-copy-data-portal/verify-sql-output.png)
3. A bal oldali panelen válassza az **Összes közzététele** lehetőséget. Ez a művelet közzéteszi a létrehozott entitásokat (társított szolgáltatások, adatkészletek és folyamatok) a Data Factoryban.

    ![Közzététel](./media/tutorial-copy-data-portal/publish-button.png)
4. Várjon, amíg megjelenik a **Sikeres közzététel** üzenet. Az értesítési üzenetek megtekintéséhez válassza a bal oldali oldalsáv **Értesítések megjelenítése** lapját. Az értesítések ablakának bezárásához válassza a **Bezárás** lehetőséget.

    ![Értesítések megjelenítése](./media/tutorial-copy-data-portal/show-notifications.png)

## <a name="configure-code-repository"></a>A kódtár konfigurálása
Az adat-előállító összetevőkhöz társított kódot közzéteheti egy Visual Studio Team Services-kódtárban. Ebben a lépésben a kódtárat fogja létrehozni. 

Ha nem szeretné használni a Visual Studio Team Services-kódtárat, ezt a lépést kihagyhatja. Folytathatja a Data Factoryban történő közzétételt, ahogyan azt az előző lépésben tette. 

1. A bal felső sarokban válassza a **Data Factory** elemet, vagy használja a mellette lévő lefelé mutató nyilat, és válassza a **Kódtár konfigurálása** lehetőséget. 

    ![A kódtár konfigurálása](./media/tutorial-copy-data-portal/configure-code-repository-button.png)
2. Az **Adattár beállításai** oldalon hajtsa végre az alábbi lépéseket:

    a. Az **Adattár típusa** alatt válassza ki a **Visual Studio Team Services Git** lehetőséget.

    b. A **Visual Studio Team Services-fiók** alatt válassza ki a Visual Studio Team Services-fiókját.

    c. A **Projekt neve** alatt válasszon egy projektet a Visual Studio Team Services-fiókjából.

    d. A **Git-adattár neve** alatt adja a **Tutorial2** nevet az adat-előállítóhoz társítani kívánt Git-adattárnak.

    e. Győződjön meg arról, hogy a **Létező Data Factory-erőforrások importálása az adattárba** jelölőnégyzet be van jelölve.

    f. A beállítások mentéséhez válassza a **Mentés** lehetőséget. 

    ![Adattár beállításai](./media/tutorial-copy-data-portal/repository-settings.png)
3. Győződjön meg arról, hogy a **VSTS GIT** van kiválasztva adattárként.

    ![A VSTS GIT kiválasztása](./media/tutorial-copy-data-portal/vsts-git-selected.png)
4. A böngésző egy másik lapján lépjen a **Tutorial2** adattárba. Két ág jelenik meg: az **adf_publish** és a **master**.

    ![A master és adf_publish ág](./media/tutorial-copy-data-portal/initial-branches-vsts-git.png)
5. Győződjön meg arról, hogy a Data Factory-entitások JSON-fájljai a **master** ágban vannak.

    ![A master ágban található fájlok](./media/tutorial-copy-data-portal/master-branch-files.png)
6. Győződjön meg arról, hogy a JSON-fájlok még nem szerepelnek az **adf_publish** ágban. 

    ![Az adf_publish ágban található fájlok](./media/tutorial-copy-data-portal/adf-publish-files.png)
7. A **Leírás** mezőben, adjon meg egy leírást a folyamatról, és válassza az eszköztár **Mentés** elemét. 

    ![Folyamat leírása](./media/tutorial-copy-data-portal/pipeline-description.png)
8. Most meg kell jelennie egy, a saját felhasználónevével ellátott ágnak a **Tutorial2** adattárban. Az elvégzett módosítás a saját ágában jelenik meg, nem a master ágban. Entitások csak a master ágból tehetők közzé.

    ![Saját ág](./media/tutorial-copy-data-portal/your-branch.png)
9. A módosítások master ággal való szinkronizálásához vigye az egeret a **Szinkronizálás** gomb fölé (ne válassza még ki), jelölje be a **Módosítások érvényesítése** jelölőnégyzetet, majd válassza a **Szinkronizálás** elemet. 

    ![Módosítások érvényesítése és szinkronizálása](./media/tutorial-copy-data-portal/commit-and-sync.png)
10. A **Módosítások szinkronizálása** ablakban tegye a következőket: 

    a. Ellenőrizze, hogy a **CopyPipeline** elem megjelenik-e a frissített **Folyamatok** listában.

    b. Győződjön meg arról, hogy a **Módosítások közzététele a szinkronizálás után** beállítás be van jelölve. Ha törli a jelölőnégyzet jelölését, akkor a rendszer csak szinkronizálja a saját ágban végzett módosításokat a master ággal. Nem teszi őket közzé a Data Factoryban. Később a **Közzététel** gomb használatával teheti közzé őket. Ha bejelöli ezt a jelölőnégyzetet, a rendszer szinkronizálja a módosításokat a master ággal, majd közzéteszi azokat a Data Factoryban.

    c. Válassza a **Szinkronizálás** elemet. 

    ![A módosítások szinkronizálása](./media/tutorial-copy-data-portal/sync-your-changes.png)
11. Most már a **Tutorial2** adattár **adf_publish** ágában is láthatók fájlok. A Data Factory-megoldás Azure Resource Manager-sablonja szintén ebben az ágban található. 

    ![Az adf_publish ágban található fájllista](./media/tutorial-copy-data-portal/adf-publish-files-after-publish.png)


## <a name="trigger-the-pipeline-manually"></a>A folyamat manuális aktiválása
Ebben a lépésben manuálisan fogja aktiválni az előző lépésben közzétett folyamatot. 

1. Kattintson az **Aktiválás** gombra az eszköztáron, majd válassza az **Aktiválás most** lehetőséget. A **Folyamatfuttatás** lapon válassza a **Befejezés** elemet.  

    ![Folyamat indítása](./media/tutorial-copy-data-portal/trigger-now-menu.png)
2. Lépjen a bal oldali **Figyelés** lapra. Itt láthat egy manuális eseményindító által aktivált folyamatfuttatást. A **Műveletek** oszlopban található hivatkozások használatával megtekintheti a tevékenységek részleteit, és újra futtathatja a folyamatot.

    ![Folyamatfuttatások monitorozása](./media/tutorial-copy-data-portal/monitor-pipeline.png)
3. A folyamat futásához társított tevékenységfuttatások megtekintéséhez kattintson a **Műveletek** oszlopban található **Tevékenységfuttatások megtekintése** hivatkozásra. Ebben a példában csak egy tevékenység található, ezért csak egy bejegyzést lát a listán. A másolási művelet részleteinek megtekintéséhez válassza a **Műveletek** oszlop **Részletek** hivatkozását (szemüveg ikon). A **Folyamatfuttatások** nézetre való visszaváltáshoz válassza a fenti **Folyamatok** hivatkozást. A nézet frissítéséhez válassza a **Frissítés** parancsot.

    ![Tevékenységfuttatások monitorozása](./media/tutorial-copy-data-portal/view-activity-runs.png)
4. Ellenőrizze, hogy hozzá lett-e adva két további sor az SQL Database **emp** táblájához. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>A folyamat aktiválása ütemezés szerint
Ebben az ütemezésben egy ütemezési eseményindítót fog létrehozni a folyamathoz. Az eseményindító a meghatározott ütemezés (például óránként vagy naponta) szerint futtatja a folyamatot. Ebben a példában úgy fogja beállítani az eseményindítót, hogy a megadott záró dátumidőig percenként futtassa a folyamatot. 

1. Lépjen a bal oldalon található **Szerkesztés** lapra. 

    ![Szerkesztés lap](./media/tutorial-copy-data-portal/edit-tab.png)
2. Válassza az **Eseményindító**, majd az **Új/szerkesztés** elemet. Ha a folyamat nem aktív, lépjen rá. 

    ![Eseményindító lehetőség](./media/tutorial-copy-data-portal/trigger-new-edit-menu.png)
3. Az **Eseményindítók hozzáadása** ablakban válassza az **Eseményindító kiválasztása**, majd pedig az **+ Új** elemet. 

    ![Új gomb](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
4. Az **Új eseményindító** ablakban hajtsa végre az alábbi lépéseket: 

    a. A **Név** mezőbe írja a **RunEveryMinute** nevet.

    b. A **Befejezés** alatt válassza a **Dátum** elemet.

    c. A **Befejezés** alatt válassza a legördülő listát.

    d. Válassza a **mai nap** lehetőséget. Alapértelmezés szerint a következő nap van beállítva.

    e. Frissítse a **perc** részt az aktuális időhöz képest néhány perccel későbbre. Az eseményindító csak a módosítások közzététele után lesz aktív. Ha csak néhány perccel későbbre állította az eseményindítót, és nem teszi azt közzé addigra, nem lesz látható eseményindító-futtatás.

    f. Kattintson az **Alkalmaz** gombra. 

    ![Eseményindító tulajdonságai](./media/tutorial-copy-data-portal/set-trigger-properties.png)

    g. Jelölje be az **Aktiválva** beállítást. A jelölőnégyzet használatával inaktiválhatja az eseményindítót, majd később ismét aktiválhatja.

    h. Kattintson a **Tovább** gombra.

    ![Aktiválva gomb](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > Minden egyes folyamatfuttatásnak van bizonyos költségvonzata, ezért a befejezés időpontját ezt figyelembe véve adja meg. 
5. Az **Eseményindító futtatási paraméterei** oldalon tekintse át a figyelmeztető üzenetet, majd válassza a **Befejezés** elemet. A jelen példában található folyamat nem használ paramétereket. 

    ![Eseményindító futtatásának paraméterei](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)
6. Válassza a **Szinkronizálás** elemet a saját ágban végzett módosítások master ággal való szinkronizálásához. Alapértelmezés szerint a **Módosítások közzététele a szinkronizálás után** beállítás be van jelölve. Ha bejelöli a **Szinkronizálás** beállítást, a program a frissített entitásokat is közzéteszi a Data Factoryban a master ágról. Az eseményindító nem aktiválódik, amíg a közzététel sikeresen be nem fejeződik.

    ![A módosítások szinkronizálása](./media/tutorial-copy-data-portal/sync-your-changes-with-trigger.png) 
7. Lépjen a bal oldali **Figyelés** lapra az aktivált folyamatfuttatások megtekintéséhez. 

    ![Aktivált folyamatfuttatások](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
8. Ha váltani szeretne a **Folyamatfuttatások** nézetéről az **Eseményindító-futtatások** nézetére, válassza a **Folyamatfuttatások** elemet, és válassza ki az **Eseményindító-futtatások** lehetőséget.
    
    ![Eseményindító-futtatások](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
9. Itt megtekintheti az eseményindító-futtatások listáját. 

    ![Eseményindító-futtatások listája](./media/tutorial-copy-data-portal/trigger-runs-list.png)
10. Ellenőrizze, hogy a megadott befejezési időig percenként (folyamatfuttatásonként) két sor be van-e szúrva az **emp** táblába. 

## <a name="next-steps"></a>További lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra a Blob Storage-ban. Megismerte, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Másolási tevékenységgel rendelkező folyamat létrehozása.
> * A folyamat próbafuttatása
> * A folyamat manuális aktiválása
> * A folyamat aktiválása ütemezés szerint
> * A folyamat és a tevékenységek futásának monitorozása


A következő oktatóanyagra lépve megismerheti az adatok helyszíni rendszerből felhőre való másolásának folyamatát: 

> [!div class="nextstepaction"]
>[Adatok másolása a helyszínről a felhőbe](tutorial-hybrid-copy-portal.md)
