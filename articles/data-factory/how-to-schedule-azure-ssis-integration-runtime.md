---
title: Az Azure-SSIS integrációs modul ütemezése |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan indítása és leállítása az Azure SSIS integrációs modul az Azure Automation és a Data Factory használatával ütemezheti.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/16/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 7e93e83f7594d30d223d37454e09943beba6d3ce
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2018
ms.locfileid: "51976730"
---
# <a name="how-to-start-and-stop-the-azure-ssis-integration-runtime-on-a-schedule"></a>Elindítása és leállítása az Azure-SSIS integrációs modul egy ütemezés szerint
Ez a cikk ismerteti a indítása és leállítása az Azure SSIS integrációs modul (IR) az Azure Automation és az Azure Data Factory használatával ütemezheti. Fut (SQL Server Integration Services) Azure-SSIS integrációs modul (IR) van társítva költsége. Ezért általában szeretné futtatni az integrációs modul csak akkor, amikor szüksége van az SSIS-csomagok futtatása az Azure-ban, és az integrációs modul leállításához, ha már nincs szükség. Használhatja a Data Factory felhasználói felületén vagy az Azure PowerShell-lel [manuális elindításához, vagy egy Azure SSIS integrációs modul leállításához](manage-azure-ssis-integration-runtime.md)).

Például hozzon létre egy Azure Automation PowerShell-runbook webhookok webes tevékenység, és a közöttük SSIS-csomag végrehajtása tevékenység hozzákapcsolva. A webes tevékenység elindíthatja és leállíthatja az Azure-SSIS integrációs modul csak az idő előtt, miután a csomag fut. Az SSIS-csomag végrehajtása tevékenységgel kapcsolatos további információkért lásd: [egy, az SSIS-tevékenység használatával az Azure Data Factory SSIS-csomag futtatása](how-to-invoke-ssis-package-ssis-activity.md).

## <a name="overview-of-the-steps"></a>A lépések áttekintése

Ebben a cikkben leírt magas szintű lépései a következők:

1. **Hozzon létre, és a egy Azure Automation-runbook tesztelése.** Ebben a lépésben a PowerShell-runbook létrehozása a parancsfájl, amely elindítja vagy leállítja egy Azure SSIS integrációs modult. Ezután a runbook indítása és LEÁLLÍTÁSA is forgatókönyvekben tesztelése, és győződjön meg arról, hogy az integrációs modul elindítja vagy leállítja. 
2. **Hozzon létre két ütemezések a runbookhoz.** Az első ütemezés konfigurálnia a runbook indítása, a műveletet. A második ütemezés konfigurálhatja a runbook LEÁLLÍTÁSA, a műveletet. Mindkét az ütemezések esetében adja meg a kiadása, amelyen a runbook futtatása ütemben történik. Például előfordulhat, hogy szeretné ütemezni a 8-kor fut, minden nap, a másodikat futtatását a mindennapi 23 óra első alkalommal. Az első runbook fut, amikor elindítja az Azure-SSIS integrációs modult. A második runbook fut, leállítja az Azure-SSIS integrációs modult. 
3. **Hozzon létre két webhookok a runbook**, egyet a KEZDŐ művelet és a másik pedig a LEÁLLÍTÁSI műveletet. Webes tevékenységek a Data Factory-folyamatok konfigurálásakor használhatja ezeket a webhook URL-címei. 
4. **Hozzon létre egy Data Factory-folyamatot**. A létrehozott folyamat három tevékenység áll. Az első **webes** tevékenység indítása az Azure-SSIS integrációs modult. az első webhook hív meg. A **tárolt eljárás** tevékenység fut egy SQL-szkript, amelyen az SSIS-csomag. A második **webes** tevékenység leállítja az Azure-SSIS integrációs modult. Az SSIS-csomag a Data Factory-folyamatok meghívása a tárolt eljárási tevékenység használatával kapcsolatos további információkért lásd: [egy SSIS-csomagok meghívásához](how-to-invoke-ssis-package-stored-procedure-activity.md). Ezután hozzon létre egy ütemezési eseményindítót, a folyamat futtatásához adja meg, hogy ütemben ütemezéséhez.

## <a name="prerequisites"></a>Előfeltételek
Ha már egy Azure SSIS integrációs modul még nincs kiépítve, építi ki található utasításokat követve a [oktatóanyag](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-test-an-azure-automation-runbook"></a>Hozzon létre, és a egy Azure Automation-runbook tesztelése
Ebben a szakaszban az alábbi lépéseket fogja végrehajtani: 

1. Hozzon létre egy Azure Automation-fiókot.
2. PowerShell-runbook létrehozása az Azure Automation-fiókban. A PowerShell-parancsfájlt a runbookhoz társított elindul vagy leáll egy Azure SSIS integrációs modul a parancsot, adja meg, ha a művelet paraméter alapján. 
3. A runbook tesztelése a egyaránt a Start menüben, és állítsa le a forgatókönyvek annak ellenőrzéséhez, hogy működik-e. 

### <a name="create-an-azure-automation-account"></a>Azure Automation-fiók létrehozása
Ha egy Azure Automation-fiók nem rendelkezik, hozzon létre egyet, ez a lépés utasításait követve. Részletes lépéseiért lásd: [hozzon létre egy Azure Automation-fiók](../automation/automation-quickstart-create-account.md). Ez a lépés részeként létrehozott egy **Azure-beli futtató** -fiókja (egyszerű szolgáltatásnevének az Azure Active Directory), majd adja hozzá a az **közreműködői** szerepe az Azure-előfizetés. Győződjön meg arról, hogy legyen ugyanaz, mint az előfizetés, amely tartalmazza az adat-előállítót, amely rendelkezik az Azure-SSIS integrációs modult. Az Azure Automation ezt a fiókot használja a hitelesítéshez az Azure Resource Manager és az erőforrások üzemeltetéséhez. 

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
2. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).    
3. Válassza ki **új** a bal oldali menüben válassza ki a **Monitoring + Management**, és válassza ki **Automation**. 

    ![Új -> Monitoring + Management Automation ->](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
2. Az a **Automation-fiók hozzáadása** ablakban tegye a következőket: 

    1. Adjon meg egy **neve** az automation-fiókhoz. 
    2. Válassza ki a **előfizetés** , amely rendelkezik az adat-előállító az Azure-SSIS integrációs modult. 
    3. A **erőforráscsoport**válassza **új létrehozása** hozzon létre egy új erőforráscsoportot, vagy válasszon **meglévő** , válasszon ki egy meglévő erőforráscsoportot. 
    4. Válassza ki a **hely** az automation-fiókhoz. 
    5. Ellenőrizze, hogy **futtató fiók létrehozása** értékre van állítva **Igen**. Egyszerű szolgáltatás létrehozása az Azure Active Directoryban. Megjelenik a **közreműködői** szerepe az Azure-előfizetés
    6. Válassza ki **rögzítés az irányítópulton** , hogy a portál irányítópultján láthatja. 
    7. Kattintson a **Létrehozás** gombra. 

        ![Új -> Monitoring + Management Automation ->](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
3. Megjelenik a **központi telepítési állapot** az irányítópulton, és az értesítésekben. 
    
    ![Automation üzembe helyezése](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
4. A kezdőlapon az automation-fiók sikeres létrehozása után láthatja. 

    ![Automation-kezdőlap](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-data-factory-modules"></a>A Data Factory-modulok importálása

1. Válassza ki **modulok** a a **megosztott erőforrások** szakaszt a bal oldali menüben, és ellenőrizze, hogy rendelkezik **AzureRM.Profile** és **AzureRM.DataFactoryV2** modulok listájában.

    ![Ellenőrizze a szükséges modulok](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Nyissa meg a PowerShell-galériájában a [AzureRM.DataFactoryV2 modul](https://www.powershellgallery.com/packages/AzureRM.DataFactoryV2/), jelölje be **üzembe helyezés az Azure Automation**, az Automation-fiókját, majd válassza ki és **OK**. Megtekintéséhez lépjen vissza **modulok** a a **megosztott erőforrások** szakaszt a bal oldali menüben, és várjon, amíg megjelenik a **állapot** , a **AzureRM.DataFactoryV2** modul módosítás **elérhető**.

    ![A Data Factory modul ellenőrzése](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Nyissa meg a PowerShell-galériájában a [AzureRM.Profile modul](https://www.powershellgallery.com/packages/AzureRM.profile/), kattintson a **üzembe helyezés az Azure Automation**, az Automation-fiókját, majd válassza ki és **OK**. Megtekintéséhez lépjen vissza **modulok** a a **megosztott erőforrások** szakaszt a bal oldali menüben, és várjon, amíg megjelenik a **állapot** , a **AzureRM.Profile**modul módosítás **elérhető**.

    ![A profil modul ellenőrzése](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-a-powershell-runbook"></a>PowerShell-runbook létrehozása
Az alábbi eljárás ismerteti a PowerShell-runbook létrehozása. A parancsfájl vagy a runbookhoz társított elindul vagy leáll egy Azure SSIS integrációs modul a megadott parancs alapján a **művelet** paraméter. Ez a szakasz nem biztosít minden runbook létrehozásához. További információkért lásd: [hozzon létre egy runbookot](../automation/automation-quickstart-create-runbook.md) cikk.

1. Váltson a **Runbookok** lapot, majd **+ forgatókönyv hozzáadása** az eszköztáron. 

    ![Runbook gomb hozzáadása](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
2. Válassza ki **hozzon létre egy új runbookot**, és hajtsa végre az alábbi lépéseket: 

    1. A **neve**, típus **StartStopAzureSsisRuntime**.
    2. A **Runbook típusa**válassza **PowerShell**.
    3. Kattintson a **Létrehozás** gombra.
    
        ![Runbook gomb hozzáadása](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
3. Másolja és illessze be a következő szkriptet a runbook parancsfájl ablakot. Mentse és tegye közzé a runbook használatával az **mentése** és **közzététel** az eszköztárban található gombokra. 

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
        Connect-AzureRmAccount `
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
        Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

    ![PowerShell-alapú runbook szerkesztése](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
5. A runbook tesztelése kiválasztásával **Start** gombra az eszköztáron. 

    ![Indítsa el a runbook gomb](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
6. Az a **Runbook indítása** ablakban hajtsa végre az alábbi lépéseket: 

    1. A **ERŐFORRÁSCSOPORT-név**, adja meg az erőforráscsoportot, amely rendelkezik az Azure-SSIS integrációs modult. az adat-előállító nevét 
    2. A **adat-előállító nevét**, adja meg a nevét, a data Factory, amely rendelkezik az Azure-SSIS integrációs modult. 
    3. A **AZURESSISNAME**, adja meg a nevét, az Azure-SSIS integrációs modult. 
    4. A **művelet**, adja meg **START**. 
    5. Kattintson az **OK** gombra.  

        ![Indítsa el a runbook ablak](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
7. A feladat ablakban válassza ki a **kimeneti** csempére. A kimeneti ablakban, a feladat, várjon, amíg megjelenik az üzenet **### befejezve ###** , miután látta, **### kezdő ###**. Egy Azure SSIS integrációs modul indítása körülbelül 20 percet vesz igénybe. Zárja be a **feladat** ablakot, és kollégáink felveszik az **Runbook** ablak.

    ![Az Azure SSIS integrációs modul – elindítva](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
8.  Ismételje meg az előző két lépést, de használatával **LEÁLLÍTÁSA** értékeként a **művelet**. Indítsa újra a runbook kiválasztásával a **Start** gombra az eszköztáron. Adja meg az erőforráscsoport neve, adat-előállító nevét és az Azure SSIS integrációs modul neve. A **művelet**, adja meg **LEÁLLÍTÁSA**. 

    A kimeneti ablakban, a feladat, várja meg, amíg az üzenet jelenik meg **### befejezve ###** , miután látta, **### leállítása ###**. Egy Azure SSIS integrációs modul leállítása nem eltarthat indítása az Azure-SSIS integrációs modult. Zárja be a **feladat** ablakot, és kollégáink felveszik az **Runbook** ablak.

## <a name="create-schedules-for-the-runbook-to-startstop-the-azure-ssis-ir"></a>Ütemezések a runbook indítása és leállítása az Azure-SSIS integrációs modul létrehozása
Az előző szakaszban létrehozott egy Azure Automation-runbook, elindítani vagy leállítani egy Azure SSIS integrációs modult. Ebben a szakaszban két ütemezések a runbook hoz létre. Az első ütemezés konfigurálásakor adja meg KEZDÉSI művelet paraméterhez. Ehhez hasonlóan a második ütemezés konfigurálásakor megadhatja a művelet LEÁLLÍTÁSA. Ütemezések létrehozására vonatkozó részletes lépéseiért lásd: [ütemezés létrehozása](../automation/automation-schedules.md#creating-a-schedule).

1. Az a **Runbook** ablakban válassza **ütemezések**, és válassza ki **+ ütemezés hozzáadása** az eszköztáron. 

    ![Az Azure SSIS integrációs modul – elindítva](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
2. Az a **ütemezés Runbook** ablakban hajtsa végre az alábbi lépéseket: 

    1. Válassza ki **összekapcsolhat egy ütemezést a runbook**. 
    2. Válassza ki **új ütemezés létrehozása**.
    3. Az a **új ütemezés** ablakot, írja be **indítsa el integrációs modul naponta** a **neve**. 
    4. Az a **szakasz kezdődik**, elindításakor néhány perccel az aktuális idő időpontnak a megadása. 
    5. A **ismétlődési**válassza **ismétlődő**. 
    6. Az a **Ismétlődés minden** szakaszban jelölje be **nap**. 
    7. Kattintson a **Létrehozás** gombra. 

        ![Azure SSIS integrációs modul indítása ütemezését](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
3. Váltson a **paraméterek és futtatási beállítások** fülre. Adja meg az erőforráscsoport neve, adat-előállító nevét és az Azure SSIS integrációs modul neve. A **művelet**, adja meg **START**. Kattintson az **OK** gombra. Válassza ki **OK** újra, tekintse meg az ütemezést a **ütemezések** lap a runbook. 

    ![Indítás előtt az Azure-SSIS integrációs modul ütemezése](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
4. Ismételje az előző két lépést nevű ütemezés létrehozása **IR leállítása naponta**. Ebben az esetben adja meg a megadott időpontban legalább 30 perccel későbbinek a **indítsa el integrációs modul naponta** ütemezés. A **művelet**, adja meg **LEÁLLÍTÁSA**. 
5. Az a **Runbook** ablakban válassza **feladatok** a bal oldali menüben. A megadott ütemezés szerint létrehozott feladatok kell megjelennie időpontokat, és azok állapotát. A feladat kimenetét hasonlóan mi láthatta, hogy a runbook tesztelésekor például részleteit láthatja. 

    ![Indítás előtt az Azure-SSIS integrációs modul ütemezése](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
6. Miután végzett tesztelést, tiltsa le az ütemezéseket szerkeszti őket, és válassza **nem** a **engedélyezve**. Válassza ki **ütemezések** a bal oldali menüben válassza ki a **integrációs modul elindítása napi és leállítása integrációs modul naponta**, és válassza ki **nem** a **engedélyezve**. 

## <a name="create-webhooks-to-start-and-stop-the-azure-ssis-ir"></a>Webhookok indítása és leállítása az Azure-SSIS integrációs modul létrehozása
Kövesse az utasításokat [hozzon létre egy webhookot](../automation/automation-webhooks.md#creating-a-webhook) két webhookok a runbook létrehozásához. Az első címtárra adja meg a KEZDŐ művelet és a másodikat megadása LEÁLLÍTÁSA, a művelet. Mentse mindkét helyre (például egy szövegfájlt vagy OneNote-jegyzetfüzet) a webhook URL-címeit. Webes tevékenység konfigurálásakor a Data Factory-folyamatot az alábbi URL-címek használhatja. Az alábbi képen látható egy példa egy webhookot, amely elindítja az Azure-SSIS integrációs modul létrehozása:

1. Az a **Runbook** ablakban válassza **Webhookok** a bal oldali menüben, és válassza a **+ Webhook hozzáadása** az eszköztáron. 

    ![Webhookok -> Webhook hozzáadása](./media/how-to-schedule-azure-ssis-integration-runtime/add-web-hook-menu.png)
2. Az a **Webhook hozzáadása** ablakban válassza **új webhook létrehozása**, és hajtsa végre a következőket: 

    1. A **neve**, adja meg **StartAzureSsisIR**. 
    2. Ellenőrizze, hogy **engedélyezve** értékre van állítva **Igen**. 
    3. Másolás a **URL-cím** helyre mentse, és. Ez a lépés nem fontos. Az URL-cím később nem látható. 
    4. Kattintson az **OK** gombra. 

        ![Új Webhook ablak](./media/how-to-schedule-azure-ssis-integration-runtime/new-web-hook-window.png)
3. Váltson a **paraméterek és futtatási beállítások** fülre. Adja meg az erőforráscsoport neve, az adat-előállító nevét és az Azure SSIS integrációs modul neve. A **művelet**, adja meg **START**. Kattintson az **OK** gombra. Ezt követően kattintson a **Create** (Létrehozás) gombra. 

    ![Webhook - paraméterek és futtatási beállítások](./media/how-to-schedule-azure-ssis-integration-runtime/webhook-parameters.png)
4. Ismételje meg az előző három lépést egy másik nevű webhook létrehozása **StopAzureSsisIR**. Ne felejtse el másolja az URL-címet. A paraméterek és futtatási beállítások megadásakor adja meg a **LEÁLLÍTÁSA** a **művelet**. 

Rendelkeznie kell két URL-címet, egyet-egyet a **StartAzureSsisIR** webhookot, a másik pedig a **StopAzureSsisIR** webhook. HTTP POST-kérelmet lehet küldeni indítása és leállítása az Azure-SSIS integrációs modult. az alábbi URL-címek 

## <a name="create-and-schedule-a-data-factory-pipeline-that-startsstops-the-ir"></a>Hozzon létre, és a egy Data Factory-folyamatot, amely elindítja és leállítja az integrációs modul ütemezése
Ez a szakasz bemutatja, hogyan meghívása a webhookok, az előző szakaszban létrehozott webes tevékenység használatával.

A létrehozott folyamat három tevékenység áll. 

1. Az első **webes** tevékenység indítása az Azure-SSIS integrációs modult. az első webhook hív meg. 
2. A **SSIS-csomag végrehajtása** tevékenység vagy a **tárolt eljárás** tevékenységfuttatások az SSIS-csomag.
3. A második **webes** tevékenység meghívja a webhookot, állítsa le az Azure-SSIS integrációs modult. 

Miután hoz létre, és a folyamat teszteléséhez, ütemezési eseményindító létrehozása, és rendelje hozzá a folyamat. Az ütemezési eseményindító egy ütemezés a folyamat határozza meg. Tegyük fel, létrehozhat egy eseményindítót, amely van ütemezve napi 23 óra. Az eseményindító minden nap 11-kor futtatja a folyamatot. A folyamat elindul az Azure-SSIS integrációs modul, az SSIS-csomag végrehajtása leáll, és ezután az Azure-SSIS integrációs modult. 

### <a name="create-a-data-factory"></a>Data factory létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).    
2. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. Az **Új data factory** lapon, a **Név** mezőben adja meg a következőt: **MyAzureSsisDataFactory**. 
      
     ![Új adat-előállító lap](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Az Azure data factory nevének **globálisan egyedinek** kell lennie. Ha a következő hibaüzenetet kapja, változtassa meg az adat-előállító nevét (például sajátneveMyAzureSsisDataFactoryra), majd próbálkozzon újra a létrehozással. A Data Factory-összetevők elnevezésére vonatkozó részleteket a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
       `Data factory name �MyAzureSsisDataFactory� is not available`
3. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
4. Az **erőforráscsoportban** hajtsa végre a következő lépések egyikét:
     
      - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot. 
      - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.   
         
      Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
4. A **Verzió** résznél válassza a **V2** értéket.
5. Válassza ki a Data Factory **helyét**. A listában csak az adat-előállítók létrehozását támogató helyek jelennek meg.
6. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
7. Kattintson a **Create** (Létrehozás) gombra.
8. Az irányítópulton megjelenő csempén a következő állapotleírás látható: **Adat-előállító üzembe helyezése**. 

    ![adat-előállító üzembe helyezése csempe](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
   ![Data factory kezdőlap](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. A Data Factory felhasználói felületének (UI) külön lapon történő elindításához kattintson a **Létrehozás és monitorozás** csempére.

### <a name="create-a-pipeline"></a>Folyamat létrehozása

1. Az a **Ismerkedés** lapon jelölje be **folyamat létrehozása**. 

   ![Első lépések lap](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
2. Az a **tevékenységek** eszközkészletben bontsa ki a **általános**, húzza a **webes** tevékenység alakzatot a folyamat tervezőfelületére. Az a **általános** lapján a **tulajdonságok** ablakban módosítsa a nevet, a tevékenység **StartIR**.

   ![Első webes tevékenység – Általános lap](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-general-tab.png)
3. Váltson a **beállítások** lapján a **tulajdonságok** ablakot, és a következő műveleteket: 

    1. A **URL-cím**, illessze be az URL-címet a webhookot, amely elindítja az Azure-SSIS integrációs modult. 
    2. A **metódus**válassza **POST**. 
    3. A **törzs**, adja meg `{"message":"hello world"}`. 
   
        ![Első webes tevékenység – beállítások lap](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-settnigs-tab.png)

4. Húzással, az SSIS-csomag végrehajtása tevékenység vagy a tárolt eljárási tevékenységet a **általános** szakaszában a **tevékenységek** eszközkészlet. Állítsa a tevékenység nevét **RunSSISPackage**. 

5. Ha az SSIS-csomag végrehajtása tevékenység, kövesse a [egy, az SSIS-tevékenység használatával az Azure Data Factory SSIS-csomag futtatása](how-to-invoke-ssis-package-ssis-activity.md) tevékenység létrehozásának befejezéséhez.  Győződjön meg arról, hogy az újrapróbálkozási kísérletek, amelyek az időpontok megfelelő, várja meg az Azure-SSIS integrációs modul rendelkezésre állását, mivel elindításához akár 30 percet vesz igénybe a megfelelő számot adjon meg. 

    ![Ismétlés beállításai](media/how-to-schedule-azure-ssis-integration-runtime/retry-settings.png)

6. Ha a tárolt eljárási tevékenység, kövesse a [egy tárolt eljárási tevékenység használatával az Azure Data Factory SSIS-csomagok meghívásához](how-to-invoke-ssis-package-stored-procedure-activity.md) tevékenység létrehozásának befejezéséhez. Győződjön meg arról, hogy szúr be a Transact-SQL parancsfájl, amely megvárja az Azure-SSIS integrációs modul rendelkezésre állását, mivel elindításához akár 30 percet vesz igénybe.
    ```sql
    DECLARE @return_value int, @exe_id bigint, @err_msg nvarchar(150)

    -- Wait until Azure-SSIS IR is started
    WHILE NOT EXISTS (SELECT * FROM [SSISDB].[catalog].[worker_agents] WHERE IsEnabled = 1 AND LastOnlineTime > DATEADD(MINUTE, -10, SYSDATETIMEOFFSET()))
    BEGIN
        WAITFOR DELAY '00:00:01';
    END

    EXEC @return_value = [SSISDB].[catalog].[create_execution] @folder_name=N'YourFolder',
        @project_name=N'YourProject', @package_name=N'YourPackage',
        @use32bitruntime=0, @runincluster=1, @useanyworker=1,
        @execution_id=@exe_id OUTPUT 

    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1

    EXEC [SSISDB].[catalog].[start_execution] @execution_id = @exe_id, @retry_count = 0

    -- Raise an error for unsuccessful package execution, check package execution status = created (1)/running (2)/canceled (3)/
    -- failed (4)/pending (5)/ended unexpectedly (6)/succeeded (7)/stopping (8)/completed (9) 
    IF (SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id = @exe_id) <> 7 
    BEGIN
        SET @err_msg=N'Your package execution did not succeed for execution ID: '+ CAST(@execution_id as nvarchar(20))
        RAISERROR(@err_msg, 15, 1)
    END
    ```

7. Csatlakozás a **webes** tevékenység, amely a **SSIS-csomag végrehajtása** vagy a **tárolt eljárás** tevékenység. 

    ![Webes és a tárolt eljárási tevékenységek összekapcsolása](./media/how-to-schedule-azure-ssis-integration-runtime/connect-web-sproc.png)

8. Húzással rendezheti át egy másik **webes** tevékenység jobb oldalára az **SSIS-csomag végrehajtása** tevékenység vagy a **tárolt eljárás** tevékenység. Állítsa a tevékenység nevét **StopIR**. 
9. Váltson a **beállítások** lapján a **tulajdonságok** ablakot, és a következő műveleteket: 

    1. A **URL-cím**, illessze be az URL-címet a webhookot, amely megakadályozza az Azure-SSIS integrációs modult. 
    2. A **metódus**válassza **POST**. 
    3. A **törzs**, adja meg `{"message":"hello world"}`.  
10. Csatlakozás a **SSIS-csomag végrehajtása** tevékenység vagy a **tárolt eljárás** a legutolsó tevékenység **webes** tevékenység.

    ![A teljes folyamat](./media/how-to-schedule-azure-ssis-integration-runtime/full-pipeline.png)
11. A folyamat beállításainak érvényesítéséhez kattintson **ellenőrzése** az eszköztáron. Zárja be a **Folyamatérvényesítési jelentés** kattintva **>>** gombra. 

    ![Folyamat érvényesítése](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-the-pipeline"></a>A folyamat próbafuttatása

1. Válassza ki **tesztfuttatás** a folyamat eszköztárán. A kimenet a jelenik meg a **kimeneti** ablak alsó ablaktábláján. 

    ![Teszt futtatása](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
2. Az a **Runbook** oldal az Azure Automation-fiók, ellenőrizheti, hogy futtatta-e a feladatok elindítása és leállítása az Azure-SSIS integrációs modult. 

    ![Runbook-feladatok](./media/how-to-schedule-azure-ssis-integration-runtime/runbook-jobs.png)
3. Indítsa el az SQL Server Management Studióval. Az a **kapcsolódás a kiszolgálóhoz** ablakban hajtsa végre a következőket: 

    1. A **kiszolgálónév**, adja meg  **&lt;az Azure SQL database&gt;. database.windows.net**.
    2. Válassza ki **beállítások >>**.
    3. A **csatlakozhat az adatbázishoz**válassza **SSISDB**.
    4. Kattintson a **Csatlakozás** gombra. 
    5. Bontsa ki a **Integration Services-katalógusok** -> **SSISDB** -> a mappa -> **projektek** -> Projekt az SSIS -> **csomagok** . 
    6. Kattintson a jobb gombbal az SSIS-csomag, és válassza ki **jelentések** -> **szabványos jelentések** -> **az összes végrehajtás**. 
    7. Győződjön meg arról, hogy futtatta-e az SSIS-csomag. 

        ![Ellenőrizze az SSIS-csomag futtatása](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-the-pipeline"></a>A folyamat rendszeres 
Most, hogy a folyamat meg a várt módon működik, létrehozhat egy olyan eseményindítót Ez a folyamat jelenleg egy megadott kiadása ütemben történik. Egy folyamat és ütemezési eseményindító társításával kapcsolatos részletekért lásd: [ütemezés a folyamat aktiválása](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

1. A folyamat eszköztárán válassza **eseményindító**, és válassza ki **új/Szerkesztés**. 

    ![Eseményindító új/Szerkesztés->](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)
2. Az a **eseményindítók hozzáadása** ablakban válassza **+ új**.

    ![Eseményindítók hozzáadása – új](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)
3. Az a **új eseményindító**, tegye a következőket: 

    1. A **neve**, adja meg az eseményindító nevét. A következő példában **naponta** az eseményindító neve. 
    2. A **típus**válassza **ütemezés**. 
    3. A **Kezdődátum**, jelölje be a kezdő dátum és idő. 
    4. A **ismétlődési**, adja meg az eseményindító a kiadása ütemben történik. A következő példában annak naponta egyszer. 
    5. A **záró**, kiválasztásával megadhatja a dátum és idő a **dátum** lehetőséget. 
    6. Válassza ki **aktiválva**. Az eseményindító aktiválása után azonnal közzéteszi a megoldást a Data Factoryban. 
    7. Kattintson a **Tovább** gombra.

        ![Eseményindító új/Szerkesztés->](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
4. Az a **eseményindító futtatási paraméterei** lapon tekintse át a figyelmeztető üzenetet, és válassza ki **Befejezés**. 
5. Közzéteszi a megoldást a Data Factory kiválasztásával **összes közzététele** a bal oldali panelen. 

    ![Összes közzététele](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-the-pipeline-and-trigger-in-the-azure-portal"></a>Figyelheti a folyamatot, és az Azure Portalon

1. Eseményindító-futtatások monitorozása és a folyamatfuttatások használja a **figyelő** lapra a bal oldalon. Részletes lépéseiért lásd: [a folyamat figyelése](quickstart-create-data-factory-portal.md#monitor-the-pipeline).

    ![Folyamatfuttatások](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)
2. A folyamat futásához társított tevékenységfuttatások megtekintéséhez jelölje ki az első hivatkozásra (**Tevékenységfuttatások megtekintése**) az a **műveletek** oszlop. Megjelenik a három társított tevékenységfuttatásokat, minden egyes tevékenységet a folyamatban az (első webes tevékenységek, Stored Procedure-tevékenység és a második webes tevékenység). Váltson vissza a folyamatfuttatások megtekintése, jelölje be **folyamatok** a fenti hivatkozásra.

    ![Tevékenységfuttatások](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)
3. Eseményindító-futtatások kiválasztásával is megtekintheti **futtatások aktiválása** melletti legördülő listából a **Folyamatfuttatások** tetején. 

    ![Eseményindító-futtatások](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-the-pipeline-and-trigger-with-powershell"></a>Figyelheti a folyamatot, és a PowerShell-lel

Például az alábbi példák parancsprogramok használatával figyelheti a folyamatot, és az eseményindító.

1. Folyamatfuttatás állapotának lekéréséhez.

  ```powershell
  Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
  ```

2. Az eseményindító kapcsolatos adatok beolvasása.

  ```powershell
  Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
  ```

3. Eseményindító-Futtatás állapotának beolvasása.

  ```powershell
  Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
  ```

## <a name="next-steps"></a>További lépések
Tekintse meg a következő blogbejegyzésben található:
-   [Végezzen modernizálást és az ETL/ELT munkafolyamatok SSIS tevékenységek az ADF folyamatok kiterjesztése](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)

Lásd az SSIS dokumentációjának alábbi cikkeit: 

- [SSIS-csomag üzembe helyezése, futtatása és monitorozása az Azure-on](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Csatlakozás SSIS-katalógushoz Azure-on](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Csomag végrehajtásának ütemezése az Azure-on](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Csatlakozás helyszíni adatforrásokhoz Windows-hitelesítéssel](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
