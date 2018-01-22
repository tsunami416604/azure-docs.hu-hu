---
title: "Az Azure Portal használata Data Factory-folyamatok létrehozására | Microsoft Docs"
description: "Az oktatóanyag részletes útmutatását követve létrehozhat egy adat-előállítót egy folyamat és az Azure Portal használatával. A folyamat egy másolási tevékenységgel másol adatokat egy Azure Blob Storage-ból egy Azure SQL Database-be. "
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
ms.openlocfilehash: 7486e7c6816538fc120fd0b0a8bea0b006fb21f0
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Adatok másolása az Azure Blobból az Azure SQL Database-be az Azure Data Factory segítségével
Ebben az oktatóanyagban az Azure Data Factory felhasználói felületének használatával hoz létre egy adat-előállítót. Az adat-előállító folyamata adatokat másol az Azure Blob Storage-ból az Azure SQL Database-be. Az oktatóanyagban szereplő konfigurációs minta fájlalapú adattárból relációs adattárba való másolásra vonatkozik. A forrásként és fogadóként támogatott adattárak listája a [támogatott adattárakat tartalmazó](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban található.

> [!NOTE]
> - Ha még csak ismerkedik az Azure Data Factory szolgáltatással: [Bevezetés az Azure Data Factory használatába](introduction.md).
>
> - Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd a [Data Factory 1. verzió használatának első lépéseit](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

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
* **Azure Storage-fiók** A blobtárolót használjuk **forrás** adattárként. Ha még nem rendelkezik Azure Storage-fiókkal, a létrehozás folyamatáért lásd a [tárfiók létrehozását](../storage/common/storage-create-storage-account.md#create-a-storage-account) ismertető cikket.
* **Azure SQL Database** Ezt az adatbázist használjuk **fogadó** adattárként. Ha még nem rendelkezik Azure SQL Database-adatbázissal, a létrehozás folyamatáért lásd az [Azure SQL-adatbázis létrehozását](../sql-database/sql-database-get-started-portal.md) ismertető cikket.

### <a name="create-a-blob-and-a-sql-table"></a>Blob és SQL-tábla létrehozása

Készítse elő az Azure Blobot és az Azure SQL Database-t az oktatóanyaghoz a következő lépésekkel:

#### <a name="create-a-source-blob"></a>Forrás blob létrehozása

1. Indítsa el a Jegyzettömböt. Másolja be a következő szöveget, és mentse **emp.txt** néven egy fájlba a lemezen.

    ```
    John,Doe
    Jane,Doe
    ```

2. Hozzon létre egy **adftutorial** nevű tárolót az Azure Blob Storage-ban. Ebben a tárolóban hozzon létre egy **input** nevű mappát. Ezután töltse fel az **emp.txt** fájlt az **input** mappába. Ezekhez a feladatokhoz használja az Azure Portalt vagy olyan eszközöket, mint az [Azure Storage Explorer](http://storageexplorer.com/).

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

2. Engedélyezze az SQL Server elérését az Azure-szolgáltatások számára. Győződjön meg arról, hogy az **Azure-szolgáltatások hozzáférésének engedélyezése** beállítás **BE** van kapcsolva az Azure SQL Serverhez, hogy a Data Factory szolgáltatás tudjon adatokat írni az Azure SQL Serverre. A beállítás ellenőrzéséhez és bekapcsolásához hajtsa végre a következő lépéseket:

    1. Kattintson a **További szolgáltatások** hubra a bal oldalon, majd az **SQL-kiszolgálók** elemre.
    2. Válassza ki a kiszolgálót, és kattintson a **BEÁLLÍTÁSOK** területen a **Tűzfal** elemre.
    3. A **Tűzfalbeállítások** lapon kattintson a **BE** kapcsolóra az **Azure-szolgáltatások hozzáférésének engedélyezése** beállítás mellett.

## <a name="create-a-data-factory"></a>Data factory létrehozása
Ebben a lépésben létrehoz egy adat-előállítót, és elindítja az Azure Data Factory felhasználói felületét, hogy létrehozzon egy folyamatot az adat-előállítóban. 

1. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/tutorial-copy-data-portal/new-azure-data-factory-menu.png)
2. Az **Új adat-előállító** lapon, a **Név** mezőben adja meg a következőt: **ADFTutorialDataFactory**. 
      
     ![Új adat-előállító lap](./media/tutorial-copy-data-portal/new-azure-data-factory.png)
 
   Az Azure data factory nevének **globálisan egyedinek** kell lennie. Ha a Név mezőnél az alábbi hiba jelenik meg, módosítsa az adat-előállító nevét (például a következőre: sajátneveADFTutorialDataFactory). A Data Factory-összetevők részleteit a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
     ![Új adat-előállító lap](./media/tutorial-copy-data-portal/name-not-available-error.png)
3. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
4. Az **erőforráscsoportban** hajtsa végre a következő lépések egyikét:
     
      - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot. 
      - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.   
         
        Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
4. Válassza a **V2 (előzetes verzió)** értéket a **verzió** esetén.
5. Válassza ki a Data Factory **helyét**. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.
6. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
7. Kattintson a **Create** (Létrehozás) gombra.      
8. Az irányítópulton megjelenő csempén a következő állapotleírás látható: **Adat-előállító üzembe helyezése**. 

    ![adat-előállító üzembe helyezése csempe](media/tutorial-copy-data-portal/deploying-data-factory.png)
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
   ![Data factory kezdőlap](./media/tutorial-copy-data-portal/data-factory-home-page.png)
10. Az Azure Data Factory felhasználói felületének (UI) külön lapon történő elindításához kattintson a **Létrehozás és monitorozás** csempére.

## <a name="create-a-pipeline"></a>Folyamat létrehozása
Ebben a lépésben létrehoz egy másolási tevékenységgel rendelkező folyamatot az adat-előállítóban. A másolási tevékenység adatokat másol egy Azure Blob Storage-ból egy Azure SQL Database-be. A [rövid útmutatóban](quickstart-create-data-factory-portal.md) létrehozott egy folyamatot az alábbi lépéseket követve:

1. Hozza létre a társított szolgáltatást. 
2. Hozzon létre bemeneti és kimeneti adatkészleteket.
3. Ezután hozzon létre egy folyamatot.

Ebben az oktatóanyagban először létrehoz egy folyamatot, majd létrehoz társított szolgáltatásokat és adatkészleteket, amikor szükség van rájuk a folyamat konfigurálásához. 

1. Az első lépéseket ismertető oldalon kattintson a **Folyamat létrehozása** csempére. 

   ![Folyamat létrehozása csempe](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
3. A folyamat **Tulajdonságok** ablakában módosítsa a folyamat **nevét** a következőre: **CopyPipeline**.

    ![Folyamat neve](./media/tutorial-copy-data-portal/pipeline-name.png)
4. A **Tevékenységek** eszközkészletben bontsa ki a **DataFlow** kategóriát, majd húzza a **másolási** tevékenységet az eszközkészletből a folyamat tervezőfelületére. 

    ![Másolási tevékenység húzása](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)
5. A **Tulajdonságok** ablak **Általános** lapján adja a **CopyFromBlobToSql** nevet a tevékenységnek.

    ![Tevékenység neve](./media/tutorial-copy-data-portal/activity-name.png)
6. Váltson a **Forrás** lapra. Forrásadatkészlet létrehozásához kattintson az **+ Új** elemre. 

    ![Új forrásadatkészlet menü](./media/tutorial-copy-data-portal/new-source-dataset-button.png)
7. Az **Új adatkészlet** ablakban válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Befejezés** gombra. A forrásadatok egy Azure Blob Storage-ban vannak, tehát forrásadatkészletként az Azure Blob Storage-ot válassza. 

    ![Select Azure Blob Storage](./media/tutorial-copy-data-portal/select-azure-storage.png)
8. Megnyílik egy új, **AzureBlob1** nevű **lap** az alkalmazásban.

    ![Azure Blob1 tab ](./media/tutorial-copy-data-portal/new-tab-azure-blob1.png)        
9. Az alul található **Tulajdonságok** ablak **Általános** lapján a **Név** mezőbe írja a **SourceBlobDataset** értéket.

    ![Adatkészlet neve](./media/tutorial-copy-data-portal/dataset-name.png)
10. Váltson a **Kapcsolat** lapra a Tulajdonságok ablakban.   

    ![Kapcsolat lap](./media/tutorial-copy-data-portal/source-dataset-connection-tab.png)
11. Kattintson az **+ Új** elemre a **Társított szolgáltatások** szövegbeviteli mező mellett. Egy társított szolgáltatás egy adattárat vagy számítási erőforrást kapcsol az adat-előállítóhoz. Ebben az esetben létrehoz egy Azure Storage-beli társított szolgáltatást, amely az Azure-tárfiókot társítja az adat-előállítóhoz. A társított szolgáltatás azon kapcsolatadatokkal rendelkezik, amelyeket a Data Factory-szolgáltatások használnak futtatáskor a Blob Storage-hoz való kapcsolódáshoz. Az adatkészlet megadja a forrásadatokat tartalmazó tárolót, mappát és fájlt (ez utóbbi nem kötelező). 

    ![Új társított szolgáltatás gomb](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)
12. Az **Új társított szolgáltatás** ablakban végezze el az alábbi lépéseket: 

    1. A **Név** mezőben adja meg az **AzureStorageLinkedService** nevet. 
    2. A **Storage-fiók neve** mezőben válassza ki saját Azure Storage-fiókját.
    3. Az Azure Storage-tárfiók kapcsolatának teszteléséhez kattintson a **Kapcsolat tesztelése** elemre.  
    4. A társított szolgáltatás mentéséhez kattintson a **Mentés** gombra.

        ![Új Azure Storage-beli társított szolgáltatás](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)
13. Kattintson a **Fájl elérési útja** mező mellett a **Tallózás** gombra.  

    ![A fájlhoz tartozó Tallózás gomb](./media/tutorial-copy-data-portal/file-browse-button.png)
14. Lépjen az **adftutorial/input** mappába, válassza ki az **emp.txt** fájlt, és kattintson a **Befejezés** elemre. Alternatív megoldásként kattinthat duplán is az emp.txt fájlra. 

    ![Bemeneti fájl kiválasztása](./media/tutorial-copy-data-portal/select-input-file.png)
15. Győződjön meg arról, hogy a **Fájlformátum** beállítása **Szöveges formátum**, és az **oszlophatároló** beállítása **Vessző (`,`)**. Ha a forrásfájl különböző sor- és oszlophatárolókat használ, kattinthat a **Szövegformátum észlelése** lehetőségre a **Fájlformátum** mezőben. Az Adatok másolása eszköz automatikusan észleli a fájlformátumot és a határolókat. Ezeket az értékeket a későbbiekben még felülbírálhatja. Az adatok előnézetének megtekintéséhez kattintson az **Adatok előnézete** elemre.

    ![Szövegformátum észlelése](./media/tutorial-copy-data-portal/detect-text-format.png)
17. Váltson a **Séma** lapra a tulajdonságok ablakban, majd kattintson a **Séma importálása** parancsra. Figyelje meg, hogy az alkalmazás két oszlopot észlelt a forrásfájlban. Azért importálja ide a sémát, hogy leképezhesse a forrásadattár oszlopait a fogadóadattárban. Ha nincs szüksége oszlopok leképezésére, ezt a lépést kihagyhatja. A jelen oktatóanyag keretében importálja a sémát.

    ![Forrás sémájának észlelése](./media/tutorial-copy-data-portal/detect-source-schema.png)  
19. Most pedig váltson a **folyamatot tartalmazó lapra**, vagy kattintson a folyamatra a bal oldalon található **fanézetben**.  

    ![Folyamat lap](./media/tutorial-copy-data-portal/pipeline-tab.png)
20. Győződjön meg arról, hogy a **SourceBlobDataset** lehetőség van kiválasztva a Tulajdonságok ablak Forrásadatkészlet mezőjében. Az adatok előnézetének megtekintéséhez kattintson az **Adatok előnézete** elemre. 
    
    ![Forrásadatkészlet](./media/tutorial-copy-data-portal/source-dataset-selected.png)
21. Váltson a **Fogadó** lapra, és kattintson az **Új** elemre egy új fogadó-adatkészlet létrehozásához. 

    ![Új fogadó-adatkészlet menü](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
22. Az **Új adatkészlet** ablakban válassza az **Azure SQL Database** lehetőséget, majd kattintson a **Befejezés** gombra. A jelen oktatóanyagban adatokat másolhat egy Azure SQL Database-be. 

    ![Az Azure SQL Database kiválasztása](./media/tutorial-copy-data-portal/select-azure-sql-database.png)
23. A Tulajdonságok ablak **Általános** lapján adja meg az **OutputSqlDataset** értéket a Név mezőben. 
    
    ![Kimeneti adatkészlet neve](./media/tutorial-copy-data-portal/output-dataset-name.png)
24. Váltson a **Kapcsolat** lapra, majd kattintson a **Társított szolgáltatáshoz** tartozó **Új** elemre. Az adatkészleteket mindig társítani kell egy társított szolgáltatáshoz. A társított szolgáltatás rendelkezik azzal a kapcsolati karakterlánccal, amely használatával a Data Factory szolgáltatás a futtatás során kapcsolódik az Azure SQL Database-hez. Az adatkészlet meghatározza azt a tárolót, mappát és fájlt (az utóbbi nem kötelező), ahova a rendszer az adatokat másolja. 
    
    ![Új társított szolgáltatás gomb](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
25. Az **Új társított szolgáltatás** ablakban végezze el az alábbi lépéseket: 

    1. A **Név** mezőben adja meg az **AzureSqlDatabaseLinkedService** értéket. 
    2. A **Kiszolgáló neve** mezőnél válassza ki az Azure SQL Server-kiszolgálót.
    4. Az **Adatbázis neve** mezőnél válassza ki az Azure SQL Database-adatbázist. 
    5. A **Felhasználónév** mezőben adja meg a felhasználó nevét. 
    6. A **Jelszó** mezőben adja meg a felhasználóhoz tartozó jelszót. 
    7. A kapcsolat teszteléséhez kattintson a **Kapcsolat tesztelése** elemre.
    8. A társított szolgáltatás mentéséhez kattintson a **Mentés** gombra. 
    
        ![Új Azure SQL Database-beli társított szolgáltatás](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

26. A **Tábla** mezőben válassza a **[dbo].[emp]** értéket. 

    ![Az emp tábla kiválasztása](./media/tutorial-copy-data-portal/select-emp-table.png)
27. Váltson a **Séma** lapra, majd kattintson a Séma importálása elemre. 

    ![Célséma importálása](./media/tutorial-copy-data-portal/import-destination-schema.png)
28. Válassza ki az **Azonosító** oszlopot, és kattintson a **Törlés** elemre. Az Azonosító oszlop egy identitásoszlop az SQL-adatbázisban, ezért a másolási tevékenységnek semmit nem kell ebbe az oszlopba beszúrnia.

    ![Azonosító oszlop törlése](./media/tutorial-copy-data-portal/delete-id-column.png)
30. Váltson a **folyamatot** tartalmazó lapra, és győződjön meg arról, hogy a **Fogadóadattár** mezőben az **OutputSqlDataset** érték van kiválasztva.

    ![Folyamat lap](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        
31. A Tulajdonságok ablak alján váltson **Leképezés** lapra, majd kattintson a **Sémák importálása** elemre. Figyelje meg, hogy a forrásfájl első és második oszlopa az SQL-adatbázis **FirstName** és **LastName** mezőjében képeződik le.

    ![Sémák leképezése](./media/tutorial-copy-data-portal/map-schemas.png)
33. Érvényesítse a folyamatot az **Érvényesítés** gombra kattintva. Az érvényesítési ablak bezárásához kattintson a **jobbra mutató nyílra**.

    ![Folyamatérvényesítési kimenet](./media/tutorial-copy-data-portal/pipeline-validation-output.png)   
34. Kattintson a jobb sarokban található **Kód** gombra. Itt a folyamat futtatásához kapcsolódó JSON-kód látható. 

    ![Kód gomb](./media/tutorial-copy-data-portal/code-button.png)
35. Egy, a következő részlethez hasonló JSON-kód jelenik meg:  

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
Elvégezheti a folyamat próbafuttatását, mielőtt összetevőket (társított szolgáltatások, adatkészletek és folyamat) tenne közzé a Data Factoryban (vagy) saját VSTS GIT-adattárában. 

1. A folyamat próbafuttatásához kattintson az eszköztár **Tesztfuttatás** elemére. A folyamat futtatási állapotát az ablak alján található **Kimenet** lapon tekintheti meg. 

    ![Tesztfuttatás gomb](./media/tutorial-copy-data-portal/test-run-output.png)
2. Ellenőrizze, hogy a forrásfájl adatai bekerültek-e a cél SQL-adatbázisba. 

    ![SQL-kimenet ellenőrzése](./media/tutorial-copy-data-portal/verify-sql-output.png)
3. A bal oldali ablaktáblán kattintson a **Közzététel** gombra. Ez a művelet közzéteszi a létrehozott entitásokat (társított szolgáltatások, adatkészletek és folyamatok) az Azure Data Factoryban.

    ![Közzététel gomb](./media/tutorial-copy-data-portal/publish-button.png)
4. Várjon, amíg megjelenik a **Sikeres közzététel** üzenet. Az értesítési üzenetek megtekintéséhez kattintson a bal oldali oldalsáv **Értesítések megjelenítése** lapjára. Az **X** gombra kattintva zárja be az értesítések ablakát.

    ![Értesítések megjelenítése](./media/tutorial-copy-data-portal/show-notifications.png)

## <a name="configure-code-repository"></a>A kódtár konfigurálása
Az adat-előállító összetevőkhöz társított kódot közzéteheti egy Visual Studio Team System- (VSTS-) kódtárban. Ebben a lépésben a kódtárat fogja létrehozni. 

Ha nem szeretne a VSTS-kódtárral dolgozni, kihagyhatja ezt a lépést, és folytathatja a Data Factoryban történő közzétételt, ahogyan azt az előző lépésben tette. 

1. Kattintson a **Data Factory** elemre a bal oldali sarokban (vagy) a mellette található lefelé mutató nyílra, és kattintson a **Kódtár konfigurálása** elemre. 

    ![Kód gomb](./media/tutorial-copy-data-portal/configure-code-repository-button.png)
2. Az **Adattár beállításai** oldalon végezze el az alábbi lépéseket: 
    1. Az **Adattár típusa** mezőben válassza ki a **Visual Studio Team Services Git** lehetőséget.
    2. A **Visual Studio Team Services-fiók** mezőben válassza ki saját VSTS-fiókját.
    3. A **Projekt neve** mezőben válassza ki a VSTS-fiók egyik projektjét.
    4. Az adat-előállítóhoz társítani kívánt **Git-adattár nevénél** adja meg a **Tutorial2** nevet. 
    5. Győződjön meg arról, hogy a **Létező Data Factory-erőforrások importálása az adattárba** beállítás be van jelölve. 
    6. Kattintson a **Mentés** gombra a beállítások mentéséhez. 

        ![Adattár beállításai](./media/tutorial-copy-data-portal/repository-settings.png)
3. Győződjön meg arról, hogy a **VSTS GIT** van kiválasztva adattárként.

    ![VSTS GIT kiválasztva](./media/tutorial-copy-data-portal/vsts-git-selected.png)
4. A webböngésző egy másik lapján lépjen a **Tutorial2** adattárba, ahol két ág jelenik meg: a **master** és az **adf_publish**.

    ![A master és adf_publish ág](./media/tutorial-copy-data-portal/initial-branches-vsts-git.png)
5. Győződjön meg arról, hogy a Data Factory-entitások **JSON-fájljai** a **master** ágban vannak.

    ![A master ágban található fájlok](./media/tutorial-copy-data-portal/master-branch-files.png)
6. Győződjön meg arról, hogy a **JSON-fájlok** még nem szerepelnek az **adf_publish** ágban. 

    ![Az adf_publish ágban található fájlok](./media/tutorial-copy-data-portal/adf-publish-files.png)
7. Adjon meg egy **leírást** a **folyamat** számára, majd kattintson az eszköztár **Mentés** gombjára. 

    ![Leírás hozzáadása a folyamathoz](./media/tutorial-copy-data-portal/pipeline-description.png)
8. Most meg kell jelennie egy, a saját felhasználónevével ellátott **ágnak** a **Tutorial2** adattárban. Az elvégzett módosítás a saját ágában jelenik meg, nem a master ágban. Entitások csak a master ágból tehetők közzé.

    ![Saját ág](./media/tutorial-copy-data-portal/your-branch.png)
9. Vigye az egeret a **Szinkronizálás** gomb fölé (ne kattintson még rá), jelölje be a **Módosítások érvényesítése** beállítást, majd kattintson a **Szinkronizálás** gombra. Ekkor szinkronizálja a módosításokat a **master** ággal. 

    ![Módosítások érvényesítése és szinkronizálása](./media/tutorial-copy-data-portal/commit-and-sync.png)
9. A Módosítások szinkronizálása ablakban tegye a következőket: 

    1. Ellenőrizze, hogy a **CopyPipeline** elem megjelenik-e a frissített folyamatlistában.
    2. Győződjön meg arról, hogy a **Módosítások közzététele a szinkronizálás után** beállítás be van jelölve. Ha törli a beállítás jelölését, akkor a rendszer csak szinkronizálja a saját ágban végzett módosításokat a master ággal, de nem teszi azokat közzé a Data Factory szolgáltatásban. Később a **Közzététel** gomb használatával teheti közzé őket. Ha bejelöli a beállítást, a rendszer először szinkronizálja a módosításokat a master ággal, aztán közzéteszi azokat a Data Factory szolgáltatásban.
    3. Kattintson a **Szinkronizálás** elemre. 

    ![Módosítások szinkronizálása ablak](./media/tutorial-copy-data-portal/sync-your-changes.png)
10. Most már a **Tutorial2** adattár **adf_publish** ágában is láthatók fájlok. A Data Factory-megoldás Azure Resource Manager-sablonja szintén ebben az ágban található.  

    ![Az adf_publish ágban található fájlok](./media/tutorial-copy-data-portal/adf-publish-files-after-publish.png)


## <a name="trigger-the-pipeline-manually"></a>A folyamat manuális aktiválása
Ebben a lépésben manuálisan fogja aktiválni az előző lépésben közzétett folyamatot. 

1. Kattintson az **Aktiválás** gombra az eszköztáron, majd az **Aktiválás most** elemre. 

    ![Aktiválás most menü](./media/tutorial-copy-data-portal/trigger-now-menu.png)
2. Váltson a bal oldali **Monitorozás** lapra. Itt láthat egy manuális eseményindító által aktivált folyamatfuttatást. A Műveletek oszlopban található hivatkozások használatával megtekintheti a tevékenységek részleteit, és újra futtathatja a folyamatot.

    ![Folyamat futtatásának figyelése](./media/tutorial-copy-data-portal/monitor-pipeline.png)
3. A folyamat futásához kapcsolódó tevékenységfuttatások megtekintéséhez kattintson a **Tevékenységfuttatások megtekintése** hivatkozásra a **Műveletek** oszlopban. Ebben a példában csak egy tevékenység található, ezért csak egy bejegyzést lát a listán. A másolási művelet részleteinek megtekintéséhez kattintson a **Műveletek** oszlop **Részletek** hivatkozására (szemüveg ikon). A **Folyamatfuttatások** nézetre való visszaváltáshoz kattintson a fenti **Folyamatok** hivatkozásra. A nézet frissítéséhez kattintson a **Frissítés** parancsra.

    ![Tevékenységfuttatások megtekintése](./media/tutorial-copy-data-portal/view-activity-runs.png)
4. Ellenőrizze, hogy hozzá lett-e adva két további sor az Azure SQL Database **emp** táblájához. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>A folyamat aktiválása ütemezés szerint
Ebben az ütemezésben egy ütemezési eseményindítót fog létrehozni a folyamathoz. Az eseményindító a meghatározott ütemezés (naponta, hetente stb.) szerint futtatja a folyamatot. Ebben a példában úgy fogja beállítani az eseményindítót, hogy a megadott záró dátumidőig percenként futtassa a folyamatot. 

1. Váltson a bal oldalon található **Szerkesztés** lapra. 

    ![Szerkesztés lap](./media/tutorial-copy-data-portal/edit-tab.png)
2. Kattintson az **Eseményindító**, majd az **Új/szerkesztés** elemre. Ha a folyamat nem aktív, váltson át arra. 

    ![Eseményindító Új/szerkesztés menüje](./media/tutorial-copy-data-portal/trigger-new-edit-menu.png)
3. Az **Eseményindítók hozzáadása** ablakban kattintson az **Eseményindító kiválasztása**, majd az **+ Új** elemre. 

    ![Eseményindítók hozzáadása – új eseményindító](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
4. Az **Új eseményindító** ablakban végezze el az alábbi lépéseket: 

    1. A **névnél** adja meg a **RunEveryMinute** értéket.
    2. A **Befejezés** elemnél válassza a **Dátum** lehetőséget. 
    3. Kattintson a **Befejezés** legördülő listára.
    4. Válassza ki a **mai napot**. Alapértelmezés szerint a következő nap van beállítva. 
    5. Frissítse a **perc** részt az aktuális időhöz képest néhány perccel későbbre. Az eseményindító csak a módosítások közzététele után lesz aktív. Ha tehát csak néhány perccel későbbre állította az eseményindítót, és nem teszi azt közzé addigra, nem lesz látható eseményindító-futtatás.  
    6. Kattintson az **Alkalmaz** gombra. 

        ![Eseményindító tulajdonságainak megadása](./media/tutorial-copy-data-portal/set-trigger-properties.png)
    7. Jelölje be az **Aktiválva** beállítást. A jelölőnégyzet használatával inaktiválhatja az eseményindítót, majd később ismét aktiválhatja.
    8. Kattintson a **Tovább** gombra.

        ![Eseményindító aktiválva – tovább](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > Minden egyes folyamatfuttatás költséggel jár. A végdátumot ennek megfelelően állítsa be. 
6. Az **Eseményindító futtatási paraméterei** oldalon tekintse át a figyelmeztető üzenetet, majd kattintson a **Befejezés** elemre. A jelen példában található folyamat nem használ paramétereket. 

    ![Folyamat paraméterei](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)
7. Kattintson a **Közzététel** gombra a módosítások az adattárban történő közzétételéhez. Az eseményindító nem aktiválódik, amíg a közzététel sikeresen be nem fejeződik. 

    ![Eseményindító közzététele](./media/tutorial-copy-data-portal/publish-trigger.png) 
8. Váltson a bal oldali **Figyelés** lapra az aktivált folyamatfuttatások megtekintéséhez. 

    ![Aktivált folyamatfuttatások](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
9. Ha váltani szeretne a folyamatfuttatások nézetéről az eseményindító-futtatások nézetére, kattintson a Folyamatfuttatások elemre, és válassza ki az Eseményindító-futtatások lehetőséget.
    
    ![Eseményindító-futtatások menü](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
10. Itt megtekintheti az eseményindító-futtatások listáját. 

    ![Eseményindító-futtatások listája](./media/tutorial-copy-data-portal/trigger-runs-list.png)
11. Ellenőrizze, hogy a megadott befejezési időig percenként (folyamatfuttatásonként) két sor be van-e szúrva az **emp** táblába. 

## <a name="next-steps"></a>További lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra egy Azure Blob Storage-ban. Megismerte, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Másolási tevékenységgel rendelkező folyamat létrehozása.
> * A folyamat próbafuttatása
> * A folyamat manuális aktiválása
> * A folyamat aktiválása ütemezés szerint
> * A folyamat és a tevékenységek futásának monitorozása


Folytassa a következő oktatóanyaggal, amelyben azzal ismerkedhet meg, hogyan másolhat adatokat a helyszíni rendszerből a felhőbe: 

> [!div class="nextstepaction"]
>[Adatok másolása a helyszínről a felhőbe](tutorial-hybrid-copy-data-tool.md)
