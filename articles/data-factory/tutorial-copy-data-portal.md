---
title: Az Azure Portal használata Data Factory-folyamatok létrehozására | Microsoft Docs
description: Az oktatóanyag részletes útmutatását követve létrehozhat egy adat-előállítót egy folyamat és az Azure Portal használatával. A folyamat egy másolási tevékenységgel másol adatokat egy Azure Blob Storage-ból egy SQL Database-be.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/21/2018
ms.author: jingwang
ms.openlocfilehash: 38c9c97af0be77bf9ad4bea2d24676c7448b3aea
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447562"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Adatok másolása az Azure Blob Storage-ból egy SQL Database-be az Azure Data Factory segítségével
Ebben az oktatóanyagban az Azure Data Factory felhasználói felületének használatával hoz létre egy adat-előállítót. Az adat-előállító folyamata adatokat másol az Azure Blob Storage-ból egy SQL Database-be. Az oktatóanyagban szereplő konfigurációs minta fájlalapú adattárból relációs adattárba való másolásra vonatkozik. A forrásként és fogadóként támogatott adattárak listája a [támogatott adattárakat tartalmazó](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban található.

> [!NOTE]
> - Ha még csak ismerkedik a Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](introduction.md) ismertető cikket.

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Másolási tevékenységgel rendelkező folyamat létrehozása.
> * A folyamat próbafuttatása
> * A folyamat manuális aktiválása
> * A folyamat aktiválása ütemezés szerint
> * A folyamat és a tevékenységek futásának monitorozása

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* **Egy Azure Storage-fiók**. A Blob Storage lesz használatban *forrás*adattárként. Ha még nem rendelkezik tárfiókkal, tekintse meg az [Azure Storage-fiók létrehozásának](../storage/common/storage-quickstart-create-account.md) lépéseit ismertető cikket.
* **Azure SQL Database** Ezt az adatbázist használjuk *fogadó*adattárként. Ha még nem rendelkezik SQL-adatbázissal, a létrehozás folyamatáért lásd az [SQL-adatbázis létrehozását](../sql-database/sql-database-get-started-portal.md) ismertető cikket.

### <a name="create-a-blob-and-a-sql-table"></a>Blob és SQL-tábla létrehozása

Készítse elő a Blob Storage-et és az SQL Database-t az oktatóanyaghoz a következő lépésekkel.

#### <a name="create-a-source-blob"></a>Forrás blob létrehozása

1. Indítsa el a Jegyzettömböt. Másolja be a következő szöveget, és mentse **emp.txt** néven egy fájlba a lemezen.

    ```
    John,Doe
    Jane,Doe
    ```

1. Hozzon létre egy **adftutorial** nevű tárolót a Blob Storage-ban. Ebben a tárolóban hozzon létre egy **input** nevű mappát. Ezután töltse fel az **emp.txt** fájlt az **input** mappába. Ezekhez a feladatokhoz használja az Azure Portalt vagy olyan eszközöket, mint az [Azure Storage Explorer](https://storageexplorer.com/).

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

1. Engedélyezze az SQL Server elérését az Azure-szolgáltatások számára. Győződjön meg arról, hogy az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** **BE** van kapcsolva az SQL Serverhez, hogy a Data Factory tudjon adatokat írni az SQL Serverre. A beállítás ellenőrzéséhez és bekapcsolásához hajtsa végre a következő lépéseket:

    a. A bal oldalon válassza a **További szolgáltatások** > **SQL-kiszolgálók** elemet.

    b. Válassza ki a kiszolgálóját, majd a **BEÁLLÍTÁSOK** alatt válassza a **Tűzfal** lehetőséget.

    c. A **Tűzfalbeállítások** lapon válassza a **BE** kapcsolót az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítás mellett.

## <a name="create-a-data-factory"></a>Data factory létrehozása
Ebben a lépésben létrehoz egy adat-előállítót, és elindítja a Data Factory felhasználói felületét, hogy létrehozzon egy folyamatot az adat-előállítóban. 

1. Nyissa meg **Microsoft Edge** vagy **Google Chrome**. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
2. A bal oldali menüben válassza ki a **erőforrás létrehozása** > **adatok + analitika** > **adat-előállító**: 
  
   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

3. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket. 
      
     ![Új adat-előállító](./media/tutorial-copy-data-portal/new-azure-data-factory.png)
 
   Az Azure data factory nevének *globálisan egyedinek* kell lennie. Ha a Név mezőnél az alábbi hibaüzenet jelenik meg, módosítsa az adat-előállító nevét (például a következőre: sajátneveADFTutorialDataFactory). A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
   ![Hibaüzenet](./media/tutorial-copy-data-portal/name-not-available-error.png)
4. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
5. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:
     
    a. Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot.

    b. Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét. 
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/resource-group-overview.md) ismertető cikket. 
6. A **Verzió** résznél válassza a **V2** értéket.
7. A **Hely** területen válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (például az Azure Storage és az SQL Database) és számítási erőforrások (például az Azure HDInsight) más régiókban is lehetnek.
8. Válassza a **Rögzítés az irányítópulton** lehetőséget. 
9. Kattintson a **Létrehozás** gombra. 
10. Az irányítópulton megjelenő csempén a **Adat-előállító üzembe helyezése** állapotleírás látható: 

    ![Adat-előállító üzembe helyezése csempe](media/tutorial-copy-data-portal/deploying-data-factory.png)
1. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
    ![Data factory kezdőlap](./media/tutorial-copy-data-portal/data-factory-home-page.png)
1. A Data Factory felhasználói felületének külön lapon történő elindításához válassza a **Létrehozás és figyelés** csempét.

## <a name="create-a-pipeline"></a>Folyamat létrehozása
Ebben a lépésben létrehoz egy másolási tevékenységgel rendelkező folyamatot az adat-előállítóban. A másolási tevékenység adatokat másol egy Blob Storage-ból egy SQL Database-be. A [rövid útmutatóban](quickstart-create-data-factory-portal.md) létrehozott egy folyamatot az alábbi lépéseket követve:

1. Hozza létre a társított szolgáltatást. 
1. Hozzon létre bemeneti és kimeneti adatkészleteket.
1. Folyamat létrehozása.

Ebben az oktatóanyagban először létrehozzuk a folyamatot. Ezután társított szolgáltatásokat és adatkészleteket hozunk létre, amikor szükség van rájuk a folyamat konfigurálásához. 

1. Az **Első lépések** lapon válassza a **Folyamat létrehozása** lehetőséget. 

   ![Folyamat létrehozása](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
1. A folyamat **Általános** lapján, a **Név** részben adja a **CopyPipeline** nevet a folyamatnak.

1. Az a **tevékenységek** eszköz bontsa ki a **andTransform áthelyezése** kategóriát, majd húzza a **adatok másolása** tevékenységet az eszközkészletből a folyamat tervezőfelületére. Adja meg a **CopyFromBlobToSql** értéket a **Név** mezőben.

    ![Másolási tevékenység](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Forrás konfigurálása

1. Váltson a **Forrás** lapra. Forrásadatkészlet létrehozásához válassza az **+ Új** elemet. 

1. Az **Új adatkészlet** lapon válassza az **Azure Blob Storage** lehetőséget, majd válassza a **Befejezés** gombot. A forrásadatok egy Blob Storage-ban vannak, tehát forrásadatkészletként az **Azure Blob Storage**-ot válassza. 

    ![Tároló kiválasztása](./media/tutorial-copy-data-portal/select-azure-blob-dataset.png)

1. Egy új lap nyílik meg a blob adatkészlethez. A **Tulajdonságok** ablak alján, az **Általános** lapon a **Név** mezőben adja meg a **SourceBlobDataset** nevet.

    ![Adatkészlet neve](./media/tutorial-copy-data-portal/dataset-name.png)

1. A **Tulajdonságok** ablakban váltson a **Kapcsolat** lapra. A **Társított szolgáltatások** szövegbeviteli mező mellett válassza az **+ Új** lehetőséget. 

    ![Új társított szolgáltatás gomb](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)

1. Az **Új társított szolgáltatás** ablakban adja meg névként az **AzureStorageLinkedService** kifejezést, válassza ki tárfiókját a **Tárfiók neve** listából, és kattintson a **Mentés** gombra a társított szolgáltatás üzembe helyezéséhez.

    ![Új társított szolgáltatás](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)

1. A társított szolgáltatás létrehozása után a rendszer visszalépteti az adatkészlet beállításaihoz. A **Fájl elérési útja** mellett válassza a **Tallózás** lehetőséget.

    ![Tallózás gomb a fájl elérési útjához](./media/tutorial-copy-data-portal/file-browse-button.png)

1. Lépjen az **adftutorial/input** mappára, válassza ki az **emp.txt** fájlt, és válassza a **Befejezés** lehetőséget. 

    ![Bemeneti fájl kiválasztása](./media/tutorial-copy-data-portal/select-input-file.png)

1. Győződjön meg arról, hogy a **Fájlformátum** beállítása **Szöveges formátum**, és az **Oszlophatároló** beállítása **Vessző (`,`)**. Ha a forrásfájl különböző sor- és oszlophatárolókat használ, választhatja a **Szövegformátum észlelése** lehetőséget a **Fájlformátum** mezőben. Az Adatok másolása eszköz automatikusan észleli a fájlformátumot és a határolókat. Ezeket az értékeket a későbbiekben még felülbírálhatja. A lapon lévő adatok előnézetének megtekintéséhez válassza az **Adatok előnézete** elemet.

    ![Szövegformátum észlelése](./media/tutorial-copy-data-portal/detect-text-format.png)

1. Lépjen a **Séma** lapra a **Tulajdonságok** ablakban, és válassza a **Séma importálása** lehetőséget. Figyelje meg, hogy az alkalmazás két oszlopot észlelt a forrásfájlban. Azért importálja ide a sémát, hogy leképezhesse a forrásadattár oszlopait a fogadóadattárban. Ha nincs szüksége oszlopok leképezésére, ezt a lépést kihagyhatja. A jelen oktatóanyag keretében importálja a sémát.

    ![Forrás sémájának észlelése](./media/tutorial-copy-data-portal/detect-source-schema.png)  

1. Lépjen vissza a folyamat **Forrás** lapjára, és ellenőrizze, hogy a **SourceBlobDataset** ki van-e választva. A lapon lévő adatok előnézetének megtekintéséhez válassza az **Adatok előnézete** elemet. 
    
    ![Forrásadatkészlet](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Fogadó konfigurálása

1. Váltson a **Fogadó** lapra, és válassza az **+ Új** elemet egy fogadó-adatkészlet létrehozásához. 

    ![Fogadó-adatkészlet](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
1. Az a **új adatkészlet** ablakban a szűrés az összekötők, majd jelölje be a keresőmezőbe írja "SQL" bemeneti **Azure SQL Database**, majd válassza ki **Befejezés**. Ebben az oktatóanyagban adatokat másol egy SQL Database-be. 

    ![SQL Database kiválasztása](./media/tutorial-copy-data-portal/select-azure-sql-dataset.png)
1. Az **Általános** lap **Tulajdonságok** ablakában a **Név** alatt írja be az **OutputSqlDataset** nevet. 
    
    ![Kimeneti adatkészlet neve](./media/tutorial-copy-data-portal/output-dataset-name.png)
1. Lépjen a **Kapcsolat** lapra, és a **Társított szolgáltatás** mellett válassza az **+ Új** lehetőséget. Az adatkészleteket mindig társítani kell egy társított szolgáltatáshoz. A társított szolgáltatás rendelkezik azzal a kapcsolati sztringgel, amely használatával a Data Factory a futtatás során kapcsolódik az SQL Database-hez. Az adatkészlet meghatározza azt a tárolót, mappát és fájlt (az utóbbi nem kötelező), ahova a rendszer az adatokat másolja. 
    
    ![Társított szolgáltatások](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
1. Az **Új társított szolgáltatás** ablakban végezze el az alábbi lépéseket: 

    a. A **Név** mezőbe írja az **AzureSqlDatabaseLinkedService** nevet.

    b. A **Kiszolgáló neve** mezőben válassza ki az SQL Server-példányát.

    c. Az **Adatbázis neve** mezőben válassza ki az SQL Database-adatbázisát.

    d. A **Felhasználónév** mezőben adja meg a felhasználó nevét.

    e. A **Jelszó** mezőben adja meg a felhasználó jelszavát.

    f. A kapcsolat teszteléséhez válassza a **Kapcsolat tesztelése** elemet.

    g. A társított szolgáltatás mentéséhez kattintson a **Mentés** gombra. 
    
    ![Új társított szolgáltatás mentése](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. A **Tábla** területen válassza a **[dbo].[emp]** elemet. 

    ![Tábla](./media/tutorial-copy-data-portal/select-emp-table.png)
1. Lépjen a **Séma** lapra, és válassza a **Séma importálása** lehetőséget. 

    ![Séma importálása lehetőség kiválasztása](./media/tutorial-copy-data-portal/import-destination-schema.png)
1. Válassza ki az **Azonosító** oszlopot, majd válassza a **Törlés** lehetőséget. Az **Azonosító** oszlop egy identitásoszlop az SQL-adatbázisban, ezért a másolási tevékenységnek semmit nem kell ebbe az oszlopba beszúrnia.

    ![Azonosító oszlop törlése](./media/tutorial-copy-data-portal/delete-id-column.png)
1. Lépjen a folyamatot tartalmazó lapra, és győződjön meg arról, hogy a **Fogadóadattár** mezőben az **OutputSqlDataset** érték van kiválasztva.

    ![Folyamat lap](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        

### <a name="configure-mapping"></a>-Leképezés konfigurálása

Lépjen a **Leképezés** lapra a **Tulajdonságok** ablak alján, és válassza a **Sémák importálása** elemet. Figyelje meg, hogy a forrásfájl első és második oszlopa az SQL-adatbázis **FirstName** és **LastName** mezőjében képeződik le.

![Sémák leképezése](./media/tutorial-copy-data-portal/map-schemas.png)

## <a name="validate-the-pipeline"></a>A folyamat érvényesítése
A folyamat érvényesítéséhez válassza az **Érvényesítés** elemet az eszköztáron.
 
A folyamathoz társított JSON-kódot a jobb felső sarokban lévő **Kód** gombra kattintva tekintheti meg.

## <a name="debug-and-publish-the-pipeline"></a>A folyamat hibakeresése és közzététele
Elvégezheti a folyamat hibakeresését, mielőtt összetevőket (társított szolgáltatások, adatkészletek és folyamat) tenne közzé a Data Factoryben vagy a saját Azure Repos Git-adattárában. 

1. A folyamat hibakereséséhez válassza a **Hibakeresés** elemet az eszköztáron. A folyamat futtatási állapotát az ablak alján található **Kimenet** lapon tekintheti meg. 

1. Ha a folyamat sikeres, futtathatja a felső eszköztáron kattintson **összes közzététele**. Ez a művelet közzéteszi a létrehozott entitásokat (adatkészleteket és folyamatokat) a Data Factoryben.

    ![Közzététel](./media/tutorial-copy-data-portal/publish-button.png)

1. Várjon, amíg megjelenik a **Sikeres közzététel** üzenet. Az értesítési üzenetek megtekintéséhez kattintson az **Értesítések megjelenítése** elemre (csengő gomb) a jobb felső sarokban. 

## <a name="trigger-the-pipeline-manually"></a>A folyamat manuális aktiválása
Ebben a lépésben manuálisan fogja aktiválni az előző lépésben közzétett folyamatot. 

1. Kattintson az **Aktiválás** gombra az eszköztáron, majd válassza az **Aktiválás most** lehetőséget. A **Folyamatfuttatás** lapon válassza a **Befejezés** elemet.  

1. Lépjen a bal oldali **Figyelés** lapra. Itt láthat egy manuális eseményindító által aktivált folyamatfuttatást. A **Műveletek** oszlopban található hivatkozások használatával megtekintheti a tevékenységek részleteit, és újra futtathatja a folyamatot.

    ![Folyamatfuttatások monitorozása](./media/tutorial-copy-data-portal/monitor-pipeline.png)
1. A folyamat futásához társított tevékenységfuttatások megtekintéséhez kattintson a **Műveletek** oszlopban található **Tevékenységfuttatások megtekintése** hivatkozásra. Ebben a példában csak egy tevékenység található, ezért csak egy bejegyzést lát a listán. A másolási művelet részleteinek megtekintéséhez válassza a **Műveletek** oszlop **Részletek** hivatkozását (szemüveg ikon). A **Folyamatfuttatások** nézetre való visszaváltáshoz válassza a fenti **Folyamatok** hivatkozást. A nézet frissítéséhez válassza a **Frissítés** parancsot.

    ![Tevékenységfuttatások monitorozása](./media/tutorial-copy-data-portal/view-activity-runs.png)
1. Ellenőrizze, hogy hozzá lett-e adva két további sor az SQL Database **emp** táblájához. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>A folyamat aktiválása ütemezés szerint
Ebben az ütemezésben egy ütemezési eseményindítót fog létrehozni a folyamathoz. Az eseményindító a meghatározott ütemezés (például óránként vagy naponta) szerint futtatja a folyamatot. Ebben a példában úgy fogja beállítani az eseményindítót, hogy a megadott záró dátumidőig percenként futtassa a folyamatot. 

1. Lépjen a bal oldali Monitorozás lap feletti **Létrehozás** lapra. 

1. Lépjen a folyamathoz, kattintson az eszköztáron az **Aktiválás**, majd az **Új/Szerkesztés** lehetőségre. 

1. Az **Eseményindítók hozzáadása** ablakban válassza az **Eseményindító kiválasztása**, majd pedig az **+ Új** elemet. 

    ![Új gomb](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
1. Az **Új eseményindító** ablakban hajtsa végre az alábbi lépéseket: 

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
1. Az **Eseményindító futtatási paraméterei** oldalon tekintse át a figyelmeztető üzenetet, majd válassza a **Befejezés** elemet. A jelen példában található folyamat nem használ paramétereket. 

    ![Eseményindító futtatásának paraméterei](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)

1. Kattintson az **Összes közzététele** gombra a módosítás közzétételéhez. 

1. Lépjen a bal oldali **Figyelés** lapra az aktivált folyamatfuttatások megtekintéséhez. 

    ![Aktivált folyamatfuttatások](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
1. Ha váltani szeretne a **Folyamatfuttatások** nézetéről az **Eseményindító-futtatások** nézetére, válassza a **Folyamatfuttatások** elemet, és válassza ki az **Eseményindító-futtatások** lehetőséget.
    
    ![Eseményindító-futtatások](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
1. Itt megtekintheti az eseményindító-futtatások listáját. 

    ![Eseményindító-futtatások listája](./media/tutorial-copy-data-portal/trigger-runs-list.png)
1. Ellenőrizze, hogy a megadott befejezési időig percenként (folyamatfuttatásonként) két sor be van-e szúrva az **emp** táblába. 

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
