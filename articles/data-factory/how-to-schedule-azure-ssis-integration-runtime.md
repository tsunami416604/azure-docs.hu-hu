---
title: Az Azure-SSIS Integration Runtimeének menetrendje | Microsoft Docs
description: Ez a cikk bemutatja, hogyan ütemezhet az Azure-SSIS Integration Runtime indítását és leállítását Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 8/2/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: d7a4a54f979cd4b14e12c5a57792241f1b2388d2
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68734704"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>Az Azure-SSIS Integration Runtime elindítása és leállítása ütemezett időközönként
Ez a cikk bemutatja, hogyan ütemezhet az Azure-SSIS Integration Runtime (IR) indítását és leállítását Azure Data Factory (ADF) használatával. Az Azure-SSIS IR a SQL Server Integration Services (SSIS) csomagok végrehajtásához dedikált, ADF számítási erőforrás. Az Azure-SSIS integrációs moduljának futtatásához hozzá kell rendelni egy díjat. Ezért általában csak akkor szeretné futtatni az IR-t, ha SSIS-csomagokat kell végrehajtania az Azure-ban, és le kell állítania az IR-t, ha már nincs rá szükség. Az IR-/app vagy a Azure PowerShell az [IR manuális indításához vagy](manage-azure-ssis-integration-runtime.md)leállításához használhatja az ADF felhasználói felületét (UI).

Azt is megteheti, hogy az ADF-folyamatokban webes tevékenységeket hoz létre, és leállítja az IR-t az ütemterv szerint, például a napi ETL-munkaterhelések végrehajtása előtt, és a befejezésük után délután leállítja.  A SSIS-csomagok végrehajtásának folyamata két olyan webes tevékenység között is felhasználható, amelyek elindítják és leállítják az IR-t, így az IR igény szerint elindul/leáll, a csomag végrehajtása előtt/után. A SSIS-csomagok végrehajtásával kapcsolatos további információkért lásd: [SSIS-csomag futtatása a SSIS-csomag végrehajtása az ADF-folyamaton](how-to-invoke-ssis-package-ssis-activity.md) című cikkben.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="prerequisites"></a>Előfeltételek
Ha még nem állította be az Azure-SSIS IR-t, az oktatóanyagban szereplő utasítások alapján adja ki [](tutorial-create-azure-ssis-runtime-portal.md)azt. 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Az Azure-SSIS IR-t elindító és leállító ADF-folyamatok létrehozása és ütemezett indítása
Ez a szakasz bemutatja, hogyan használhatók a webes tevékenységek az ADF-folyamatokban az Azure-SSIS integrációs modul ütemezés szerinti elindításához vagy leállításához, & az igény szerinti leállításához. A következő három folyamat létrehozásához nyújtunk segítséget: 

1. Az első folyamat egy webes tevékenységet tartalmaz, amely elindítja az Azure-SSIS IR-t. 
2. A második folyamat egy webes tevékenységet tartalmaz, amely leállítja az Azure-SSIS IR-t.
3. A harmadik folyamat az Azure-SSIS IR-t elindító/leállító két webes tevékenység között láncolt SSIS-csomag-tevékenységet tartalmaz. 

Miután létrehozta és teszteli ezeket a folyamatokat, létrehozhat egy ütemezett triggert, és társíthatja azt bármelyik folyamathoz. Az ütemezett trigger meghatározza a társított folyamat futtatásának ütemtervét. 

Létrehozhat például két eseményindítót, az elsőt a rendszer naponta 6 órakor futtatja, és az első folyamathoz társítja, a második pedig napi 6 ÓRAKOR ütemezve, a második folyamathoz társítva.  Így az IR futása során minden nap 6 – 6 óráig tart, és készen áll a napi ETL-alapú számítási feladatok végrehajtására.  

Ha olyan harmadik triggert hoz létre, amely naponta éjfélkor fut, és a harmadik folyamathoz társítva van, akkor a folyamat minden nap éjfélkor fut le, közvetlenül a csomag végrehajtása előtt, majd végrehajtja a csomagot, és azonnal az IR leállítása a csomag végrehajtása után, így az IR nem fog tétlenül futni.

### <a name="create-your-adf"></a>Az ADF létrehozása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).    
2. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. Az **új adatgyár** lapon adja meg a **MyAzureSsisDataFactory** **nevet**. 
      
   ![Új adat-előállító lap](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Az ADF nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa az ADF nevét (pl. Sajátnevemyazuressisdatafactoryra), és próbálja meg újból létrehozni. Az ADF-összetevők elnevezési szabályainak megismeréséhez tekintse meg a [Data Factory elnevezési szabályokról](naming-rules.md) szóló cikket.
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. Válassza ki azt az Azure-előfizetést, amelyben létre szeretné hozni az ADF-t. 
5. Az **Erőforráscsoportban** hajtsa végre a következő lépések egyikét:
     
   - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot. 
   - Válassza az **új létrehozása**lehetőséget, és adja meg az új erőforráscsoport nevét.   
         
   Az erőforráscsoportok megismeréséhez tekintse meg [Az Azure-erőforrások kezelésével foglalkozó erőforráscsoportok használata](../azure-resource-manager/resource-group-overview.md) című cikket.
   
6. A **verziónál**válassza a **v2** elemet.
7. A **hely**mezőben válassza ki az ADF létrehozásához támogatott helyek egyikét a legördülő listából.
8. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
9. Kattintson a **Create** (Létrehozás) gombra.
10. Az Azure-irányítópulton a következő csempe jelenik meg állapottal: **Data Factory üzembe helyezése**. 

    ![adat-előállító üzembe helyezése csempe](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. A létrehozás befejezése után megjelenik az ADF-lap az alábbi ábrán látható módon.
   
    ![Data factory kezdőlap](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. Kattintson a **szerző & a figyelő** elemre, ha egy külön lapon szeretné ELINDÍTANI az ADF felhasználói felületét/alkalmazását.

### <a name="create-your-pipelines"></a>A folyamatok létrehozása

1. Az **első lépések** lapon válassza a **folyamat létrehozása**lehetőséget. 

   ![Első lépések lap](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
   
2. A **tevékenységek** eszközkészletben bontsa ki az **általános** menü elemet, majd húzza a & eldobása **webes** tevékenységet a folyamat tervezői felületére. A tevékenység tulajdonságai ablak **általános** lapján módosítsa a tevékenység nevét **startMyIR**értékre. Váltson a **Beállítások** lapra, és végezze el a következő műveleteket.

    1. Az **URL-cím**mezőben adja meg a következő URL-címet REST API amely elindítja az Azure `{subscriptionId}`-SSIS `{factoryName}`IR- `{integrationRuntimeName}` t, és cserélje `{resourceGroupName}`le a, a, a és az értéket az IR tényleges értékeire: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01`Azt is megteheti, hogy átmásolja & illessze be az IR erőforrás-AZONOSÍTÓját az ADF felhasználói felületének/alkalmazásának figyelési lapjáról, és cserélje le a fenti URL-cím következő részét:`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
       ![ADF SSIS IR-erőforrás azonosítója](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. A **metódusnál**válassza a **post**lehetőséget. 
    3. A **törzs**mezőbe írja `{"message":"Start my IR"}`be a következőt:. 
    4. A **hitelesítéshez**válassza az **MSI** -t az ADF felügyelt identitásának használatához, további információért lásd: [felügyelt identitás Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) cikkhez.
    5. Az **erőforrás**mezőben adja `https://management.azure.com/`meg a következőt:.
    
       ![Webes tevékenységek automatikus kiosztása a SSIS-ben – IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. Az első folyamat klónozásával hozzon létre egy másodikat, módosítsa a tevékenység nevét, hogy **stopMyIR** , és cserélje le a következő tulajdonságokat.

    1. Az **URL-cím**mezőben adja meg a következő URL-címet REST API, amely leállítja `{subscriptionId}`az Azure-SSIS `{integrationRuntimeName}` IR-t, lecseréli `{resourceGroupName}` `{factoryName}`a (z),, és értéket az IR tényleges értékeivel:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`
    
    2. A **törzs**mezőbe írja `{"message":"Stop my IR"}`be a következőt:. 

4. Hozzon létre egy harmadik folyamatot, húzzon & drop an **SSIS Package** tevékenységet a **tevékenységek** eszközkészletből a folyamat-tervező felületre, és konfigurálja a SSIS-csomag meghívása a [végrehajtási SSIS-csomag használatával című témakör útmutatásait követve. a tevékenység az ADF-ben](how-to-invoke-ssis-package-ssis-activity.md) című cikkben.  Ehelyett egy **tárolt eljárási** tevékenységet is használhat, és konfigurálhatja azt a [SSIS-csomag meghívása a tárolt eljárás használata az ADF-ben](how-to-invoke-ssis-package-stored-procedure-activity.md) című cikkben leírt utasításokat követve.  Ezután a SSIS-csomag/tárolt eljárás végrehajtása két webes tevékenység között, amelyek az első/második folyamatokban hasonló webes tevékenységekhez hasonlítanak.

   ![Igény szerinti webes SSIS – IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. Rendelje hozzá a felügyelt identitást az ADF **közreműködői** szerepkörhöz, így a folyamatokban a webes tevékenységek meghívhatják REST API az Azure-SSIS IRs üzembe helyezésének elindításához és leállításához.  A Azure Portal ADF-lapján kattintson a **hozzáférés-vezérlés (iam)** elemre, kattintson a **+ szerepkör-hozzárendelés hozzáadása**lehetőségre, majd a **szerepkör-hozzárendelés hozzáadása** panelen végezze el a következő műveleteket.

    1. A **szerepkör**területen válassza a **közreműködő**lehetőséget. 
    2. A **hozzáférésének hozzárendeléséhez**válassza az **Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév**lehetőséget. 
    3. A **kiválasztás**elemnél keresse meg az ADF nevét, és jelölje ki. 
    4. Kattintson a **Save** (Mentés) gombra.
    
   ![Felügyelt identitás szerepkör-hozzárendelésének automatikus kiosztása](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. A gyári/folyamat eszköztáron az **összes ellenőrzése/érvényesítés** elemre kattintva ellenőrizze az ADF és az összes folyamat beállításait. A **gyári/folyamat-ellenőrzési kimenet** bezárásához kattintson **>>** a gombra.  

   ![Folyamat érvényesítése](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>A folyamatok futtatásának tesztelése

1. Válassza a **teszt futtatása** lehetőséget az összes folyamat eszköztárán, és tekintse meg a **kimenet** ablakot az alsó ablaktáblán. 

   ![Teszt futtatása](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. A harmadik folyamat teszteléséhez indítsa el a SQL Server Management Studiot (SSMS). A **Kapcsolódás a kiszolgálóhoz** ablakban végezze el a következő műveleteket. 

    1. A **kiszolgálónév**mezőben adja meg  **&lt;a Azure SQL Database-kiszolgáló&gt;nevét. database.Windows.net**.
    2. Válassza a **beállítások > > lehetőséget**.
    3. Az **adatbázishoz**való kapcsolódáshoz válassza a **SSISDB**lehetőséget.
    4. Kattintson a **Csatlakozás** gombra. 
    5. Bontsa ki az **Integration Services** -> -katalógusok**SSISDB** - > a mappa-> projects-> a SSIS Project-> **csomagokat**. 
    6. Kattintson a jobb gombbal a futtatni kívánt SSIS-csomagra, és válassza a **jelentések** -> **szabványos jelentések** -> **minden végrehajtás**elemet. 
    7. Ellenőrizze, hogy futott-e. 

   ![SSIS-csomag futtatásának ellenőrzése](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>A folyamatok ütemezhetnek

Most, hogy a folyamatok a várt módon működnek, létrehozhat eseményindítókat a megadott lépésszám futtatásához. Az eseményindítók folyamatokkal való társításával kapcsolatos részletekért lásd: [a folyamat aktiválása ütemezett](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule) cikkekben.

1. A folyamat eszköztárán válassza az **aktiválás** lehetőséget, és válassza az **új/szerkesztés**lehetőséget. 

   ![Trigger – > új/szerkesztés](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. Az **Eseményindítók hozzáadása** panelen válassza az **+ új**lehetőséget.

   ![Eseményindítók hozzáadása – új](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. Az **új trigger** ablaktáblán hajtsa végre a következő műveleteket: 

    1. A **név**mezőben adja meg az trigger nevét. A következő példában a **napi Futtatás** az trigger neve. 
    2. A **Típus mezőben**válassza az **ütemterv**lehetőséget. 
    3. A **kezdő dátum (UTC)** mezőben adjon meg egy kezdési dátumot és időpontot (UTC). 
    4. Az **ismétlődéshez**adja meg az eseményindítóhoz tartozó lépésszám értéket. A következő példában **naponta** egyszer. 
    5. A **befejezéshez**válassza a **nincs vége** lehetőséget, vagy adjon meg egy befejezési dátumot és időpontot **a dátum**kiválasztása után. 
    6. A teljes ADF-beállítások közzététele után azonnal aktiválja az **aktivált** elemet. 
    7. Kattintson a **Tovább** gombra.

   ![Trigger – > új/szerkesztés](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. A **trigger-paraméterek futtatása** lapon tekintse át a figyelmeztetéseket, és kattintson a **Befejezés gombra**. 
5. Tegye közzé a teljes ADF-beállításokat úgy, hogy kiválasztja az **összes közzététele** elemet a gyári eszköztáron. 

   ![Az összes közzététele](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Folyamatok és triggerek figyelése Azure Portal

1. Az trigger futtatásának és a folyamat futtatásának figyeléséhez használja a **monitor** lapot az ADF UI/app bal oldalán. A részletes lépésekért lásd [a folyamat figyelése](quickstart-create-data-factory-portal.md#monitor-the-pipeline) című cikket.

   ![Folyamatfuttatások](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. A folyamat futtatásához társított tevékenységek megtekintéséhez válassza ki az első hivatkozást (a**tevékenység futásának megtekintése**) a **műveletek** oszlopban. A harmadik folyamat esetében három tevékenység fut, egyet a folyamat összes láncolt tevékenységéhez (webes tevékenység, amely elindítja az IR-t, a tárolt eljárási tevékenységet a csomag végrehajtásához, valamint a webes tevékenységet az IR leállításához). A folyamat ismételt futtatásához válassza a felső **folyamatok** hivatkozását.

   ![Tevékenységfuttatások](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. Az trigger futtatásának megtekintéséhez válassza az **aktiválási futtatások** lehetőséget a legördülő listából a **folyamat futtatása** alatt a felső részen. 

   ![Eseményindító-futtatások](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>Folyamatok és triggerek figyelése a PowerShell-lel

A folyamatok és eseményindítók figyeléséhez használja a következő példákhoz hasonló parancsfájlokat.

1. Egy folyamat futtatási állapotának beolvasása.

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. Egy trigger információinak beolvasása.

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. Egy trigger futtatási állapotának beolvasása.

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Az Azure-SSIS IR-t elindító/leállító runbook létrehozása és ütemezett Azure Automation

Ebből a szakaszból megtudhatja, hogyan hozhat létre Azure Automation runbook, amelyek PowerShell-parancsfájlt hajtanak végre, és az Azure-SSIS IR-t az ütemterv szerint elindítják/leállítják.  Ez akkor hasznos, ha további parancsfájlokat szeretne végrehajtani, mielőtt elkezdené vagy leállítja az IR-t az előzetes/utólagos feldolgozáshoz.

### <a name="create-your-azure-automation-account"></a>Azure Automation fiók létrehozása

Ha már nincs Azure Automation fiókja, hozzon létre egyet a lépés utasításait követve. A részletes lépéseket lásd: [Azure Automation fiók létrehozása](../automation/automation-quickstart-create-account.md) című cikk. Ennek a lépésnek a részeként létre kell hoznia egy Azure-beli **futtató** fiókot (egy egyszerű szolgáltatásnevet a Azure Active Directory), és hozzá kell rendelnie egy **közreműködő** szerepkört az Azure-előfizetésében. Győződjön meg arról, hogy ugyanaz az előfizetés, amely tartalmazza az ADF-t az Azure SSIS IR-vel. Azure Automation ezt a fiókot fogja használni a hitelesítéshez Azure Resource Manager és az erőforrásokon való működéshez. 

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. Jelenleg az ADF UI/app csak a Microsoft Edge és a Google Chrome böngészőben támogatott.
2. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).    
3. Válassza az **új** lehetőséget a bal oldali menüben, majd válassza a **monitoring és felügyelet**lehetőséget, majd válassza az **Automation**lehetőséget. 

   ![New-> Monitoring és felügyelet-> Automation](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. Az **Automation-fiók hozzáadása** panelen hajtsa végre a következő műveleteket.

    1. A **név**mezőben adja meg a Azure Automation-fiók nevét. 
    2. Az **előfizetés**mezőben válassza ki azt az előfizetést, AMELYEN az ADF az Azure-SSIS IR-vel rendelkezik. 
    3. Az **erőforráscsoport**területen válassza az **új létrehozása** lehetőséget egy új erőforráscsoport létrehozásához, vagy **használja** a meglévőt egy meglévő kiválasztásához. 
    4. A **hely**mezőben válassza ki a Azure Automation fiókjának helyét. 
    5. Győződjön meg arról, hogy az Azure-beli **futtató fiók létrehozása** **Igen**. A rendszer létrehoz egy egyszerű szolgáltatásnevet a Azure Active Directoryban, és hozzárendeli a **közreműködői** szerepkört az Azure-előfizetésében.
    6. Válassza a **rögzítés az irányítópulton** lehetőséget az Azure-irányítópulton való végleges megjelenítéséhez. 
    7. Kattintson a **Létrehozás** gombra. 

   ![New-> Monitoring és felügyelet-> Automation](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. A Azure Automation-fiók üzembe helyezési állapota az Azure-irányítópulton és-értesítéseken jelenik meg. 
    
   ![Automatizálás üzembe helyezése](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. A sikeres létrehozást követően megjelenik a Azure Automation fiókjának kezdőlapja. 

   ![Automation kezdőlapja](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>ADF-modulok importálása

1. Válassza ki a bal oldali menüben található **modulok** elemet a **megosztott erőforrások** szakaszban, és ellenőrizze, hogy a **AzureRM. szerepel datafactoryv2** + **AzureRM. profil** van-e a modulok listájában.

   ![A szükséges modulok ellenőrzése](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Ha nincs **AzureRM. szerepel datafactoryv2**, nyissa meg a [AzureRM. szerepel datafactoryv2 modulhoz](https://www.powershellgallery.com/packages/AzureRM.DataFactoryV2/)tartozó PowerShell-Galéria, válassza az **üzembe helyezés Azure Automation**lehetőséget, válassza ki a Azure Automation fiókot, majd kattintson **az OK gombra**. Lépjen vissza a a bal oldali menüben, és várjon, amíg meg nem jelenik a **AzureRM. szerepel datafactoryv2** modul **állapota** .

    ![A Data Factory modul ellenőrzése](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Ha nem rendelkezik **AzureRM. profilval**, nyissa meg a PowerShell-Galéria for [AzureRM. Profile modult](https://www.powershellgallery.com/packages/AzureRM.profile/), válassza a **telepítés a Azure Automation**lehetőséget, válassza ki a Azure Automation fiókot, majd kattintson **az OK gombra**. Lépjen vissza a bal oldali menüben található **modulok** megtekintése a **megosztott erőforrások** részben, és várjon, amíg megjelenik az **AzureRM. Profile** modul **állapota** **elérhetőre**módosult.

    ![Profil modul ellenőrzése](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>A PowerShell-runbook létrehozása

A következő szakasz a PowerShell-runbook létrehozásának lépéseit ismerteti. A runbook társított parancsfájl az Azure-SSIS IR-t az **Operation** paraméterhez megadott parancs alapján elindítja vagy leállítja. Ez a szakasz nem tartalmazza a runbook létrehozásának teljes részleteit. További információ: [create a runbook](../automation/automation-quickstart-create-runbook.md) article.

1. Váltson a **runbookok** lapra, és válassza a **+ runbook hozzáadása** elemet az eszköztárból. 

   ![Runbook gomb hozzáadása](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. Válassza az **új Runbook létrehozása** lehetőséget, és végezze el a következő műveleteket: 

    1. A **név**mezőbe írja be a következőt: **StartStopAzureSsisRuntime**.
    2. A **Runbook típusa**beállításnál válassza a **PowerShell**lehetőséget.
    3. Kattintson a **Létrehozás** gombra.
    
   ![Runbook gomb hozzáadása](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. Másolja & illessze be a következő PowerShell-szkriptet a runbook-parancsfájl ablakába. Mentse, majd tegye közzé a runbook az eszköztár **Mentés** és **Közzététel** gombjának használatával. 

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

   ![PowerShell-runbook szerkesztése](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
    
4. Tesztelje a runbook az eszköztár **Start** gombjának kiválasztásával. 

   ![Runbook elindítása gomb](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. A **Start Runbook** (indításkor) ablaktáblán hajtsa végre a következő műveleteket: 

    1. Az **ERŐFORRÁSCSOPORT neve**mezőben adja meg az Azure-SSIS IR-vel rendelkező ADF-sel rendelkező erőforráscsoport nevét. 
    2. A **ADATELŐÁLLÍTÓ neve**mezőbe írja be az ADF nevét az Azure-SSIS IR használatával. 
    3. A **AZURESSISNAME**mezőben adja meg az Azure-SSIS IR nevét. 
    4. A **művelet**mezőben adja meg a **Start**értéket. 
    5. Kattintson az **OK** gombra.  

   ![Runbook ablak elindítása](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. A feladatok ablakban válassza a **kimenet** csempét. Az output (kimenet) ablakban várjon a következő üzenetre: # # **# # # befejezett # #** # # #. Az Azure-SSIS IR elindítása körülbelül 20 percet vesz igénybe. A **feladatok** ablakának bezárásához és a **Runbook** ablakhoz való visszatéréshez.

   ![Azure SSIS IR – elindítva](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. Ismételje meg az előző két lépést a **Leállítás** beállításnál a **művelet**értékeként. Indítsa el újra a runbook az eszköztár **Start** gombjának kiválasztásával. Adja meg az erőforráscsoportot, az ADF-t és az Azure-SSIS IR-neveket. A **művelethez**írja be a **stop**értéket. A kimenet ablakban várjon a következő üzenetre: # # # **# # befejezett # #** # # # # # # # # **Leállítás**# # # # #. Az Azure-SSIS IR leállítása nem tart, amíg meg nem kezdődik. A **feladatok** ablakának bezárásához és a **Runbook** ablakhoz való visszatéréshez.

8. A runbook egy webhookon keresztül is aktiválhatja, amely a webhookok menüpont kiválasztásával vagy egy olyan ütemtervtel indítható el, amely az alábbi táblázatban megadott **ütemtervek** kiválasztásával hozható létre.  

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Ütemtervek létrehozása a runbook az Azure-SSIS IR elindításához és leállításához

Az előző szakaszban létrehozta a Azure Automation runbook, amely elindíthatja vagy leállíthatja az Azure-SSIS IR-t. Ebben a szakaszban két ütemtervet fog létrehozni a runbook. Az első ütemterv konfigurálásakor meg kell adnia a művelet megkezdése **műveletet**. Hasonlóképpen, a második konfigurálásakor meg kell adnia a **Leállítás** **műveletet**. Az ütemtervek létrehozásával kapcsolatos részletes lépésekért tekintse meg az [Ütemterv létrehozása](../automation/shared-resources/schedules.md#creating-a-schedule) című cikket.

1. A **Runbook** ablakban válassza az **ütemtervek**lehetőséget, majd válassza az **+ ütemterv hozzáadása** lehetőséget az eszköztáron. 

   ![Azure SSIS IR – elindítva](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. A **Runbook-jegyzék** ablaktáblán hajtsa végre a következő műveleteket: 

    1. Válassza **az ütemterv csatolása a runbook**lehetőséget. 
    2. Válassza **az új ütemterv létrehozása**lehetőséget.
    3. Az **új ütemterv** ablaktáblán írja be a következőt: a **név**megkezdése **napi IR** . 
    4. Az **indításhoz**adjon meg egy olyan időpontot, amely az aktuális időpontnál néhány perccel korábbi. 
    5. Az **ismétlődéshez**válassza az **ismétlődő**lehetőséget. 
    6. Az **ismétlődéshez**adja meg az **1** értéket, és válassza a **napot**. 
    7. Kattintson a **Létrehozás** gombra. 

   ![Az Azure SSIS IR-kezdésének ütemezett időpontja](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. Váltson a **paraméterek és a futtatási beállítások** lapra. Adja meg az erőforráscsoportot, az ADF-t és az Azure-SSIS IR-neveket. A **művelethez**írja be a **Start** parancsot, és kattintson **az OK gombra**. Kattintson ismét az **OK gombra** a runbook ütemezett ütemtervének megtekintéséhez. 

   ![Az Azure SSIS integrációs moduljának beosztása](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. Ismételje meg az előző két lépést, és hozzon létre egy " **stop IR**" utasítással ellátott ütemtervet. Adja meg azt az időpontot, amely legalább 30 perccel azután van megadva, hogy a **Start IR napi** ütemterve meg van adva. A **művelethez**írja be a **Leállítás** értéket, és kattintson **az OK gombra**. Kattintson ismét az **OK gombra** a runbook ütemezett ütemtervének megtekintéséhez. 

5. A **Runbook** ablakban válassza a bal oldali menüben a **feladatok** elemet. Az ütemtervek által létrehozott feladatokat a megadott időpontokban és állapotukban kell megtekinteni. A runbook tesztelése után megtekintheti a feladatok részleteit, például a kimenetét. 

   ![Az Azure SSIS integrációs moduljának beosztása](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. Miután végzett a teszteléssel, tiltsa le az ütemterveket szerkesztéssel. Válassza az ütemtervek lehetőséget a bal oldali menüben, válassza a **napi IR/leállítás**(IR) indítása naponta lehetőséget, majd válassza a **nem** lehetőséget az **engedélyezéshez**. 

## <a name="next-steps"></a>További lépések
Tekintse meg a következő blogbejegyzést:
-   [ETL/ELT-munkafolyamatok modernizálása és kiterjesztése SSIS-tevékenységekkel az ADF-folyamatokban](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)

Lásd az SSIS dokumentációjának alábbi cikkeit: 

- [SSIS-csomag üzembe helyezése, futtatása és monitorozása az Azure-on](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Csatlakozás SSIS-katalógushoz Azure-on](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Csomag végrehajtásának ütemezése az Azure-on](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Csatlakozás helyszíni adatforrásokhoz Windows-hitelesítéssel](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
