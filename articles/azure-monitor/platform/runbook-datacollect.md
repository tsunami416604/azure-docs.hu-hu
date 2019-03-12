---
title: Az Azure Automation runbookok Log Analytics-adatok gyűjtése |} A Microsoft Docs
description: Lépésenkénti útmutató, amely végigvezeti Önt egy runbook létrehozása az Azure Automation-adatok gyűjtésére a tárházba elemzése a Log Analytics által.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/27/2017
ms.author: bwren
ms.openlocfilehash: 4f6076407ea4745556e59d44bd37ab85288e6bd2
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772931"
---
# <a name="collect-data-in-log-analytics-with-an-azure-automation-runbook"></a>Adatgyűjtés, a Log Analytics az Azure Automation-runbook

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Számos különféle forrásból például gyűjthet adatokat a Log Analytics jelentős mennyiségű [adatforrások](../../azure-monitor/platform/agent-data-sources.md) ügynökökön és is [adatokat gyűjteni az Azure-ból](../../azure-monitor/platform/collect-azure-metrics-logs.md). Vannak olyan forgatókönyvek ellenére, hogy hol kell gyűjtenie az adatokat, amely nem érhető el standard források segítségével. Ezekben az esetekben használhatja a [HTTP-adatgyűjtő API](../../azure-monitor/platform/data-collector-api.md) használatával írhat adatokat a Log Analyticsbe bármely REST API-ügyfélből. Egy általános módszer az adatgyűjtést az Azure Automation runbook használ.

Ez az oktatóanyag végigvezeti a folyamat létrehozásához és az adatok írása a Log Analytics az Azure Automation runbook ütemezése.

## <a name="prerequisites"></a>Előfeltételek
Ebben a forgatókönyvben a következő erőforrások konfigurált az Azure-előfizetés szükséges. Mindkettő egy ingyenes fiók is lehet.

- [Log Analytics-munkaterület](../../azure-monitor/learn/quick-create-workspace.md).
- [Azure automation-fiók](../..//automation/automation-quickstart-create-account.md).

## <a name="overview-of-scenario"></a>Forgatókönyv áttekintése
Ebben az oktatóanyagban egy runbook Automation-feladatokkal kapcsolatos információkat összegyűjtő fog írni. Az Azure Automation Runbookjai vannak megvalósítva a PowerShell-lel, így fogja kezdeni a és a egy parancsfájl tesztelésre az Azure Automation-szerkesztőben. Miután meggyőződött arról, hogy a szükséges adatokat gyűjt, fog adatokat írni a Log Analytics és az egyéni adattípus ellenőrzése. Végül létre fog hozni egy ütemezést, rendszeres időközönként a runbook elindításához.

> [!NOTE]
> Azure Automation-feladat adatok küldése a Log Analytics ennek a runbooknak nélkül is beállíthatja. Ebben a forgatókönyvben elsősorban az oktatóanyag támogatásához, és javasolt, hogy küldjön az adatokat egy tesztelési munkaterületet.

## <a name="1-install-data-collector-api-module"></a>1. Adatgyűjtő API-modul telepítése
Minden [kérelme. a HTTP-adatgyűjtő API](../../azure-monitor/platform/data-collector-api.md#create-a-request) megfelelően kell formázni, és a egy engedélyezési fejléc. Ezt megteheti a runbookban is, de csökkentheti a modul, amely leegyszerűsíti a folyamatot a szükséges kód. Egy modul használható [OMSIngestionAPI modul](https://www.powershellgallery.com/packages/OMSIngestionAPI) a PowerShell-galériában.

Használata egy [modul](../../automation/automation-integration-modules.md) egy runbook kell telepíteni az Automation-fiókban.  Minden runbook ugyanazt a fiókot az a funkciók a modul használhatja. Telepíthet új modul kiválasztásával **eszközök** > **modulok** > **modul hozzáadása** az Automation-fiókban.

A PowerShell-galériából, ha lehetővé teszi egy gyors lehetőség, amely közvetlenül üzembe helyezhetők a modul az automation-fiók ebben az oktatóanyagban használhassa ezt a lehetőséget.

![OMSIngestionAPI module](media/runbook-datacollect/OMSIngestionAPI.png)

1. Lépjen a [PowerShell-galériából](https://www.powershellgallery.com/).
2. Keresse meg **OMSIngestionAPI**.
3. Kattintson a **üzembe helyezés az Azure Automation** gombra.
4. Válassza ki az automation-fiókját, és kattintson a **OK** a modul telepítése.

## <a name="2-create-automation-variables"></a>2. Automation-változók létrehozása
[Automation-változók](../../automation/automation-variables.md) értékeket, amelyek segítségével az Automation-fiók az összes runbook tárolásához. Vállalnak a runbookok rugalmasabb azáltal, hogy ezeket az értékeket módosítsa a tényleges runbook szerkesztése nélkül. A HTTP-adatgyűjtő API érkező minden kérés szükséges a Azonosítóját és kulcsát a Log Analytics-munkaterület, és a változó adategységek ideálisak az adattárolásra.

![Változók](media/runbook-datacollect/variables.png)

1. Az Azure Portalon lépjen az Automation-fiók.
2. Válassza ki **változók** alatt **megosztott erőforrások**.
2. Kattintson a **változó hozzáadása** , és hozzon létre két változót a következő táblázatban.

| Tulajdonság | Munkaterület-azonosító értéke | Munkaterület kulcs-érték |
|:--|:--|:--|
| Name (Név) | WorkspaceId | WorkspaceKey |
| Typo | String | String |
| Érték | Illessze be a munkaterület Azonosítóját, a Log Analytics-munkaterületre. | Beillesztés jelentkezzen be az elsődleges vagy másodlagos kulcsot a Log Analytics-munkaterület. |
| Titkosított | Nem | Igen |

## <a name="3-create-runbook"></a>3. Runbook létrehozása

Az Azure Automation-szerkesztő rendelkezik a portálon, ahol szerkesztheti, és tesztelje a forgatókönyvet. Lehetősége van a parancsprogram-szerkesztő használatával dolgozhat [közvetlenül a PowerShell](../../automation/automation-edit-textual-runbook.md) vagy [grafikus runbook létrehozása](../../automation/automation-graphical-authoring-intro.md). Ebben az oktatóanyagban egy PowerShell-parancsprogrammal fog működni.

![Forgatókönyv szerkesztése](media/runbook-datacollect/edit-runbook.png)

1. Keresse meg az Automation-fiók.
2. Kattintson a **Runbookok** > **forgatókönyv hozzáadása** > **hozzon létre egy új runbookot**.
3. A runbook nevéhez, írja be a **gyűjtése – Automation-feladat**. A forgatókönyv típusának kiválasztása **PowerShell**.
4. Kattintson a **létrehozás** hozza létre a runbookot, és-szerkesztő elindításához.
5. Másolja és illessze be a következő kódot a runbookot. Tekintse meg a megjegyzéseket, a parancsfájl a kód ismertetése.
    ```
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
    Connect-AzAccount `
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
    $jobs = Get-AzAutomationJob -ResourceGroupName $resourceGroupName -AutomationAccountName $automationAccountName -StartTime (Get-Date).AddHours(-1)
    
    if ($jobs -ne $null) {
        # Convert the job data to json
        $body = $jobs | ConvertTo-Json
    
        # Write the body to verbose output so we can inspect it if verbose logging is on for the runbook.
        Write-Verbose $body
    
        # Send the data to Log Analytics.
        Send-OMSAPIIngestionFile -customerId $customerId -sharedKey $sharedKey -body $body -logType $logType -TimeStampField CreationTime
    }
    ```

## <a name="4-test-runbook"></a>4. Runbook tesztelése
Az Azure Automation tartalmaz egy környezetet [tesztelheti a runbookját](../../automation/automation-testing-runbook.md) közzététel előtt. Vizsgálja meg a runbook által összegyűjtött adatokat, és győződjön meg arról, hogy ír a Log Analytics, a várható éles környezetben történő közzététel előtt.

![Runbook tesztelése](media/runbook-datacollect/test-runbook.png)

6. Kattintson a **mentése** , mentse a forgatókönyvet.
1. Kattintson a **teszt panel** , nyissa meg a runbookot a tesztkörnyezetben.
3. A runbook paraméterekkel rendelkezik, mivel érték megadására kéri. Adja meg az erőforráscsoport nevét, és az automation-fiók, amely a folyamatban lévő feladat adatainak gyűjtésére.
4. Kattintson a **Start** a Start a runbookot.
3. A runbook állapota kezdődik **várólistán** előtt kerül **futó**.
3. A runbook részletes kimenet megjelenjen a feladatokkal gyűjtött json formátumban. Ha nincsenek feladatok sem jelenik meg, majd nem lehetséges, hogy történt nincsenek feladatok az elmúlt órában az automation-fiókban létrehozott. Próbálja meg elindítani minden runbook az automation-fiókban, és hajtsa végre újból a vizsgálatot.
4. Győződjön meg arról, a kimenet a Log Analytics nem mutassa a hibákat a post parancsot. A következőhöz hasonló üzenetet kell rendelkeznie.

    ![POST-kimenet](media/runbook-datacollect/post-output.png)

## <a name="5-verify-records-in-log-analytics"></a>5. A Log Analytics ellenőrzése
Miután befejeződött a runbook tesztelése, és ellenőrizte, hogy a kimenet sikeresen megérkezett, ellenőrzéséhez, hogy a rekordok használatával létrehozott egy [a Log Analytics naplóbeli keresés](../../azure-monitor/log-query/log-query-overview.md).

![Napló kimenetét](media/runbook-datacollect/log-output.png)

1. Az Azure Portalon válassza ki a Log Analytics-munkaterületre.
2. Kattintson a **naplóbeli keresés**.
3. Írja be a következő parancsot `Type=AutomationJob_CL` , és kattintson a Keresés gombra. Vegye figyelembe, hogy typ záznamu tartalmazza, amely nincs meghatározva a parancsfájl _CL. Névutótagot automatikusan hozzáfűzi a napló típusa jelzi, hogy ez egy egyéni napló típusa.
4. Egy vagy több rekordot ad vissza, amely azt jelzi, hogy a várt módon működik-e a runbook kell megjelennie.

## <a name="6-publish-the-runbook"></a>6. A runbook közzététele
Miután meggyőződött arról, hogy a runbook megfelelően működik-e, kell közzétenni, így éles környezetben is futtatható. Továbbra is szerkesztheti, és a runbook tesztelése a közzétett verzió módosítása nélkül.

![Runbook közzététele](media/runbook-datacollect/publish-runbook.png)

1. Térjen vissza az automation-fiók.
2. Kattintson a **Runbookok** válassza **gyűjtése – Automation-feladat**.
3. Kattintson a **szerkesztése** , majd **közzététele**.
4. Kattintson a **Igen** Ha a rendszer kéri, győződjön meg arról, hogy szeretné-e felülírja a korábban közzétett verziót.

## <a name="7-set-logging-options"></a>7. Naplózás beállítása
A vizsgálat sikeresen megtekintéséhez [részletes kimenet](../../automation/automation-runbook-output-and-messages.md#message-streams) , mert a $VerbosePreference változó állítsa be a szkriptet. Éles környezetben a runbook naplózási tulajdonságainak beállítása, ha meg szeretné jeleníteni a részletes kimenet kell. A runbook a jelen oktatóanyagban használt Ez megjeleníti a json-adatokat küld a Log Analytics.

![Naplózás és nyomkövetés](media/runbook-datacollect/logging.png)

1. A runbook tulajdonságai között nyissa **naplózás és nyomkövetés** alatt **Runbookbeállítások**.
2. A beállításának módosítása **részletes rekordok naplózására** való **a**.
3. Kattintson a **Save** (Mentés) gombra.

## <a name="8-schedule-runbook"></a>8. Forgatókönyv ütemezése
Indítson el egy runbookot, amely monitorozási adatokat gyűjt a leggyakoribb módja az automatikus futásra ütemezheti. Hozzon létre ehhez a [az Azure Automation ütemezési](../../automation/automation-schedules.md) és a runbookban való csatlakoztatás irányába.

![Forgatókönyv ütemezése](media/runbook-datacollect/schedule-runbook.png)

1. A runbook tulajdonságai között nyissa **ütemezések** alatt **erőforrások**.
2. Kattintson a **ütemezés hozzáadása** > **összekapcsolhat egy ütemezést a runbook** > **új ütemezés létrehozása**.
5. Írja be az ütemezés a következő értékeket, és kattintson a **létrehozás**.

| Tulajdonság | Érték |
|:--|:--|
| Name (Név) | AutomationJobs-óránként |
| Kezdés | Válassza ki, amikor legalább 5 percnek korábbi az aktuális idő. |
| Ismétlődés | Ismétlődő |
| Ismétlődés minden | 1 óra |
| Elévülés beállítása | Nem |

Az ütemezés létrehozása után kell beállítani, amely minden alkalommal, amikor az ütemezés elindítja a runbookot használható paraméter értékét.

6. Kattintson a **konfigurálása paraméterek és futtatási beállítások**.
7. Adja meg az értékeket a **ResourceGroupName** és **AutomationAccountName**.
8. Kattintson az **OK** gombra.

## <a name="9-verify-runbook-starts-on-schedule"></a>9. Runbook indítása a ütemezés ellenőrzése
Minden alkalommal, amikor egy runbook elindult, [létrejön egy feladat](../../automation/automation-runbook-execution.md) és a kimenetet a naplóba. Sőt ezek a ugyanazt a feladatot, amely a runbook gyűjti. Ellenőrizheti, hogy a runbookot a runbook a feladatok ellenőrzésével, az ütemezés kezdési ideje után megfelelően elindul-e.

![Feladatok](media/runbook-datacollect/jobs.png)

1. A runbook tulajdonságai között nyissa **feladatok** alatt **erőforrások**.
2. Minden alkalommal, amikor a runbook elindításának feladatok listáját kell megjelennie.
3. Kattintson az egyik a feladatok a részletek megtekintéséhez.
4. Kattintson a **az összes napló** tekintse át a naplókat, és a runbook kimenete.
5. Görgessen az alul található egy bejegyzés az alábbi képen láthatóhoz hasonló.<br>![Részletes](media/runbook-datacollect/verbose.png)
6. Ez a bejegyzés a részletes json-adatokat a Log Analytics szolgáltatásba küldött megtekintéséhez kattintson.

## <a name="next-steps"></a>További lépések
- Használat [adatforrásnézet-tervezőből](../../azure-monitor/platform/view-designer.md) a Log Analytics-adattárban gyűjtött adatok megjelenítésének nézet létrehozásához.
- A runbook a csomag egy [felügyeleti megoldás](../../azure-monitor/insights/solutions-creating.md) az ügyfelek számára terjeszteni.
- Tudjon meg többet [Log Analytics](https://docs.microsoft.com/azure/log-analytics/).
- Tudjon meg többet [Azure Automation](https://docs.microsoft.com/azure/automation/).
- Tudjon meg többet a [HTTP-adatgyűjtő API](../../azure-monitor/platform/data-collector-api.md).
