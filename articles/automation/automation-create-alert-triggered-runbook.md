---
title: Használjon olyan riasztást, egy Azure Automation-runbook elindítása
description: Ismerje meg, hogyan kezdeményezi egy runbook futtatását, amikor az Azure riasztást hoz létre.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/18/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 98de7a84dc388f74c64d7c265d2ce8ed32995a5a
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2018
ms.locfileid: "48784775"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Használjon olyan riasztást, egy Azure Automation-runbook elindítása

Használhat [Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) figyelése base-szintű metrikákat és naplókat a legtöbb szolgáltatás az Azure-ban. Azure Automation-runbookok használatával meghívhatja [Műveletcsoportok](../monitoring-and-diagnostics/monitoring-action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) vagy a klasszikus riasztások segítségével automatizálhatja a feladatokat a riasztások alapján. Ez a cikk bemutatja, hogyan konfigurálásához és a egy runbook futtatására riasztások használatával.

## <a name="alert-types"></a>Riasztástípusok

Automation-runbookok három riasztási típusokkal használható:
* Klasszikus metrikariasztásokat
* Tevékenységnapló-riasztások
* Közel valós idejű metrikákhoz kapcsolódó riasztások

Riasztás a runbookot hívja, a tényleges hívás esetén, a webhook HTTP POST-kérelmet. A POST-kérés törzse tartalmazza a JSON-formátumú objektum, amelynek hasznosak a riasztáshoz kapcsolódó tulajdonságok. Az alábbi táblázat az egyes riasztástípusok tartalom sémáját mutató hivatkozásokat:

|Riasztás  |Leírás|Adattartalom-séma  |
|---------|---------|---------|
|[Klasszikus metrikariasztás](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |Értesítést küld, ha bármely platformszintű metrika megfelel egy adott feltételnek. Például, ha az érték **százalékos processzorhasználatról** a virtuális gép értéke nagyobb, mint **90** az elmúlt 5 percben.| [Metrikaalapú riasztás adattartalom-osztályséma](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[Tevékenységnapló-riasztás](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Értesítést küld minden olyan új esemény, az Azure tevékenységnaplójában megegyezik a megadott feltételeknek. Például, hogy amikor egy `Delete VM` művelethez az szükséges, **myProductionResourceGroup** , vagy ha egy új Azure Service Health-esemény az egy **aktív** állapot jelenik meg.| [Műveletnapló-adattartalom riasztási séma](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md)        |
|[Közel valós idejű metrikariasztás](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Értesítés gyorsabban metrikákhoz kapcsolódó riasztások küldése, ha egy vagy több platform-szintű metrikákat megfelel egy bizonyos feltételnek. Például, hogy amikor értéke **processzorhasználatról** a virtuális gép értéke nagyobb, mint **90**, és az értéke **bejövő** nagyobb, mint **500 MB-os** az elmúlt 5 perc.| [Közel valós idejű metrikaalapú riasztási adattartalom-séma](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

Mivel az adatok különböző típusú riasztás által biztosított különböző, az egyes riasztástípusok eltérően kell kezelni. A következő szakaszban megismerheti, hogyan kezelje a különböző típusú riasztásokat runbook létrehozása.

## <a name="create-a-runbook-to-handle-alerts"></a>Hozzon létre egy runbookot, hogy a riasztások kezelése

Automation használata riasztásokkal, egy runbookot, amely rendelkezik a logika, amely kezeli a riasztási JSON-adattartalom van átadva a runbooknak szüksége. A következő példa runbook Azure riasztásból kell meghívni.

Az előző szakaszban leírtak minden riasztás típusát különböző sémákkal rendelkeznek. A parancsfájlnak a webhook-adatokat a `WebhookData` forgatókönyv bemeneti paramétere a riasztást. A parancsfájl összeveti a JSON-adattartalom meghatározni, melyik riasztási típus lett megadva.

Ebben a példában egy virtuális gépről egy riasztást. Lekéri a virtuális gép adatait a hasznos-, és ezt az információt használja majd a virtuális gép leállítása. A kapcsolat kell beállítani az Automation-fiókban, a runbook futtatása. Riasztások runbookok való használatakor fontos a runbook, amely akkor aktiválódik a riasztás állapotának ellenőrzéséhez. A runbook minden alkalommal, amikor a riasztás állapota aktivál. Riasztások több állapota rendelkezzen, a két leggyakoribb állapotok `Activated` és `Resolved`. Ellenőrizze, hogy a runbook logikai győződjön meg arról, hogy a runbook nem futtatható többször, ebben az állapotban. Ebben a cikkben szereplő példa mutatja be a keresett `Activated` csak riasztást küld.

A runbook használja a **AzureRunAsConnection** [Futtatás mint fiók](automation-create-runas-account.md) ellen a virtuális Gépet a felügyeleti művelet végrehajtásához Azure-hitelesítésre.

Ez a példa használatával hozza létre a nevű runbook **Stop-AzureVmInResponsetoVMAlert**. Módosítása a PowerShell-parancsfájlt, és számos különböző erőforrásokat használhatja.

1. Nyissa meg az Azure Automation-fiók.
1. A **FOLYAMATAUTOMATIZÁLÁS**válassza **Runbookok**.
1. A runbookok listája tetején válassza **forgatókönyv hozzáadása**. 
1. A **Runbook hozzáadása** oldalon válassza a **Gyors létrehozás** lehetőséget.
1. A runbook nevét adja meg annak **Stop-AzureVmInResponsetoVMAlert**. A forgatókönyv típusának kiválasztása **PowerShell**. Ezután kattintson a **Létrehozás** elemre.  
1. Másolja be a következő PowerShell-példa az **szerkesztése** ablaktáblán. 

    ```powershell-interactive
    <#
    .SYNOPSIS
    This runbook stops a resource management VM in response to an Azure alert trigger.

    .DESCRIPTION
    This runbook stops a resource management VM in response to an Azure alert trigger.
    The input is alert data that has the information required to identify which VM to stop.
    
    DEPENDENCIES
    - The runbook must be called from an Azure alert via a webhook.
    
    REQUIRED AUTOMATION ASSETS
    - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
    - An Automation certificate asset called "AzureRunAsCertificate".

    .PARAMETER WebhookData
    Optional. (The user doesn't need to enter anything, but the service always passes an object.)
    This is the data that's sent in the webhook that's triggered from the alert.

    .NOTES
    AUTHOR: Azure Automation Team
    LASTEDIT: 2017-11-22
    #>

    [OutputType("PSAzureOperationResponse")]

    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )

    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData.
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema).
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # The schema isn't supported.
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if ($status -eq "Activated")
        {
            $ResourceType = $AlertContext.resourceType
            $ResourceGroupName = $AlertContext.resourceGroupName
            $SubId = $AlertContext.subscriptionId
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Use this only if this is a resource management VM.
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is the VM.
                Write-Verbose "This is a resource management VM." -Verbose

                # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the VM.
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType isn't supported.
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated', so no action taken.
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```
1. Válassza ki **közzététel** menti és közzéteszi a runbookot.

## <a name="create-an-action-group"></a>Műveletcsoport létrehozása

Műveletcsoport gyűjteménye, egy riasztás kiváltó műveletek. A forgatókönyvek olyan csak egyike a számos végrehajtandó művelet csoportokkal is használhatja.

1. Az Azure Portalon válassza ki a **figyelő** > **beállítások** > **Műveletcsoportok**.
1. Válassza ki **műveleti csoport hozzáadása**, majd adja meg a szükséges adatokat:  
    1. Az a **műveletcsoport neve** adjon meg egy nevet.
    1. Az a **rövid, nevet** adjon meg egy nevet. Rövid nevét használja egy teljes műveletcsoport neve helyett a műveletcsoportban használatával elküldött értesítések.
    1. A **előfizetés** mező automatikusan kitölti a jelenlegi előfizetésében. Ez az az előfizetést, amelyben a műveletcsoport mentve van.
    1. Válassza ki az erőforráscsoportot, amelyben a rendszer menti a műveletcsoport.

Ebben a példában két műveletet hoz létre: a runbook műveletet és a egy értesítési műveletet.

### <a name="runbook-action"></a>Runbook-művelet

A runbook műveletet a műveletcsoport létrehozása:

1. A **műveletek**, a **MŰVELETNÉV**, adja meg a művelet nevét. A **MŰVELETTÍPUS**válassza **Automation-Runbook**.
1. A **részletek**válassza **részleteinek szerkesztése**.  
1. Az a **Runbook konfigurálása** lap **forgatókönyv forrása**válassza **felhasználói**.  
1. Válassza ki a **előfizetés** és **Automation-fiók**, majd válassza ki a **Stop-AzureVmInResponsetoVMAlert** runbook.  
1. Ha elkészült, válassza ki a **OK**.

### <a name="notification-action"></a>Értesítési művelet

A műveletcsoport létrehozása egy értesítési műveletet:

1. A **műveletek**, a **MŰVELETNÉV**, adja meg a művelet nevét. A **MŰVELETTÍPUS**válassza **E-mail**.  
1. A **részletek** választ ki, **részleteinek szerkesztése**.  
1. Az a **E-mail** oldalon, írja be az értesítéshez, és válassza ki az e-mail címet **OK**. Hasznos lehet egy e-mail-cím mellett a runbook hozzáadása egy műveletet. Értesítést kap a runbook indítását.  

    A műveleti csoport a következő képhez hasonlóan kell kinéznie:

   ![Adja hozzá a műveleti csoport oldalán](./media/automation-create-alert-triggered-runbook/add-action-group.png)
1. A műveletcsoport létrehozásához válassza **OK**.

Ez a művelet a csoport is használhatja a [tevékenységnapló-riasztások](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) és [közel valós idejű riasztások](../monitoring-and-diagnostics/monitoring-overview-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) létrehozott.

## <a name="classic-alert"></a>Klasszikus riasztás

Klasszikus riasztások a metrikák alapulnak, és ne használja a Műveletcsoportok. Beállíthat viszont egy runbook műveletet a klasszikus riasztás alapján. 

Egy klasszikus riasztás létrehozása:

1. Válassza a **Metrikariasztás hozzáadása** lehetőséget.
1. A metrikariasztás neve **myVMCPUAlert**. Adja meg a riasztás rövid leírását.
1. Válassza ki a metrikaalapú riasztási feltétel **nagyobb, mint**. Az a **küszöbérték** értéket, válassza ki **10**. Az a **időszak** értéket, válassza ki **az elmúlt öt percben**.
1. A **semmit**válassza **runbook futtatása ebből a riasztásból**.
1. Az a **Runbook konfigurálása** lapon a **forgatókönyv forrása**válassza **felhasználói**. Válassza ki az automation-fiókját, és válassza ki a **Stop-AzureVmInResponsetoVMAlert** runbook. Kattintson az **OK** gombra.
1. A riasztási szabály mentéséhez válassza **OK**.

## <a name="next-steps"></a>További lépések

* Automation-runbook indítása egy webhook használatával kapcsolatos további információkért lásd: [runbook indítása webhookból](automation-webhooks.md).
* Módjai, indítson el egy runbookot, lásd: [runbook elindítása](automation-starting-a-runbook.md).
* Tevékenységnapló-riasztás létrehozása kapcsolatban lásd: [tevékenységnapló-riasztások létrehozása](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Riasztás létrehozása, közel valós idejű kezelésével kapcsolatos információkért lásd: [riasztási szabályt létrehozni az Azure Portalon](../monitoring-and-diagnostics/alert-metric.md?toc=/azure/azure-monitor/toc.json).
