---
title: Használja a riasztást egy Azure Automation-runbook elindítása
description: Információ kezdeményezi egy runbook fut, amikor Azure riasztást hoz létre.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 2a226a348df4f289dd68924e24b8d4b374a87766
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Használja a riasztást egy Azure Automation-runbook elindítása

Használhat [Azure figyelő](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md?toc=%2fazure%2fautomation%2ftoc.json) figyelése talál szintekhez tartozó metrikákat és naplókat a legtöbb szolgáltatások az Azure-ban. Azure Automation-runbook meghívása használatával [művelet csoportok](../monitoring-and-diagnostics/monitoring-action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) vagy klasszikus riasztásokat a riasztások alapján feladatok automatizálásához. Ez a cikk bemutatja, hogyan konfigurálhatja és riasztások segítségével egy runbook futtatására.

## <a name="alert-types"></a>Riasztástípusok

Automation-runbook három riasztástípusok használható:
* Klasszikus metrika riasztások
* Tevékenység napló riasztások
* Közel valós idejű metrika riasztások

Riasztás egy runbookot hív, a tényleges hívás esetén a webhook HTTP POST-kérelmet. A POST kérelem törzsében egy JSON-formátumú objektum, amely hasznos a riasztáshoz kapcsolódó tulajdonságokkal rendelkezik. A következő táblázat felsorolja a hasznos séma az egyes riasztási mutató hivatkozásokat:

|Riasztás  |Leírás|Hasznos séma  |
|---------|---------|---------|
|[Klasszikus metrika riasztás](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |Értesítést küld, ha minden platform szintű metrika megfelel egy adott feltétel. Például, ha az érték **CPU %** a virtuális gép érték nagyobb, mint **90** az elmúlt 5 percben.| [Osztály metrika riasztási hasznos séma](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[Figyelmeztetés a napló](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Értesítést küld, amikor az Azure tevékenységnapló bármely új esemény megfelel a megadott feltételek. Például, ha egy `Delete VM` művelethez a **myProductionResourceGroup** , vagy ha egy új Azure szolgáltatás állapotesemény, amelynél egy **aktív** állapot jelenik meg.| [Műveletnapló riasztási hasznos séma](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)        |
|[Valós idejű metrika figyelmeztetés közelében](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Gyorsabb, mint az metrika riasztásokat küld értesítést, amikor egy vagy több platform szintekhez tartozó metrikákat a megadott feltételeknek megfelelő. Például, ha az értéke **CPU %** a virtuális gép érték nagyobb, mint **90**, és az értéke **hálózati a** nagyobb, mint **500 MB** az elmúlt 5 perc.| [Közel valós idejű metrika riasztási hasznos séma](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

Mivel az egyes riasztás által biztosított adatok különböző, minden riasztási típus eltérően kell kezelni. A következő szakaszban megtanulhatja a különböző típusú riasztások kezelésének runbook létrehozása.

## <a name="create-a-runbook-to-handle-alerts"></a>Hozzon létre egy runbookot, a riasztások kezelése

A riasztások Automation használatához logika, amely kezeli a riasztási JSON-adattartalmat a runbook átadott rendelkező runbook kell létrehoznia. A következő példa runbook az Azure-riasztás alapján kell meghívni. 

Az előző szakaszban leírtak minden típusú riasztás rendelkezik egy másik sémát. A parancsfájl a webhook adatai fogadja el a `WebhookData` runbook bemeneti paraméter a riasztást. Ezt követően a parancsfájl kiértékeli a JSON-adattartalmat határozza meg, melyik riasztási típus lett megadva. 

A példa egy riasztás egy virtuális gépről. A virtuális gép adatai lekéri a tartalmat, és ezt az információt használja fel a virtuális gép leállítása. A kapcsolat kell kell beállítani az Automation-fiók ahol a runbook futtatja.

A runbook felhasználja a **AzureRunAsConnection** [Futtatás mint fiók](automation-create-runas-account.md) hitelesítéséhez az Azure felügyeleti művelethez nyújt a virtuális gépek ellen.

Ebben a példában segítségével létrehozhat egy nevű **Stop-AzureVmInResponsetoVMAlert**. Módosítsa a PowerShell-parancsfájlt, és számos különböző erőforrások együtt használja azt.

1. Ugrás az Azure Automation-fiók.
1. A **folyamat**, jelölje be **Runbookok**.
1. A runbookok a lista tetején, válassza ki a **hozzáadása egy runbook**. 
1. A **Runbook hozzáadása** oldalon válassza a **Gyors létrehozás** lehetőséget.
1. Adja meg a runbook neve **Stop-AzureVmInResponsetoVMAlert**. Válassza ki a runbook típusa **PowerShell**. Ezután kattintson a **Létrehozás** elemre.  
1. Másolja be a következő PowerShell-példa a **szerkesztése** ablaktáblán. 

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
1. Válassza ki **közzététel** mentéséhez és közzétenni a runbookot.

## <a name="create-an-action-group"></a>A művelet-csoport létrehozása

Egy olyan riasztást kiváltó műveletek gyűjteménye. A forgatókönyvek olyan csak egy művelet csoportok használatával számos műveleteket.

1. Válassza ki az Azure-portálon **figyelő** > **beállítások** > **művelet csoportok**.
1. Válassza ki **művelet csoport hozzáadása**, majd adja meg a szükséges adatokat:  
    1. Az a **művelet csoportnév** adjon meg egy nevet.
    1. Az a **rövid név** adjon meg egy nevet. A rövid nevét használja a teljes műveletet csoport neve helyett Ha értesítéseket küld a művelet csoport használatával.
    1. A **előfizetés** mezőben automatikusan kitölti a jelenlegi előfizetéséhez. Ez azért, amelyben a művelet csoport menti az előfizetést.
    1. Válassza ki az erőforráscsoportot, amelyben a művelet csoport mentett.

Ehhez a példához hoz létre két műveletek: a runbook műveletet és egy értesítési műveletet.

### <a name="runbook-action"></a>Runbook-művelet

A runbook műveletet a művelet csoport létrehozásához:

1. A **műveletek**, a **MŰVELETNÉV**, adjon meg egy nevet a művelethez. A **MŰVELETTÍPUS**, jelölje be **Automation-Runbook**.
1. A **részletek**, jelölje be **részletes Szerkesztés**.  
1. Az a **konfigurálása Runbook** lap **Runbook forrás**, jelölje be **felhasználói**.  
1. Válassza ki a **előfizetés** és **Automation-fiók**, majd válassza ki a **Stop-AzureVmInResponsetoVMAlert** runbook.  
1. Ha elkészült, válassza ki a **OK**.

### <a name="notification-action"></a>Az értesítési művelet

A művelet csoport létrehozása egy értesítési műveletet:

1. A **műveletek**, a **MŰVELETNÉV**, adjon meg egy nevet a művelethez. A **MŰVELETTÍPUS**, jelölje be **E-mail**.  
1. A **részletek** kijelöléséhez **részletes Szerkesztés**.  
1. Az a **E-mail** lapján írja be az értesítéshez, és válassza az e-mail cím **OK**. Az e-mail címet, a runbook mellett hozzáadása művelet bizonyulhat hasznosnak. Értesítést kap a runbook indítását.  

    A művelet csoport a következő kép kell hasonlítania:

   ![A művelet egy kiszolgálócsoport lapját hozzáadása](./media/automation-create-alert-triggered-runbook/add-action-group.png)
1. A művelet csoport létrehozásához válassza **OK**.

Ez a művelet csoport is használhatja a [napló tevékenységriasztásokat](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) és [majdnem valós idejű riasztások](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal) az Ön által létrehozott.

## <a name="classic-alert"></a>Klasszikus riasztás

Klasszikus riasztások metrikák alapulnak, és ne használjon művelet csoportok. Azonban állíthat be a runbook műveletet a klasszikus riasztások alapján. 

A klasszikus riasztások létrehozásához:

1. Válassza a **Metrikariasztás hozzáadása** lehetőséget.
1. A metrika riasztás neve **myVMCPUAlert**. Adja meg a riasztás rövid leírását.
1. A metrika riasztási feltétel kiválasztása **nagyobb, mint**. Az a **küszöbérték** érték, jelölje be **10**. Az a **időszak** érték, jelölje be **az elmúlt öt percben**.
1. A **hajtsa végre a műveletet**, jelölje be **futtassa ezt a riasztást egy runbook**.
1. Az a **konfigurálása Runbook** lap, a **Runbook forrás**, jelölje be **felhasználói**. Válassza ki az automation-fiók, és válassza ki a **Stop-AzureVmInResponsetoVMAlert** runbook. Kattintson az **OK** gombra.
1. Válassza ki a riasztási szabály mentése, **OK**.

## <a name="next-steps"></a>További lépések

* Az Automation-runbook indítása a webhook használatával kapcsolatos további információkért lásd: [runbook indítása a webhook](automation-webhooks.md).
* Runbook-indítási módok részletekért lásd: [runbook indítása](automation-starting-a-runbook.md).
* Tevékenység napló riasztás létrehozása, lásd: [tevékenység létrehozása napló riasztások](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Riasztás létrehozása, a közel valós idejű lásd: [riasztási szabályt létrehozni az Azure portálon](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal).
