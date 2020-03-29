---
title: Egy Azure Automation-runbook aktiválásához használjon riasztást
description: Megtudhatja, hogyan aktiválhatja a runbook ot, ha egy Azure-riasztás tanusítani.
services: automation
ms.subservice: process-automation
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: df28116c588ed77f02c78a42a85feb91ca339e7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75366700"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Egy Azure Automation-runbook aktiválásához használjon riasztást

Az [Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) segítségével figyelheti az azure-beli szolgáltatások többségének alapszintű metrikáit és naplóit. Az Azure Automation-runbookok hívható [műveletcsoportok](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) használatával vagy klasszikus riasztások használatával a riasztásokon alapuló feladatok automatizálásához. Ez a cikk bemutatja, hogyan konfigurálhatja és futtathatja a runbook riasztások használatával.

## <a name="alert-types"></a>Riasztástípusok

Az automatizálási runbookok három riasztástípussal használhatók:

* Gyakori riasztások
* Tevékenységnapló-riasztások
* Közel valós idejű metrikariasztások

> [!NOTE]
> A közös riasztási séma szabványosítja a riasztási értesítések felhasználási élményét az Azure-ban ma. A mai azure-beli három riasztástípus (metrika, napló és tevékenységnapló) korábban saját e-mail-sablonokkal, webhook-sémákkal stb. További információ: [Common alert schema](../azure-monitor/platform/alerts-common-schema.md)

Amikor egy riasztás egy runbookot hív meg, a tényleges hívás egy HTTP POST-kérés a webhookhoz. A POST-kérelem törzse egy JSON-formátumú objektumot tartalmaz, amely a riasztáshoz kapcsolódó hasznos tulajdonságokkal rendelkezik. Az alábbi táblázat az egyes riasztástípusok hasznos adatsémájára mutató hivatkozásokat sorolja fel:

|Riasztás  |Leírás|Hasznos adatséma  |
|---------|---------|---------|
|[Gyakori riasztás](../azure-monitor/platform/alerts-common-schema.md?toc=%2fazure%2fautomation%2ftoc.json)|A közös riasztási séma, amely ma egységesíti a riasztási értesítések felhasználási élményét.|Gyakori riasztási hasznos adatséma|
|[Tevékenységnapló riasztása](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Értesítést küld, ha az Azure-tevékenységnaplóbármely új eseménye megfelel bizonyos feltételeknek. Például ha `Delete VM` egy művelet történik **a myProductionResourceGroup,** vagy ha egy új Azure Service Health esemény **aktív** állapottal jelenik meg.| [Tevékenységnapló riasztási hasznos adatsémája](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[Közel valós idejű metrikariasztás](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |Riasztást küld gyorsabban, mint a metrikariasztások, ha egy vagy több platformszintű metrika megfelel a megadott feltételeknek. Ha például egy virtuális gép **CPU%-ának** értéke nagyobb, mint **90**, és a Hálózati be értéke az elmúlt 5 **percben** **500 MB-nál** nagyobb.| [Közel valós idejű metrika riasztási hasznos adatséma](../azure-monitor/platform/alerts-webhooks.md#payload-schema)          |

Mivel az egyes riasztástípusok által megadott adatok eltérőek, az egyes riasztástípusok kezelése eltérő. A következő szakaszban megtudhatja, hogyan hozhat létre egy runbook ot a különböző típusú riasztások kezeléséhez.

## <a name="create-a-runbook-to-handle-alerts"></a>Runbook létrehozása a riasztások kezeléséhez

Az Automation riasztások használatával, szüksége van egy runbook, amely rendelkezik a logika, amely kezeli a riasztási JSON-hasznos, amely átadott a runbook. A következő példa runbook kell hívni egy Azure-riasztás.

Az előző szakaszban leírtak szerint minden típusú riasztás egy másik séma. A parancsfájl a webhook-adatokat a `WebhookData` runbook bemeneti paraméter egy riasztást. Ezután a parancsfájl kiértékeli a JSON hasznos adat határozza meg, hogy melyik riasztástípust használták.

Ez a példa egy virtuális gép riasztását használja. Lekéri a virtuális gép adatait a hasznos adat, majd használja ezt az információt a virtuális gép leállításához. A kapcsolatot be kell állítani az Automation-fiókban, ahol a runbook fut. Amikor riasztások használatával runbookok, fontos, hogy ellenőrizze a riasztás állapotát a runbook, amely aktiválódik. A runbook minden alkalommal aktiválódik, amikor a riasztás állapota megváltozik. A riasztások nak több állapota van, `Activated` `Resolved`a két leggyakoribb állapot és a . Ellenőrizze ezt az állapotot a runbook logikájában, hogy a runbook nem fut-e egynél többször. Ebben a cikkben például `Activated` bemutatja, hogyan kereshet csak riasztásokat.

A runbook az **AzureRunAsConnection** [Run As fiók használatával](automation-create-runas-account.md) hitelesíti magát az Azure-ral a virtuális gép en végrehajtott felügyeleti művelet végrehajtásához.

Ebben a példában hozzon létre egy Runbook nevű **Stop-AzureVmInResponsetoVMAlert.** Módosíthatja a PowerShell-parancsfájlt, és számos különböző erőforrással használhatja.

1. Nyissa meg az Azure Automation-fiókját.
2. A **Folyamatautomatizálás**csoportban válassza a **Runbooks (Runbooks)** lehetőséget.
3. A runbookok listájának tetején válassza a **+ Runbook létrehozása**lehetőséget.
4. A **Runbook hozzáadása** lapon adja meg a **Stop-AzureVmInResponsetoVMAlert** a runbook nevét. A runbook-típushoz válassza a **PowerShell**lehetőséget. Ezután kattintson a **Létrehozás** elemre.  
5. Másolja a következő PowerShell-példát a **Szerkesztés** lapra.

    ```powershell-interactive
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )
    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema)
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose

                # Authenticate to Azure with service principal and certificate and set subscription
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

6. A **Runbook** mentéséhez és közzétételéhez válassza a Közzététel lehetőséget.

## <a name="create-the-alert"></a>A riasztás létrehozása

A riasztások műveletcsoportokat használnak, amelyek a riasztás által kiváltott műveletek gyűjteményei. A Runbookok csak egy a műveletcsoportokhoz használható számos művelet közül.

1. Az Automation-fiókban válassza a **Figyelés csoportban a Riasztások** **lehetőséget.**
1. Válassza az **+ Új riasztási szabály** lehetőséget.
1. Kattintson **a Kijelölés gombra** **az Erőforrás csoportban.** Az **Erőforrás kiválasztása** lapon jelölje ki a virtuális gép ét, és kattintson a **Kész**gombra.
1. Kattintson a **Feltétel hozzáadása** gombra a **Feltétel csoportban.** Válassza ki a használni kívánt jelet, például **a Százalék processzort,** és kattintson a **Kész**gombra.
1. A **Jellogika konfigurálása** lapon adja meg a **Küszöbértékértéket** a **Riasztási logika csoportban,** majd kattintson a **Kész**gombra.
1. A **Műveletcsoportok csoportban**válassza **az Új létrehozása lehetőséget.**
1. A **Műveletcsoport hozzáadása** lapon adjon nevet és rövid nevet a műveletcsoportnak.
1. Adjon nevet a műveletnek. A művelettípushoz válassza az **Automation Runbook**lehetőséget.
1. Válassza **a Részletek szerkesztése lehetőséget.** A **Runbook konfigurálása** lap **Runbook-forrás csoportban**válassza a **Felhasználó**lehetőséget.  
1. Válassza ki az **előfizetési** és **automation-fiókot,** majd válassza a **Stop-AzureVmInResponsetoVMAlert** runbook.  
1. Válassza az **Igen** lehetőséget **a gyakori riasztási séma engedélyezése**lehetőséget.
1. A műveletcsoport létrehozásához válassza az **OK gombot.**

    ![Műveletcsoport hozzáadása lap](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Ezt a műveletcsoportot használhatja a [tevékenységnapló-riasztásokban](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) és a [létrehozott közel valós idejű riasztásokban.](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json)

1. A **Riasztás részletei csoportban**adja meg a riasztási szabály nevét és leírását, majd kattintson **a Figyelmeztetési szabály létrehozása gombra.**

## <a name="next-steps"></a>További lépések

* Az Automation-runbook webhook használatával történő indításáról a [Runbook indítása webhookból](automation-webhooks.md)című témakörben talál további információt.
* A runbookok indításának különböző módjairól a [Runbook indítása című témakörben talál részleteket.](automation-starting-a-runbook.md)
* A tevékenységnapló-riasztások létrehozásáról a [Tevékenységnapló-riasztások létrehozása című témakörben](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)olvashat.
* Ha tudni szeretné, hogyan hozhat létre közel valós idejű riasztást, [olvassa el a Riasztási szabály létrehozása az Azure Portalon című témakört.](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json)
