---
title: Egy runbookhoz, az Azure Automationben Log Analytics-adatok gyűjtése |} Microsoft Docs
description: Lépésenkénti útmutató, amely végigvezeti egy runbook létrehozása az Azure Automation szolgáltatásban, hogy a OMS tárházba elemzés, Log Analyticshez úgy gyűjthet adatokat.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: operations-management-suite
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2017
ms.author: bwren
ms.openlocfilehash: 0784e2317fbc98561b486547654ca27bb30e76c3
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="collect-data-in-log-analytics-with-an-azure-automation-runbook"></a>Egy Azure Automation-runbook Naplóelemzési az adatok gyűjtése
Jelentős mennyiségű Naplóelemzési adatokat gyűjteni különböző forrásokból, beleértve a [adatforrások](../log-analytics/log-analytics-data-sources.md) az ügynökökre és is [adatgyűjtés az Azure-ból](../log-analytics/log-analytics-azure-storage.md).  Nincsenek olyan forgatókönyvek, ha az adatok gyűjtéséhez kell, amely nem érhető el a szabványos forrásokon keresztül.  Ebben az esetben is használhatja a [HTTP adatait gyűjtője API](../log-analytics/log-analytics-data-collector-api.md) Naplóelemzési bármely REST API-ügyfél adatokat írni.  Az adatgyűjtés elvégzéséhez gyakran használják az Azure Automationben egy runbook használ.   

Ez az oktatóanyag végigvezeti a folyamat létrehozásának és az Azure Automationben adatokat írni a Naplóelemzési runbook ütemezése.


## <a name="prerequisites"></a>Előfeltételek
Ebben a forgatókönyvben a következő erőforrások az Azure-előfizetéshez konfigurált igényel.  Egy ingyenes fiókot is lehet.

- [A Naplóelemzési munkaterület jelentkezzen](../log-analytics/log-analytics-get-started.md).
- [Azure automation-fiók](../automation/automation-offering-get-started.md).

## <a name="overview-of-scenario"></a>Forgatókönyv áttekintése
Ebben az oktatóanyagban automatizálási feladatok adatokat gyűjt a runbook fog írni.  Az Azure Automation Runbookjai PowerShell használatával valósíthatók meg, írása, és egy parancsfájl tesztelése az Azure Automation-szerkesztőben kezdi.  Miután ellenőrizte, hogy a szükséges adatokat gyűjt, fogja, hogy adatokat írjon Naplóelemzési, és ellenőrizze, az egyéni adattípus.  Végezetül létre fog hozni egy ütemezést, rendszeres időközönként a runbook elindításához.

> [!NOTE]
> Azure Automation-feladat információinak küldendő Log Analytics ezt a runbookot nélkül is konfigurálhat.  Ebben a forgatókönyvben elsősorban az oktatóanyag támogatásához, és elküldheti az adatokat egy tesztelési munkaterület ajánlott.  


## <a name="1-install-data-collector-api-module"></a>1. Data Collector API-modul telepítése
Minden [kérelem érkezett a HTTP-adatokat gyűjtő API](../log-analytics/log-analytics-data-collector-api.md#create-a-request) megfelelően formázva, és egy authorization fejlécet tartalmaz.  Ehhez a runbookban, de a kódot, amely leegyszerűsíti ezt a folyamatot modul használatával mennyisége csökkenthető.  Egy modul, melyekkel [OMSIngestionAPI modul](https://www.powershellgallery.com/packages/OMSIngestionAPI) a PowerShell-galériában.

Használatához a [modul](../automation/automation-integration-modules.md) egy runbookban, telepítenie kell az Automation-fiókban.  Minden runbook ugyanazt a fiókot használhatja a funkciók a modulban.  Új modul választásával telepítheti **eszközök** > **modulok** > **a modul hozzá lesz adva** az Automation-fiókban.  

A PowerShell-galériában azonban lehetővé teszi a gyors kapcsolóval kell telepítenie egy modul közvetlenül az automation-fiók, hogy használhassa ezt a beállítást a jelen oktatóanyag.  

![OMSIngestionAPI modul](media/operations-management-suite-runbook-datacollect/OMSIngestionAPI.png)

1. Ugrás a [PowerShell-galériában](https://www.powershellgallery.com/).
2. Keresse meg **OMSIngestionAPI**.
3. Kattintson a **központi telepítése az Azure Automation** gombra.
4. Válassza ki az automation-fiók, és kattintson a **OK** -modul telepítéséhez.


## <a name="2-create-automation-variables"></a>2. Automatizálási változók létrehozása
[Automatizálási változók](..\automation\automation-variables.md) az Automation-fiók minden forgatókönyve használt értékek tárolásához.  Akkor runbookok rugalmasabb azáltal, hogy ezek az értékek módosítása az aktuális runbook szerkesztése nélkül. A HTTP-adatokat gyűjtő API minden kérelemnél szükséges azonosítója és kulcsa az OMS-munkaterület, és a változó eszközök ideálisak ezek az információk tárolására.  

![Változók](media/operations-management-suite-runbook-datacollect/variables.png)

1. Az Azure-portálon lépjen az Automation-fiók.
2. Válassza ki **változók** alatt **megosztott erőforrások**.
2. Kattintson a **változó hozzáadása** , és hozzon létre két változót a következő táblázatban.

| Tulajdonság | Munkaterület-azonosító értéke | Munkaterület-kulcs értéke |
|:--|:--|:--|
| Name (Név) | WorkspaceId | WorkspaceKey |
| Típus | Karakterlánc | Karakterlánc |
| Érték | Illessze be a Naplóelemzési munkaterület a munkaterület azonosítója. | Illessze be kell jelentkezniük az elsődleges vagy másodlagos kulcsot a Naplóelemzési munkaterületet. |
| Titkosított | Nem | Igen |



## <a name="3-create-runbook"></a>3. Runbook létrehozása

Azure Automation szolgáltatásbeli szerkesztővé rendelkezik a portálon, ahol szerkesztheti, és tesztelje a forgatókönyvet.  Lehetősége van a parancsprogram-szerkesztő segítségével dolgozhat [PowerShell közvetlenül](../automation/automation-edit-textual-runbook.md) vagy [létrehozhat egy grafikus](../automation/automation-graphical-authoring-intro.md).  Ebben az oktatóanyagban a PowerShell-parancsfájllal fog működni. 

![Forgatókönyv szerkesztése](media/operations-management-suite-runbook-datacollect/edit-runbook.png)

1. Nyissa meg az Automation-fiók.  
2. Kattintson a **Runbookok** > **hozzáadása egy runbook** > **hozzon létre egy új runbookot**.
3. A runbook nevét, írja be a következőt **gyűjtése-Automation-feladat**.  Válassza ki a runbook típusa **PowerShell**.
4. Kattintson a **létrehozása** hozza létre a runbookot és a szerkesztő elindításához.
5. Másolja és illessze be a következő kódot a runbookot.  Tekintse meg a megjegyzéseket, a kód ismertetése a parancsfájlban.
    
        # Get information required for the automation account from parameter values when the runbook is started.
        Param
        (
            [Parameter(Mandatory = $True)]
            [string]$resourceGroupName,
            [Parameter(Mandatory = $True)]
            [string]$automationAccountName
        )
        
        # Authenticate to the Automation account using the Azure connection created when the Automation account was created.
        # Code copied from the runbook AzureAutomationTutorial.
        $connectionName = "AzureRunAsConnection"
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
        Connect-AzureRmAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
        
        # Set the $VerbosePreference variable so that we get verbose output in test environment.
        $VerbosePreference = "Continue"
        
        # Get information required for Log Analytics workspace from Automation variables.
        $customerId = Get-AutomationVariable -Name 'WorkspaceID'
        $sharedKey = Get-AutomationVariable -Name 'WorkspaceKey'
        
        # Set the name of the record type.
        $logType = "AutomationJob"
        
        # Get the jobs from the past hour.
        $jobs = Get-AzureRmAutomationJob -ResourceGroupName $resourceGroupName -AutomationAccountName $automationAccountName -StartTime (Get-Date).AddHours(-1)
        
        if ($jobs -ne $null) {
            # Convert the job data to json
            $body = $jobs | ConvertTo-Json
        
            # Write the body to verbose output so we can inspect it if verbose logging is on for the runbook.
            Write-Verbose $body
        
            # Send the data to Log Analytics.
            Send-OMSAPIIngestionFile -customerId $customerId -sharedKey $sharedKey -body $body -logType $logType -TimeStampField CreationTime
        }


## <a name="4-test-runbook"></a>4. Runbook tesztelése
Azure Automation szolgáltatásbeli tartalmaz egy környezet [tesztelje a forgatókönyvet](../automation/automation-testing-runbook.md) közzététel előtt.  Vizsgálja meg a runbook által gyűjtött adatokat, és győződjön meg arról, hogy ír Naplóelemzési éles történő közzététel előtt várt módon. 
 
![Runbook tesztelése](media/operations-management-suite-runbook-datacollect/test-runbook.png)

6. Kattintson a **mentése** menteni a runbookot.
1. Kattintson a **teszt ablaktábla** való nyissa meg a runbookot a tesztkörnyezetben.
3. A runbook paraméterekkel rendelkezik, mivel érték megadására kéri.  Adja meg az erőforráscsoport nevét, és az automatizálás figyelembe, hogy a folyamatban lévő feladat adatainak gyűjtéséről.
4. Kattintson a **Start** a Start a runbookot.
3. A runbook elindul állapotú **várakozik** előtt kerül **futtató**.  
3. A runbook részletes kimenet megjelenjen a feladatok gyűjtött json formátumban.  Ha nincsenek feladatok jelenik meg, majd lépett nincsenek feladatok az elmúlt órában az automation-fiókban létrehozni.  Minden olyan forgatókönyvben indítsa el az automation-fiókban, és végezze el ismét a vizsgálatot.
4. Gondoskodjon arról, hogy a kimeneti szolgáltatáshoz nem mutatja ki a hibákat a post parancsot.  A következőhöz hasonló üzenetet kell rendelkeznie.

    ![POST kimeneti](media/operations-management-suite-runbook-datacollect/post-output.png)

## <a name="5-verify-records-in-log-analytics"></a>5. A Naplóelemzési rekordjainak ellenőrzése
Ha a runbook-teszt befejeződött, és ellenőrizte, hogy a kimeneti sikeresen megérkezett, ellenőrizheti, a rekordok használatával lettek létrehozva egy [napló keresés a Naplóelemzési](../log-analytics/log-analytics-log-searches.md).

![Kimenet](media/operations-management-suite-runbook-datacollect/log-output.png)

1. Válassza ki a Naplóelemzési munkaterület az Azure-portálon.
2. Kattintson a **keresési jelentkezzen**.
3. Írja be a következő parancsot `Type=AutomationJob_CL` , majd kattintson a Keresés gombra. Vegye figyelembe, hogy a rekordtípust, amely nincs meghatározva a parancsfájl _CL tartalmaz.  Adott utótagot automatikusan fűz hozzá a hibanapló-típus azt jelzi, hogy az egy egyéni napló típusa.
4. Meg kell jelennie egy vagy több rekordot adott vissza, amely azt jelzi, hogy a várt módon működik-e a runbookot.


## <a name="6-publish-the-runbook"></a>6. A runbook közzététele
Miután ellenőrizte, hogy, hogy a runbook megfelelően működik-e, kell közzétenni ahhoz az éles környezetben futtatható.  Továbbra is szerkesztése és a runbook tesztelése a közzétett változatban módosítása nélkül.  

![Runbook közzététele](media/operations-management-suite-runbook-datacollect/publish-runbook.png)

1. Térjen vissza az automation-fiók.
2. Kattintson a **Runbookok** válassza **gyűjtése-Automation-feladat**.
3. Kattintson a **szerkesztése** , majd **közzététele**.
4. Kattintson a **Igen** Ha kéri, hogy ellenőrizze, hogy szeretné-e felülírja a korábban közzétett verziót.

## <a name="7-set-logging-options"></a>7. Naplózási beállítások megadása 
A vizsgálat sikerült megtekintéséhez [részletes kimenet](../automation/automation-runbook-output-and-messages.md#message-streams) , mert a parancsfájl a $VerbosePreference változó beállítása.  Termelési környezetben a runbook naplózási tulajdonságainak beállításához, ha meg szeretné jeleníteni a részletes kimenet kell.  Az ebben az oktatóanyagban használt runbookot Ez a json-adatokat küldi el a Naplóelemzési jelenik meg.

![Naplózás és nyomkövetés](media/operations-management-suite-runbook-datacollect/logging.png)

1. Válassza ki a runbook tulajdonságok **naplózás és nyomkövetés** alatt **Runbookbeállítások**.
2. A beállításának módosítása **részletes rekordok naplózására** való **a**.
3. Kattintson a **Save** (Mentés) gombra.

## <a name="8-schedule-runbook"></a>8. Runbook ütemezése
A leggyakoribb figyelési adatokat összegyűjtő runbook-indítási módja ütemezése úgy, hogy automatikusan elindul.  Hozzon létre ehhez a [Azure Automation ütemezési](../automation/automation-schedules.md) és a runbookban való csatlakoztatás.

![Runbook ütemezése](media/operations-management-suite-runbook-datacollect/schedule-runbook.png)

1. Válassza ki a runbook tulajdonságok, **ütemezések** alatt **erőforrások**.
2. Kattintson a **ütemezés hozzáadása** > **ütemezés kapcsolása a forgatókönyvhöz** > **hozzon létre egy új ütemezést**.
5. Adja meg az ütemezés a következő értékeket, és kattintson a **létrehozása**.

| Tulajdonság | Érték |
|:--|:--|
| Name (Név) | AutomationJobs-óránként |
| Kezdés | Válassza ki, amikor legalább 5 perccel későbbi, mint a jelenlegi időpont. |
| Ismétlődés | Ismétlődő |
| Ismétlődés minden | 1 óra |
| Készlet lejárati | Nem |

Az ütemezés létrehozása után kell beállítani, amely minden alkalommal, amikor az ütemezés elindítja a runbookot használjuk paraméterértékek.

6. Kattintson a **konfigurálása paraméterek és futtatási beállítások**.
7. Adja meg az értékeket a **ResourceGroupName** és **AutomationAccountName**.
8. Kattintson az **OK** gombra. 

## <a name="9-verify-runbook-starts-on-schedule"></a>9. Ellenőrizze a runbook elindul az ütemezés szerint
Egy runbook indítását, Everytime [feladat jön létre](../automation/automation-runbook-execution.md) és a kimenetet a naplóba.  Valójában ezek a ugyanazt a feladatot, amely runbook gyűjt.  Ellenőrizheti, hogy a runbook által a runbookhoz tartozó feladatok után az ütemezés kezdési idejét megfelelően elindul-e.

![Feladatok](media/operations-management-suite-runbook-datacollect/jobs.png)

1. Válassza ki a runbook tulajdonságok, **feladatok** alatt **erőforrások**.
2. Minden alkalommal, amikor a runbook elindításának feladatok listáját kell megjelennie.
3. Kattintson az egyik feladatához a részletek megtekintéséhez.
4. Kattintson a **összes napló** a naplók megtekintéséhez, és kimeneti a runbookból.
5. Görgessen az alsó hasonló az alábbi képen található bejegyzés.<br>![Részletes](media/operations-management-suite-runbook-datacollect/verbose.png)
6. Ez a bejegyzés Naplóelemzési küldött json részletes adatainak megtekintéséhez kattintson.



## <a name="next-steps"></a>További lépések
- Használjon [adatforrásnézet-tervezőből](../log-analytics/log-analytics-view-designer.md) a Naplóelemzési tárház összegyűjtött adatokat megjelenítő nézet létrehozásához.
- A runbookot a csomag egy [felügyeleti megoldás](operations-management-suite-solutions-creating.md) terjeszteni az ügyfél számára.
- További információ [Naplóelemzési](https://docs.microsoft.com/azure/log-analytics/).
- További információ [Azure Automation](https://docs.microsoft.com/azure/automation/).
- További információ a [HTTP adatait gyűjtője API](../log-analytics/log-analytics-data-collector-api.md).
