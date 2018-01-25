---
title: "Adatok másolása az SQL Server-adatbázisból Blob Storage-tárolóba az Azure Data Factory használatával | Microsoft Docs"
description: "Megismerheti az adatok a helyszíni adattárolókból az Azure-beli felhőbe, az Azure Data Factory saját üzemeltetésű Integration Runtime átjáróval történő másolásának folyamatát."
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
ms.date: 01/11/2018
ms.author: jingwang
ms.openlocfilehash: 64cd758e2f40ff2b18abbff1194a7e57389d8a54
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="tutorial-copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Oktatóanyag: Adatok másolása helyszíni SQL Server-adatbázisból Azure Blob Storage-tárolóba
Ebben az oktatóanyagban az Azure Data Factory felhasználói felületének (UI) használatával egy Data Factory-folyamatot hoz létre az adatok egy helyszíni SQL Server-adatbázisból egy Azure Blob-tárolóba történő másolására. Létrehozhat és alkalmazhat egy saját üzemeltetésű integrációs modult, amely adatokat helyez át a helyszíni és a felhőalapú adattárolók között. 

> [!NOTE]
> Ez a cikk az Azure Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd [a Data Factory 1. verziójának dokumentációját](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Ez a cikk nem mutatja be részletesen a Data Factory szolgáltatást. További információkért lásd: [Az Azure Data Factory bemutatása](introduction.md). 

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Hozzon létre egy saját üzemeltetésű integrációs modult.
> * SQL Server- és Azure Storage-beli társított szolgáltatások létrehozása. 
> * SQL Server- és Azure Blob-adatkészletek létrehozása.
> * Folyamat létrehozása másolási tevékenységgel az adatok áthelyezéséhez
> * Folyamat futásának indítása
> * A folyamat futásának monitorozása.

## <a name="prerequisites"></a>Előfeltételek
### <a name="azure-subscription"></a>Azure-előfizetés
Ha még nem rendelkezik Azure-előfizetéssel, első lépésként [hozzon létre egy ingyenes](https://azure.microsoft.com/free/) fiókot.

### <a name="azure-roles"></a>Azure-szerepkörök
Az adat-előállító példányok létrehozásához annak a felhasználói fióknak, amellyel belép az Azure-ba, a *közreműködő* vagy *tulajdonos* szerepkör tagjának, vagy az Azure-előfizetés *rendszergazdájának* kell lennie. 

Az előfizetésben található engedélyek megtekintéséhez kattintson az Azure Portalon a felhasználónevére a jobb felső sarokban, majd válassza az **Engedélyek** elemet. Ha több előfizetéshez is rendelkezik hozzáféréssel, válassza ki a megfelelő előfizetést. Ha szeretne példautasításokat látni egy felhasználó szerepkörhöz adására, olvassa el a [Szerepkörök hozzáadása](../billing/billing-add-change-azure-subscription-administrator.md) című cikket.

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 és 2017
Ebben az oktatóanyagban egy helyszíni SQL Server-adatbázist használunk *forrásadattárként*. Az oktatóanyag során létrehozott adat-előállító folyamata adatokat másol egy helyszíni SQL Server-adatbázisból (forrás) egy Azure Blob Storage-tárolóba (fogadó). Ezután létre fog hozni egy **emp** nevű táblát az SQL Server-adatbázisban, és beszúr a táblába néhány mintabejegyzést. 

1. Indítsa el az SQL Server Management Studiót. Ha még nincs telepítve a számítógépen, tekintse meg az [Az SQL Server Management Studio letöltését ismertető](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) szakaszt. 

2. Csatlakozzon az SQL Server-példányához a hitelesítő adataival. 

3. Hozzon létre egy mintaadatbázist. A fanézetben kattintson a jobb gombbal a **Databases** (Adatbázisok) elemre, majd válassza a **New Database** (Új adatbázis) elemet. 
 
4. Az **New Database** (Új adatbázis) ablakban adjon nevet az új adatbázisnak, majd kattintson az **OK** gombra. 

5. Az **emp** tábla létrehozásához és néhány mintaadat beszúrásához futtassa a következő lekérdezési szkriptet az adatbázison:

   ```
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO
    
    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
   ```

6. A fanézetben kattintson a jobb gombbal a létrehozott adatbázisra, majd válassza a **New Query** (Új lekérdezés) elemet.

### <a name="azure-storage-account"></a>Azure Storage-fiók
Ebben az oktatóanyagban egy általános célú Azure Storage-fiókot (ebben az esetben Azure Blob Storage-tárolót) használunk cél-/fogadóadattárként. Ha még nem rendelkezik általános célú Azure Storage-fiókkal, tekintse meg a [Tárfiók létrehozását](../storage/common/storage-create-storage-account.md#create-a-storage-account) ismertető cikket. Az oktatóanyag során létrehozott adat-előállító folyamata adatokat másol a helyszíni SQL Server-adatbázisból (forrás) az Azure Blob Storage tárolóba (fogadó). 

#### <a name="get-storage-account-name-and-account-key"></a>Tárfióknév és fiókkulcs beszerzése
Ebben az oktatóanyagban az Azure Storage-fiók nevét és kulcsát használjuk. A tárfiók nevét és kulcsát az alábbi módon szerezheti be: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure felhasználónevével és jelszavával. 

2. A bal oldali ablaktáblán válassza ki a **További szolgáltatások** lehetőséget, szűrjön rá a **Tárolás** kulcsszóra, majd válassza ki a **Tárfiókok** lehetőséget.

    ![Tárfiók keresése](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. A tárfiókok listájában állítson be szűrőt a tárfiók nevéhez (ha szükséges), majd válassza ki a tárfiókját. 

4. A **Tárfiók** ablakban válassza a **Hozzáférési kulcsok** elemet.

    ![Tárfióknév és -kulcs beszerzése](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. Másolja a **Tárfiók neve** és **1. kulcs** mező értékét, majd illessze be őket egy jegyzettömbbe vagy más szerkesztőbe az oktatóanyag későbbi részeiben történő használatra. 

#### <a name="create-the-adftutorial-container"></a>Adftutorial tároló létrehozása 
Ebben a szakaszban egy **adftutorial** nevű blobtárolót hoz létre az Azure Blob Storage-ban. 

1. A **Tárfiók** ablakban váltson át az **Áttekintés** panelre, majd válassza a **Blobok** elemet. 

    ![A Blobok elem választása](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. A **Blob service** ablakban válassza a **Tároló** elemet. 

    ![Tároló hozzáadása gomb](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. Az **Új tároló** ablak **Név** mezőjébe írja be az **adftutorial** nevet, majd kattintson az **OK** gombra. 

    ![Tárolónév megadása](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. A tárolók listájában kattintson az **adftutorial** elemre.  

    ![A tároló kiválasztása](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. Ne zárja be az **adftutorial** **tároló** ablakát. A segítségével ellenőrizheti az oktatóanyag eredményét. A Data Factory automatikusan létrehozza a kimeneti mappát a tárolóban, így nem kell újat létrehoznia.

    ![Tároló ablaka](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Data factory létrehozása
Ebben a lépésben létrehoz egy adat-előállítót, és elindítja az Azure Data Factory felhasználói felületét, hogy létrehozzon egy folyamatot az adat-előállítóban. 

1. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/tutorial-hybrid-copy-portal/new-azure-data-factory-menu.png)
2. Az **Új adat-előállító** lapon, a **Név** mezőben adja meg a következőt: **ADFTutorialDataFactory**. 
      
     ![Új adat-előállító lap](./media/tutorial-hybrid-copy-portal/new-azure-data-factory.png)
 
   Az Azure data factory nevének **globálisan egyedinek** kell lennie. Ha a Név mezőnél az alábbi hiba jelenik meg, módosítsa az adat-előállító nevét (például a következőre: sajátneveADFTutorialDataFactory). A Data Factory-összetevők részleteit a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
     ![Új adat-előállító lap](./media/tutorial-hybrid-copy-portal/name-not-available-error.png)
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

    ![adat-előállító üzembe helyezése csempe](media/tutorial-hybrid-copy-portal/deploying-data-factory.png)
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
   ![Data factory kezdőlap](./media/tutorial-hybrid-copy-portal/data-factory-home-page.png)
10. Az Azure Data Factory felhasználói felületének külön lapon történő elindításához kattintson a **Létrehozás és monitorozás** csempére. 


## <a name="create-a-pipeline"></a>Folyamat létrehozása

1. Az **első lépéseket** ismertető oldalon kattintson a **Folyamat létrehozása** elemre. A rendszer automatikusan létrehoz egy folyamatot. A folyamat fanézetben, megnyitott szerkesztővel jelenik meg. 

   ![Első lépések lap](./media/tutorial-hybrid-copy-portal/get-started-page.png)
2. A **Tulajdonságok** ablak **Általános** lapján (a képernyő alján) adja meg a **SQLServerToBlobPipeline** értéket a **Név** mezőben.

   ![Folyamat neve](./media/tutorial-hybrid-copy-portal/pipeline-name.png)
2. A **Tevékenységek** eszközkészletben bontsa ki az **Adatfolyam** elemet, és húzza át a **Másolás** tevékenységet a folyamat tervezőfelületére. Állítsa a tevékenység nevét a következőre: **CopySqlServerToAzureBlobActivity**.

   ![Tevékenység neve](./media/tutorial-hybrid-copy-portal/copy-activity-name.png)
3. A tulajdonságok ablakában váltson a **Forrás** lapra, és kattintson az **+ Új** elemre.

   ![Új forrásadatkészlet – gomb](./media/tutorial-hybrid-copy-portal/source-dataset-new-button.png)
4. Az **Új adatkészlet** ablakban keresse meg az **Azure SQL Server** elemet, válassza ki az **SQL Server** elemet, majd kattintson a **Befejezés** gombra. Ekkor megjelenik egy **SqlServerTable1** nevű új lap. A bal oldalon fanézetben is megtekintheti az **SqlServerTable1** adatkészletet. 

   ![Select SQL Server](./media/tutorial-hybrid-copy-portal/select-sql-server.png)
5. A Tulajdonságok ablak **Általános** lapján a **SqlServerDataset** értéket adja meg a **Név** mezőben.
    
   ![Forrásadatkészlet – név](./media/tutorial-hybrid-copy-portal/source-dataset-name.png)
6. Váltson a **Kapcsolatok** lapra, és kattintson az **+ Új** gombra. Ebben a lépésben egy kapcsolatot hoz létre a forrásadatkészlettel (az SQL Server-adatbázissal). 

   ![Forrásadatkészlet – új kapcsolat gomb](./media/tutorial-hybrid-copy-portal/source-connection-new-button.png)
7. Az **Új társított szolgáltatás** ablakban kattintson az **Új integrációs modul** elemre: Ebben a szakaszban egy saját üzemeltetésű Integration Runtime átjárót hozhat létre, és társíthatja azt az SQL Server-adatbázist futtató helyszíni géppel. A saját üzemeltetésű Integration Runtime az a komponens, amely adatokat másol a gépen futó SQL Server-adatbázisból az Azure Blob Storage-ba. 

   ![Új integrációs modul](./media/tutorial-hybrid-copy-portal/new-integration-runtime-button.png)
8. Az **Integration modul beállítása** ablakban válassza a **Magánhálózat** lehetőséget, majd kattintson a **Tovább** gombra. 

   ![Magánhálózat kiválasztása](./media/tutorial-hybrid-copy-portal/select-private-network.png)
9. Adja meg az integrációs modul nevét, majd kattintson a **Tovább** gombra.  
    
   ![Integrációs modul – név](./media/tutorial-hybrid-copy-portal/integration-runtime-name.png)
10. Kattintson a **Kattintson ide a számítógépen történő expressz telepítés indításához** elemre az **1. lehetőség: Expressz telepítés** szakaszban. 

   ![Kattintás az Expressz telepítés hivatkozásra](./media/tutorial-hybrid-copy-portal/click-exress-setup.png)
11. Az **Integrációs modul (Saját üzemeltetésű) – Expressz telepítés** ablakban kattintson a **Bezárás** elemre. 

   ![Integrációs modul telepítése – sikeres](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)
12. A webböngészőben kattintson a **Befejezés** gombra az **Integrációs modul telepítése** ablakban. 

   ![Integrációs modul telepítése – befejezés](./media/tutorial-hybrid-copy-portal/click-finish-integration-runtime-setup.png)
13. Az **Új társított szolgáltatás** ablakban végezze el az alábbi lépéseket:

    1. A **Név** mezőben adja meg az **SqlServerLinkedService** értéket.
    2. Győződjön meg róla, hogy a korábban létrehozott saját üzemeltetésű integrációs modul jelenik meg a **Csatlakozás integrációs modulon keresztül** lehetőség kiválasztásakor.
    3. A **Kiszolgáló neve** mezőben adja meg az SQL Server-kiszolgáló nevét. 
    4. Adja meg az adatbázis nevét az **Adatbázis neve** mezőhöz tartozó **emp** táblával. 
    5. Válassza ki azt a **hitelesítési típust**, amelyet a Data Factorynak az SQL Server-adatbázishoz történő csatlakozáshoz használnia kell. 
    6. Adja meg a **felhasználónevet** és a **jelszót**. Ha fordított perjel karaktert (\\) kell használnia a felhasználói fiók vagy a kiszolgáló nevében, használja előtte a feloldójelet (\\). Használja például a *sajáttartomány\\\\sajátfelhasználó* értéket. 
    7. Kattintson a **Kapcsolat tesztelése** elemre. Ez a lépés annak megerősítésére szolgál, hogy a Data Factory szolgáltatás kapcsolódni tud az SQL Server-adatbázishoz az Ön által létrehozott saját üzemeltetésű integrációs modullal. 
    8. A társított szolgáltatás mentéséhez kattintson a **Mentés** gombra.

       ![SQL Server-beli társított szolgáltatás – beállítások](./media/tutorial-hybrid-copy-portal/sql-server-linked-service-settings.png)
14. Ekkor újra a megnyitott forrásadatkészletet mutató ablak jelenik meg. A **Tulajdonságok** ablak **Kapcsolat** lapján hajtsa végre a következő lépéseket: 

    1. Ellenőrizze, hogy a **Társított szolgáltatás** mezőben szerepel-e az **SqlServerLinkedService** szolgáltatás. 
    2. A **Tábla** mezőben válassza a **[dbo].[emp]** értéket.

        ![Forrásadatkészlet – kapcsolatadatok](./media/tutorial-hybrid-copy-portal/source-dataset-connection.png)
15. Váltson az SQLServerToBlobPipeline folyamatot tartalmazó lapra (vagy) kattintson a **SQLServerToBlobPipeline** folyamatra a fanézetben. 

    ![Folyamat lap](./media/tutorial-hybrid-copy-portal/pipeliene-tab.png)
16. A **Tulajdonságok** ablakban váltson a **Fogadó** lapra, és kattintson a **+ Új** elemre. 

    ![Fogadó adatkészlet – Új gomb](./media/tutorial-hybrid-copy-portal/sink-dataset-new-button.png)
17. Az **Új adatkészlet** ablakban válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Befejezés** gombra. Egy új lap nyílik meg az adatkészlethez. Az adatkészlet fanézetben is megjelenik. 

    ![Select Azure Blob Storage](./media/tutorial-hybrid-copy-portal/select-azure-blob-storage.png)
18. A **Név** mezőben adja meg az **AzureBlobDataset** értéket.

    ![Fogadó adatkészlet – név](./media/tutorial-hybrid-copy-portal/sink-dataset-name.png)
19. Váltson a **Kapcsolat** lapra a **Tulajdonságok** ablakban, majd kattintson a **Társított szolgáltatáshoz** tartozó **+ Új** elemre. 

    ![Új társított szolgáltatás – gomb](./media/tutorial-hybrid-copy-portal/new-storage-linked-service-button.png)
20. Az **Új társított szolgáltatás** ablakban végezze el az alábbi lépéseket:

    1. A **Név** mezőbe írja az **AzureStorageLinkedService** nevet.
    2. A **Storage-fiók neve** elemnél válassza ki saját Azure Storage-fiókját. 
    3. Az Azure Storage-fiók kapcsolatának teszteléséhez kattintson a **Kapcsolat tesztelése** elemre.
    4. Kattintson a **Save** (Mentés) gombra.

        ![Azure Storage-beli társított szolgáltatás – beállítások](./media/tutorial-hybrid-copy-portal/azure-storage-linked-service-settings.png) 
21.  Ekkor újra a megnyitott fogadó adatkészletet mutató ablak jelenik meg. A **Kapcsolat** lapon végezze el a következő lépéseket: 

        1. Győződjön meg róla, hogy az **AzureStorageLinkedService** lehetőség van kiválasztva **társított szolgáltatásként**.
        2. A **Fájl elérési útja** mező **mappa** részében adja meg az **adftutorial/fromonprem** értéket. Ha a kimeneti mappa nem létezik az adftutorial tárolóban, a Data Factory szolgáltatás automatikusan létrehozza.
        3. A **Fájl elérési útja** mező **fájlnév** részében adja meg a következő értéket: `@CONCAT(pipeline().RunId, '.txt')`.

            ![Fogadó adatkészlet – kapcsolat](./media/tutorial-hybrid-copy-portal/sink-dataset-connection.png)
22. Váltson a megnyitott folyamatot tartalmazó lapra (vagy) kattintson a **folyamatra** a **fanézetben**. Győződjön meg arról, hogy a **Fogadó adatkészlet** mezőben az **AzureBlobDataset** érték van kiválasztva. 

    ![Fogadó adatkészlet kiválasztva ](./media/tutorial-hybrid-copy-portal/sink-dataset-selected.png)
23. A folyamat beállításainak érvényesítéséhez kattintson az Érvényesítés elemre a folyamat eszköztárán. A jobb sarokban található **X** gombra kattintva zárja be a **Folyamatérvényesítési jelentés** oldalt. 

    ![Folyamat érvényesítése](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
1. Az entitásokat a **Közzététel** gombra kattintva teheti közzé az Azure Data Factory szolgáltatásban.

    ![Közzététel gomb](./media/tutorial-hybrid-copy-portal/publish-button.png)
24. Várjon, amíg megjelenik a **Sikeres közzététel** felugró üzenet. A közzététel állapotát a bal oldalon található **Értesítések megjelenítése** hivatkozásra kattintva is megtekintheti. Az **X** gombra kattintva zárja be az értesítési ablakot. 

    ![A közzététel sikerült](./media/tutorial-hybrid-copy-portal/publishing-succeeded.png)
    

## <a name="trigger-a-pipeline-run"></a>Folyamat futtatásának aktiválása
Kattintson az **Aktiválás** gombra a folyamat eszköztárán, majd az **Aktiválás most** elemre.

![Aktiválás most](./media/tutorial-hybrid-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

1. Váltson a **Monitorozás** lapra. Az előző lépésben manuálisan aktivált folyamatot fogja látni. 

    ![Folyamatfuttatások](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
2. A folyamat futásához társított tevékenységfuttatások megtekintéséhez kattintson a **Tevékenységfuttatások megtekintése** hivatkozásra a **Műveletek** oszlopban. Csak tevékenységfuttatásokat fog látni, mivel csak egyetlen tevékenység található a folyamatban. A másolási művelet részleteinek megtekintéséhez kattintson a **Műveletek** oszlop **Részletek** hivatkozására (szemüveg ikon). A fenti **Folyamatok** elemre kattintva visszaválthat a folyamatfuttatások nézetre.

    ![Tevékenységfuttatások](./media/tutorial-hybrid-copy-portal/activity-runs.png)

## <a name="verify-the-output"></a>Kimenet ellenőrzése
A folyamat automatikusan létrehozza a *fromonprem* nevű kimeneti mappát az `adftutorial` blobtárolóban. Győződjön meg arról, hogy a *dbo.emp.txt* fájl megjelenik a kimeneti mappában. 

1. Az Azure Portal **adftutorial** tároló ablakában kattintson a **Frissítés** elemre a kimeneti mappa megtekintéséhez.

    ![Kimeneti mappa létrehozva](media/tutorial-hybrid-copy-portal/fromonprem-folder.png)
2. A mappák listájában válassza a `fromonprem` elemet. 
3. Ellenőrizze, hogy megjelenik-e a `dbo.emp.txt` nevű fájl.

    ![Kimeneti fájl](media/tutorial-hybrid-copy-portal/fromonprem-file.png)


## <a name="next-steps"></a>További lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra egy Azure Blob Storage-ban. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Hozzon létre egy saját üzemeltetésű integrációs modult.
> * SQL Server- és Azure Storage-beli társított szolgáltatások létrehozása. 
> * SQL Server- és Azure Blob-adatkészletek létrehozása.
> * Folyamat létrehozása másolási tevékenységgel az adatok áthelyezéséhez
> * Folyamat futásának indítása
> * A folyamat futásának monitorozása.

A Data Factory által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) cikk tartalmazza.

Folytassa a következő oktatóanyaggal, amelyben az adatok egy forrásból egy célhelyre történő tömeges másolását ismerheti meg:

> [!div class="nextstepaction"]
>[Adatok tömeges másolása](tutorial-bulk-copy-portal.md)
