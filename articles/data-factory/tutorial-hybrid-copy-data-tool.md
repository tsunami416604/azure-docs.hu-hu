---
title: Helyszíni adatok másolása az Azure Adatok másolása eszközével | Microsoft Docs
description: Létrehozhat egy Azure-beli adat-előállítót, majd az Adatok másolása eszközzel másolhat adatokat egy helyszíni SQL Server-adatbázisból az Azure Blob Storage-tárolóba.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/04/2018
ms.author: jingwang
ms.openlocfilehash: 85b721df1e666903c4966ca240c433ded01c06b7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Adatok másolása az Adatok másolása eszközzel helyszíni SQL Server-adatbázisból Azure Blob Storage-tárolóba
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [2. verzió – Előzetes verzió](tutorial-hybrid-copy-data-tool.md)

Ebben az oktatóanyagban egy adat-előállítót hoz létre az Azure Portal használatával. Ezután az Adatok másolása eszközzel létrehoz egy folyamatot, amely egy helyszíni SQL Server-adatbázisból másol adatokat egy Azure-blobtárolóba.

> [!NOTE]
> - Ha még csak ismerkedik az Azure Data Factory szolgáltatással, olvassa el a [Data Factory szolgáltatást bemutató](introduction.md) cikket.
>
> - Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory általánosan elérhető 1. verzióját használja, tekintse meg a [Data Factory 1. verzió használatának első lépéseit](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása az Adatok másolása eszközzel.
> * A folyamat és a tevékenységek futásának monitorozása

## <a name="prerequisites"></a>Előfeltételek
### <a name="azure-subscription"></a>Azure-előfizetés
Ha még nem rendelkezik Azure-előfizetéssel, első lépésként [hozzon létre egy ingyenes](https://azure.microsoft.com/free/) fiókot.

### <a name="azure-roles"></a>Azure-szerepkörök
Az adat-előállító példányok létrehozásához annak a felhasználói fióknak, amellyel belép az Azure-ba, a *közreműködő* vagy *tulajdonos* szerepkör tagjának, vagy az Azure-előfizetés *rendszergazdájának* kell lennie. 

Az előfizetésben található engedélyek megtekintéséhez lépjen be az Azure Portalra. Válassza ki a felhasználónevét a jobb felső sarokban, majd válassza az **Engedélyek** elemet. Ha több előfizetéshez is rendelkezik hozzáféréssel, válassza ki a megfelelő előfizetést. Ha szeretne példautasításokat látni egy felhasználó szerepkörhöz adására, olvassa el a [Szerepkörök hozzáadása](../billing/billing-add-change-azure-subscription-administrator.md) című cikket.

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 és 2017
Ebben az oktatóanyagban egy helyszíni SQL Server-adatbázist használunk *forrásadattárként*. Az oktatóanyag során létrehozott adat-előállító folyamata adatokat másol egy helyszíni SQL Server-adatbázisból (forrás) egy Blob Storage-tárolóba (fogadó). Ezután létre fog hozni egy **emp** nevű táblát az SQL Server-adatbázisban, és beszúr a táblába néhány mintabejegyzést. 

1. Indítsa el az SQL Server Management Studiót. Ha még nincs telepítve a számítógépen, tekintse meg az [SQL Server Management Studio letöltését ismertető](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) cikket. 

2. Csatlakozzon az SQL Server-példányához a hitelesítő adataival. 

3. Hozzon létre egy mintaadatbázist. A fanézetben kattintson a jobb gombbal a **Databases** (Adatbázisok) elemre, majd válassza a **New Database** (Új adatbázis) elemet. 
 
4. Az **New Database** (Új adatbázis) ablakban adjon nevet az új adatbázisnak, majd kattintson az **OK** gombra. 

5. Az **emp** tábla létrehozásához és néhány mintaadat beszúrásához futtassa a következő lekérdezési szkriptet az adatbázison. A fanézetben kattintson a jobb gombbal a létrehozott adatbázisra, majd válassza a **New Query** (Új lekérdezés) elemet.

    ```sql
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
Ebben az oktatóanyagban egy általános célú Azure Storage-fiókot (ebben az esetben blobtárolót) használunk cél-/fogadóadattárként. Ha még nem rendelkezik általános célú tárfiókkal, a létrehozást segítő útmutatásért tekintse meg a [tárfiók létrehozását](../storage/common/storage-create-storage-account.md#create-a-storage-account) ismertető cikket. Az oktatóanyag során létrehozott adat-előállító folyamata adatokat másol a helyszíni SQL Server-adatbázisból (forrás) a Blob Storage tárolóba (fogadó). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Tárfióknév és fiókkulcs beszerzése
Ebben az oktatóanyagban a tárfiók nevét és kulcsát használjuk. A tárfiók nevét és kulcsát az alábbi lépéseket követve szerezheti be: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-ban használt felhasználónevével és jelszavával. 

2. A bal oldali panelen válassza a **További szolgáltatások** lehetőséget. Szűrjön rá a **Tárolás** kulcsszóra, majd válassza a **Tárfiókok** lehetőséget.

    ![Tárfiók keresése](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. A tárfiókok listájában állítson be szűrőt a tárfiókhoz, ha szükséges. Ezután válassza ki a tárfiókot. 

4. A **Tárfiók** ablakban válassza a **Hozzáférési kulcsok** elemet.

    ![Elérési kulcs](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. Másolja a **Tárfiók neve** és **1. kulcs** mező értékét, majd illessze be őket egy jegyzettömbbe vagy más szerkesztőbe az oktatóanyag későbbi részeiben történő használatra. 

#### <a name="create-the-adftutorial-container"></a>Adftutorial tároló létrehozása 
Ebben a szakaszban egy **adftutorial** nevű blobtárolót hoz létre a Blob Storage-ban. 

1. A **Tárfiók** ablakban váltson át az **Áttekintés** panelre, majd válassza a **Blobok** elemet. 

    ![A Blobok elem választása](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. A **Blob service** ablakban válassza a **Tároló** elemet. 

    ![Tároló gomb](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. Az **Új tároló** ablak **Név** mezőjébe írja be az **adftutorial** nevet, majd kattintson az **OK** gombra. 

    ![Új tároló](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. A tárolók listájában kattintson az **adftutorial** elemre.

    ![Tároló kiválasztása](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. Ne zárja be az **adftutorial** **tároló** ablakát. A segítségével ellenőrizheti az oktatóanyag eredményét. A Data Factory automatikusan létrehozza a kimeneti mappát a tárolóban, így nem kell újat létrehoznia.

    ![Tároló ablaka](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza az **Új** > **Adatok + analitika** > **Adat-előállító** elemet. 
   
   ![Új adat-előállító létrehozása](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory-menu.png)
2. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket. 
      
     ![Új adat-előállító](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory.png)
 
   Az adat-előállító nevének *globálisan egyedinek* kell lennie. Ha a Név mezőnél az alábbi hibaüzenet jelenik meg, módosítsa az adat-előállító nevét (például a következőre: sajátneveADFTutorialDataFactory). A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
   ![Új adat-előállító neve](./media/tutorial-hybrid-copy-data-tool/name-not-available-error.png)
3. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
4. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:
     
      - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot.

      - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét. 
         
      Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/resource-group-overview.md) ismertető cikket.
5. A **Verzió** alatt válassza a **V2 (előzetes verzió)** értéket.
6. A **Hely** alatt válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (például Azure Storage és SQL Database) és számítási erőforrások (például Azure HDInsight) más helyeken/régiókban is lehetnek.
7. Válassza a **Rögzítés az irányítópulton** lehetőséget. 
8. Kattintson a **Létrehozás** gombra.
9. Az irányítópulton megjelenő csempén a **Adat-előállító üzembe helyezése** állapotleírás látható:

    ![Adat-előállító üzembe helyezése csempe](media/tutorial-hybrid-copy-data-tool/deploying-data-factory.png)
10. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
    ![Data factory kezdőlap](./media/tutorial-hybrid-copy-data-tool/data-factory-home-page.png)
11. A Data Factory felhasználói felületének külön lapon történő elindításához válassza a **Tartalomkészítés és monitorozás** csempét. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Folyamat létrehozása az Adatok másolása eszközzel

1. Az Adatok másolása eszköz elindításához az **első lépéseket** ismertető oldalon válassza az **Adatok másolása** csempét. 

   ![Az Adatok másolása eszköz csempéje](./media/tutorial-hybrid-copy-data-tool/copy-data-tool-tile.png)
2. Az Adatok másolása eszköz **Tulajdonságok** lapján a **Feladat neve** alatt írja be a következőt: **CopyFromOnPremSqlToAzureBlobPipeline**. Ezután kattintson a **Tovább** gombra. Az Adatok másolása eszköz létrehoz egy folyamatot a mezőben megadott néven. 
    
   ![Feladat neve](./media/tutorial-hybrid-copy-data-tool/properties-page.png)
3. A **Forrásadattár** lapon válassza ki az **SQL Server** elemet, majd kattintson a **Tovább** gombra. Lehet, hogy az **SQL Server** elemet csak legörgetve találja meg a listában. 

   ![Az SQL Server kiválasztása](./media/tutorial-hybrid-copy-data-tool/select-source-data-store.png)
4. A **Kapcsolat neve** alatt adja meg a **SqlServerLinkedService** nevet. Kattintson az **Integrációs modul létrehozása** hivatkozásra. Létre kell hoznia egy saját üzemeltetésű integrációs modult, le kell töltenie a gépére, és regisztrálnia kell a Data Factoryban. A saját üzemeltetésű integrációs modul adatokat másol a helyszíni környezetből a felhőbe.

   ![Saját üzemeltetésű integrációs modul létrehozása](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-link.png)
5. Az **Integrációs modul létrehozása** párbeszédpanelen a **Név** mezőbe írja a **TutorialIntegrationRuntime** értéket. Ezután kattintson a **Létrehozás** elemre. 

   ![Integrációs modul neve](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog.png)
6. Kattintson az **Expressz telepítés indítása ezen a számítógépen** elemre. Ez a művelet telepíti az integrációs modult a számítógépére, és regisztrálja azt a Data Factoryban. Használhatja a manuális telepítési lehetőséget is. Ehhez töltse le a telepítőfájlt, futtassa, majd a kulccsal regisztrálja az integrációs modult. 

    ![Expressz telepítés indítása ezen a számítógépen hivatkozás](./media/tutorial-hybrid-copy-data-tool/launch-express-setup-link.png)
7. Futtassa a letöltött alkalmazást. Az expressz telepítés állapota látható az ablakban. 

    ![Expressz telepítés állapota](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)
8. Ellenőrizze, hogy a **TutorialIntegrationRuntime** van-e kiválasztva az **Integrációs modul** mezőben.

    ![Integrációs modul kiválasztva](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)
9. A **Helyszíni SQL Server-adatbázis megadása** területen tegye a következőket: 

    a. A **Kapcsolat neve** mezőben adja meg az **OnPremSqlLinkedService** értéket.

    b. A **Kiszolgáló neve** alatt adja meg a helyszíni SQL Server-példánya nevét.

    c. Az **Adatbázis neve** mezőben adja meg a helyszíni adatbázis nevét.

    d. A **Hitelesítés típusa** mezőben válassza a megfelelő hitelesítést.

    e. A **Felhasználónév** mezőben adja meg a helyszíni SQL Server-kiszolgálóhoz hozzáféréssel rendelkező felhasználó nevét.

    f. Adja meg a felhasználónévhez tartozó **jelszót**. 
10. A **Másolni kívánt adatokat tartalmazó táblák kiválasztása vagy egyéni lekérdezés használata** lapon válassza a listáról a **[dbo].[emp]** táblát, és kattintson a **Tovább** gombra. 

    ![Az emp tábla kiválasztása](./media/tutorial-hybrid-copy-data-tool/select-emp-table.png)
11. A **Céladattár** lapon válassza ki az **Azure Blob Storage** elemet, majd kattintson a **Tovább** gombra.

    ![Blob-tároló kiválasztása](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)
12. **Az Azure Blob Storage-fiók megadása** oldalon hajtsa végre a következő lépéseket: 

    a. A **Kapcsolat neve** mezőben adja meg az **AzureStorageLinkedService** nevet.

    b. A **Tárfiók neve** alatt a legördülő menüből válassza ki a tárfiókja nevét. 

    c. Kattintson a **Tovább** gombra.

    ![A tárfiók megadása](./media/tutorial-hybrid-copy-data-tool/specify-azure-blob-storage-account.png)
13. **A kimeneti fájl vagy mappa kiválasztása** lapon a **Mappa elérési útja** alatt adja meg az **adftutorial/fromonprem** nevet. Az előfeltételek részeként létrehozott egy **adftutorial** nevű tárolót. Ha a kimeneti mappa nem létezik, a Data Factory automatikusan létrehozza. A **Tallózás** gombbal is megkeresheti a blobtárolót és a hozzá tartozó tárolókat/mappákat. A kimeneti fájl neve alapértelmezés szerint **dbo.emp**.
        
    ![Kimeneti fájl vagy mappa kiválasztása](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)
14. A **Fájlformátum beállításai** lapon kattintson a **Tovább** gombra. 

    ![Fájlformátum beállításai lap](./media/tutorial-hybrid-copy-data-tool/file-format-settings-page.png)
15. A **Beállítások** lapon kattintson a **Tovább** gombra. 

    ![Beállítások lap](./media/tutorial-hybrid-copy-data-tool/settings-page.png)
16. Az **Összefoglaló** lapon tekintse át az összes beállítás értékét, és kattintson a **Tovább** gombra. 

    ![Összefoglaló lap](./media/tutorial-hybrid-copy-data-tool/summary-page.png)
17. A létrehozott folyamat vagy feladat monitorozásához az **Üzembe helyezés** lapon válassza a **Monitorozás** elemet.

    ![Üzembe helyezés lap](./media/tutorial-hybrid-copy-data-tool/deployment-page.png)
18. A **Monitorozás** lapon megtekintheti a létrehozott folyamat állapotát. A **Művelet** oszlop hivatkozásaival megtekintheti a folyamat futásához társított tevékenységfuttatásokat, illetve újra futtathatja a folyamatot. 

    ![Folyamatfuttatások monitorozása](./media/tutorial-hybrid-copy-data-tool/monitor-pipeline-runs.png)
19. Kattintson a **Műveletek** oszlopban található **Tevékenységfuttatások megtekintése** hivatkozásra a folyamat futásához társított tevékenységfuttatások megtekintéséhez. A másolási művelet részleteinek megtekintéséhez válassza a **Műveletek** oszlop **Részletek** hivatkozását (szemüveg ikon). A **Folyamatfuttatások** nézetre való visszalépéshez válassza a lap tetejének közelében található **Folyamatok** elemet.

    ![Tevékenységfuttatások monitorozása](./media/tutorial-hybrid-copy-data-tool/monitor-activity-runs.png)
20. Ellenőrizze, hogy látja-e a kimeneti fájlt az **adftutorial** tároló **fromonprem** mappájában. 
 
    ![Kimeneti blob](./media/tutorial-hybrid-copy-data-tool/output-blob.png)
21. A szerkesztő módra való váltáshoz kattintson a bal oldalon található **Szerkesztés** fülre. A szerkesztővel frissítheti a társított szolgáltatásokat, az adatkészleteket és az eszközzel létrehozott folyamatokat. A szerkesztőben megnyitott entitáshoz társított JSON-kód megtekintéséhez kattintson a **Kód** elemre. Az entitások Data Factory felhasználói felületen való szerkesztéséről [a jelen oktatóanyag Azure Portal-verziójában](tutorial-copy-data-portal.md) talál további információt.

    ![Szerkesztés lap](./media/tutorial-hybrid-copy-data-tool/edit-tab.png)


## <a name="next-steps"></a>További lépések
A példában szereplő folyamat adatokat másol egy helyszíni SQL Server-adatbázisból Blob Storage-tárolóba. Megismerte, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása az Adatok másolása eszközzel.
> * A folyamat és a tevékenységek futásának monitorozása

A Data Factory által támogatott adattárak listáját a [támogatott adattárakat](copy-activity-overview.md#supported-data-stores-and-formats) ismertető cikk tartalmazza.

Az adatok egy forrásból egy célhelyre történő tömeges másolásának megismeréséhez lépjen a következő oktatóanyagra:

> [!div class="nextstepaction"]
>[Adatok tömeges másolása](tutorial-bulk-copy-portal.md)
