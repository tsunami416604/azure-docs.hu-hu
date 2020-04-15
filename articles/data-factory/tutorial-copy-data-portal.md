---
title: Adatfeldolgozó folyamat létrehozása az Azure Portal használatával
description: Az oktatóanyag részletes útmutatását követve létrehozhat egy adat-előállítót egy folyamat és az Azure Portal használatával. A folyamat a másolási tevékenység segítségével másolja az adatokat az Azure Blob storage egy Azure SQL-adatbázisba.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 04/13/2020
ms.author: jingwang
ms.openlocfilehash: 9e81efd826c78bfa1af5624272ae9b190cb54348
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313873"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Adatok másolása az Azure Blob Storage-ból egy SQL Database-be az Azure Data Factory segítségével
Ebben az oktatóanyagban az Azure Data Factory felhasználói felületének használatával hoz létre egy adat-előállítót. Az adat-előállító ban lévő folyamat adatokat másol az Azure Blob storage-ból egy Azure SQL-adatbázisba. Az oktatóanyagban szereplő konfigurációs minta fájlalapú adattárból relációs adattárba való másolásra vonatkozik. A forrásként és fogadóként támogatott adattárak listája a [támogatott adattárakat tartalmazó](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban található.

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
* **Egy Azure Storage-fiók**. A Blob Storage lesz használatban *forrás*adattárként. Ha még nem rendelkezik tárfiókkal, tekintse meg az [Azure Storage-fiók létrehozásának](../storage/common/storage-account-create.md) lépéseit ismertető cikket.
* **Az Azure SQL Database .** Ezt az adatbázist használjuk *fogadó*adattárként. Ha nem rendelkezik Azure SQL-adatbázissal, olvassa [el az SQL-adatbázis létrehozása](../sql-database/sql-database-get-started-portal.md) című témakört a létrehozási lépésekért.

### <a name="create-a-blob-and-a-sql-table"></a>Blob és SQL-tábla létrehozása

Készítse elő a Blob Storage-et és az SQL Database-t az oktatóanyaghoz a következő lépésekkel.

#### <a name="create-a-source-blob"></a>Forrás blob létrehozása

1. Indítsa el a Jegyzettömböt. Másolja be a következő szöveget, és mentse **emp.txt** néven egy fájlba a lemezen.

    ```
    FirstName,LastName
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

1. Engedélyezze az SQL Server elérését az Azure-szolgáltatások számára. Győződjön meg arról, hogy az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése****BE** van kapcsolva az SQL Serverhez, hogy a Data Factory tudjon adatokat írni az SQL Serverre. A beállítás ellenőrzéséhez és bekapcsolásához nyissa meg az Azure SQL Server > Overview > > Set server firewall> set the **Allow access to Azure Services (Hozzáférés engedélyezése az Azure-szolgáltatásokhoz)** beállítást BE **-re.**

## <a name="create-a-data-factory"></a>Data factory létrehozása
Ebben a lépésben létrehoz egy adat-előállítót, és elindítja a Data Factory felhasználói felületét, hogy létrehozzon egy folyamatot az adat-előállítóban.

1. Nyissa meg **a Microsoft Edge** vagy a Google **Chrome**. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
2. A bal oldali menüben válassza **az Erőforrás-elemzési** > **Analytics** > **adatgyár**létrehozása lehetőséget.
3. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket.

   Az Azure-adat-előállító nevének *globálisan egyedinek*kell lennie. Ha a név értékével kapcsolatos hibaüzenet kap, adjon meg másik nevet az adat-előállítóhoz. (például yournameADFTutorialDataFactory). A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.

     ![Új adat-előállító](./media/doc-common-process/name-not-available-error.png)
4. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni.
5. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:

    a. Válassza **a Meglévő használata**lehetőséget, és válasszon ki egy meglévő erőforráscsoportot a legördülő listából.

    b. Válassza **az Új létrehozása**lehetőséget, és írja be egy erőforráscsoport nevét. 
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket. 
6. A **Verzió** résznél válassza a **V2** értéket.
7. A **Hely** területen válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (például az Azure Storage és az SQL Database) és számítási erőforrások (például az Azure HDInsight) más régiókban is lehetnek.
8. Kattintson a **Létrehozás** gombra.
9. A létrehozás befejezése után megjelenik az értesítés az Értesítésközpontban. Válassza **az Ugrás az erőforráshoz** lehetőséget az Adatgyár lapra való navigáláshoz.
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

1. A **Tevékenységek** eszközmezőben bontsa ki az **Áthelyezés és átalakítás** kategóriát, és húzza az Adatok **másolása** tevékenységet az eszközmezőből a folyamattervező felületére. Adja meg a **CopyFromBlobToSql** értéket a **Név** mezőben.

    ![Másolási tevékenység](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Forrás konfigurálása

>[!TIP]
>Ebben az oktatóanyagban a *Fiókkulcsot* használja a forrás-adattár hitelesítési típusaként, de más támogatott hitelesítési módszereket is választhat: *SAS URI*,*egyszerű szolgáltatás* és *felügyelt identitás,* ha szükséges. A részleteket a [cikk](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#linked-service-properties) megfelelő szakaszaiban találja.
>Az adattárak titkos kulcsainak biztonságos tárolásához is ajánlott egy Azure Key Vault használata. A részletes illusztrációkat ebben a [cikkben](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) olvashatja.

1. Lépjen a **Forrás** lapra. **+ New**

1. Az **Új adatkészlet** párbeszédpanelen válassza az **Azure Blob Storage**lehetőséget, majd kattintson a Folytatás **gombra.** A forrásadatok egy Blob Storage-ban vannak, tehát forrásadatkészletként az **Azure Blob Storage**-ot válassza.

1. A **Formátum kiválasztása** párbeszédpanelen válassza ki az adatok formátumtípusát, majd kattintson a **Folytatás gombra.**

1. A **Tulajdonságok beállítása** párbeszédpanelen írja be a **SourceBlobDataset parancsot** a Név mezőbe. Jelölje be az **Első sor fejlécként jelölőnégyzetét.** A **Csatolt szolgáltatás** szövege mezőben válassza a **+ Új**lehetőséget.

1. Az **új csatolt szolgáltatás (Azure Blob Storage)** párbeszédpanelen adja meg **az AzureStorageLinkedService** nevet, válassza ki a tárfiókot a **Storage-fiók** névlistájából. Tesztelje a kapcsolatot, és válassza a **Létrehozás** lehetőséget a csatolt szolgáltatás üzembe helyezéséhez.

1. A csatolt szolgáltatás létrehozása után a szolgáltatás visszakerül a **Tulajdonságok beállítása** lapra. A **Fájl elérési útja** mellett válassza a **Tallózás** lehetőséget.

1. Nyissa meg az **adftutorial/input** mappát, jelölje ki az **emp.txt** fájlt, majd kattintson az **OK gombra.**

1. Válassza **az OK gombot.** Automatikusan a folyamatlapra navigál. A **Forrás** lapon ellenőrizze, hogy a **SourceBlobDataset** ki van-e jelölve. A lapon lévő adatok előnézetének megtekintéséhez válassza az **Adatok előnézete** elemet.

    ![Forrásadatkészlet](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Fogadó konfigurálása
>[!TIP]
>Ebben az oktatóanyagban az *SQL-hitelesítést* használja a fogadó adattárának hitelesítési típusaként, de más támogatott hitelesítési módszereket is választhat: *egyszerű szolgáltatás* és *felügyelt identitás,* ha szükséges. A részleteket a [cikk](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#linked-service-properties) megfelelő szakaszaiban találja.
>Az adattárak titkos kulcsainak biztonságos tárolásához is ajánlott egy Azure Key Vault használata. A részletes illusztrációkat ebben a [cikkben](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) olvashatja.

1. Váltson a **Fogadó** lapra, és válassza az **+ Új** elemet egy fogadó-adatkészlet létrehozásához.

1. Az **Új adatkészlet** párbeszédpanelen adja meg az "SQL" kifejezést a keresőmezőbe az összekötők szűréséhez, válassza az **Azure SQL Database**lehetőséget, majd kattintson a Continue **(Folytatás) gombra.** Ebben az oktatóanyagban adatokat másol egy SQL Database-be.

1. A **Tulajdonságok beállítása** párbeszédpanelen írja be a **Name (KimenetsqlDataset) értéket** a Név mezőbe. A **Csatolt szolgáltatás** legördülő listájában válassza a **+ Új**lehetőséget. Az adatkészleteket mindig társítani kell egy társított szolgáltatáshoz. A társított szolgáltatás rendelkezik azzal a kapcsolati sztringgel, amely használatával a Data Factory a futtatás során kapcsolódik az SQL Database-hez. Az adatkészlet meghatározza azt a tárolót, mappát és fájlt (az utóbbi nem kötelező), ahova a rendszer az adatokat másolja.

1. Az **Új csatolt szolgáltatás (Azure SQL Database)** párbeszédpanelen tegye a következő lépéseket:

    a. A **Név** mezőbe írja az **AzureSqlDatabaseLinkedService** nevet.

    b. A **Kiszolgáló neve** mezőben válassza ki az SQL Server-példányát.

    c. Az **Adatbázis neve** mezőben válassza ki az SQL Database-adatbázisát.

    d. A **Felhasználónév** mezőben adja meg a felhasználó nevét.

    e. A **Jelszó** mezőben adja meg a felhasználó jelszavát.

    f. A kapcsolat teszteléséhez válassza a **Kapcsolat tesztelése** elemet.

    g. Válassza a **Létrehozás** gombot a csatolt szolgáltatás üzembe helyezéséhez.

    ![Új társított szolgáltatás mentése](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. Automatikusan a **Tulajdonságok beállítása** párbeszédpanelre navigál. A **Tábla** területen válassza a **[dbo].[emp]** elemet. Ezután kattintson az **OK** gombra.

1. Lépjen a folyamatot tartalmazó lapra, és győződjön meg arról, hogy a **Fogadóadattár** mezőben az **OutputSqlDataset** érték van kiválasztva.

    ![Folyamat lap](./media/tutorial-copy-data-portal/pipeline-tab-2.png)       

A forrás sémáját leképezheti a cél megfelelő sémájához, ha a [másolási tevékenységben a Séma-hozzárendelést](copy-activity-schema-and-type-mapping.md)követi.

## <a name="validate-the-pipeline"></a>A folyamat érvényesítése
A folyamat érvényesítéséhez válassza az **Érvényesítés** elemet az eszköztáron.

A folyamathoz társított JSON-kódot a jobb felső sarokban található **Kód** elemre kattintva láthatja.

## <a name="debug-and-publish-the-pipeline"></a>A folyamat hibakeresése és közzététele
Elvégezheti a folyamat hibakeresését, mielőtt összetevőket (társított szolgáltatások, adatkészletek és folyamat) tenne közzé a Data Factoryben vagy a saját Azure Repos Git-adattárában.

1. A folyamat hibakereséséhez válassza a **Hibakeresés** elemet az eszköztáron. A folyamat futtatási állapotát az ablak alján található **Kimenet** lapon tekintheti meg.

1. Miután a folyamat sikeresen lefutott, a felső eszköztáron válassza az **Összes közzététele**lehetőséget. Ez a művelet közzéteszi a létrehozott entitásokat (adatkészleteket és folyamatokat) a Data Factoryben.

1. Várjon, amíg megjelenik a **Sikeres közzététel** üzenet. Az értesítési üzenetek megtekintéséhez kattintson az **Értesítések megjelenítése** elemre (csengő gomb) a jobb felső sarokban.

## <a name="trigger-the-pipeline-manually"></a>A folyamat manuális aktiválása
Ebben a lépésben manuálisan fogja aktiválni az előző lépésben közzétett folyamatot.

1. Kattintson az **Aktiválás** gombra az eszköztáron, majd válassza az **Aktiválás most** lehetőséget. A **Folyamat futtatása** lapon válassza az **OK gombot.**  

1. Lépjen a bal oldali **Figyelés** lapra. Itt láthat egy manuális eseményindító által aktivált folyamatfuttatást. A **FOLYAMAT NEVE** oszlopban lévő hivatkozások segítségével megtekintheti a tevékenység részleteit, és újra futtathatja a folyamatot.

    [![Folyamatfuttatások monitorozása](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png)](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png#lightbox)

1. A folyamatfutáshoz társított tevékenységfuttatások megtekintéséhez válassza a **CopyPipeline** kapcsolatot a **FOLYAMAT NEVE** oszlopban. Ebben a példában csak egy tevékenység van, így csak egy bejegyzés jelenik meg a listában. A másolási művelettel kapcsolatos részletekért válassza a **Részletek** hivatkozást (szemüveg ikon) a **ACTIVITY NAME** oszlopban. Válassza le **az Összes folyamat fut** a tetején, hogy visszatérjen a folyamat futtatása nézetben. A nézet frissítéséhez válassza a **Frissítés** parancsot.

    [![Tevékenységfuttatások monitorozása](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png#lightbox)

1. Ellenőrizze, hogy hozzá lett-e adva két további sor az SQL Database **emp** táblájához.

## <a name="trigger-the-pipeline-on-a-schedule"></a>A folyamat aktiválása ütemezés szerint
Ebben az ütemezésben egy ütemezési eseményindítót fog létrehozni a folyamathoz. Az eseményindító a meghatározott ütemezés (például óránként vagy naponta) szerint futtatja a folyamatot. Itt állíthatja be, hogy az eseményindító minden percben fusson a megadott befejezési dátumidőig.

1. Lépjen a bal oldali Monitorozás lap feletti **Létrehozás** lapra.

1. Lépjen a folyamathoz, kattintson az eszköztáron az **Aktiválás**, majd az **Új/Szerkesztés** lehetőségre.

1. Az **Eseményindítók hozzáadása** párbeszédpanelen válassza a **+ Új** lehetőséget az **Eseményindító kiválasztása** területen lehetőséget.

1. Az **Új eseményindító** ablakban hajtsa végre az alábbi lépéseket:

    a. A **Név** mezőbe írja a **RunEveryMinute** nevet.

    b. A **Befejezés** alatt válassza a **Dátum** elemet.

    c. A **Befejezés** alatt válassza a legördülő listát.

    d. Válassza a **mai nap** lehetőséget. Alapértelmezés szerint a következő nap van beállítva.

    e. Frissítse a **Befejezési** részt, hogy az néhány perccel az aktuális dátumidő után legyen. Az eseményindító csak a módosítások közzététele után lesz aktív. Ha csak néhány percre állítja be, és addig nem teszi közzé, nem jelenik meg az eseményindító-futtatás.

    f. Válassza **az OK gombot.**

    g. **Az Aktivált** beállítás nál válassza az **Igen**lehetőséget.

    h. Válassza **az OK gombot.**

    > [!IMPORTANT]
    > Minden egyes folyamatfuttatásnak van bizonyos költségvonzata, ezért a befejezés időpontját ezt figyelembe véve adja meg.

1. Az **Eseményindító szerkesztése** lapon tekintse át a figyelmeztetést, és válassza a **Mentés gombot.** A jelen példában található folyamat nem használ paramétereket.

1. A módosítás közzétételéhez kattintson az **Összes** közzététele gombra.

1. Lépjen a bal oldali **Figyelés** lapra az aktivált folyamatfuttatások megtekintéséhez.

    [![Aktivált folyamatfuttatások](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png#lightbox)

1. Ha a **folyamatfuttatások** nézetről az **Eseményindító futtatások** nézetre szeretne váltani, válassza az **Eseményindítófuttatások** lehetőséget az ablak bal oldalán.

1. Itt megtekintheti az eseményindító-futtatások listáját.

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
