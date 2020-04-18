---
title: Az Azure-SSIS-integrációs futásidő ütemezése
description: Ez a cikk bemutatja, hogyan ütemezheti az Azure-SSIS-integrációs futásidő indítását és leállítását az Azure Data Factory használatával.
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
manager: anandsub
ms.openlocfilehash: 50ff0afe13b5b098fbc8d9fbeefba295a6217192
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606073"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>Azure-SSIS integrációs modul indítása és leállítása ütemezés szerint

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk ismerteti, hogyan ütemezheti az Azure-SSIS-integrációs futásidejű (IR) indítása és leállítása az Azure Data Factory (ADF) használatával. Az Azure-SSIS IR az SQL Server Integration Services (SSIS) csomagok végrehajtására szolgáló ADF számítási erőforrás. Az Azure-SSIS IR futtatásához költségek járnak. Ezért általában csak akkor szeretné futtatni az infravörös szolgáltatást, ha SSIS-csomagokat kell végrehajtania az Azure-ban, és le kell állítania az infravörös szolgáltatást, ha már nincs rá szüksége. Az ADF felhasználói felület (UI)/app vagy az Azure PowerShell segítségével [manuálisan indíthatja el vagy állíthatja le az infravörös szolgáltatást.](manage-azure-ssis-integration-runtime.md)

Másik lehetőségként létrehozhat webes tevékenységeket az ADF-folyamatokban, hogy elindítsa/leállítsa az infravörös kapcsolat ütemezését, például reggel indítsa el a napi ETL-munkaterhelések végrehajtása előtt, és állítsa le délután, miután azok elkészült.  Az SSIS-csomag végrehajtása tevékenységet két olyan webes tevékenység között is láncolhatja, amelyek elindítják és leállítják az infravörös kapcsolatot, így az infravörös rendszer igény szerint elindul/leáll, éppen a csomag végrehajtása előtt/után. Az SSIS-csomagtevékenység végrehajtásáról további információt az [SSIS-csomag futtatása SSIS-csomag tevékenység végrehajtásával című témakörben talál az ADF-folyamat cikkében.](how-to-invoke-ssis-package-ssis-activity.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek
Ha még nem kiépítette az Azure-SSIS IR-t, az [oktatóanyag](tutorial-create-azure-ssis-runtime-portal.md)utasításait követve. 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Az Azure-SSIS IR-t elinduló és leállító ADF-folyamatok létrehozása és ütemezése
Ez a szakasz bemutatja, hogyan használhatja a webes tevékenységeket az ADF-folyamatokban az Azure-SSIS ir ütemezésszerinti elindításához vagy leállításához, vagy az igény szerinti leállításhoz, & állítsa le. Három csővezeték létrehozását vezetjük: 

1. Az első folyamat tartalmaz egy webes tevékenységet, amely elindítja az Azure-SSIS IR.The first pipeline contains a Web activity that starts your Azure-SSIS IR. 
2. A második folyamat tartalmaz egy webes tevékenységet, amely leállítja az Azure-SSIS IR.
3. A harmadik folyamat tartalmaz egy SSIS-csomag végrehajtása tevékenység et két webes tevékenység között, amelyek elindítják/leállítják az Azure-SSIS IR-t. 

A folyamatok létrehozása és tesztelése után létrehozhat egy ütemezési eseményindítót, és társíthatja azt bármely folyamathoz. Az ütemezési eseményindító határozza meg a társított folyamat futtatásának ütemezését. 

Létrehozhat például két eseményindítót, az első takarásban van ütemezve, és naponta 6 órakor fut, és az első folyamathoz van társítva, míg a második a napi futtatást 18:00 órakor futtatja, és a második folyamathoz van társítva.  Ily módon van egy időszak között 06:00-18:00 minden nap, amikor az infravörös fut, készen áll a napi ETL számítási feladatok végrehajtására.  

Ha létrehoz egy harmadik eseményindítót, amely a tervek szerint naponta éjfélkor fut, és a harmadik folyamathoz társítva van, akkor a folyamat minden nap éjfélkor fog futni, és közvetlenül a csomag végrehajtása előtt elindítja az infravörös csomagot, majd végrehajtja a csomagot, és azonnal leállítja az infravörös csomagot közvetlenül a csomag végrehajtása után, így az infravörös nem fog futni.

### <a name="create-your-adf"></a>Az ADF létrehozása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).    
2. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. Az **Új adatgyár** lapon adja meg a **MyAzureSsisDataFactory** for **Name**parancsot. 
      
   ![Új adat-előállító lap](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Az ADF nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenet jelenik meg, módosítsa az ADF nevét (pl. yournameMyAzureSsisDataFactory), és próbálja meg újra létrehozni. Az [Data Factory - Naming Rules](naming-rules.md) ADF-összetevők elnevezési szabályainak elnevezési szabályai című témakörben olvashat.
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. Válassza ki azt az **Azure-előfizetést,** amely alatt létre szeretné hozni az ADF-et. 
5. Az **Erőforráscsoportban** hajtsa végre a következő lépések egyikét:
     
   - Válassza **a Meglévő használata**lehetőséget, és válasszon ki egy meglévő erőforráscsoportot a legördülő listából. 
   - Válassza **az Új létrehozása**lehetőséget, és adja meg az új erőforráscsoport nevét.   
         
   Az erőforráscsoportokról az [Erőforráscsoportok használata az Azure-erőforrások kezelése](../azure-resource-manager/management/overview.md) című témakörben olvashat.
   
6. Verzió **esetén**válassza a **V2** lehetőséget.
7. A **Hely listában**válassza ki az ADF létrehozásához támogatott helyek egyikét a legördülő listából.
8. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
9. Kattintson **a Létrehozás gombra.**
10. Az Azure irányítópultján a következő csempe jelenik meg állapottal: **Data Factory telepítése.** 

    ![adat-előállító üzembe helyezése csempe](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. A létrehozás befejezése után láthatja az ADF-oldalt az alábbiak szerint.
   
    ![Data factory kezdőlap](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. Kattintson **a &-figyelő szerkesztése** elemre az ADF felhasználói felületének/alkalmazásának külön lapon való elindításához.

### <a name="create-your-pipelines"></a>A folyamatok létrehozása

1. A **Let's get started (Kezdjük)** lapon válassza a **Folyamat létrehozása lehetőséget.** 

   ![Első lépések lap](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
   
2. A **Tevékenységek** eszközkészletben bontsa ki az **Általános** menüt, és húzza & **a webes** tevékenységet a folyamattervező felületére. A tevékenységtulajdonságok ablak **Általános** lapján módosítsa a tevékenység nevét **startMyIR**kezdetre. Váltson a **Beállítások** lapra, és hajtsa végre a következő műveleteket.

    1. Url-cím esetén adja meg a rest API következő URL-címét, `{factoryName}`amely `{integrationRuntimeName}` elindítja az Azure-SSIS IR, **URL**a `{subscriptionId}`, `{resourceGroupName}`a , és az infravörös rendszer tényleges értékeit: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01` Másik lehetőségként másolhatja & beillesztheti az infravörös erőforrásazonosítót az ADF felhasználói felületén/alkalmazásában a figyelési lapról a fenti URL-cím következő részének lecseréléséhez:`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
       ![ADF SSIS ir erőforrás azonosítója](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. **A Metódus csoportban**válassza a **POST**lehetőséget. 
    3. A **Törzs** `{"message":"Start my IR"}`mezőbe írja be a mezőbe a . 
    4. **Hitelesítéshez**válassza az **MSI** lehetőséget az ADF felügyelt identitásának használatához, további információa [felügyelt identitás a Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) cikkben.
    5. Az **Erőforrás** `https://management.azure.com/`mezőbe írja be a értéket.
    
       ![ADF webes tevékenységütemezés ssis ir](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. Klónozza az első folyamatot egy második létrehozásához, módosítsa a tevékenység nevét **stopMyIR-re,** és cserélje le a következő tulajdonságokat.

    1. Url-cím esetén adja meg a REST API következő URL-címét, `{factoryName}`amely `{integrationRuntimeName}` leállítja az Azure-SSIS ir-t, a **URL** `{subscriptionId}`, `{resourceGroupName}`a , a , és az infravörös adatok tényleges értékeit:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`
    
    2. A **Törzs** `{"message":"Stop my IR"}`mezőbe írja be a mezőbe a . 

4. Hozzon létre egy harmadik folyamatot, húzza & az **SSIS-csomag végrehajtása tevékenységet** a **Tevékenységek** eszközkészletből a folyamattervező felületére, és konfigurálja azt az [SSIS-csomag meghívása az ADF-cikk SSIS-csomagvégrehajtásával című utasításainak](how-to-invoke-ssis-package-ssis-activity.md) megfelelően.  Azt is megteheti, hogy a **tárolt eljárás** tevékenység helyett, és konfigurálja azt az utasításokat [követve Az SSIS-csomag meghívása tárolt eljárás tevékenység ADF cikkben.](how-to-invoke-ssis-package-stored-procedure-activity.md)  Ezután láncolja az SSIS-csomag/tárolt eljárás végrehajtása tevékenységet két olyan webes tevékenység között, amelyek elindítják/leállítják az infravörös kapcsolatát, hasonlóan az első/második folyamatokban lévő webes tevékenységekhez.

   ![ADF webes tevékenység igény szerinti SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. Rendeljen hozzá az ADF felügyelt identitását egy **közreműködői** szerepkörön saját magához, így a folyamataiban lévő webes tevékenységek meghívhatják a REST API-t az Azure-SSIS-i beosztott a benne kiosztott indításhoz/leállításához.  Az Azure Portalon az ADF-lapon kattintson **a Hozzáférés-vezérlés (IAM)** elemre, kattintson **a + Szerepkör-hozzárendelés hozzáadása**gombra, majd a **Szerepkör-hozzárendelés hozzáadása** panelen végezze el a következő műveleteket.

    1. A **Role (Szerepkör) területen**válassza a **Közreműködő**lehetőséget. 
    2. A **Hozzáférés hozzárendelése a területen**válassza az Azure **AD felhasználó, csoport vagy egyszerű szolgáltatás**lehetőséget. 
    3. A **Select területen**keresse meg az ADF-nevet, és jelölje ki. 
    4. Kattintson a **Save** (Mentés) gombra.
    
   ![ADF felügyelt identitásszerepkör-hozzárendelés](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. Ellenőrizze az ADF-et és az összes folyamatbeállítást a **gyári/folyamateszköztár Összes érvényesítése/Érvényesítés** elemre kattintva. A **gyári/csővezeték-érvényesítési kimenet bezárása** a gombra kattintva. **>>**  

   ![Folyamat érvényesítése](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>A folyamatok tesztelése

1. Válassza az egyes folyamatok eszköztárának **Tesztfuttatás** parancsát, és az alsó ablak **kimeneti** ablaka jelenik meg. 

   ![Próbaüzem](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. A harmadik folyamat teszteléséhez indítsa el az SQL Server Management Studio (SSMS) programot. A **Csatlakozás kiszolgálóhoz** ablakban végezze el a következő műveleteket. 

    1. A **Kiszolgálónév mezőbe**írja be ** &lt;&gt;az Azure SQL Database-kiszolgáló nevét .database.windows.net**.
    2. Válassza **a Beállítások >>** lehetőséget.
    3. Az **adatbázishoz való csatlakozás**kor válassza az **SSISDB**lehetőséget.
    4. Kattintson a **Csatlakozás** gombra. 
    5.  -> Bontsa ki az**SSISDB** -> A mappa ->-> Az SSIS projekt -> csomagok **Integration Services Catalogs** **Projects** kibontása. **Packages** 
    6. A futtatáshoz kattintson a jobb gombbal a megadott SSIS-csomagra, és válassza **a Jelentések** -> **szabványos jelentései** -> **minden végrehajtást**lehetőséget. 
    7. Ellenőrizze, hogy futott-e. 

   ![Az SSIS-csomag futtatásának ellenőrzése](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>A folyamatok ütemezése

Most, hogy a folyamatok a várt módon működnek, létrehozhat eseményindítókat a megadott ütemben való futtatásukhoz. Az eseményindítók és a folyamatok társításáról a [folyamat aktiválása ütemezési cikkben című](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule) témakörben olvashat részletesen.

1. A folyamat eszköztárán válassza az **Eseményindító** lehetőséget, és válassza az **Új/Szerkesztés lehetőséget.** 

   ![Eseményindító -> Új/Szerkesztés](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. Az **Eseményindítók hozzáadása** ablaktáblában válassza **a + Új**lehetőséget.

   ![Eseményindítók hozzáadása - Új](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. Az **Új eseményindító** ablaktáblában tegye a következő műveleteket: 

    1. A **Név mezőbe**írja be az eseményindító nevét. A következő példában a **Futtatás napi** az eseményindító neve. 
    2. A **Típus mezőben**válassza az **Ütemezés**lehetőséget. 
    3. A **Kezdő dátum (UTC)** mezőbe írja be a kezdő dátumot és az időt UTC értékben. 
    4. Az **Ismétlődés**mezőbe írja be az eseményindító pedálfordulatát. A következő példában a **Napi** egyszer látható. 
    5. A **Befejezés csoportban**válassza a **Nincs befejezés** lehetőséget, vagy adja meg a befejezési dátumot és az időt a **Dátum**kiválasztása után. 
    6. Válassza **az Aktiválva** lehetőséget, ha az eseményindítót a teljes ADF-beállítások közzététele után azonnal aktiválhatja. 
    7. Kattintson a **Tovább** gombra.

   ![Eseményindító -> Új/Szerkesztés](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. Az **Eseményindító futtatási paramétereinek** lapon tekintse át a figyelmeztetéseket, és válassza a **Befejezés gombot.** 
5. Tegye közzé a teljes ADF-beállításokat a gyári eszköztár **Összes ének közzététele** parancsával. 

   ![Az összes közzététele](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>A folyamatok és az eseményindítók figyelése az Azure Portalon

1. Az eseményindítók és a folyamatfuttatások figyeléséhez használja a **Figyelő** lapot az ADF felhasználói felületének/alkalmazásának bal oldalán. A részletes lépésekért lásd: A csővezeték cikk [figyelése.](quickstart-create-data-factory-portal.md#monitor-the-pipeline)

   ![Folyamatfuttatások](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. A folyamatfuttatáshoz társított tevékenységfuttatások megtekintéséhez jelölje ki az első kapcsolatot (**Tevékenységfuttatások megtekintése**) a **Műveletek** oszlopban. A harmadik folyamat, látni fogja, három tevékenység fut, egy-egy láncolt tevékenység a folyamatban (webes tevékenység elindításához az infravörös, tárolt eljárás tevékenység a csomag végrehajtásához, és a webes tevékenység leállítása az infravörös). A folyamat futtatásának ismételt megtekintéséhez válassza a **folyamatkapcsolatok** közötti kapcsolatot a tetején.

   ![Tevékenységfuttatások](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. Az eseményindító-futtatások megtekintéséhez válassza az **Eseményindító-futtatások** elemet a **folyamatfuttatások legördülő** listájából a felül. 

   ![Eseményindító-futtatások](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>A folyamatok és az eseményindítók figyelése a PowerShell segítségével

Parancsfájlok használatával, mint például az alábbi példákat a folyamatok és az eseményindítók figyeléséhez.

1. A folyamatfuttatás állapotának beírása.

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. Információ az eseményindítóról.

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. Az eseményindító-futtatás állapotának leminősítése.

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Egy Azure-SSIS IR-t elindító vagy leállító Azure Automation-runbook létrehozása és ütemezése

Ebben a szakaszban megtudhatja, hogy hozzon létre Azure Automation runbook, amely végrehajtja a PowerShell-parancsfájlt, az Azure-SSIS-ir indítása/leállítása ütemezés szerint.  Ez akkor hasznos, ha további parancsfájlokat szeretne végrehajtani az infravörös elő-/utófeldolgozás előtti/leállítása előtt/után.

### <a name="create-your-azure-automation-account"></a>Az Azure Automation-fiók létrehozása

Ha még nem rendelkezik Azure Automation-fiókkal, hozzon létre egyet az ebben a lépésben található utasításokat követve. Részletes lépések: [Azure Automation-fiók létrehozása](../automation/automation-quickstart-create-account.md) cikk. Ennek a lépésnek a részeként hozzon létre egy **Azure Run As** fiókot (az Azure Active Directoryban egy egyszerű szolgáltatást), és hozzárendeli azt egy **közreműködői** szerepkört az Azure-előfizetésben. Győződjön meg arról, hogy ugyanaz az előfizetés, amely tartalmazza az ADF-et az Azure SSIS IR-vel. Az Azure Automation ezt a fiókot használja az Azure Resource Manager hitelesítéséhez és az erőforrások működtetéséhez. 

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. Jelenleg az ADF felhasználói felületét/alkalmazását csak a Microsoft Edge és a Google Chrome böngészők támogatják.
2. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).    
3. Válassza a bal oldali menü **Új** parancsát, válassza a **Figyelés + Kezelés**lehetőséget, majd az **Automatizálás**lehetőséget. 

   ![Új -> Monitoring + Management -> Automation](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. Az **Automation-fiók hozzáadása** ablaktáblában végezze el az alábbi műveleteket.

    1. A **név**mezőbe írja be az Azure Automation-fiók nevét. 
    2. **Előfizetés esetén**válassza ki azt az előfizetést, amely rendelkezik az ADF-tel az Azure-SSIS IR-vel. 
    3. Az **Erőforráscsoport csoportban**válassza **az Új létrehozása** lehetőséget új erőforráscsoport létrehozásához, vagy a Meglévő **használata** egy meglévő kijelöléséhez lehetőséget. 
    4. A **Hely**lehetőséget válassza ki az Azure Automation-fiók helyét. 
    5. Erősítse meg az Azure Run létrehozása fiókként **igenként**című **fiókot.** Az Azure Active Directoryban létrejön egy egyszerű szolgáltatás, amely az Azure-előfizetésben **közreműködői** szerepkörhöz rendel.
    6. Válassza **a Rögzítés az irányítópultra** lehetőséget, ha véglegesen meg szeretné jeleníteni az Azure irányítópultján. 
    7. Kattintson a **Létrehozás** gombra. 

   ![Új -> Monitoring + Management -> Automation](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. Az Azure Automation-fiók üzembe helyezési állapotát az Azure irányítópultján és az értesítésekben fogja látni. 
    
   ![Automatizálás telepítése](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. Az Azure Automation-fiók kezdőlapja a sikeres létrehozása után jelenik meg. 

   ![Automatizálás kezdőlapja](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>ADF-modulok importálása

1. Válassza a bal oldali menü **Megosztott erőforrások** **szakaszának Modulok elemét,** és ellenőrizze, hogy az **Az.DataFactory** + **Az.Profile** szerepel-e a modulok listájában.

   ![A szükséges modulok ellenőrzése](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Ha nem rendelkezik **az Az.DataFactory**szolgáltatással, nyissa meg az [Az.DataFactory](https://www.powershellgallery.com/packages/Az.DataFactory/)PowerShell-galériában , válassza **a Deploy to Azure Automation (Üzembe helyezés az Azure Automation-szolgáltatásba)** lehetőséget, válassza ki az Azure Automation-fiókot, majd kattintson az **OK gombra.** Lépjen vissza a bal oldali menü **MEGOSZTOTT ERŐFORRÁSOK szakaszában** található **modulok** megtekintéséhez, és várja meg, amíg az **Az.DataFactory** modul **állapota** **Elérhetőre változik.**

    ![A Data Factory modul ellenőrzése](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Ha nem rendelkezik **az Az.Profile szolgáltatással,** nyissa meg a PowerShell-katalógus az [Az.Profile modult,](https://www.powershellgallery.com/packages/Az.profile/)válassza **az Üzembe helyezés az Azure Automation-be**lehetőséget, válassza ki az Azure Automation-fiókot, majd kattintson az **OK gombra.** Lépjen vissza a bal oldali menü **MEGOSZTOTT ERŐFORRÁSOK szakaszában** található **modulok** megtekintéséhez, és várja meg, amíg az **Az.Profile** modul **ÁLLAPOTA** Elérhető re **változik.**

    ![A Profil modul ellenőrzése](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>A PowerShell-runbook létrehozása

A következő szakasz a PowerShell-runbookok létrehozásának lépéseit ismerteti. A runbookhoz társított parancsfájl vagy elindítja/leállítja az Azure-SSIS IR-t a **MŰVELET** paraméterhez megadott parancs alapján. Ez a szakasz nem tartalmazza a teljes részleteket a runbook oka. További információ: [Runbook-cikk létrehozása.](../automation/automation-quickstart-create-runbook.md)

1. Váltson a **Runbooks** lapra, és válassza a **+ Runbook hozzáadása** lehetőséget az eszköztárról. 

   ![Runbook hozzáadása gomb](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. Válassza **az Új runbook létrehozása lehetőséget,** és tegye a következő műveleteket: 

    1. A **Név mezőbe**írja be a **StartStopAzureSsisRuntime**értéket.
    2. **Runbook-típus esetén**válassza a **PowerShell**lehetőséget.
    3. Kattintson a **Létrehozás** gombra.
    
   ![Runbook hozzáadása gomb](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. Másolja & illessze be a következő PowerShell-parancsfájlt a runbook-parancsfájl ablakába. Mentse, majd tegye közzé a runbookot az eszköztár **Mentés** és **közzététel** gombjaival. 

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
    
4. Tesztelje a runbookot az eszköztár **Start** gombjára kattintva. 

   ![Runbook indítása gomb](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. A **Runbook indítása** ablaktáblán tegye a következő műveleteket: 

    1. Az **ERŐFORRÁS-CSOPORT NEVE**mezőbe írja be annak az erőforráscsoportnak a nevét, amely az ADF-et az Azure-SSIS IR-vel rendelkezik. 
    2. A **DATA FACTORY NAME**mezőbe írja be az ADF nevét az Azure-SSIS IR segítségével. 
    3. AZ **AZURESSISNAME**mezőbe írja be az Azure-SSIS IR nevét. 
    4. A **MŰVELET**mezőbe írja be a **START értéket.** 
    5. Válassza **az OK gombot.**  

   ![Runbook-ablak indítása](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. A feladatablakban válassza a **Kimenet** csempe lehetőséget. A kimeneti ablakban várja meg, amíg a **##### befejeződött #####** üzenet megjelenik, miután **megjelenik a ##### kezdő #####**. Az Azure-SSIS ir indítása körülbelül 20 percet vesz igénybe. Zárja be **a Feladat** ablakot, és tértem vissza a **Runbook** ablakba.

   ![Azure SSIS IR – indítás](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. Ismételje meg az előző két lépést a **STOP** értékkel az **OPERATION**értékként. Indítsa el újra a runbookot az eszköztár **Start** gombjának kiválasztásával. Adja meg az erőforráscsoportot, az ADF- és az Azure-SSIS infravörös neveket. Az **OPERATION**mezőbe írja be a **STOP**értéket. A kimeneti ablakban várja meg, amíg a **##### befejeződött #####** üzenet megjelenik, miután **megjelenik ##### megállás #####**. Az Azure-SSIS ir leállítása nem tart olyan sokáig, mint az indítás. Zárja be **a Feladat** ablakot, és tértem vissza a **Runbook** ablakba.

8. A runbookot egy webhookon keresztül is elindíthatja, amely a **Webhooks** menüelem kiválasztásával vagy az alábbiakban megadott **Ütemezések** menüelem kiválasztásával hozható létre.  

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Ütemezések létrehozása a runbook hoz létre az Azure-SSIS IR elindításához/leállításához

Az előző szakaszban létrehozta az Azure Automation-runbookot, amely elindíthatja vagy leállíthatja az Azure-SSIS IR-t. Ebben a szakaszban két ütemezést hoz létre a runbookhoz. Az első ütemezés konfigurálásakor adja meg a **START** értéket az **OPERATION**művelethez. Hasonlóképpen, a második konfigurálásakor a **STOP** értéket adja meg az **OPERATION**értékhez. Az ütemezések létrehozásának részletes lépéseit az [Ütemezési](../automation/shared-resources/schedules.md#creating-a-schedule) cikk létrehozása című témakörben olvashat.

1. A **Runbook** ablakban válassza **az Ütemezések**lehetőséget, majd az **eszköztáron** a + Ütemezés hozzáadása lehetőséget. 

   ![Azure SSIS IR – indítás](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. A **Runbook ütemezése** ablaktáblában tegye a következő műveleteket: 

    1. Válassza **az Ütemezés csatolása a runbookhoz**lehetőséget. 
    2. Válassza **az Új ütemezés létrehozása**lehetőséget.
    3. Az **Új ütemezés** ablaktáblában adja meg naponta a Kezdő infravörös **értéket** a **Név mezőbe.** 
    4. Az **Indítás mezőbe**írja be az aktuális időnél néhány perccel korábbi időpontot. 
    5. Az **Ismétlődés (Ismétlődés)** területen válassza **az Ismétlődő**lehetőséget. 
    6. A **Recur every**mezőbe írja be **az 1** értéket, és válassza a **Nap**lehetőséget. 
    7. Kattintson a **Létrehozás** gombra. 

   ![Az Azure SSIS infravörös indításának ütemezése](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. Váltson **a Paraméterek lapra, és futtassa** a beállításokat. A **MŰVELET**mezőbe írja be a **START parancsot,** és válassza az OK **gombot.** Válassza **az OK gombot** ismét az ütemezés a runbook **ütemezési** lapján. 

   ![Az Azure SSIS IR bámulásának ütemezése](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. Ismételje meg az előző két lépést a **Stop IR naponta**nevű ütemezés létrehozásához. Adja meg azt az időpontot, amely legalább 30 perccel a **kezdési infravörös napi** ütemezéshez megadott időpont után van. A **MŰVELET**mezőbe írja be a **STOP parancsot,** és válassza az OK **gombot.** Válassza **az OK gombot** ismét az ütemezés a runbook **ütemezési** lapján. 

5. A **Runbook** ablakban válassza a bal oldali menü **Feladatok** parancsát. Meg kell jelennie az ütemezések által létrehozott feladatoknak a megadott időpontokban és állapotukban. Láthatja a feladat részleteit, például a kimenetét, hasonlóan ahhoz, amit a runbook tesztelése után látott. 

   ![Az Azure SSIS IR bámulásának ütemezése](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. Miután végzett a teszteléssel, tiltsa le az ütemezést szerkesztéssel. Válassza a bal oldali menü **Ütemezések** parancsát, válassza az **Infravörös indítás napi/leállítása naponta**lehetőséget, és válassza a **Nem** lehetőséget **az Engedélyezve parancsra.** 

## <a name="next-steps"></a>További lépések
Lásd a következő blogbejegyzést:
-   [Az ETL/ELT munkafolyamatok korszerűsítése és bővítése SSIS-tevékenységekkel az ADF-folyamatokban](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)

Lásd az SSIS dokumentációjának alábbi cikkeit: 

- [SSIS-csomag üzembe helyezése, futtatása és monitorozása az Azure-on](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Csatlakozás SSIS-katalógushoz Azure-on](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Csomag végrehajtásának ütemezése az Azure-on](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Csatlakozás helyszíni adatforrásokhoz Windows-hitelesítéssel](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
