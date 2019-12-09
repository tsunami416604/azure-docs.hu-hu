---
title: Adatfeldolgozó folyamat létrehozása a Azure Portal használatával
description: Az oktatóanyag részletes útmutatását követve létrehozhat egy adat-előállítót egy folyamat és az Azure Portal használatával. A folyamat egy másolási tevékenységgel másol adatokat egy Azure Blob Storage-ból egy SQL Database-be.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 06/21/2018
ms.author: jingwang
ms.openlocfilehash: a42d796010368ec24cc6cfde8a704c4b7608963d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926626"
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

1. Nyissa meg a Jegyzettömböt. Másolja be a következő szöveget, és mentse **emp.txt** néven egy fájlba a lemezen.

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

1. Engedélyezze az SQL Server elérését az Azure-szolgáltatások számára. Győződjön meg arról, hogy az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** **BE** van kapcsolva az SQL Serverhez, hogy a Data Factory tudjon adatokat írni az SQL Serverre. A beállítás ellenőrzéséhez és bekapcsolásához nyissa meg az Azure SQL Server > áttekintés > **a**kiszolgáló tűzfalának beállítása > az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** lehetőséget.

## <a name="create-a-data-factory"></a>Data factory létrehozása
Ebben a lépésben létrehoz egy adat-előállítót, és elindítja a Data Factory felhasználói felületét, hogy létrehozzon egy folyamatot az adat-előállítóban. 

1. Nyissa meg a **Microsoft Edge** vagy a **Google Chrome böngészőt**. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
2. A bal oldali menüben válassza az **erőforrás létrehozása** > **Analytics** > **Data Factory**: 
  
   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket. 
 
   Az Azure data factory nevének *globálisan egyedinek* kell lennie. Ha a név értékével kapcsolatos hibaüzenet kap, adjon meg másik nevet az adat-előállítóhoz. (például Sajátneveadftutorialdatafactory). A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
        
     ![Új adat-előállító](./media/doc-common-process/name-not-available-error.png)
4. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
5. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:
     
    a. Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot.

    b. Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét. 
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/resource-group-overview.md) ismertető cikket. 
6. A **Verzió** résznél válassza a **V2** értéket.
7. A **Hely** területen válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (például az Azure Storage és az SQL Database) és számítási erőforrások (például az Azure HDInsight) más régiókban is lehetnek.
8. Kattintson a **Létrehozás** gombra. 
9. A létrehozás befejezése után megjelenik az értesítési központban megjelenő értesítés. Válassza az **Ugrás az erőforráshoz** lehetőséget, hogy megnyissa az adatfeldolgozó lapot.
10. A Data Factory felhasználói felületének külön lapon történő elindításához válassza a **Létrehozás és figyelés** csempét.


## <a name="create-a-pipeline"></a>Folyamat létrehozása
Ebben a lépésben létrehoz egy másolási tevékenységgel rendelkező folyamatot az adat-előállítóban. A másolási tevékenység adatokat másol egy Blob Storage-ból egy SQL Database-be. A [rövid útmutatóban](quickstart-create-data-factory-portal.md) létrehozott egy folyamatot az alábbi lépéseket követve:

1. Hozza létre a társított szolgáltatást. 
1. Hozzon létre bemeneti és kimeneti adatkészleteket.
1. Folyamat létrehozása.

Ebben az oktatóanyagban először létrehozzuk a folyamatot. Ezután társított szolgáltatásokat és adatkészleteket hozunk létre, amikor szükség van rájuk a folyamat konfigurálásához. 

1. Az **Első lépések** lapon válassza a **Folyamat létrehozása** lehetőséget. 

   ![Folyamat létrehozása](./media/doc-common-process/get-started-page.png)
1. A folyamat **Általános** lapján, a **Név** részben adja a **CopyPipeline** nevet a folyamatnak.

1. A **tevékenységek** eszközben bontsa ki az **áthelyezés és átalakítás** kategóriát, majd húzza a **adatok másolása** tevékenységet az eszközkészletből a folyamat tervező felületére. Adja meg a **CopyFromBlobToSql** értéket a **Név** mezőben.

    ![Másolási tevékenység](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Forrás konfigurálása

1. Lépjen a **forrás** lapra. Válassza az **+ új** lehetőséget a forrás adatkészlet létrehozásához. 

1. Az **új adatkészlet** párbeszédpanelen válassza az **Azure Blob Storage**lehetőséget, majd kattintson a **Folytatás**gombra. A forrásadatok egy Blob Storage-ban vannak, tehát forrásadatkészletként az **Azure Blob Storage**-ot válassza. 

1. A **formátum kiválasztása** párbeszédpanelen válassza ki az adatformátum típusát, majd kattintson a **Continue (folytatás**) elemre.

    ![Adatformátum típusa](./media/doc-common-process/select-data-format.png)

1. A **Tulajdonságok megadása** párbeszédpanelen adja meg a **sourceblobdataset lehetőség** nevet. A **Társított szolgáltatások** szövegbeviteli mező mellett válassza az **+ Új** lehetőséget. 
    
1. Az **új társított szolgáltatás (Azure Blob Storage)** párbeszédpanelen írja be a **AzureStorageLinkedService** nevet, válassza ki a Storage-fiók nevét a **Storage-fiók neve** listából. Tesztelje a kapcsolódást, majd válassza a **Befejezés** lehetőséget a társított szolgáltatás telepítéséhez.

1. A társított szolgáltatás létrehozása után a rendszer a **tulajdonságok beállítása** lapra navigál. A **Fájl elérési útja** mellett válassza a **Tallózás** lehetőséget.

1. Lépjen az **adftutorial/input** mappára, válassza ki az **emp.txt** fájlt, és válassza a **Befejezés** lehetőséget.

1. Automatikusan megnyitja a folyamat lapot. A **forrás** lapon ellenőrizze, hogy a **sourceblobdataset lehetőség** van-e kiválasztva. A lapon lévő adatok előnézetének megtekintéséhez válassza az **Adatok előnézete** elemet. 
    
    ![Forrásadatkészlet](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Fogadó konfigurálása

1. Váltson a **Fogadó** lapra, és válassza az **+ Új** elemet egy fogadó-adatkészlet létrehozásához. 

1. Az **új adatkészlet** párbeszédpanelen írja be az "SQL" kifejezést a keresőmezőbe az összekötők szűréséhez, válassza a **Azure SQL Database**lehetőséget, majd kattintson a **Folytatás**gombra. Ebben az oktatóanyagban adatokat másol egy SQL Database-be. 

1. A **Tulajdonságok megadása** párbeszédpanelen adja meg a **outputsqldataset érték** nevet. A **Társított szolgáltatások** szövegbeviteli mező mellett válassza az **+ Új** lehetőséget. Az adatkészleteket mindig társítani kell egy társított szolgáltatáshoz. A társított szolgáltatás rendelkezik azzal a kapcsolati sztringgel, amely használatával a Data Factory a futtatás során kapcsolódik az SQL Database-hez. Az adatkészlet meghatározza azt a tárolót, mappát és fájlt (az utóbbi nem kötelező), ahova a rendszer az adatokat másolja. 
      
1. Az **új társított szolgáltatás (Azure SQL Database)** párbeszédpanelen hajtsa végre a következő lépéseket: 

    a. A **Név** mezőbe írja az **AzureSqlDatabaseLinkedService** nevet.

    b. A **Kiszolgáló neve** mezőben válassza ki az SQL Server-példányát.

    c. Az **Adatbázis neve** mezőben válassza ki az SQL Database-adatbázisát.

    d. A **Felhasználónév** mezőben adja meg a felhasználó nevét.

    e. A **Jelszó** mezőben adja meg a felhasználó jelszavát.

    f. A kapcsolat teszteléséhez válassza a **Kapcsolat tesztelése** elemet.

    g. Válassza a **Befejezés** lehetőséget a társított szolgáltatás telepítéséhez. 
    
    ![Új társított szolgáltatás mentése](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. Automatikusan megnyitja a **tulajdonságok beállítása** párbeszédpanelt. A **Tábla** területen válassza a **[dbo].[emp]** elemet. Ezután kattintson a **Befejezés** gombra.

1. Lépjen a folyamatot tartalmazó lapra, és győződjön meg arról, hogy a **Fogadóadattár** mezőben az **OutputSqlDataset** érték van kiválasztva.

    ![Folyamat lap](./media/tutorial-copy-data-portal/pipeline-tab-2.png)       

A forrás sémáját leképezheti a cél megfelelő sémájára a [másolási tevékenységben lévő séma-hozzárendelés](copy-activity-schema-and-type-mapping.md) után.
    
## <a name="validate-the-pipeline"></a>A folyamat érvényesítése
A folyamat érvényesítéséhez válassza az **Érvényesítés** elemet az eszköztáron.
 
A folyamathoz társított JSON-kód megtekintéséhez kattintson a jobb felső sarokban található **kód** elemre.

## <a name="debug-and-publish-the-pipeline"></a>A folyamat hibakeresése és közzététele
Elvégezheti a folyamat hibakeresését, mielőtt összetevőket (társított szolgáltatások, adatkészletek és folyamat) tenne közzé a Data Factoryben vagy a saját Azure Repos Git-adattárában. 

1. A folyamat hibakereséséhez válassza a **Hibakeresés** elemet az eszköztáron. A folyamat futtatási állapotát az ablak alján található **Kimenet** lapon tekintheti meg. 

1. Ha a folyamat sikeresen futtatható, a felső eszköztáron válassza az **összes közzététele**lehetőséget. Ez a művelet közzéteszi a létrehozott entitásokat (adatkészleteket és folyamatokat) a Data Factoryben.

1. Várjon, amíg megjelenik a **Sikeres közzététel** üzenet. Az értesítési üzenetek megtekintéséhez kattintson az **Értesítések megjelenítése** elemre (csengő gomb) a jobb felső sarokban. 

## <a name="trigger-the-pipeline-manually"></a>A folyamat manuális aktiválása
Ebben a lépésben manuálisan fogja aktiválni az előző lépésben közzétett folyamatot. 

1. Válassza az **aktiválás hozzáadása** lehetőséget az eszköztáron, majd válassza az **aktiválás most**lehetőséget. A **Folyamatfuttatás** lapon válassza a **Befejezés** elemet.  

1. Lépjen a bal oldali **Figyelés** lapra. Itt láthat egy manuális eseményindító által aktivált folyamatfuttatást. A **Műveletek** oszlopban található hivatkozások használatával megtekintheti a tevékenységek részleteit, és újra futtathatja a folyamatot.

    ![Folyamatfuttatások monitorozása](./media/tutorial-copy-data-portal/monitor-pipeline.png)

1. A folyamat futásához társított tevékenységfuttatások megtekintéséhez kattintson a **Műveletek** oszlopban található **Tevékenységfuttatások megtekintése** hivatkozásra. Ebben a példában csak egy tevékenység van, így csak egy bejegyzés jelenik meg a listában. A másolási művelet részleteinek megtekintéséhez válassza a **Műveletek** oszlop **Részletek** hivatkozását (szemüveg ikon). Válassza a felső **folyamat futtatása** lehetőséget a folyamat futási nézetének visszalépéséhez. A nézet frissítéséhez válassza a **Frissítés** parancsot.

    ![Tevékenységfuttatások monitorozása](./media/tutorial-copy-data-portal/view-activity-runs.png)

1. Ellenőrizze, hogy hozzá lett-e adva két további sor az SQL Database **emp** táblájához. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>A folyamat aktiválása ütemezés szerint
Ebben az ütemezésben egy ütemezési eseményindítót fog létrehozni a folyamathoz. Az eseményindító a meghatározott ütemezés (például óránként vagy naponta) szerint futtatja a folyamatot. Itt úgy állíthatja be a triggert, hogy percenként fusson a megadott záró dátumig. 

1. Lépjen a bal oldali Monitorozás lap feletti **Létrehozás** lapra. 

1. Nyissa meg a folyamatot, kattintson az **trigger hozzáadása** elemre az eszköztáron, majd válassza az **új/szerkesztés**lehetőséget. 

1. Az **Eseményindítók hozzáadása** párbeszédpanelen válassza az **+ új** lehetőséget az **eseményindító-területek kiválasztásához** .

1. Az **Új eseményindító** ablakban hajtsa végre az alábbi lépéseket: 

    a. A **Név** mezőbe írja a **RunEveryMinute** nevet.

    b. A **Befejezés** alatt válassza a **Dátum** elemet.

    c. A **Befejezés** alatt válassza a legördülő listát.

    d. Válassza a **mai nap** lehetőséget. Alapértelmezés szerint a következő nap van beállítva.

    e. Frissítse a **befejezési időt** , hogy az aktuális dátum és idő pár perccel korábbi legyen. Az eseményindító csak a módosítások közzététele után lesz aktív. Ha úgy állítja be, hogy csak néhány percre van beállítva, és nem teszi közzé azt, akkor nem jelenik meg a trigger futtatása.

    f. Kattintson az **Alkalmaz** gombra. 

    g. Az **aktivált** beállításnál válassza az **Igen**lehetőséget. 

    h. Kattintson a **Tovább** gombra.

    ![Aktiválva gomb](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > Minden egyes folyamatfuttatásnak van bizonyos költségvonzata, ezért a befejezés időpontját ezt figyelembe véve adja meg. 
1. Az **Eseményindító futtatási paraméterei** oldalon tekintse át a figyelmeztető üzenetet, majd válassza a **Befejezés** elemet. A jelen példában található folyamat nem használ paramétereket. 

1. Kattintson az **Összes közzététele** gombra a módosítás közzétételéhez. 

1. Lépjen a bal oldali **Figyelés** lapra az aktivált folyamatfuttatások megtekintéséhez. 

    ![Aktivált folyamatfuttatások](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)   
 
1. Ha váltani szeretne a **folyamat futtatása** nézetből az **trigger futtatása** nézetre, válassza az **aktiválási futtatások** lehetőséget az ablak tetején.

1. Itt megtekintheti az eseményindító-futtatások listáját. 

1. Ellenőrizze, hogy a megadott befejezési időig percenként (folyamatfuttatásonként) két sor be van-e szúrva az **emp** táblába. 

## <a name="next-steps"></a>Következő lépések
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
