<properties 
    pageTitle="Oktatóanyag: Folyamat létrehozása a Másolás varázsló használatával" 
    description="Az oktatóanyag során létrehoz egy másolási tevékenységgel rendelkező Azure Data Factory-folyamatot a Data Factory által támogatott Másolás varázslóval." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/01/2016" 
    ms.author="spelluru"/>

# Oktatóanyag: Másolási tevékenységgel rendelkező folyamat létrehozása a Data Factory Másolás varázslója használatával
> [AZURE.SELECTOR]
- [Az oktatóanyag áttekintése](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [A Data Factory Editor használata](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [A PowerShell használata](data-factory-copy-activity-tutorial-using-powershell.md)
- [A Visual Studio használata](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [A REST API használata](data-factory-copy-activity-tutorial-using-rest-api.md) 
- [A Másolás varázsló használata](data-factory-copy-data-wizard-tutorial.md)

Az oktatóanyag során létrehoz egy másolási tevékenységgel rendelkező folyamatot egy data factoryben a Data Factory Másolás varázslójával. Először létrehoz egy data factoryt az Azure Portal használatával, majd a Másolás varázslóval létrehozza a Data Factoryval társított szolgáltatásokat, az adatkészleteket és egy folyamatot a Másolás tevékenységgel, amely adatokat másol egy Azure blobtárolóból egy Azure SQL-adatbázisba.

> [AZURE.IMPORTANT] Tekintse meg [Az oktatóanyag áttekintése](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című cikket, és az oktatóanyag elvégzése előtt hajtsa végre az előfeltételként felsorolt lépéseket.

## Data factory létrehozása
Ebben a lépésben az Azure Portal használatával létrehozza az **ADFTutorialDataFactory** nevű Azure data factoryt.

1.  Miután bejelentkezett az [Azure Portalra](https://portal.azure.com), kattintson az **+ÚJ** elemre a bal felső sarokban, válassza az **Adatelemzés** lehetőséget a **Létrehozás** panelen, és kattintson a **Data Factory** elemre az **Adatelemzés** panelen. 

    ![New (Új)->DataFactory](./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png)

6. A **New data factory** (Új data factory) panelen:
    1. A**Name** (Név) mezőbe írja be a következőt: **ADFTutorialDataFactory**. 
    
        ![A New data factory (Új data factory) panel](./media/data-factory-copy-data-wizard-tutorial/getstarted-new-data-factory.png)
    2. Kattintson a **RESOURCE GROUP NAME** (ERŐFORRÁSCSOPORT NEVE) elemre, és tegye a következőket:
        1. Kattintson a **Create a new resource group** (Új erőforráscsoport létrehozása) elemre.
        2. A **Create resource group** (Erőforráscsoport létrehozása) panelen az erőforráscsoport **neveként** adja meg az **ADFTutorialResourceGroup** kifejezést, és kattintson az **OK** gombra. 

            ![Create resource group (Erőforráscsoport létrehozása)](./media/data-factory-copy-data-wizard-tutorial/create-new-resource-group.png)

        Az oktatóanyag egyes lépései azt feltételezik, hogy az **ADFTutorialResourceGroup** nevet adta az erőforráscsoportnak. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
7. Győződjön meg arról, hogy a **New data factory** (Új data factory) panelen be van jelölve az **Add to Startboard** (Hozzáadás a kezdőpulthoz) beállítás.
8. Kattintson a **Create** (Létrehozás) elemre a **New data factory** (Új data factory) panelen.

    Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a **Data factory name “ADFTutorialDataFactory” is not available** (Az „ADFTutorialDataFactory” data factory-név nem érhető el) hibaüzenetet kapja, módosítsa a data factory nevét (például sajátnévADFTutorialDataFactory-re), majd próbálkozzon újra a létrehozással. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.  
     
    ![A Data Factory name not available (A data factory neve nem érhető el) üzenet](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png)
    
    > [AZURE.NOTE] A data factory neve később DNS-névként regisztrálható, így nyilvánosan láthatóvá válhat.  

9. Kattintson a bal oldali **NOTIFICATIONS** (ÉRTESÍTÉSEK) központra, és keresse meg a létrehozási folyamat értesítéseit. Ha a **NOTIFICATIONS** (ÉRTESÍTÉSEK) panel meg van nyitva, kattintson az **X**-re a bezárásához. 
10. A létrehozás befejezése után megjelenik a **DATA FACTORY** panel, ahogy alább látható.

    ![Data factory kezdőlap](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png)

## Folyamat létrehozása

1. A Data Factory kezdőlapján kattintson a **Copy data** (Adatok másolása) csempére a **Copy Wizard** (Másolás varázsló) elindításához. 

    > [AZURE.NOTE] Ha a böngésző azt jeleníti meg, hogy „Authorizing...” (Hitelesítés), és nem lép tovább, tiltsa le a **Block third party cookies and site data** (Harmadik féltől származó cookie-k és webhelyadatok tiltása) jelölőnégyzetet, illetve törölje a jelölését, vagy hagyja engedélyezve, és hozzon létre kivételt a **login.microsoftonline.com** webhelyhez, majd próbálkozzon ismét a varázsló elindításával.
2. A **Properties** (Tulajdonságok) oldalon:
    1. Írja be a **CopyFromBlobToAzureSql** kifejezést a **Task name** (Feladat neve) mezőbe.
    2. Adjon meg **leírást** (opcionális).
    3. Ellenőrizze a **Start date time** (Kezdő dátum és időpont) és az **End date time** (Záró dátum és időpont) mezők értékét. Módosítsa az **End date time** (Záró dátum és időpont) értékét, hogy az a **Start date time** (Kezdő dátum és időpont) értékéhez képest a következő napon legyen. 
    3. Kattintson a **Tovább** gombra.  

    ![Copy (Másolás) eszköz – Properties (Tulajdonságok) oldal](./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png) 
3. A **Source data store** (Forrásadattár) oldalon kattintson az **Azure Blob Storage** csempére. Az oldal használatával megadhatja a forrásadattárat a másolási feladathoz. Használhatja egy meglévő adattár társított szolgáltatását, vagy megadhat egy új adattárat. Egy meglévő társított szolgáltatás használatához kattintson a **FROM EXISTING LINKED SERVICES** (MEGLÉVŐ TÁRSÍTOTT SZOLGÁLTATÁSOKBÓL) elemre, és válassza ki a megfelelő társított szolgáltatást. 

    ![Copy (Másolás) eszköz – Source data store (Forrásadattár) oldal](./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png)
5. A **Specify the Azure Blob storage account** (Az Azure Blob Storage-fiók megadása) oldalon:
    1. Adja meg az **AzureStorageLinkedService** nevet a **Linked service name** (Társított szolgáltatás neve) mezőben.
    2. Győződjön meg arról, hogy az **Account selection method** (Fiókválasztási módszer) mezőben a **From Azure subscriptions** (Azure-előfizetésekből) lehetőség van kiválasztva. 
    3. A kiválasztott előfizetéshez elérhető Azure Storage-fiókok listájából válasszon ki egy **Azure Storage-fiókot**. Manuálisan is megadhatja a tárfiók beállításait, ha az **Account selection method** (Fiókválasztási módszer) mezőben az **Enter manually** (Manuális megadás) lehetőséget választja, majd a **Next** (Tovább) gombra kattint. 

    ![Copy (Másolás) eszköz – Specify the Azure Blob storage account (Az Azure Blob Storage-fiók megadása) oldal](./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png)
6. A **Choose the input file or folder** (A bemeneti fájl vagy mappa kiválasztása) oldalon:
    1. Lépjen az **adftutorial** mappába.
    2. Jelölje ki az **emp.txt** fájlt, és kattintson a **Choose** (Kiválasztás) lehetőségre.
    3. Kattintson a **Tovább** gombra. 

    ![Copy (Másolás) eszköz – Choose the input file or folder (A bemeneti fájl vagy mappa kiválasztása) oldal](./media/data-factory-copy-data-wizard-tutorial/copy-tool-choose-input-file-or-folder.png)
7. A **File format settings** (Fájlformátum beállításai) oldalon válassza az **alapértelmezett** értékeket, és kattintson a **Next** (Tovább) gombra.

    ![Copy (Másolás eszköz) – File format settings (Fájlformátum beállításai) oldal](./media/data-factory-copy-data-wizard-tutorial/copy-tool-file-format-settings.png)  
8. A Destination data store (Céladattár) oldalon kattintson az **Azure SQL Database** csempére, majd kattintson a **Next** (Tovább) gombra.
9. A **Specify the Azure SQL database** (Az Azure SQL Database megadása) oldalon:
    1. Adja meg az **AzureSqlLinkedService** nevet a **Linked service name** (Társított szolgáltatás neve) mezőben. 
    2. Győződjön meg arról, hogy a **Server/database selection method** (Kiszolgáló-/adatbázis-kiválasztási módszer) esetén a **From Azure subscriptions** (Azure-előfizetésekből) beállítás van kiválasztva.
    3. Válassza ki a **Server name** (Kiszolgálónév) és a **Database** (Adatbázis) mezők értékeit.
    4. Adja meg a **felhasználónevet** és a **jelszót**.
    5. Kattintson a **Tovább** gombra.  
9. A **Table mapping** (Tábla-hozzárendelés) oldalon válassza ki a legördülő listából az **emp** beállítást a **Destination** (Cél) mezőhöz, és kattintson a **lefelé mutató nyílra** (opcionális) a séma és az adatok előnézetének megtekintéséhez.

    ![Copy (Másolás) eszköz – Table mapping (Tábla-hozzárendelés) oldal](./media/data-factory-copy-data-wizard-tutorial/copy-tool-table-mapping-page.png) 
10. A **Schema mapping** (Séma-hozzárendelés) oldalon kattintson a **Next** (Tovább) gombra.
11. Tekintse át a **Summary ** (Összegzés) oldalon szereplő információkat, majd kattintson a **Finish** (Befejezés) gombra. Ezzel létrehoz két társított szolgáltatást, két adatkészletet (bemeneti és kimeneti), valamint egy folyamatot a data factoryben (ahonnét elindította a Másolás varázslót). 
12. A **Deployment succeeded** (Sikeres üzembe helyezés) oldalon kattintson a **Click here to monitor copy pipeline** (Kattintson ide a másolási folyamat figyeléséhez) lehetőségre.

    ![Copy (Másolás) eszköz – Deployment succeeded (Sikeres üzembe helyezés) oldal](./media/data-factory-copy-data-wizard-tutorial/copy-tool-deployment-succeeded.png)  
13. A [Monitor and manage pipelines using Monitoring App](data-factory-monitor-manage-app.md) (Folyamatok figyelése és felügyelete a Monitoring App használatával) című cikk útmutatásaiból megtudhatja, hogyan figyelheti a létrehozott folyamatot.

    ![Monitoring App](./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png) 
 

## Lásd még:
| Témakör | Leírás |
| :---- | :---- |
| [Adattovábbítási tevékenységek](data-factory-data-movement-activities.md) | Ez a cikk részletes információkat tartalmaz a jelen oktatóanyagban használt Másolás tevékenységről. |
| [Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) | Ez a cikk ismerteti az Azure Data Factory-alkalmazásmodell ütemezési és végrehajtási aspektusait. |
| [Folyamatok](data-factory-create-pipelines.md) | Ennek a cikknek a segítségével megismerheti a Azure Data Factory folyamatait és tevékenységeit, és megtudhatja, hogyan hozhat létre velük teljes körű, adatvezérelt munkafolyamatokat saját forgatókönyvéhez vagy vállalkozásához. |
| [Adatkészletek](data-factory-create-datasets.md) | Ennek a cikknek a segítségével megismerheti az adatkészleteket az Azure Data Factoryban.
| [Folyamatok figyelése és felügyelete a Monitoring App használatával](data-factory-monitor-manage-app.md) | Ez a cikk ismerteti, hogyan figyelheti és felügyelheti a folyamatokat, illetve hogyan kereshet bennük hibákat a Monitoring & Management App használatával. 


<!--HONumber=sep16_HO1-->


