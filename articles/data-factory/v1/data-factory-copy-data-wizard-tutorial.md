---
title: "Oktatóanyag: Folyamat létrehozása a Másolás varázsló használatával | Microsoft Docs"
description: "Az oktatóanyag során létrehoz egy másolási tevékenységgel rendelkező Azure Data Factory-folyamatot a Data Factory által támogatott Másolás varázslóval."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: b87afb8e-53b7-4e1b-905b-0343dd096198
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/10/2017
ms.author: spelluru
robots: noindex
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 6b42ad8d52f2c25327508f8fbfa14292169d1c05
ms.contentlocale: hu-hu
ms.lasthandoff: 09/25/2017

---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-data-factory-copy-wizard"></a>Oktatóanyag: Másolási tevékenységgel rendelkező folyamat létrehozása a Data Factory Másolás varázslója használatával
> [!div class="op_single_selector"]
> * [Áttekintés és előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Másolás varázsló](data-factory-copy-data-wizard-tutorial.md)
> * [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-sablon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

Ez az oktatóanyag megmutatja, hogyan másolhat adatokat egy Azure Blob Storage-tárból egy Azure SQL-adatbázisba a **Másolás varázsló** segítségével. 

Az Azure Data Factory **Másolás varázslójával** gyorsan létrehozhat egy olyan adatátviteli folyamatot, amely a támogatott forrásadattárból adatokat másol a támogatott céladattárba. Ezért azt javasoljuk, hogy a varázsló segítségével első lépésként hozzon létre mintafolyamatot az adatátviteli forgatókönyvhöz. A forrásként és célként támogatott adattárak listáját a [támogatott adattárakról](data-factory-data-movement-activities.md#supported-data-stores-and-formats) szóló témakörben találja.  

Ez az oktatóanyag bemutatja, hogyan hozhat létre Azure data factoryt, indíthatja el a Másolás varázslót, és végigvezeti az adatfeldolgozási/-átviteli forgatókönyv részletes ismertetését tartalmazó lépések sorozatán. Amikor befejezte a varázsló lépéseit, a varázsló automatikusan létrehoz egy másolási tevékenységet tartalmazó folyamatot, amelynek során az adatokat átmásolja egy Azure Blob Storage-ból egy Azure SQL Databese-be. A másolási tevékenységről további információk az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkben találhatók.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzése előtt hajtsa végre [Az oktatóanyag áttekintése](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című cikkben előfeltételként felsorolt lépéseket.

## <a name="create-data-factory"></a>Data factory létrehozása
Ebben a lépésben az Azure Portal használatával létrehozza az **ADFTutorialDataFactory** nevű Azure data factoryt.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **+ ÚJ** elemre a jobb felső sarokban, kattintson az **Adatok + analitika**, majd a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png)
2. A **New data factory** (Új data factory) panelen:
   
   1. A**Name** (Név) mezőbe írja be a következőt: **ADFTutorialDataFactory**.
       Az Azure data factory nevének globálisan egyedinek kell lennie. A `Data factory name “ADFTutorialDataFactory” is not available` hibaüzenet esetén változtassa meg az adat-előállító nevét (legyen például sajátnévADFTutorialDataFactoryYYYYMMDD), majd kísérelje meg újra a létrehozást. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.  
      
       ![A Data Factory name not available (A data factory neve nem érhető el) üzenet](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png)    
   2. Jelölje ki az Azure-**előfizetést**.
   3. Az erőforráscsoportban hajtsa végre a következő lépések egyikét: 
      
      - Meglévő erőforráscsoport kiválasztásához kattintson a **Use existing** (Meglévő használata) elemre.
      - Az erőforráscsoport nevének megadásához válassza ki a **Create new** (Új létrehozása) lehetőséget.
          
        Az oktatóanyag egyes lépései azt feltételezik, hogy az **ADFTutorialResourceGroup** nevet adta az erőforráscsoportnak. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.
   4. Válassza ki a Data Factory **helyét**.
   5. A panel alján jelölje be a **Pin to dashboard** (Rögzítés az irányítópulton) jelölőnégyzetet.  
   6. Kattintson a **Létrehozás** gombra.
      
       ![A New data factory (Új data factory) panel](media/data-factory-copy-data-wizard-tutorial/new-data-factory-blade.png)            
3. A létrehozás befejezése után a **Data Factory** panel a következő képen látható módon jelenik meg:
   
   ![Data factory kezdőlap](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png)

## <a name="launch-copy-wizard"></a>A Másolás varázsló indítása
1. A Data Factory panelen kattintson az **Adatok másolása (ELŐZETES VERZIÓ)** elemre a **Copy Wizard** (Másolás varázsló) elindításához. 
   
   > [!NOTE]
   > Ha a böngésző az Authorizing... (Hitelesítés...) lépésnél megakad, távolítsa el a jelölést a **Block third-party cookies and site data** (Harmadik féltől származó cookie-k és webhelyadatok tiltása) jelölőnégyzetből a böngésző beállításai között, vagy hagyja engedélyezve a beállítást, és hozzon létre kivételt a **login.microsoftonline.com** webhelyhez, majd kísérelje meg ismét a varázsló elindítását.
2. A **Properties** (Tulajdonságok) oldalon:
   
   1. Írja be a **CopyFromBlobToAzureSql** kifejezést a **Task name** (Feladat neve) mezőbe.
   2. Adjon meg **leírást** (opcionális).
   3. A **Start date time** (Kezdő dátum és időpont) és az **End date time** (Záró dátum és időpont) mező értékét módosítsa úgy, hogy a záró dátum a mai napra, a kezdő dátum pedig öt nappal korábbra essen.  
   4. Kattintson a **Tovább** gombra.  
      
      ![Copy (Másolás) eszköz – Properties (Tulajdonságok) oldal](./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png) 
3. A **Source data store** (Forrásadattár) oldalon kattintson az **Azure Blob Storage** csempére. Az oldal használatával megadhatja a forrásadattárat a másolási feladathoz. 
   
    ![Copy (Másolás) eszköz – Source data store (Forrásadattár) oldal](./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png)
4. A **Specify the Azure Blob storage account** (Az Azure Blob Storage-fiók megadása) oldalon:
   
   1. Adja meg az **AzureStorageLinkedService** nevet a **Linked service name** (Társított szolgáltatás neve) mezőben.
   2. Győződjön meg arról, hogy az **Account selection method** (Fiókválasztási módszer) mezőben a **From Azure subscriptions** (Azure-előfizetésekből) lehetőség van kiválasztva.
   3. Jelölje ki az Azure-**előfizetést**.  
   4. A kiválasztott előfizetéshez elérhető Azure Storage-fiókok listájából válasszon ki egy **Azure Storage-fiókot**. Manuálisan is megadhatja a tárfiók beállításait, ha az **Account selection method** (Fiókválasztási módszer) mezőben az **Enter manually** (Manuális megadás) lehetőséget választja, majd a **Next** (Tovább) gombra kattint. 
      
      ![Copy (Másolás) eszköz – Specify the Azure Blob storage account (Az Azure Blob Storage-fiók megadása) oldal](./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png)
5. A **Choose the input file or folder** (A bemeneti fájl vagy mappa kiválasztása) oldalon:
   
   1. Kattintson duplán az **adftutorial** mappára.
   2. Jelölje ki az **emp.txt** fájlt, és kattintson a **Choose** (Kiválasztás) lehetőségre.
      
      ![Copy (Másolás) eszköz – Choose the input file or folder (A bemeneti fájl vagy mappa kiválasztása) oldal](./media/data-factory-copy-data-wizard-tutorial/copy-tool-choose-input-file-or-folder.png)
6. A **Choose the input file or folder** (A bemeneti fájl vagy mappa kiválasztása) lapon kattintson a **Next** (Tovább) gombra. Ne jelölje be a **Binary copy** (Bináris másolat) beállítást. 
   
    ![Copy (Másolás) eszköz – Choose the input file or folder (A bemeneti fájl vagy mappa kiválasztása) oldal](./media/data-factory-copy-data-wizard-tutorial/chose-input-file-folder.png) 
7. A **File format settings** (Fájlformátum beállításai) oldalon a fájl elemzése során a varázsló által automatikusan észlelt elválasztó karakterek és séma láthatók. Az elválasztó karaktereket manuálisan is megadhatja a varázsló számára az automatikus észlelés leállításához vagy a felülíráshoz. Az elválasztó karakterek megtekintése és adatok előzetes megtekintése után kattintson a **Next** (Tovább) gombra. 
   
    ![Copy (Másolás eszköz) – File format settings (Fájlformátum beállításai) oldal](./media/data-factory-copy-data-wizard-tutorial/copy-tool-file-format-settings.png)  
8. A Destination data store (Céladattár) oldalon válassza ki az **Azure SQL Database** csempét, majd kattintson a **Next** (Tovább) gombra.
   
    ![Copy (Másolás) eszköz – Choose destination store (Céltár kiválasztása) oldal](./media/data-factory-copy-data-wizard-tutorial/choose-destination-store.png)
9. A **Specify the Azure SQL database** (Az Azure SQL Database megadása) oldalon:
   
   1. Adja meg az **AzureSqlLinkedService** nevet a **Connection name** (Kapcsolat neve) mezőben.
   2. Győződjön meg arról, hogy a **Server/database selection method** (Kiszolgáló-/adatbázis-kiválasztási módszer) mezőben a **From Azure subscriptions** (Azure-előfizetésekből) lehetőség van kiválasztva.
   3. Jelölje ki az Azure-**előfizetést**.  
   4. Válassza ki a **Server name** (Kiszolgálónév) és a **Database** (Adatbázis) mezők értékeit.
   5. Adja meg a **felhasználónevet** és a **jelszót**.
   6. Kattintson a **Tovább** gombra.  
      
      ![Copy (Másolás) eszköz – specify Azure SQL database (Az Azure SQL Database megadása) oldal](./media/data-factory-copy-data-wizard-tutorial/specify-azure-sql-database.png)
10. A **Table mapping** (Tábla-hozzárendelés) oldalon válassza ki a legördülő listából az **emp** beállítást a **Destination** (Cél) mezőhöz, és kattintson a **lefelé mutató nyílra** (opcionális) a séma és az adatok előnézetének megtekintéséhez.
    
     ![Copy (Másolás) eszköz – Table mapping (Tábla-hozzárendelés) oldal](./media/data-factory-copy-data-wizard-tutorial/copy-tool-table-mapping-page.png) 
11. A **Schema mapping** (Séma-hozzárendelés) oldalon kattintson a **Next** (Tovább) gombra.
    
    ![Copy (Másolás) eszköz – schema mapping (Séma-hozzárendelés) oldal](./media/data-factory-copy-data-wizard-tutorial/schema-mapping-page.png)
12. A **Performance settings** (Teljesítménybeállítások) lapon kattintson a **Next** (Tovább) gombra. 
    
    ![Copy (Másolás) eszköz – performance settings (Teljesítménybeállítások) oldal](./media/data-factory-copy-data-wizard-tutorial/performance-settings.png)
13. Tekintse át a **Summary** (Összegzés) oldalon szereplő információkat, majd kattintson a **Finish** (Befejezés) gombra. A varázsló létrehoz két társított szolgáltatást, két adathalmazt (bemeneti és kimeneti), valamint egy folyamatot a data factoryban (ahonnét elindította a Másolás varázslót). 
    
    ![Copy (Másolás) eszköz – performance settings (Teljesítménybeállítások) oldal](./media/data-factory-copy-data-wizard-tutorial/summary-page.png)

## <a name="launch-monitor-and-manage-application"></a>A Monitor and Manage alkalmazás elindítása
1. A **Deployment** (Üzembe helyezés) lapon kattintson a következő hivatkozásra: `Click here to monitor copy pipeline`.
   
   ![Copy (Másolás) eszköz – Deployment succeeded (Sikeres üzembe helyezés) oldal](./media/data-factory-copy-data-wizard-tutorial/copy-tool-deployment-succeeded.png)  
2. A figyelési alkalmazás a böngészőben külön lapon nyílik meg.   
   
   ![Monitoring App](./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png)   
3. Az óránkénti szeletek legfrissebb állapotának megtekintéséhez kattintson a**Refresh** (Frissítés) gombra az alul látható **ACTIVITY WINDOWS** (TEVÉKENYSÉGABLAKOK) listán. Ekkor öt tevékenységablak jelenik meg, amelyek a folyamat kezdő és záró időpontja közötti öt napot ábrázolják. Mivel a lista nem frissül automatikusan, szükség lehet rá, hogy többször kattintson a Refresh (Frissítés) gombra, mire minden tevékenységablak üzemkész állapotba kerül. 
4. Válassza ki az egyik tevékenységablakot a listáról. A kapcsolódó részletes adatokat az **Activity Window Explorer** mutatja a jobb oldalon.

    ![Tevékenységablakok részletei](media/data-factory-copy-data-wizard-tutorial/activity-window-details.png)    

    Figyelje meg, hogy a hónap 11., 12., 13., 14. és 15. napját zöld színű számok jelölik, mert az e napokra vonatkozó napi kimeneti szeletek már elkészültek. Ugyanezt a színkódot láthatja a folyamaton és a grafikonos nézet kimeneti adatkészleténél is. Az előző lépésnél figyelje meg, hogy két szelet már elkészült, egy harmadik feldolgozás alatt van, a fennmaradó kettő pedig feldolgozásra vár (a színkódok alapján). 

    Az alkalmazás használatáról további tudnivalókat talál a [Monitor and manage pipeline using Monitoring App](data-factory-monitor-manage-app.md) (Folyamat figyelése és felügyelete a Monitoring App használatával) című cikkben.

## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag egy olyan másolási műveletet mutatott be, amelynek a forrásadattára egy Azure Blob Storage-tár, a céladattára pedig egy Azure SQL-adatbázis volt. Az alábbi táblázatban a másolási tevékenység által támogatott forrásadattárak és céladattárak listája látható: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Ha a táblázatban az adattárnak megfelelő hivatkozásra kattint, megtekintheti az adattár Másolás varázslóban megjelenő mezőinek, illetve tulajdonságainak részleteit. 
