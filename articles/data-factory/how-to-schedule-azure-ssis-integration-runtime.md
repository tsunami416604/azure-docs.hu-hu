---
title: Az Azure-SSIS integrációs modul ütemezése |} A Microsoft Docs
description: Ez a cikk azt ismerteti, indítását és leállítását Azure-SSIS integrációs modul ütemezése az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 1/9/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 0b84f02d11e278950e4e44874e7b1af9da58f83f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58092446"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>Elindítása és leállítása az Azure-SSIS integrációs modul ütemezés szerint
Ez a cikk ismerteti a indítása és leállítása az Azure-SSIS integrációs modul (IR) ütemezése az Azure Data Factory (ADF) használatával. Az Azure-SSIS integrációs modul az ADF számítási erőforrás dedikált SQL Server Integration Services (SSIS) csomagjainak végrehajtásához. Rendszert futtató Azure-SSIS integrációs modul rendelkezik egy hozzá társított költségek. Ezért általában szeretné futtatni az integrációs modul csak akkor, amikor szüksége van leállítása az integrációs modul helyének, ha Ön már nincs rájuk szükség, és az SSIS-csomagok végrehajtása az Azure-ban. Használhatja az ADF felhasználói felületének (UI) / alkalmazás vagy az Azure PowerShell használatával [manuálisan indítása vagy leállítása az integrációs modul helyének](manage-azure-ssis-integration-runtime.md)).

Másik lehetőségként hozhat létre webes tevékenység indítása és leállítása az integrációs modul ütemezés szerint, például indítása előtt a napi ETL számítási feladatok végrehajtása, és azok elvégzése után délután leállítása folyamatban van a reggel ADF folyamatok.  SSIS-csomag végrehajtása tevékenység közötti két webes tevékenység, amely elindításához és leállításához az integrációs, így az integrációs rendszer indítása és leállítása az igény szerinti időt a csomag végrehajtása előtti/utáni láncolhatja is. SSIS-csomag végrehajtása tevékenységgel kapcsolatos további információkért lásd: [futtatása SSIS-csomag végrehajtása tevékenység ADF folyamat használatával egy SSIS-csomag](how-to-invoke-ssis-package-ssis-activity.md) cikk.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="prerequisites"></a>Előfeltételek
Ha már nem kiépítése az Azure-SSIS integrációs Modult, építi ki található utasításokat követve a [oktatóanyag](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Hozzon létre, és indítsa el, és vagy állítsa le az Azure-SSIS integrációs modul ADF folyamatok ütemezése
Ez a szakasz bemutatja, hogyan használhatja a webes tevékenység ADF folyamatokban ütemezés szerint az Azure-SSIS integrációs modul indítása és leállítása vagy elindítása & igény szerint állítsa le. Segítünk, hogy három folyamatokat hozhat létre: 

1. Az első folyamat tartalmazza egy webes tevékenységgel, amely elindítja az Azure-SSIS integrációs modult. 
2. A második folyamat tartalmazza egy webes tevékenységgel, amely megakadályozza az Azure-SSIS integrációs modult.
3. A harmadik folyamat tartalmazza egy SSIS-csomag végrehajtása tevékenység láncolt között két webes tevékenység, amely indítása és leállítása az Azure-SSIS integrációs modult. 

Miután létrehozta, és ezek a folyamatok tesztelése, ütemezési eseményindító létrehozása, és társíthatja azt bármely folyamatba. Az ütemezési eseményindító meghatározása a kapcsolódó folyamat futtatási ütemezésének. 

Például létrehozhat két eseményindítók, az elsőt napi 6 órakor futtatott, ütemezett és az első folyamat társítva, míg a második egy ütemezett napi Este 6 Órára, és a második folyamat társított.  Ezzel a módszerrel rendelkezik egy időszak közötti 6 6 és 18: minden nap, amikor az integrációs modul fut, a napi ETL számítási feladatok végrehajtására kész.  

Ha létrehoz egy harmadik eseményindítót, ütemezett napi éjfélkor futtatja, és a harmadik folyamat társított, hogy a folyamat fog futni minden nap, éjfélkor indítása az integrációs csomag a Futtatás előtt a csomagot, ezt követően végrehajtása, és azonnal leállítása az integrációs csomag végrehajtása után, így az integrációs modul helyének nem fut üresjáratban kiírt adatbázislapok.

### <a name="create-your-adf"></a>Hozzon létre az ADF használatával

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).    
2. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. Az a **új adat-előállító** lap, adja meg **MyAzureSsisDataFactory** a **neve**. 
      
   ![Új adat-előállító lap](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Az ADF neve globálisan egyedinek kell lennie. A következő hibaüzenet jelenik meg, ha az ADF (például Sajátnevemyazuressisdatafactoryra) nevének módosítása, és próbálkozzon újra a létrehozással. Lásd: [Data Factory elnevezési szabályait](naming-rules.md) a cikkben megismerheti az ADF-összetevők elnevezési szabályait.
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. Jelölje ki az Azure **előfizetés** alapján, amelyeket szeretne létrehozni az ADF. 
5. Az **Erőforráscsoportban** hajtsa végre a következő lépések egyikét:
     
   - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot. 
   - Válassza ki **új létrehozása**, és adja meg az új erőforráscsoport nevét.   
         
   Erőforráscsoportok kapcsolatos további információkért lásd: [erőforráscsoportok használata az Azure-erőforrások kezelése](../azure-resource-manager/resource-group-overview.md) cikk.
   
6. A **verzió**válassza **V2** .
7. A **hely**, válassza ki a legördülő listából az ADF létrehozását támogató helyek.
8. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
9. Kattintson a **Create** (Létrehozás) gombra.
10. Azure-irányítópulton a következő állapotleírás látható: **Adat-előállító üzembe helyezése**. 

    ![adat-előállító üzembe helyezése csempe](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. A létrehozás befejezése után megtekintheti az ADF oldalon, lent látható módon.
   
    ![Data factory kezdőlap](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. Kattintson a **létrehozás és Monitorozás** elindításához ADF felhasználói felületén vagy alkalmazás külön lapon.

### <a name="create-your-pipelines"></a>A folyamatok létrehozása

1. A **első lépések** lapon jelölje be **folyamat létrehozása**. 

   ![Első lépések lap](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
   
2. A **tevékenységek** eszközkészletben bontsa ki a **általános** & legördülő menüben, és húzza a **webes** tevékenység alakzatot a folyamat tervezőfelületére. A **általános** lapon, a tevékenység tulajdonságok ablakában módosítsa a tevékenység nevét a **startMyIR**. Váltson **beállítások** lapra, és a következő műveleteket hajthatja végre.

    1. A **URL-cím**, adja meg a következő URL-címet REST API-hoz, amely elindítja az Azure-SSIS integrációs Modult, és cserélje le `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}`, és `{integrationRuntimeName}` tényleges értéke alapján az integrációs modul számára: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01` Azt is megteheti, is is másolja és illessze be az erőforrás-azonosítója a figyelési oldaláról az integrációs modul helyének ADF felhasználói felületén vagy alkalmazás cserélje le a fenti URL-cím a következő részét: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
       ![ADF-SSIS integrációs modul az erőforrás-azonosító](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. A **metódus**válassza **POST**. 
    3. A **törzs**, adja meg `{"message":"Start my IR"}`. 
    4. A **hitelesítési**válassza **MSI** az ADF a felügyelt identitást használja, lásd: [felügyelt identiy adat-előállító](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) cikkben további információkat.
    5. A **erőforrás**, adja meg `https://management.azure.com/`.
    
       ![ADF Web Activity Schedule SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. Az első folyamat létrehozása egy másikat, a tevékenység nevének módosítása klónozása **stopMyIR** , és cserélje le a következő tulajdonságokat.

    1. A **URL-cím**, adja meg a következő URL-címet REST API-hoz, hogy leállítja az Azure-SSIS integrációs Modult, és cserélje le `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}`, és `{integrationRuntimeName}` tényleges értéke alapján az integrációs modul számára: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`
    
    2. A **törzs**, adja meg `{"message":"Stop my IR"}`. 

4. Hozzon létre egy harmadik folyamatot, áthúzással egy **SSIS-csomag végrehajtása** tevékenységet **tevékenységek** alakzatot a folyamattervezőben eszközkészlet tervezőfelületére, és konfigurálja a következő témakör utasításait követve [ Az SSIS-csomag végrehajtása tevékenység az ADF-ben egy SSIS-csomagok meghívásához](how-to-invoke-ssis-package-ssis-activity.md) cikk.  Másik lehetőségként használhatja egy **tárolt eljárás** tevékenység inkább utasításait követve konfigurálja és [az ADF-ben tárolt eljárási tevékenység használatával egy SSIS-csomagok meghívásához](how-to-invoke-ssis-package-stored-procedure-activity.md) cikk.  Ezután összekapcsolja az SSIS-csomag/tárolt eljárás végrehajtása tevékenység két webes tevékenység, amely indítása és leállítása az integrációs modul, ezek webes tevékenység az első/s folyamatokban hasonló között.

   ![ADF Web Activity On Demand SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. A felügyelt identitás hozzárendelése az ADF- **közreműködői** magát, így a folyamatai a webes tevékenység segítségével meghívhatja a REST API-t indítása és leállítása, kiépített Azure-SSIS integrációs modulokról szerepkört.  Az Azure Portalon az ADF lapon kattintson **hozzáférés-vezérlés (IAM)**, kattintson a **+ szerepkör-hozzárendelés hozzáadása**, majd a **szerepkör-hozzárendelés hozzáadása** panelen a következő műveleteket hajthatja végre.

    1. A **szerepkör**válassza **közreműködői**. 
    2. A **rendelhet hozzáféréseket**válassza **az Azure AD-felhasználó, csoport vagy szolgáltatásnév**. 
    3. A **kiválasztása**, keresse meg az ADF-nevet, és válassza ki azt. 
    4. Kattintson a **Save** (Mentés) gombra.
    
   ![Az ADF felügyelt identitás szerepkör-hozzárendelés](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. Az ADF ellenőrzése és a beállításokat az összes folyamat kattintva **összes ellenőrzése / ellenőrzése** az előállító és folyamat eszköztárán. Bezárás **gyári/Folyamatérvényesítési kimenet** kattintva **>>** gombra.  

   ![Folyamat érvényesítése](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>A folyamatok futtatásához tesztelés

1. Válassza ki **tesztfuttatás** minden folyamatot, és tekintse meg az eszköztáron **kimeneti** ablak alsó ablaktábláján. 

   ![Teszt futtatása](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. A harmadik folyamat teszteléséhez nyissa meg az SQL Server Management Studio (SSMS). A **kapcsolódás a kiszolgálóhoz** ablakban az alábbi műveleteket hajthatja végre. 

    1. A **kiszolgálónév**, adja meg  **&lt;az Azure SQL Database-kiszolgálónév&gt;. database.windows.net**.
    2. Válassza ki **beállítások >>**.
    3. A **csatlakozhat az adatbázishoz**válassza **SSISDB**.
    4. Kattintson a **Csatlakozás** gombra. 
    5. Bontsa ki a **Integration Services-katalógusok** -> **SSISDB** -> a mappa -> **projektek** -> Projekt az SSIS -> **csomagok** . 
    6. Kattintson a jobb gombbal a futtatásához, és válassza ki a megadott SSIS-csomag **jelentések** -> **szabványos jelentések** -> **az összes végrehajtás**. 
    7. Győződjön meg arról, hogy futtatta. 

   ![Ellenőrizze az SSIS-csomag futtatása](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>A folyamatok ütemezése

Most, hogy a folyamatok meg a várt módon működnek, futtatását őket a megadott cadences eseményindítókat is létrehozhat. További folyamatok és eseményindítók társításával kapcsolatos információkért lásd: [ütemezés a folyamat aktiválása](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule) cikk.

1. A folyamat eszköztárán válassza **eseményindító** válassza **új/Szerkesztés**. 

   ![Eseményindító új/Szerkesztés->](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. A **eseményindítók hozzáadása** ablaktáblán válassza előbb **+ új**.

   ![Eseményindítók hozzáadása – új](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. A **új eseményindító** ablaktáblán tegye a következőket: 

    1. A **neve**, adja meg az eseményindító nevét. A következő példában **naponta** eseményindító nevét. 
    2. A **típus**válassza **ütemezés**. 
    3. A **Start dátuma (UTC)**, adja meg a kezdő dátum és idő (UTC). 
    4. A **ismétlődési**, adja meg az eseményindító egy kiadása ütemben történik. A következő példában, a **napi** után. 
    5. A **záró**válassza **nem teljes** vagy adjon meg egy záró dátum és idő kiválasztása után **dátum**. 
    6. Válassza ki **aktiválva** az eseményindító aktiválása a teljes ADF-beállításokat a közzététel után azonnal. 
    7. Kattintson a **Tovább** gombra.

   ![Eseményindító új/Szerkesztés->](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. A **eseményindító futtatási paraméterei** lapon tekintse át a figyelmeztetéseket, és válassza ki **Befejezés**. 
5. A teljes ADF nastavení publikování kiválasztásával **összes közzététele** az előállító eszköztáron. 

   ![Összes közzététele](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Figyelheti a folyamatok és eseményindítók az Azure Portalon

1. Eseményindító-futtatások monitorozása és a folyamatfuttatások használja **figyelő** ADF felhasználói felületén vagy alkalmazás a bal oldali lapon. Részletes lépéseiért lásd: [a folyamat figyelése](quickstart-create-data-factory-portal.md#monitor-the-pipeline) cikk.

   ![Folyamatfuttatások](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. A folyamat futásához társított tevékenységfuttatások megtekintéséhez jelölje ki az első hivatkozásra (**Tevékenységfuttatások megtekintése**) a **műveletek** oszlop. A harmadik folyamat megjelenik a három tevékenység fut, egy az egyes láncolt tevékenységet a folyamatban (webes tevékenység elindítani az integrációs, hajtsa végre a csomagot, és a webes tevékenység Stored Procedure-tevékenység leállítása az integrációs modul). Válassza ki a folyamat megtekintéséhez futtatja újra **folyamatok** a fenti hivatkozásra.

   ![Tevékenységfuttatások](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. Az eseményindító-futtatások megtekintéséhez jelölje ki **eseményindító-futtatások** alatti legördülő menüből **Folyamatfuttatások** tetején. 

   ![Eseményindító-futtatások](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>Monitorozza a folyamatok és eseményindítók a PowerShell-lel

Például az alábbi példák parancsprogramok használatával figyelheti a folyamatok és eseményindítók.

1. Folyamatfuttatás állapotának lekéréséhez.

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. Eseményindító kapcsolatos adatok beolvasása.

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. Eseményindító-Futtatás állapotának beolvasása.

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Létrehozása és ütemezése az Azure Automation-runbookot, amely Azure-SSIS integrációs modul indítása/leállítása

Ebben a szakaszban megtanulhatja, amely végrehajtja a PowerShell-példaszkript Azure Automation-runbook létrehozása az Azure-SSIS integrációs modul ütemezett indítása/leállítása.  Ez akkor hasznos, ha meg szeretné végrehajtani az integrációs Modult előtti/utáni-feldolgozás indítása/leállítása előtt vagy után további parancsfájlokat.

### <a name="create-your-azure-automation-account"></a>Az Azure Automation-fiók létrehozása

Ha egy Azure Automation-fiók már nem rendelkezik, hozzon létre egyet, ez a lépés utasításait követve. Részletes lépéseiért lásd: [hozzon létre egy Azure Automation-fiók](../automation/automation-quickstart-create-account.md) cikk. Ez a lépés részeként létrehozott egy **Azure-beli futtató** fiókot (egyszerű szolgáltatásnevének az Azure Active Directory), és rendelje hozzá egy **közreműködői** szerepkört az Azure-előfizetésében. Győződjön meg arról, hogy-e ugyanahhoz az előfizetéshez, amely tartalmazza az ADF az Azure-SSIS integrációs modult. Az Azure Automation fogja használni a fiók hitelesítéséhez az Azure Resource Manager és az erőforrások üzemeltetéséhez. 

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. ADF felhasználói felületén vagy alkalmazás jelenleg csak a Microsoft Edge és a Google Chrome böngészők támogatott.
2. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).    
3. Válassza ki **új** a bal oldali menüben válassza ki a **Monitoring + Management**, és válassza ki **Automation**. 

   ![Új -> Monitoring + Management Automation ->](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. A **Automation-fiók hozzáadása** panelen a következő műveleteket hajthatja végre.

    1. A **neve**, adja meg az Azure Automation-fiók nevét. 
    2. A **előfizetés**, válassza ki az előfizetést, amely rendelkezik az ADF az Azure-SSIS integrációs modult. 
    3. A **erőforráscsoport**, jelölje be **új létrehozása** hozzon létre egy új erőforráscsoportot, vagy **meglévő** , válasszon ki egy meglévőt. 
    4. A **hely**, válassza ki az Azure Automation-fiók helyét. 
    5. Győződjön meg róla **létrehozása Azure-beli futtató fiók** , **Igen**. Egy egyszerű szolgáltatást az Azure Active Directoryban létrejön és hozzárendelt egy **közreműködői** szerepkört az Azure-előfizetésében.
    6. Válassza ki **rögzítés az irányítópulton** véglegesen megjelenítése az Azure-irányítópulton. 
    7. Kattintson a **Létrehozás** gombra. 

   ![Új -> Monitoring + Management Automation ->](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. Látni fogja az Azure Automation-fiókját az Azure-irányítópult és az értesítések központi telepítésének állapotát. 
    
   ![Automation üzembe helyezése](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. Látni fogja a kezdőlap elérését, az Azure Automation-fiók sikeres létrehozása után. 

   ![Automation-kezdőlap](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>ADF-modulok importálása

1. Válassza ki **modulok** a **megosztott erőforrások** szakaszt a bal oldali menüben, és ellenőrizze, hogy rendelkezik **AzureRM.DataFactoryV2**  +   **AzureRM.Profile** modulok listájában.

   ![Ellenőrizze a szükséges modulok](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Ha nem rendelkezik **AzureRM.DataFactoryV2**, nyissa meg a PowerShell-galériájában [AzureRM.DataFactoryV2 modul](https://www.powershellgallery.com/packages/AzureRM.DataFactoryV2/), jelölje be **üzembe helyezés az Azure Automation**, jelölje ki az Azure Automation-fiókot, és válassza ki **OK**. Lépjen vissza a megtekintéséhez **modulok** a **megosztott erőforrások** szakaszt a bal oldali menüben, és várjon, amíg megjelenik **állapot** , **AzureRM.DataFactoryV2** a modul értékre módosult **elérhető**.

    ![A Data Factory modul ellenőrzése](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Ha nem rendelkezik **AzureRM.Profile**, nyissa meg a PowerShell-galériájában [AzureRM.Profile modul](https://www.powershellgallery.com/packages/AzureRM.profile/), jelölje be **üzembe helyezés az Azure Automation**, válassza ki az Azure Automation fiók, és válassza ki **OK**. Lépjen vissza a megtekintéséhez **modulok** a **megosztott erőforrások** szakaszt a bal oldali menüben, és várjon, amíg megjelenik **állapota** , a **AzureRM.Profile** a modul értékre módosult **elérhető**.

    ![A profil modul ellenőrzése](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>A PowerShell-runbook létrehozása

Az alábbi szakasz ismerteti a PowerShell-runbook létrehozása. A parancsfájl vagy a runbookhoz társított elindul vagy leáll a megadott parancs alapján az Azure-SSIS integrációs modul **művelet** paraméter. Ez a szakasz nem biztosít a runbook létrehozása a részleteket. További információkért lásd: [hozzon létre egy runbookot](../automation/automation-quickstart-create-runbook.md) cikk.

1. Váltson **Runbookok** lapot, és válasszon **+ forgatókönyv hozzáadása** az eszköztáron. 

   ![Runbook gomb hozzáadása](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. Válassza ki **hozzon létre egy új runbookot** , és hajtsa végre a következőket: 

    1. A **neve**, adja meg **StartStopAzureSsisRuntime**.
    2. A **Runbook típusa**válassza **PowerShell**.
    3. Kattintson a **Létrehozás** gombra.
    
   ![Runbook gomb hozzáadása](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. Másolja és illessze be a következő PowerShell-parancsfájlt a runbook parancsfájl ablakra. Mentse és tegye közzé a runbook használatával **mentése** és **közzététel** az eszköztárban található gombokra. 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Connect-AzAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

   ![PowerShell-alapú runbook szerkesztése](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
    
4. A runbook tesztelése kiválasztásával **Start** gombra az eszköztáron. 

   ![Indítsa el a runbook gomb](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. A **Runbook indítása** ablaktáblán tegye a következőket: 

    1. A **ERŐFORRÁSCSOPORT-név**, adja meg az erőforrás nevét, amely rendelkezik az ADF az Azure-SSIS integrációs modult. 
    2. A **adat-előállító nevét**, adja meg a nevét, az ADF az Azure-SSIS integrációs modult. 
    3. A **AZURESSISNAME**, adja meg a nevét, az Azure-SSIS integrációs modult. 
    4. A **művelet**, adja meg **START**. 
    5. Kattintson az **OK** gombra.  

   ![Indítsa el a runbook ablak](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. A feladat ablakban válassza ki a **kimeneti** csempére. A kimeneti ablakban, várjon, amíg az üzenet **### befejezve ###** , miután látta, **### kezdő ###**. Az Azure-SSIS integrációs modul indítása körülbelül 20 percet vesz igénybe. Bezárás **feladat** ablakot, és biztonsági get való **Runbook** ablak.

   ![Az Azure SSIS integrációs modul – elindítva](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. Ismételje az előző két lépést használatával **LEÁLLÍTÁSA** értékeként **művelet**. Indítsa újra a runbook kiválasztásával **Start** gombra az eszköztáron. Adja meg az erőforráscsoport, az ADF és az Azure-SSIS integrációs modul nevét. A **művelet**, adja meg **LEÁLLÍTÁSA**. A kimeneti ablakban, várjon, amíg az üzenet **### befejezve ###** , miután látta, **### leállítása ###**. Az Azure-SSIS integrációs modul leállítása nem eltarthat elindításával. Bezárás **feladat** ablakot, és biztonsági get való **Runbook** ablak.

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Ütemezések a runbook indítása és leállítása az Azure-SSIS integrációs modul létrehozása

Az előző szakaszban létrehozott az Azure Automation-runbook, indítása vagy leállítása az Azure-SSIS integrációs modult. Ebben a szakaszban létrehoz két ütemezések a runbookhoz. Az első ütemezés konfigurálásakor megadott **START** a **művelet**. Hasonlóképpen, a másodikat konfigurálásakor megadott **LEÁLLÍTÁSA** a **művelet**. Ütemezések létrehozása részletes lépéseiért lásd: [ütemezés létrehozása](../automation/automation-schedules.md#creating-a-schedule) cikk.

1. A **Runbook** ablakban válassza **ütemezések**, és válassza ki **+ ütemezés hozzáadása** az eszköztáron. 

   ![Az Azure SSIS integrációs modul – elindítva](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. A **ütemezés Runbook** ablaktáblán tegye a következőket: 

    1. Válassza ki **összekapcsolhat egy ütemezést a runbook**. 
    2. Válassza ki **új ütemezés létrehozása**.
    3. A **új ütemezés** panelen adja meg **indítsa el integrációs modul naponta** a **neve**. 
    4. A **elindul**, adjon meg, amely néhány perccel az aktuális idő időt. 
    5. A **ismétlődési**válassza **ismétlődő**. 
    6. A **Ismétlődés minden**, adja meg **1** válassza **nap**. 
    7. Kattintson a **Létrehozás** gombra. 

   ![Azure SSIS integrációs modul indítása ütemezését](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. Váltson **paraméterek és futtatási beállítások** fülre. Adja meg az erőforráscsoport, az ADF és az Azure-SSIS integrációs modul nevét. A **művelet**, adja meg **START** válassza **OK**. Válassza ki **OK** ismételt használatával ellenőrizheti az ütemezés a **ütemezések** a runbook lapján. 

   ![Indítás előtt az Azure-SSIS integrációs modul ütemezése](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. Ismételje az előző két lépést nevű ütemezés létrehozása **IR leállítása naponta**. Adjon meg, amely legalább 30 perccel későbbinek megadott időt **indítsa el integrációs modul naponta** ütemezés. A **művelet**, adja meg **LEÁLLÍTÁSA** válassza **OK**. Válassza ki **OK** ismételt használatával ellenőrizheti az ütemezés a **ütemezések** a runbook lapján. 

5. A **Runbook** ablakban válassza **feladatok** a bal oldali menüben. Megjelenik a a megadott ütemezés szerint létrehozott feladatok időpontokat, és azok állapotát. Láthatja, hogy a feladat részleteit, például a kimenetét, hasonló után meg kell vizsgálni a runbook tesztelése. 

   ![Indítás előtt az Azure-SSIS integrációs modul ütemezése](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. Miután befejezte a tesztelést, tiltsa le az ütemezések szerkesztésével őket. Válassza ki **ütemezések** a bal oldali menüben válassza **integrációs modul elindítása napi és leállítása integrációs modul naponta**, és válassza ki **nem** a **engedélyezve**. 

## <a name="next-steps"></a>További lépések
Tekintse meg a következő blogbejegyzésben található:
-   [Végezzen modernizálást és az ETL/ELT munkafolyamatok SSIS tevékenységek az ADF folyamatok kiterjesztése](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)

Lásd az SSIS dokumentációjának alábbi cikkeit: 

- [SSIS-csomag üzembe helyezése, futtatása és monitorozása az Azure-on](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Csatlakozás SSIS-katalógushoz Azure-on](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Csomag végrehajtásának ütemezése az Azure-on](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Csatlakozás helyszíni adatforrásokhoz Windows-hitelesítéssel](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
