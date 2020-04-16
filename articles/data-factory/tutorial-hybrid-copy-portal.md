---
title: Adatok másolása az SQL Serverből a Blob storage-ba az Azure Portal használatával
description: Megismerheti az adatok a helyszíni adattárolókból a felhőbe, az Azure Data Factory saját üzemeltetésű integrációs moduljával történő másolásának folyamatát.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 7e222762c8bfa7218fd27e15b97de7b1166d749d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418575"
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Adatok másolása helyszíni SQL Server-adatbázisból Azure Blob Storage-tárolóba

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

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
Adat-előállító példányok létrehozásához az Azure-ba való bejelentkezéshez használt felhasználói fióknak *közreműködői* vagy *tulajdonosi* szerepkört kell rendelnie, vagy az Azure-előfizetés *rendszergazdájának* kell lennie.

Az előfizetésben található engedélyek megtekintéséhez lépjen be az Azure Portalra. Válassza ki a felhasználónevet a jobb felső sarokban, majd válassza az **Engedélyek** elemet. Ha több előfizetéshez is rendelkezik hozzáféréssel, válassza ki a megfelelő előfizetést. Ha szeretne példautasításokat látni egy felhasználó szerepkörhöz adására, olvassa el a [Hozzáférés kezelése az RBAC és az Azure Portal használatával](../role-based-access-control/role-assignments-portal.md) című cikket.

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 és 2017
Ebben az oktatóanyagban egy helyszíni SQL Server-adatbázist használunk *forrásadattárként*. Az oktatóanyag során létrehozott adat-előállító folyamata adatokat másol egy helyszíni SQL Server-adatbázisból (forrás) egy Blob Storage-tárolóba (fogadó). Ezután hozzon létre egy **emp** nevű táblát az SQL Server adatbázisában, és szúrjon be néhány mintabejegyzést a táblába.

1. Indítsa el az SQL Server Management Studiót. Ha még nincs telepítve a számítógépen, tekintse meg az [SQL Server Management Studio letöltését ismertető](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) cikket.

1. Csatlakozzon az SQL Server-példányához a hitelesítő adataival.

1. Hozzon létre egy mintaadatbázist. A fanézetben kattintson a jobb gombbal a **Databases** (Adatbázisok) elemre, majd válassza a **New Database** (Új adatbázis) elemet.
1. Az **New Database** (Új adatbázis) ablakban adjon nevet az új adatbázisnak, majd kattintson az **OK** gombra.

1. Az **emp** tábla létrehozásához és néhány mintaadat beszúrásához futtassa a következő lekérdezési parancsfájlt az adatbázisban. A fanézetben kattintson a jobb gombbal a létrehozott adatbázisra, majd válassza a **New Query** (Új lekérdezés) elemet.

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

### <a name="azure-storage-account"></a>Azure Storage-fiók
Ebben az oktatóanyagban egy általános célú Azure Storage-fiókot (ebben az esetben blobtárolót) használunk cél-/fogadóadattárként. Ha még nem rendelkezik általános célú Azure Storage-fiókkal, tekintse meg a [Tárfiók létrehozását](../storage/common/storage-account-create.md) ismertető cikket. Az oktatóanyag során létrehozott adat-előállító folyamata adatokat másol a helyszíni SQL Server-adatbázisból (forrás) a Blob Storage-tárolóba (fogadó). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Tárfióknév és fiókkulcs beszerzése
Ebben az oktatóanyagban a tárfiók nevét és kulcsát használjuk. A tárfiók nevét és kulcsát az alábbi lépéseket követve szerezheti be:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-ban használt felhasználónevével és jelszavával.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Szűrjön rá a **Tárolás** kulcsszóra, majd válassza a **Tárfiókok** lehetőséget.

    ![Tárfiók keresése](media/doc-common-process/search-storage-account.png)

1. A tárfiókok listájában szükség esetén szűrje a tárfiókot. Ezután válassza ki a tárfiókot.

1. A **Tárfiók** ablakban válassza a **Hozzáférési kulcsok** elemet.

1. Másolja a **Tárfiók neve** és **1. kulcs** mező értékét, majd illessze be őket egy jegyzettömbbe vagy más szerkesztőbe az oktatóanyag későbbi részeiben történő használatra.

#### <a name="create-the-adftutorial-container"></a>Adftutorial tároló létrehozása
Ebben a szakaszban egy **adftutorial** nevű blobtárolót hoz létre a Blob Storage-ban.

1. A **Tárfiók** ablakban válassza **az Áttekintés**lehetőséget, és válassza a **Tárolók**lehetőséget.

    ![A Blobok elem választása](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. A **Tárolók ablakban** válassza a **+ Tároló lehetőséget** egy új létrehozásához.

1. Az **Új tároló** ablak **Név** mezőjébe írja be az **adftutorial** nevet. Ezután válassza **a Létrehozás lehetőséget.**

1. A tárolók listájában válassza az imént létrehozott **adftutorial** elemet.

1. Tartsa nyitva az **adftutorial** **tárolóablakát.** Segítségével ellenőrizze a kimenet végén az oktatóanyag. A Data Factory automatikusan létrehozza a kimeneti mappát a tárolóban, így nem kell újat létrehoznia.

## <a name="create-a-data-factory"></a>Data factory létrehozása
Ebben a lépésben létrehoz egy adat-előállítót, és elindítja a Data Factory felhasználói felületét, hogy létrehozzon egy folyamatot az adat-előállítóban.

1. Nyissa meg a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
1. A bal oldali menüben válassza az **Erőforrás-elemzési** > **Analytics** > **adatgyár**létrehozása lehetőséget:

   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket.

   Az adat-előállító nevének *globálisan egyedinek* kell lennie. Ha a Név mezőnél az alábbi hibaüzenet jelenik meg, módosítsa az adat-előállító nevét (például a következőre: sajátneveADFTutorialDataFactory). A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.

   ![Új adat-előállító neve](./media/doc-common-process/name-not-available-error.png)

1. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni.
1. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:

   - Válassza **a Meglévő használata**lehetőséget, és válasszon ki egy meglévő erőforráscsoportot a legördülő listából.

   - Válassza **az Új létrehozása**lehetőséget, és írja be egy erőforráscsoport nevét.
        
     Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket.
1. A **Verzió** résznél válassza a **V2** értéket.
1. A **Hely csoportban**válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. A Data Factory által használt adattárak (például a Storage és az SQL Database) és számítási erőforrások (például az Azure HDInsight) más régiókban is lehetnek.
1. Kattintson a **Létrehozás** gombra.

1. A létrehozás befejezése után megjelenik a **Data Factory** oldal a képen látható módon:

    ![Data factory kezdőlap](./media/doc-common-process/data-factory-home-page.png)
1. A Data Factory felhasználói felületének külön lapon történő elindításához válassza a **Létrehozás és monitorozás** csempét.


## <a name="create-a-pipeline"></a>Folyamat létrehozása

1. Az **Első lépések** lapon válassza a **Folyamat létrehozása** lehetőséget. A rendszer automatikusan létrehoz egy folyamatot. A folyamat fanézetben jelenik meg, és megnyílik a szerkesztő is.

   ![Első lépések lap](./media/doc-common-process/get-started-page.png)

1. A **Tulajdonságok** ablak alján lévő **Általános** lap **Név csoportjában**írja be az **SQLServerToBlobPipeline parancsot.**

1. A **Tevékenységek** eszközmezőben bontsa ki **a & átalakítás a menü ben.** Húzza a **Másolás** tevékenységet a folyamat tervezési felületére. Állítsa a tevékenység nevét a következőre: **CopySqlServerToAzureBlobActivity**.

1. A **Tulajdonságok** ablakban lépjen a **Forrás** lapra, és válassza a **+ Új** elemet.

1. Az **Új adatkészlet** párbeszédpanelen keresse meg az **SQL Server programot.** Válassza **az SQL Server**( SQL Server ) lehetőséget, majd a Continue **(Folytatás)** lehetőséget.
    ![Új SqlServer-adatkészlet](./media/tutorial-hybrid-copy-portal/create-sqlserver-dataset.png)

1. A **Tulajdonságok beállítása** párbeszédpanel **Név**mezőjébe írja be az **SqlServerDataset parancsot.** A **Csatolt szolgáltatás csoportban**válassza **a + Új**lehetőséget. Ebben a lépésben egy kapcsolatot hoz létre a forrásadatkészlettel (az SQL Server-adatbázissal).

1. Az **Új csatolt szolgáltatás** párbeszédpanelen adja hozzá a **Nevet** **SqlServerLinkedService néven.** A **Csatlakozás integrációs futásidővel csoportban**válassza a **+New**lehetőséget.  Ebben a szakaszban egy saját üzemeltetésű Integration Runtime átjárót hozhat létre, és társíthatja azt az SQL Server-adatbázist futtató helyszíni géppel. A saját üzemeltetésű integrációs modul az a komponens, amely adatokat másol a gépen futó SQL Server-adatbázisból a Blob Storage-ba.

1. Az **Integrációs futásidejű beállítás** párbeszédpanelen válassza a **Saját üzemeltetés**e lehetőséget, majd kattintson a **Folytatás gombra.**

1. Név alatt írja be **a TutorialIntegrationRuntime ( tutorialIntegrationRuntime**) értéket. Ezután válassza **a Létrehozás lehetőséget.**

1. A Beállítások csoportban **kattintson ide a számítógép expressz beállításának elindításához.** Ez a művelet telepíti az integrációs modult a számítógépére, és regisztrálja azt a Data Factoryban. Használhatja a manuális telepítési lehetőséget is. Ehhez töltse le a telepítőfájlt, futtassa, majd a kulccsal regisztrálja az integrációs modult.
    ![Integrációs modul telepítése](./media/tutorial-hybrid-copy-portal/intergration-runtime-setup.png)

1. Az **Integrációs futásidejű (saját üzemeltetésű) express telepítő** ablakban válassza a **Bezárás** lehetőséget, amikor a folyamat befejeződött.

    ![Az integrációs modul (saját üzemeltetésű) expressz telepítése](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)

1. Az **Új csatolt szolgáltatás (SQL Server)** párbeszédpanelen ellenőrizze, hogy a **TutorialIntegrationRuntime** be van-e jelölve a **Csatlakozás integrációs futásidő vel**csoportban. Ezután tegye a következő lépéseket:

    a. A **Név** mezőben adja meg az **SqlServerLinkedService** nevet.

    b. A **Kiszolgáló neve** alatt adja meg az SQL Server-példány nevét.

    c. Az **Adatbázis neve** alatt adja meg az **emp** táblával rendelkező adatbázis nevét.

    d. A **Hitelesítés típusa** alatt válassza ki azt a hitelesítési típust, amelyet a Data Factorynak az SQL Server-adatbázishoz történő csatlakozáshoz használnia kell.

    e. A **Felhasználónév** és a **Jelszó** alatt adja meg a felhasználónevet és a jelszót. Ha fordított perjel karaktert (\\) kell használnia a felhasználói fiók vagy a kiszolgáló nevében, használja előtte a feloldójelet (\\). Használja például *a\\\\mydomain myuser t.*

    f. Válassza a **Kapcsolat tesztelése** elemet. Ez a lépés annak ellenőrzése, hogy a Data Factory képes-e csatlakozni az SQL Server adatbázisához a saját üzemeltetésű integrációs futásidő használatával.

    g. A csatolt szolgáltatás mentéséhez válassza a **Létrehozás gombot.**
 
    ![Új csatolt szolgáltatás (SQL Server)](./media/tutorial-hybrid-copy-portal/new-sqlserver-linked-service.png)

1. A csatolt szolgáltatás létrehozása után visszatér az SqlServerDataset **tulajdonságkészlet készlete** lapjára. Hajtsa végre a következő lépéseket:

    a. Ellenőrizze, hogy a **Társított szolgáltatás** mezőben látható-e az **SqlServerLinkedService** szolgáltatás.

    b. A **Tábla neve csoportban**válassza a **[dbo].[ emp]**.
    
    c. Válassza **az OK gombot.**

1. Lépjen az **SQLServerToBlobPipeline** folyamatot tartalmazó lapra, vagy válassza az **SQLServerToBlobPipeline** folyamatot a fanézetben.

1. Lépjen a **Fogadó** lapra a **Tulajdonságok** ablak alján, és válassza a **+ Új** elemet.

1. Az **Új adatkészlet** párbeszédpanelen válassza az **Azure Blob Storage lehetőséget.** Ezután válassza a **Folytatás** elemet.

1. A **Formátum kiválasztása** párbeszédpanelen adja meg az adatok formátumtípusát. Ezután válassza a **Folytatás** elemet.

    ![Adatformátum kiválasztása](./media/doc-common-process/select-data-format.png)

1. A **Tulajdonságok beállítása** párbeszédpanelen adja meg az **AzureBlobDataset** értéket a Név mezőbe. A **Társított szolgáltatások** szövegbeviteli mező mellett válassza az **+ Új** lehetőséget.

1. Az **új csatolt szolgáltatás (Azure Blob Storage)** párbeszédpanelen adja meg **az AzureStorageLinkedService** nevet, válassza ki a tárfiókot a **Storage-fiók** névlistájából. Tesztelje a kapcsolatot, majd a **Létrehozás gombra** a csatolt szolgáltatás központi telepítéséhez.

1. A csatolt szolgáltatás létrehozása után visszatér a **Tulajdonságok beállítása** laphoz. Válassza **az OK gombot.**

1. Nyissa meg a fogadó adatkészletet. A **Kapcsolat** lapon hajtsa végre az alábbi lépéseket:

    a. Ellenőrizze, hogy a **Társított szolgáltatás** részen az **AzureStorageLinkedService** van-e kiválasztva.

    b. A **Fájl elérési útja**mezőbe írja be az **adftutorial/fromonprem** értéket a **Tároló/ Könyvtár** részhez. Ha a kimeneti mappa nem létezik az adftutorial tárolóban, a Data Factory automatikusan létrehozza azt.

    c. A **Fájl** résznél válassza a **Dinamikus tartalom hozzáadása**lehetőséget.
    ![dinamikus kifejezés a fájlnév feloldásához](./media/tutorial-hybrid-copy-portal/file-name.png)

    d. Adja `@CONCAT(pipeline().RunId, '.txt')`hozzá a lehetőséget, majd válassza **a Befejezés gombot.** Ez a művelet átnevezi a fájlt pipelineRunID.txt fájllal.

1. Lépjen a megnyitott folyamatot tartalmazó lapra, vagy válassza ki a folyamatot a fanézetben. Ellenőrizze, hogy a **Fogadó-adatkészlet** mezőben az **AzureBlobDataset** érték van-e kiválasztva.

1. A folyamat beállításainak érvényesítéséhez válassza az **Érvényesítés** elemet a folyamat eszköztárán. A **Csőérvényesítési kimenet** **>>** bezárásához kattintson az ikonra.
    ![folyamat ellenőrzése](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
    

1. Ha a Létrehozott entitásokat adatgyárban szeretné közzétenni, válassza **az Összes közzététele**lehetőséget.

1. Várjon, amíg megjelenik a **közzététel befejeződött** előugró ablak. A közzététel állapotának ellenőrzéséhez válassza az **Értesítések megjelenítése** hivatkozást az ablak tetején. Az értesítési ablak bezárásához válassza a **Bezárás** elemet.


## <a name="trigger-a-pipeline-run"></a>Folyamat futtatásának aktiválása
Válassza az **Eseményindító hozzáadása lehetőséget** a folyamat eszköztárán, majd az **Eseményindító most**lehetőséget.

## <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

1. Nyissa meg a **Monitor** lapot. Az előző lépésben manuálisan aktivált folyamat jelenik meg.

1. A folyamatfuttatáshoz társított tevékenységfuttatások megtekintéséhez válassza az **SQLServerToBlobPipeline** kapcsolatot a *PIPELINE NAME (FOLYAMATNEVE)* területen. 
    ![Folyamatfuttatások monitorozása](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)

1. A **Tevékenység futtatása** lapon válassza a Részletek (szemüveg kép) hivatkozást a másolási művelet részleteinek megtekintéséhez. Ha vissza szeretne lépni a Folyamatfuttatások nézetbe, válassza a Minden **folyamat futtatása** a tetején lehetőséget.

## <a name="verify-the-output"></a>Kimenet ellenőrzése
A folyamat automatikusan létrehozza a *fromonprem* nevű kimeneti mappát az `adftutorial` blobtárolóban. Ellenőrizze, hogy a *[pipeline().RunId].txt* fájl megjelenik-e a kimeneti mappában.


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
