---
title: Azure SSIS integrációs futásidejű ütemezéséről |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan ütemezése indítása és leállítása egy Azure SSIS-integráció futtatókörnyezetet Azure Automation és a Data Factory használatával.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 06/01/2018
ms.author: douglasl
ms.openlocfilehash: 8eeed91da3942d00bbab17a2dffc4b4e888a6f70
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34725108"
---
# <a name="how-to-schedule-starting-and-stopping-of-an-azure-ssis-integration-runtime"></a>Indítása és leállítása egy Azure SSIS-integráció futtatókörnyezetet ütemezése 
Ez a cikk ismerteti, hogyan ütemezése indítása és leállítása egy Azure SSIS-integráció futtatókörnyezetet (IR) Azure Automation és az Azure Data Factory használatával. Egy Azure SSIS (SQL Server Integration Services) integrációs futásidejű fut (IR) van társítva járnak. Ezért futtatni kívánt az infravörös csak akkor, ha SSIS-csomagok futtathatja az Azure-ban, és állítsa le, ha már nincs szükség van szüksége. A Data Factory felhasználói felületén vagy az Azure PowerShell [manuálisan indítsa el, vagy állítsa le az Azure SSIS-IR](manage-azure-ssis-integration-runtime.md)).

Például hozzon létre egy Azure Automation PowerShell runbook webhookok webes tevékenységek, és a közöttük hajtható végre SSIS-csomag tevékenység láncolt. A webes tevékenységek elindíthatók és leállíthatók az Azure-SSIS-IR JIT előtt, és a csomag futtatása után. SSIS-csomag hajtható végre tevékenységével kapcsolatos további információk: [futtassa egy SSIS-csomagot a SSIS-tevékenység használata az Azure Data Factory](how-to-invoke-ssis-package-ssis-activity.md).

## <a name="overview-of-the-steps"></a>Lépéseinek áttekintése

Ebben a cikkben leírt magas szintű lépései a következők:

1. **Hozzon létre, és egy Azure Automation-runbook tesztelése.** Ebben a lépésben a parancsfájl, amely elindítja vagy leállítja egy Azure SSIS infravörös létrehozhat egy PowerShell-forgatókönyv Ezt követően tesztelje a forgatókönyvet a KEZDŐ- és a LEÁLLÍTÁSI forgatókönyvek, és győződjön meg arról, hogy IR elindul vagy leáll. 
2. **Hozzon létre két ütemezések a runbookhoz.** Az első ütemezés konfigurálnia a runbook indítása a művelet. A második ütemezés beállítása a runbook le a műveletet. Mindkét ütemezéseket akkor adja meg a ütemben történik, amelyen a runbook futtatása. Érdemes lehet például az első sablon kiválasztásával 8 órakor futtatása naponta, és a második futtatását, hogy a mindennapi 23 óra ütemezni. Amikor az első runbook fut, az Azure SSIS infravörös megkezdése A második runbook futtatása, leállítja az Azure SSIS infravörös 
3. **A forgatókönyv két webhook létrehozása**, egyet a START műveletet, a másik pedig a STOP műveletet. A Data Factory-folyamathoz webes tevékenységek konfigurálásakor használhatja a webhook URL-címei. 
4. **Hozzon létre a Data Factory-folyamathoz**. A folyamat létrehozása három tevékenységek áll. Az első **webes** tevékenység hív meg elindítani a Azure SSIS infravörös első webhook A **tárolt eljárás** tevékenység fut egy SQL-parancsfájl, amely futtatja a SSIS-csomagot. A második **webes** tevékenység leállítja az Azure SSIS infravörös Egy SSIS-csomagot a Data Factory-folyamathoz meghívása a tárolt eljárási tevékenység használatával kapcsolatos további információkért lásd: [meghívni egy SSIS-csomag](how-to-invoke-ssis-package-stored-procedure-activity.md). Ezután a létrehozott ütemezés futtatásához megadott ütemben folyamat ütemezéséhez.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [meghívása SSIS-csomagok használata tárolt eljárási tevékenység az 1-es](v1/how-to-invoke-ssis-package-stored-procedure-activity.md).

 
## <a name="prerequisites"></a>Előfeltételek
Ha egy Azure SSIS-integrációs futásidejű már még nem létesített, építi ki a következő témakör utasításait: a [oktatóanyag](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-test-an-azure-automation-runbook"></a>Hozzon létre, és egy Azure Automation-runbook tesztelése
Ebben a szakaszban hajtsa végre a következő lépéseket: 

1. Hozzon létre egy Azure Automation-fiók.
2. Az Azure Automation-fiók létrehozása a PowerShell-forgatókönyv. A PowerShell-parancsfájl a runbookhoz társított elindítja vagy leállítja egy Azure SSIS-IR alapján a művelet paraméter számára megadott parancsot. 
3. Tesztelje a forgatókönyvet a nyitó, és ellenőrizze, hogy működik a forgatókönyvek leállítása. 

### <a name="create-an-azure-automation-account"></a>Azure Automation-fiók létrehozása
Ha egy Azure Automation-fiók nem rendelkezik, hozzon létre ebben a lépésben szereplő utasításokat követve. Részletes útmutató: [hozzon létre egy Azure Automation-fiók](../automation/automation-quickstart-create-account.md). Ez a lépés részeként létrehozhat egy **Azure-beli futtató** fiókhoz (egy egyszerű szolgáltatásnév az Azure Active Directoryban), és hozzá kell adnia a **közreműködő** szerepe az Azure-előfizetéshez. Győződjön meg arról, hogy az ugyanaz, mint az előfizetés, amely tartalmazza az adat-előállítóban, amely rendelkezik az Azure SSIS infravörös Azure Automation szolgáltatásbeli hitelesítéshez az Azure Resource Manager és az erőforrások működik ezt a fiókot használja. 

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
2. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).    
3. Válassza ki **új** a bal oldali menüben válassza **figyelés + felügyeleti**, és válassza ki **Automation**. 

    ![Új -> figyelés + Management Automation ->](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
2. Az a **Automation-fiók hozzáadása** ablakban, a következő lépéseket: 

    1. Adjon meg egy **neve** az automation-fiókhoz. 
    2. Válassza ki a **előfizetés** , amely rendelkezik az Azure SSIS infravörös adat-előállító 
    3. A **erőforráscsoport**, jelölje be **hozzon létre új** hozzon létre egy új erőforráscsoportot, vagy válasszon **meglévő** számára válasszon ki egy meglévő erőforráscsoportot. 
    4. Válassza ki a **hely** az automation-fiókhoz. 
    5. Ellenőrizze, hogy **létrehozása Futtatás mint fiók** értéke **Igen**. Egy egyszerű szolgáltatást az Azure Active Directoryban jön létre. Felvettük a **közreműködő** az Azure-előfizetéshez szerepe
    6. Válassza ki **rögzítés az irányítópulton** , hogy a portál az irányítópulton megjelenik. 
    7. Kattintson a **Létrehozás** gombra. 

        ![Új -> figyelés + Management Automation ->](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
3. Megjelenik a **központi telepítési állapot** az irányítópult és az értesítéseket. 
    
    ![Automatizálás telepítése](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
4. Sikeres létrehozása után megjelenik a kezdőlap, az automation-fiókhoz. 

    ![Automatizálási kezdőlap](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-data-factory-modules"></a>Adat-előállító modul importálása

1. Válassza ki **modulok** a a **megosztott erőforrások** a bal oldali menü szakaszt, és ellenőrizze, hogy rendelkezik **AzureRM.Profile** és **AzureRM.DataFactoryV2** modulok listáján.

    ![A szükséges modulokat ellenőrzése](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  A PowerShell-galériában navigáljon a [AzureRM.DataFactoryV2 modul](https://www.powershellgallery.com/packages/AzureRM.DataFactoryV2/), jelölje be **központi telepítése az Azure Automation**, az Automation-fiók, majd válassza ki és **OK**. Megtekintéséhez lépjen vissza **modulok** a a **megosztott erőforrások** a bal oldali menü szakaszt, és várjon, amíg megjelenik a **állapot** , a **AzureRM.DataFactoryV2** modul módosítás **elérhető**.

    ![A Data Factory modul ellenőrzése](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  A PowerShell-galériában navigáljon a [AzureRM.Profile modul](https://www.powershellgallery.com/packages/AzureRM.profile/), kattintson a **központi telepítése az Azure Automation**, az Automation-fiók, majd válassza ki és **OK**. Megtekintéséhez lépjen vissza **modulok** a a **megosztott erőforrások** a bal oldali menü szakaszt, és várjon, amíg megjelenik a **állapot** , a **AzureRM.Profile**modul módosítás **elérhető**.

    ![A profil modul ellenőrzése](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-a-powershell-runbook"></a>PowerShell-runbook létrehozása
Az alábbi eljárások ismertetik PowerShell runbook létrehozása. A parancsfájl vagy a runbookhoz társított elindul vagy leáll egy Azure SSIS-IR alapján állít be a parancsot a **művelet** paraméter. Ez a szakasz nem biztosítanak a részletek runbook létrehozása. További információkért lásd: [létrehozhat egy](../automation/automation-quickstart-create-runbook.md) cikket.

1. Váltás a **Runbookok** lapot, és jelölje be **+ Hozzáadás runbook** az eszköztáron. 

    ![Egy runbook gomb felvétele](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
2. Válassza ki **hozzon létre egy új runbookot**, és hajtsa végre a következő lépéseket: 

    1. A **neve**, típus **StartStopAzureSsisRuntime**.
    2. A **runbooktípusba**, jelölje be **PowerShell**.
    3. Kattintson a **Létrehozás** gombra.
    
        ![Egy runbook gomb felvétele](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
3. Másolja és illessze be a következő parancsfájlt, és a runbook parancsfájl ablak. Mentse, és tegye közzé a runbookot használatával a **mentése** és **közzététel** gomb az eszköztáron. 

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

    ![PowerShell-forgatókönyv szerkesztése](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
5. A runbook tesztelése kiválasztásával **Start** gomb az eszköztáron. 

    ![Indítsa el a runbook gomb](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
6. Az a **runbook meghívása** ablak, hajtsa végre a következő lépéseket: 

    1. A **ERŐFORRÁSCSOPORT-név**, adja meg a data Factory, amely rendelkezik az Azure SSIS infravörös az erőforráscsoport nevét 
    2. A **adat-előállító**, adja meg, amely rendelkezik az Azure SSIS infravörös adat-előállító nevét 
    3. A **AZURESSISNAME**, írja be a Azure SSIS infravörös nevét 
    4. A **művelet**, adja meg **START**. 
    5. Kattintson az **OK** gombra.  

        ![Indítsa el a runbook ablak](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
7. A feladatok ablakban válassza ki a **kimeneti** csempére. A kimeneti ablakban, a feladat, várjon, amíg megjelenik az üzenet **### befejezve ###** Miután meggyőződött arról **### indítása ###**. Egy Azure SSIS-IR indítása körülbelül 20 percet vesz igénybe. Zárja be a **feladat** ablakot, és visszatérhet a **Runbook** ablak.

    ![Az Azure SSIS-IR - megkezdődött](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
8.  Ismételje az előző két lépést, de a segítségével **LEÁLLÍTÁSA** értéke a **művelet**. A runbook indítását kiválasztásával a **Start** gomb az eszköztáron. Adja meg az erőforráscsoport neve, adat-előállító és Azure SSIS IR nevét. A **művelet**, adja meg **LEÁLLÍTÁSA**. 

    A kimeneti ablakban, a feladat, várjon, amíg megjelenik az üzenet **### befejezve ###** Miután meggyőződött arról **### leállítása ###**. Egy Azure SSIS-IR leállítása nem eltarthat az Azure SSIS infravörös indítása Zárja be a **feladat** ablakot, és visszatérhet a **Runbook** ablak.

## <a name="create-schedules-for-the-runbook-to-startstop-the-azure-ssis-ir"></a>A runbook indítása/leállítása az Azure SSIS infravörös ütemezések létrehozása
Az előző szakaszban létrehozott egy Azure Automation-runbook, amelyek elindítani vagy leállítani egy Azure SSIS infravörös Ez a szakasz két ütemezések a runbook hoz létre. Az első ütemezési konfigurálásakor adja meg KEZDÉSI művelet paramétereként. Hasonlóképpen a második ütemezés konfigurálásakor megadhatja állítsa le a művelethez. Ütemezések létrehozásának részletes lépései: [ütemezés létrehozása](../automation/automation-schedules.md#creating-a-schedule).

1. Az a **Runbook** ablakban válassza ki **ütemezések**, és válassza ki **+ Hozzáadás ütemezés** az eszköztáron. 

    ![Az Azure SSIS-IR - megkezdődött](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
2. Az a **ütemterv forgatókönyv** ablak, hajtsa végre a következő lépéseket: 

    1. Válassza ki **ütemezés kapcsolása a forgatókönyvhöz**. 
    2. Válassza ki **hozzon létre egy új ütemezést**.
    3. Az a **új ütemezés** ablakot, írja be **IR Start naponta** a **neve**. 
    4. Az a **szakasz kezdődik**, az idő, adja meg egy időpontot az aktuális idő néhány perc. 
    5. A **ismétlődési**, jelölje be **ismétlődő**. 
    6. Az a **Ismétlődés minden** szakaszban jelölje be **nap**. 
    7. Kattintson a **Létrehozás** gombra. 

        ![Azure SSIS IR start ütemezését](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
3. Váltás a **paraméterek és futtatási beállítások** fülre. Adja meg az erőforráscsoport neve, adat-előállító és Azure SSIS IR nevét. A **művelet**, adja meg **START**. Kattintson az **OK** gombra. Válassza ki **OK** ismételt használatával ellenőrizheti az ütemezés a a **ütemezések** oldalán a runbookban. 

    ![Az Azure SSIS infravörös indítás ütemezése](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
4. Ismételje az előző két lépést nevű ütemezés létrehozása **IR leállítása naponta**. Most, megadott idő legalább 30 perc után adja meg a **IR Start naponta** ütemezés. A **művelet**, adja meg **LEÁLLÍTÁSA**. 
5. Az a **Runbook** ablakban válassza ki **feladatok** a bal oldali menüben. A megadott ütemezés szerint létrehozott feladatok kell megjelennie idejét és a hozzájuk állapotok. Megtekintheti például hasonló mi láthatta, a runbook tesztelésekor annak kimenetét a feladat részleteit. 

    ![Az Azure SSIS infravörös indítás ütemezése](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
6. Miután végzett tesztelése, tiltsa le az ütemezéseket azok szerkesztése, majd válassza **nem** a **engedélyezve**. Válassza ki **ütemezések** a bal oldali menüben válassza ki a **Start IR napi/Stop IR naponta**, és válassza ki **nem** a **engedélyezve**. 

## <a name="create-webhooks-to-start-and-stop-the-azure-ssis-ir"></a>Indítása és leállítása az Azure SSIS infravörös webhook létrehozása
Kövesse az utasításokat [a webhook létrehozása](../automation/automation-webhooks.md#creating-a-webhook) két webhookok a runbook létrehozásához. Az első címtárra START adja meg a műveletet, és a második egy meg állítsa le a műveletet. Mentés valahol (például egy szövegfájlt vagy a OneNote-jegyzetfüzet) egyaránt a webhook URL-címeit. A Data Factory-folyamathoz webes tevékenységeket konfigurálásakor használhatja URL. Az alábbi képen is látható, hogy az Azure SSIS infravörös elindul egy webhook létrehozására láthat példát:

1. Az a **Runbook** ablakban válassza ki **Webhookok** a bal oldali menüben, majd válassza a **+ Hozzáadás Webhook** az eszköztáron. 

    ![Webhook -> Webhook hozzáadása](./media/how-to-schedule-azure-ssis-integration-runtime/add-web-hook-menu.png)
2. Az a **hozzáadása Webhook** ablakban válassza ki **hozzon létre új webhook**, és a következő műveleteket hajthatja végre: 

    1. A **neve**, adja meg **StartAzureSsisIR**. 
    2. Ellenőrizze, hogy **engedélyezve** értéke **Igen**. 
    3. Másolás a **URL-cím** helyre mentse. Ez a lépés fontos. Az URL-cím később nem látható. 
    4. Kattintson az **OK** gombra. 

        ![Új Webhook ablak](./media/how-to-schedule-azure-ssis-integration-runtime/new-web-hook-window.png)
3. Váltás a **paraméterek és futtatási beállítások** fülre. Adja meg az erőforráscsoport neve, az adat-előállító és az Azure SSIS IR nevét. A **művelet**, adja meg **START**. Kattintson az **OK** gombra. Ezt követően kattintson a **Create** (Létrehozás) gombra. 

    ![Webhook - paraméterek és futtatási beállítások](./media/how-to-schedule-azure-ssis-integration-runtime/webhook-parameters.png)
4. Ismételje az előző három lépést nevű másik webhook létrehozása **StopAzureSsisIR**. Ne feledje másolja az URL-címet. A paraméterek és futtatási beállítások megadásakor adja meg a **LEÁLLÍTÁSA** a **művelet**. 

Rendelkeznie kell két URL-címet, egyet a **StartAzureSsisIR** webhook, a másik pedig a **StopAzureSsisIR** webhook. HTTP POST-kérelmet küld az Azure SSIS infravörös indítása/leállítása az URL- 

## <a name="create-and-schedule-a-data-factory-pipeline-that-startsstops-the-ir"></a>Hozzon létre, és a Data Factory-feldolgozási folyamat elindul vagy leáll az infravörös ütemezése
Ez a szakasz bemutatja, hogyan lehet meghívni a webhookok az előző szakaszban létrehozott egy webes tevékenység használata.

A folyamat létrehozása három tevékenységek áll. 

1. Az első **webes** tevékenység hív meg elindítani a Azure SSIS infravörös első webhook 
2. A **SSIS-csomag hajtható végre** tevékenység vagy a **tárolt eljárás** tevékenység fut a SSIS-csomag.
3. A második **webes** tevékenység meghívja a webhook az Azure SSIS infravörös leállítása 

Létrehozott, és tesztelje a folyamatot, egy ütemezést létrehozni, és rendelje hozzá a feldolgozási sor. Az ütemezés eseményindító meghatározza annak ütemezését, hogy a folyamat. Tegyük fel, amelyek van ütemezve napi 23 óra eseményindítót hoz létre. Az eseményindító a feldolgozási sor lefuttat minden nap 23 óra. A folyamat elindítja az Azure SSIS infravörös, végrehajtja a SSIS-csomag leáll, és majd a Azure SSIS infravörös 

### <a name="create-a-data-factory"></a>Data factory létrehozása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).    
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
4. Válassza a **V2 (előzetes verzió)** értéket a **verzió** esetén.
5. Válassza ki a Data Factory **helyét**. A listában csak az adat-előállítók létrehozását támogató helyek jelennek meg.
6. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
7. Kattintson a **Create** (Létrehozás) gombra.
8. Az irányítópulton megjelenő csempén a következő állapotleírás látható: **Adat-előállító üzembe helyezése**. 

    ![adat-előállító üzembe helyezése csempe](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
   ![Data factory kezdőlap](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. A Data Factory felhasználói felületének (UI) külön lapon történő elindításához kattintson a **Létrehozás és monitorozás** csempére.

### <a name="create-a-pipeline"></a>Folyamat létrehozása

1. Az a **Ismerkedés** lapon jelölje be **létrehozási folyamat**. 

   ![Első lépések lap](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
2. Az a **tevékenységek** eszközkészlet, bontsa ki a **általános**, egérrel a **webes** tevékenység csővezeték Tervező felületére. Az a **általános** lapján a **tulajdonságok** ablakban, a tevékenység nevének módosítása **StartIR**.

   ![Első webes tevékenység – Általános lap](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-general-tab.png)
3. Váltás a **beállítások** lapra a **tulajdonságok** ablakot, és a következő műveleteket hajthatja végre: 

    1. A **URL-cím**, illessze be, hogy az Azure SSIS infravörös elindul a webhook URL-címe 
    2. A **metódus**, jelölje be **POST**. 
    3. A **törzs**, adja meg `{"message":"hello world"}`. 
   
        ![Első webes tevékenység - beállítások lap](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-settnigs-tab.png)

4. Áthúzása a SSIS-csomag végrehajtása vagy a tárolt eljárás tevékenység a a **általános** szakasza a **tevékenységek** eszközkészlet. A tevékenység nevének beállítása **RunSSISPackage**. 

5. Ha a végrehajtás SSIS-csomag tevékenység választja, kövesse az utasításokat a [futtassa egy SSIS-csomagot a SSIS-tevékenység használata az Azure Data Factory](how-to-invoke-ssis-package-ssis-activity.md) tevékenység létrehozásának befejezéséhez.  Győződjön meg arról, hogy megadja a megfelelő számú ismételt kísérletek számát, amelyek megfelelő gyakoriságát, amellyel az Azure-SSIS infravörös rendelkezésre állását várja meg, mert elindításához akár 30 percet vesz igénybe. 

    ![Ismétlés beállításai](media/how-to-schedule-azure-ssis-integration-runtime/retry-settings.png)

6. Ha bejelöli a tárolt eljárási tevékenység, kövesse az utasításokat a [meghívni egy SSIS-csomagot, a tárolt eljárási tevékenység az Azure Data Factory](how-to-invoke-ssis-package-stored-procedure-activity.md) tevékenység létrehozásának befejezéséhez. Győződjön meg arról, hogy a lemezt egy Transact-SQL parancsfájlt, amely megvárja-e az Azure-SSIS infravörös rendelkezésre állását a óta elindításához akár 30 percet vesz igénybe.
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

7. Csatlakozás a **webes** tevékenység és a **SSIS-csomag hajtható végre** vagy a **tárolt eljárás** tevékenység. 

    ![Csatlakozás a webes és a tárolt eljárás tevékenység](./media/how-to-schedule-azure-ssis-integration-runtime/connect-web-sproc.png)

8. Adatértékmezők áthúzása egy másik **webes** tevékenység jobb oldalán a **SSIS-csomag hajtható végre** tevékenység vagy a **tárolt eljárás** tevékenység. A tevékenység nevének beállítása **StopIR**. 
9. Váltás a **beállítások** lapra a **tulajdonságok** ablakot, és a következő műveleteket hajthatja végre: 

    1. A **URL-cím**, illessze be, amely az Azure SSIS infravörös leállítja a webhook URL-címe 
    2. A **metódus**, jelölje be **POST**. 
    3. A **törzs**, adja meg `{"message":"hello world"}`.  
10. Csatlakozás a **SSIS-csomag hajtható végre** tevékenység vagy a **tárolt eljárás** a legutolsó tevékenység **webes** tevékenység.

    ![Teljes feldolgozási folyamat](./media/how-to-schedule-azure-ssis-integration-runtime/full-pipeline.png)
11. Az adatcsatorna-beállítások gombra kattintva érvényesítenie **ellenőrzése** az eszköztáron. Zárja be a **folyamat ellenőrzési jelentés** kattintva **>>** gombra. 

    ![Folyamat érvényesítése](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-the-pipeline"></a>A folyamat próbafuttatása

1. Válassza ki **teszt futtatása** a tölcsér az eszköztáron. A kimenet jelenik meg a **kimeneti** ablak az alsó ablaktáblán. 

    ![Teszt futtatása](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
2. Az a **Runbook** lap az Azure Automation-fiók, ellenőrizheti, hogy futtatta-e a feladatok indítása és leállítása a Azure SSIS infravörös 

    ![Runbook-feladatok](./media/how-to-schedule-azure-ssis-integration-runtime/runbook-jobs.png)
3. Indítsa el az SQL Server Management Studio eszközt. Az a **kapcsolódás a kiszolgálóhoz** ablakban, a következő műveleteket hajthatja végre: 

    1. A **kiszolgálónév**, adja meg  **&lt;az Azure SQL-adatbázis&gt;. database.windows.net**.
    2. Válassza ki **beállítások >>**.
    3. A **adatbázis kapcsolódás**, jelölje be **SSISDB**.
    4. Kattintson a **Csatlakozás** gombra. 
    5. Bontsa ki a **integrációs szolgáltatások katalógusok** -> **SSISDB** -> a mappa -> **projektek** -> project Your SSIS -> **csomagok** . 
    6. Kattintson a jobb gombbal a SSIS-csomag, és válassza ki **jelentések** -> **szabványos jelentések** -> **összes végrehajtások**. 
    7. Győződjön meg arról, hogy futtatta-e a SSIS-csomag. 

        ![Ellenőrizze a SSIS-csomag futtatása](./media/how-to-schedule-azure-ssis-integration-runtime/verfiy-ssis-package-run.png)

### <a name="schedule-the-pipeline"></a>A folyamat ütemezése 
Most, hogy az adatcsatorna működik, ha Ön várt, létrehozhat egy eseményindítót, ez az adatcsatorna futtatását, hogy a megadott ütemben történik. Egy ütemezés eseményindító társít egy folyamatot, lásd: [indul el, a folyamat ütemezés szerint](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

1. Válassza a tölcsér eszköztár **eseményindító**, és válassza ki **új/szerkesztése**. 

    ![Eseményindító -> új/szerkesztése](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)
2. Az a **hozzáadása eseményindítók** ablakban válassza ki **+ új**.

    ![Eseményindítók - Új hozzáadása](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)
3. Az a **eseményindító**, a következő műveleteket hajthatja végre: 

    1. A **neve**, adja meg az eseményindító nevét. A következő példában **naponta** az eseményindító neve. 
    2. A **típus**, jelölje be **ütemezés**. 
    3. A **Kezdődátum**, a kezdő dátum és idő kiválasztása. 
    4. A **ismétlődési**, adja meg az eseményindító a ütemben történik. A következő példában az naponta egyszer. 
    5. A **End**, kiválasztásával megadhatja a dátum és idő a **dátuma** lehetőséget. 
    6. Válassza ki **aktiválva**. Az eseményindító aktiválódik, közvetlenül a Data Factory a megoldás közzététele után. 
    7. Kattintson a **Tovább** gombra.

        ![Eseményindító -> új/szerkesztése](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
4. Az a **Trigger futtatása paraméterek** lapon olvassa el a figyelmeztetést, majd válassza ki **Befejezés**. 
5. A megoldás közzétételére az adat-előállító kiválasztásával **összes** a bal oldali ablaktáblán. 

    ![Az összes közzététele](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)
6. Figyelése eseményindító fut, és -feldolgozási folyamat fut, használja a **figyelő** a bal oldali lapon. Részletes útmutató: [a folyamat figyelése](quickstart-create-data-factory-portal.md#monitor-the-pipeline).

    ![Folyamatfuttatások](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)
7. Válassza ki, ha a tevékenység fut társított futtatni egy folyamatot, az első hivatkozás (**nézet tevékenység fut**) található a **műveletek** oszlop. Megjelenik a három tevékenység fut, a folyamat minden egyes tevékenységhez társított (először webalkalmazás, tárolt eljárási tevékenység, és a második webes tevékenység). Váltson vissza a megtekintheti a folyamat fut, válassza ki **folyamatok** tetején.

    ![Tevékenységfuttatások](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)
8. Is megtekintheti az eseményindító futtatása kiválasztásával **indítás futtatása** melletti legördülő listából a **folyamat fut** tetején. 

    ![Eseményindító-futtatások](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

## <a name="next-steps"></a>További lépések
Tekintse meg a következő blogbejegyzésben található:
-   [Korszerűsítésére és kiterjesztése a ETL/ELT munkafolyamatok SSIS-tevékenységek számára az ADF-folyamatok](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)

Lásd az SSIS dokumentációjának alábbi cikkeit: 

- [SSIS-csomag üzembe helyezése, futtatása és monitorozása az Azure-on](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Csatlakozás SSIS-katalógushoz Azure-on](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Csomag végrehajtásának ütemezése az Azure-on](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Csatlakozás helyszíni adatforrásokhoz Windows-hitelesítéssel](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
