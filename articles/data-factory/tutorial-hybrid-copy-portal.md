---
title: Adatok másolása az SQL Server-adatbázisból Blob Storage-tárolóba az Azure Data Factory használatával | Microsoft Docs
description: Megismerheti az adatok a helyszíni adattárolókból a felhőbe, az Azure Data Factory saját üzemeltetésű integrációs moduljával történő másolásának folyamatát.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: jingwang
ms.openlocfilehash: e007fd5e616b24b3a802b7ded0cd879bb0d7f34b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436008"
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Adatok másolása helyszíni SQL Server-adatbázisból Azure Blob Storage-tárolóba
Ebben az oktatóanyagban az Azure Data Factory felhasználói felületének (UI) használatával egy adat-előállító folyamatot hoz létre az adatok egy helyszíni SQL Server-adatbázisból egy Azure Blob-tárolóba történő másolására. Létrehozhat és alkalmazhat egy saját üzemeltetésű integrációs modult, amely adatokat helyez át a helyszíni és a felhőalapú adattárolók között.

> [!NOTE]
> Ez a cikk nem mutatja be részletesen a Data Factory szolgáltatást. További információ: [A Data Factory bemutatása](introduction.md). 

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
Az adat-előállító példányok létrehozásához annak a felhasználói fióknak, amellyel bejelentkezik az Azure-ba, a *közreműködő* vagy *tulajdonos* szerepkör tagjának, vagy az Azure-előfizetés *rendszergazdájának* kell lennie. 

Az előfizetésben található engedélyek megtekintéséhez lépjen be az Azure Portalra. Válassza ki a felhasználónevet a jobb felső sarokban, majd válassza az **Engedélyek** elemet. Ha több előfizetéshez is rendelkezik hozzáféréssel, válassza ki a megfelelő előfizetést. Ha szeretne példautasításokat látni egy felhasználó szerepkörhöz adására, olvassa el a [Szerepkörök hozzáadása](../billing/billing-add-change-azure-subscription-administrator.md) című cikket.

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 és 2017
Ebben az oktatóanyagban egy helyszíni SQL Server-adatbázist használunk *forrásadattárként*. Az oktatóanyag során létrehozott adat-előállító folyamata adatokat másol egy helyszíni SQL Server-adatbázisból (forrás) egy Blob Storage-tárolóba (fogadó). Ezután létre fog hozni egy **emp** nevű táblát az SQL Server-adatbázisban, és beszúr a táblába néhány mintabejegyzést. 

1. Indítsa el az SQL Server Management Studiót. Ha még nincs telepítve a számítógépen, tekintse meg az [SQL Server Management Studio letöltését ismertető](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) cikket. 

1. Csatlakozzon az SQL Server-példányához a hitelesítő adataival. 

1. Hozzon létre egy mintaadatbázist. A fanézetben kattintson a jobb gombbal a **Databases** (Adatbázisok) elemre, majd válassza a **New Database** (Új adatbázis) elemet. 
1. Az **New Database** (Új adatbázis) ablakban adjon nevet az új adatbázisnak, majd kattintson az **OK** gombra. 

1. Az **emp** tábla létrehozásához és néhány mintaadat beszúrásához futtassa a következő lekérdezési szkriptet az adatbázison:

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

1. A fanézetben kattintson a jobb gombbal a létrehozott adatbázisra, majd válassza a **New Query** (Új lekérdezés) elemet.

### <a name="azure-storage-account"></a>Azure Storage-fiók
Ebben az oktatóanyagban egy általános célú Azure Storage-fiókot (ebben az esetben blobtárolót) használunk cél-/fogadóadattárként. Ha még nem rendelkezik általános célú Azure Storage-fiókkal, tekintse meg a [Tárfiók létrehozását](../storage/common/storage-create-storage-account.md#create-a-storage-account) ismertető cikket. Az oktatóanyag során létrehozott adat-előállító folyamata adatokat másol a helyszíni SQL Server-adatbázisból (forrás) a Blob Storage-tárolóba (fogadó). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Tárfióknév és fiókkulcs beszerzése
Ebben az oktatóanyagban a tárfiók nevét és kulcsát használjuk. A tárfiók nevét és kulcsát az alábbi lépéseket követve szerezheti be: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-ban használt felhasználónevével és jelszavával. 

1. A bal oldali panelen válassza a **További szolgáltatások** lehetőséget. Szűrjön rá a **Tárolás** kulcsszóra, majd válassza a **Tárfiókok** lehetőséget.

    ![Tárfiók keresése](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

1. A tárfiókok listájában állítson be szűrőt a tárfiókhoz, ha szükséges. Ezután válassza ki a tárfiókot. 

1. A **Tárfiók** ablakban válassza a **Hozzáférési kulcsok** elemet.

    ![Elérési kulcs](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

1. Másolja a **Tárfiók neve** és **1. kulcs** mező értékét, majd illessze be őket egy jegyzettömbbe vagy más szerkesztőbe az oktatóanyag későbbi részeiben történő használatra. 

#### <a name="create-the-adftutorial-container"></a>Adftutorial tároló létrehozása 
Ebben a szakaszban egy **adftutorial** nevű blobtárolót hoz létre a Blob Storage-ban. 

1. A **Tárfiók** ablakban lépjen az **Áttekintés** panelre, majd válassza a **Blobok** elemet. 

    ![A Blobok elem választása](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. A **Blob service** ablakban válassza a **Tároló** elemet. 

    ![Tároló gomb](media/tutorial-hybrid-copy-powershell/add-container-button.png)

1. Az **Új tároló** ablak **Név** mezőjébe írja be az **adftutorial** nevet. Ezután kattintson az **OK** gombra. 

    ![Új tároló ablak](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

1. A tárolók listájában kattintson az **adftutorial** elemre.

    ![Tároló kiválasztása](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

1. Ne zárja be az **adftutorial** **tároló** ablakát. A segítségével ellenőrizheti az oktatóanyag eredményét. A Data Factory automatikusan létrehozza a kimeneti mappát a tárolóban, így nem kell újat létrehoznia.

    ![Tároló ablaka](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Data factory létrehozása
Ebben a lépésben létrehoz egy adat-előállítót, és elindítja a Data Factory felhasználói felületét, hogy létrehozzon egy folyamatot az adat-előállítóban. 

1. Nyissa meg a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
1. A bal oldali menüben válassza az **Új** > **Adatok + analitika** > **Adat-előállító** elemet.
   
   ![Új adat-előállító létrehozása](./media/tutorial-hybrid-copy-portal/new-azure-data-factory-menu.png)
1. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket. 
   
     ![Új adat-előállító lap](./media/tutorial-hybrid-copy-portal/new-azure-data-factory.png)

Az adat-előállító nevének *globálisan egyedinek* kell lennie. Ha a Név mezőnél az alábbi hibaüzenet jelenik meg, módosítsa az adat-előállító nevét (például a következőre: sajátneveADFTutorialDataFactory). A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.

   ![Új adat-előállító neve](./media/tutorial-hybrid-copy-portal/name-not-available-error.png)
1. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni.
1. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:
   
      - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot.

      - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.
        
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/resource-group-overview.md) ismertető cikket.
1. A **Verzió** résznél válassza a **V2** értéket.
1. A **Hely** alatt válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. A Data Factory által használt adattárak (például a Storage és az SQL Database) és számítási erőforrások (például az Azure HDInsight) más régiókban is lehetnek.
1. Válassza a **Rögzítés az irányítópulton** lehetőséget. 
1. Kattintson a **Létrehozás** gombra.
1. Az irányítópulton megjelenő csempén a **Adat-előállító üzembe helyezése** állapotleírás látható:

    ![Adat-előállító üzembe helyezése csempe](media/tutorial-hybrid-copy-portal/deploying-data-factory.png)
1. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg:
   
    ![Data factory kezdőlap](./media/tutorial-hybrid-copy-portal/data-factory-home-page.png)
1. A Data Factory felhasználói felületének külön lapon történő elindításához válassza a **Létrehozás és monitorozás** csempét. 


## <a name="create-a-pipeline"></a>Folyamat létrehozása

1. Az **Első lépések** lapon válassza a **Folyamat létrehozása** lehetőséget. A rendszer automatikusan létrehoz egy folyamatot. A folyamat fanézetben jelenik meg, és megnyílik a szerkesztő is. 

   ![Első lépések lap](./media/tutorial-hybrid-copy-portal/get-started-page.png)

1. Az **Általános** lap **Tulajdonságok** ablakának alján a **Név** alatt írja be az **SQLServerToBlobPipeline** nevet.

   ![Folyamat neve](./media/tutorial-hybrid-copy-portal/pipeline-name.png)

1. A **Tevékenységek** eszközkészletben bontsa ki a **DataFlow** elemet. Húzza a **Másolás** tevékenységet a folyamat tervezési felületére. Állítsa a tevékenység nevét a következőre: **CopySqlServerToAzureBlobActivity**.

   ![Tevékenység neve](./media/tutorial-hybrid-copy-portal/copy-activity-name.png)

1. A **Tulajdonságok** ablakban lépjen a **Forrás** lapra, és válassza a **+ Új** elemet.

   ![Forrás lap](./media/tutorial-hybrid-copy-portal/source-dataset-new-button.png)

1. Az **Új adatkészlet** ablakban keresse meg az **SQL Server** elemet. Válassza az **SQL Server**, majd a **Befejezés** elemet. Ekkor megjelenik egy **SqlServerTable1** nevű új lap. A bal oldalon fanézetben is megtekintheti az **SqlServerTable1** adatkészletet. 

   ![Az SQL Server kiválasztása](./media/tutorial-hybrid-copy-portal/select-sql-server.png)

1. Az **Általános** lap **Tulajdonságok** ablakának alján a **Név** alatt írja be az **SqlServerDataset** nevet.

   ![Forrásadatkészlet neve](./media/tutorial-hybrid-copy-portal/source-dataset-name.png)

1. Lépjen a **Kapcsolat** lapra, és válassza a **+ Új** lehetőséget. Ebben a lépésben egy kapcsolatot hoz létre a forrásadatkészlettel (az SQL Server-adatbázissal). 

   ![Kapcsolódás a forrásadatkészlethez](./media/tutorial-hybrid-copy-portal/source-connection-new-button.png)

1. Az **Új társított szolgáltatás** ablakban a **Név** értékeként adja meg a következőt: **SqlServerLinkedService**. A **Csatlakozás integrációs modulon keresztül** területen válassza az **Új** lehetőséget. Ebben a szakaszban egy saját üzemeltetésű Integration Runtime átjárót hozhat létre, és társíthatja azt az SQL Server-adatbázist futtató helyszíni géppel. A saját üzemeltetésű integrációs modul az a komponens, amely adatokat másol a gépen futó SQL Server-adatbázisból a Blob Storage-ba. 

   ![Új integrációs modul](./media/tutorial-hybrid-copy-portal/new-integration-runtime-button.png)

1. Az **Integrációs modul telepítése** ablakban válassza a **Magánhálózat** lehetőséget, majd kattintson a **Tovább** gombra. 

   ![Magánhálózat kiválasztása](./media/tutorial-hybrid-copy-portal/select-private-network.png)

1. Adja meg az integrációs modul nevét, majd kattintson a **Tovább** gombra.

    ![Integrációs modul neve](./media/tutorial-hybrid-copy-portal/integration-runtime-name.png)

1. Az **1. lehetőség: Expressz telepítés** alatt kattintson a **Kattintson ide a számítógépen történő expressz telepítés indításához** elemre. 

    ![Expressz telepítés hivatkozás](./media/tutorial-hybrid-copy-portal/click-exress-setup.png)

1. Az **Integrációs modul (Saját üzemeltetésű) – Expressz telepítés** ablakban válassza a **Bezárás** elemet. 

    ![Az integrációs modul (saját üzemeltetésű) expressz telepítése](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)

1. Az **Új társított szolgáltatás** ablakban ellenőrizze, hogy a fent létrehozott **integrációs modul** ki van-e választva a **Csatlakozás integrációs modulon keresztül** területen. 

    ![](./media/tutorial-hybrid-copy-portal/select-integration-runtime.png)

1. Az **Új társított szolgáltatás** ablakban végezze el az alábbi lépéseket:

    a. A **Név** mezőben adja meg az **SqlServerLinkedService** nevet.

    b. Ellenőrizze, hogy a **Csatlakozás integrációs modulon keresztül** alatt a korábban létrehozott saját üzemeltetésű integrációs modul jelenik-e meg.

    c. A **Kiszolgáló neve** alatt adja meg az SQL Server-példány nevét. 

    d. Az **Adatbázis neve** alatt adja meg az **emp** táblával rendelkező adatbázis nevét.

    e. A **Hitelesítés típusa** alatt válassza ki azt a hitelesítési típust, amelyet a Data Factorynak az SQL Server-adatbázishoz történő csatlakozáshoz használnia kell.

    f. A **Felhasználónév** és a **Jelszó** alatt adja meg a felhasználónevet és a jelszót. Ha fordított perjel karaktert (\\) kell használnia a felhasználói fiók vagy a kiszolgáló nevében, használja előtte a feloldójelet (\\). Használja például a *sajáttartomány\\\\sajátfelhasználó* értéket.

    g. Válassza a **Kapcsolat tesztelése** elemet. Ez a lépés annak ellenőrzésére szolgál, hogy a Data Factory tud-e kapcsolódni az SQL Server-adatbázishoz az Ön által létrehozott saját üzemeltetésű integrációs modullal.

    h. A társított szolgáltatás mentéséhez kattintson a **Befejezés** gombra.

       

1. Ekkor újra a megnyitott forrásadatkészletet mutató ablak jelenik meg. A **Tulajdonságok** ablak **Kapcsolat** lapján hajtsa végre az alábbi lépéseket: 

    a. Ellenőrizze, hogy a **Társított szolgáltatás** mezőben látható-e az **SqlServerLinkedService** szolgáltatás.

    b. A **Tábla** területen válassza a **[dbo].[emp]** elemet.

    ![Forrásadatkészlet – kapcsolatadatok](./media/tutorial-hybrid-copy-portal/source-dataset-connection.png)

1. Lépjen az **SQLServerToBlobPipeline** folyamatot tartalmazó lapra, vagy válassza az **SQLServerToBlobPipeline** folyamatot a fanézetben. 

    ![Folyamat lap](./media/tutorial-hybrid-copy-portal/pipeliene-tab.png)

1. Lépjen a **Fogadó** lapra a **Tulajdonságok** ablak alján, és válassza a **+ Új** elemet. 

    ![Fogadó lap](./media/tutorial-hybrid-copy-portal/sink-dataset-new-button.png)

1. Az **Új adatkészlet** ablakban válassza az **Azure Blob Storage** lehetőséget. Ezután kattintson a **Befejezés** gombra. Egy új lap nyílik meg az adatkészlethez. Az adatkészlet fanézetben is megjelenik. 

    ![Blob-tároló kiválasztása](./media/tutorial-hybrid-copy-portal/select-azure-blob-storage.png)

1. A **Név** mezőben adja meg az **AzureBlobDataset** nevet.

    ![Fogadó-adatkészlet neve](./media/tutorial-hybrid-copy-portal/sink-dataset-name.png)

1. Lépjen a **Kapcsolat** lapra a **Tulajdonságok** ablak alján. A **Társított szolgáltatás** mellett válassza a **+ Új** lehetőséget. 

    ![Új társított szolgáltatás gomb](./media/tutorial-hybrid-copy-portal/new-storage-linked-service-button.png)

1. Az **Új társított szolgáltatás** ablakban végezze el az alábbi lépéseket:

    a. A **Név** mezőbe írja be az **AzureStorageLinkedService** nevet.

    b. A **Tárfiók neve** alatt válassza ki a tárfiókja nevét.

    c. A tárfiókkal létesített kapcsolat teszteléséhez válassza a **Kapcsolat tesztelése** elemet.

    d. Kattintson a **Mentés** gombra.

    ![A Storage társított szolgáltatásának beállításai](./media/tutorial-hybrid-copy-portal/azure-storage-linked-service-settings.png) 

1. Ekkor újra a megnyitott fogadó adatkészletet mutató ablak jelenik meg. A **Kapcsolat** lapon hajtsa végre az alábbi lépéseket: 

       a. In **Linked service**, confirm that **AzureStorageLinkedService** is selected.

       b. For the **folder**/ **Directory** part of **File path**, enter **adftutorial/fromonprem**. If the output folder doesn't exist in the adftutorial container, Data Factory automatically creates the output folder.

       c. For the **file name** part of **File path**, select **Add dynamic content**.   

    ![dinamikus fájlnévérték](./media/tutorial-hybrid-copy-portal/file-name.png)

       d. Add `@CONCAT(pipeline().RunId, '.txt')`, select **Finish**. This will rename the file with PipelineRunID.txt. 

    ![dinamikus kifejezés a fájlnév feloldásához](./media/tutorial-hybrid-copy-portal/add-dynamic-file-name.png)

    ![Kapcsolódás a fogadó-adatkészlethez](./media/tutorial-hybrid-copy-portal/sink-dataset-connection.png)

1. Lépjen a megnyitott folyamatot tartalmazó lapra, vagy válassza ki a folyamatot a fanézetben. Ellenőrizze, hogy a **Fogadó-adatkészlet** mezőben az **AzureBlobDataset** érték van-e kiválasztva. 

    ![Fogadó adatkészlet kiválasztva](./media/tutorial-hybrid-copy-portal/sink-dataset-selected.png)

1. A folyamat beállításainak érvényesítéséhez válassza az **Érvényesítés** elemet a folyamat eszköztárán. A **Folyamatérvényesítési jelentés** bezárásához válassza a **Bezárás** elemet. 

    ![Folyamat érvényesítése](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)

1. A Data Factory használatával létrehozott entitások közzétételéhez válassza az **Összes közzététele** elemet.

    ![Közzététel gomb](./media/tutorial-hybrid-copy-portal/publish-button.png)

1. Várjon, amíg megjelenik a **Sikeres közzététel** felugró üzenet. A közzététel állapotának ellenőrzéséhez válassza a bal oldalon található **Értesítések megjelenítése** hivatkozást. Az értesítési ablak bezárásához válassza a **Bezárás** elemet. 

    ![A közzététel sikerült](./media/tutorial-hybrid-copy-portal/publishing-succeeded.png)


## <a name="trigger-a-pipeline-run"></a>Folyamat futtatásának aktiválása
A folyamat eszköztárán kattintson az **Aktiválás** gombra, majd válassza az **Aktiválás most** elemet.

![Folyamat futtatásának aktiválása](./media/tutorial-hybrid-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

1. Lépjen a **Monitorozás** lapra. Az előző lépésben manuálisan aktivált folyamatot fogja látni. 

    ![Folyamatfuttatások monitorozása](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
1. A folyamat futásához társított tevékenységfuttatások megtekintéséhez kattintson a **Műveletek** oszlopban található **Tevékenységfuttatások megtekintése** hivatkozásra. Csak tevékenységfuttatásokat fog látni, mert csak egyetlen tevékenység található a folyamatban. A másolási művelet részleteinek megtekintéséhez válassza a **Műveletek** oszlop **Részletek** hivatkozását (szemüveg ikon). A **Folyamatfuttatások** nézetre való visszalépéshez válassza a lap tetejének közelében található **Folyamatok** elemet.

    ![Tevékenységfuttatások monitorozása](./media/tutorial-hybrid-copy-portal/activity-runs.png)

## <a name="verify-the-output"></a>Kimenet ellenőrzése
A folyamat automatikusan létrehozza a *fromonprem* nevű kimeneti mappát az `adftutorial` blobtárolóban. Ellenőrizze, hogy a *[pipeline().RunId].txt* fájl megjelenik-e a kimeneti mappában. 

![kimeneti fájl nevének ellenőrzése](./media/tutorial-hybrid-copy-portal/sink-output.png)


## <a name="next-steps"></a>További lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra a Blob Storage-ban. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Hozzon létre egy saját üzemeltetésű integrációs modult.
> * SQL Server- és Storage-beli társított szolgáltatások létrehozása. 
> * SQL Server- és Blob Storage-adatkészletek létrehozása.
> * Folyamat létrehozása másolási tevékenységgel az adatok áthelyezéséhez
> * Folyamat futásának indítása
> * A folyamat futásának monitorozása.

A Data Factory által támogatott adattárak listáját a [támogatott adattárakat](copy-activity-overview.md#supported-data-stores-and-formats) ismertető cikk tartalmazza.

Az adatok egy forrásból egy célhelyre történő tömeges másolásának megismeréséhez lépjen a következő oktatóanyagra:

> [!div class="nextstepaction"]
>[Adatok tömeges másolása](tutorial-bulk-copy-portal.md)
