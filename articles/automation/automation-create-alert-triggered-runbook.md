---
title: Használjon olyan riasztást, egy Azure Automation-runbook elindítása
description: Ismerje meg, hogyan kezdeményezi egy runbook futtatását, amikor az Azure riasztást hoz létre.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8a1ae906a72d781f638fb171a409b860ffa6d501
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65517706"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Használjon olyan riasztást, egy Azure Automation-runbook elindítása

Használhat [Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) figyelése base-szintű metrikákat és naplókat a legtöbb szolgáltatás az Azure-ban. Azure Automation-runbookok használatával meghívhatja [Műveletcsoportok](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) vagy a klasszikus riasztások segítségével automatizálhatja a feladatokat a riasztások alapján. Ez a cikk bemutatja, hogyan konfigurálásához és a egy runbook futtatására riasztások használatával.

## <a name="alert-types"></a>Riasztástípusok

Automation-runbookok három riasztási típusokkal használható:

* Leggyakoribb riasztások
* Tevékenységnapló-riasztások
* Közel valós idejű metrikákhoz kapcsolódó riasztások

> [!NOTE]
> A gyakori riasztási séma egységesíti a használati élményt, a riasztási értesítéseket az Azure-ban még ma. A három riasztási típusok az Azure-ban még ma (a mérőszám, a napló és a tevékenységnaplóhoz) volt korábban, a saját e-mail-sablonok, a webhook-sémákat, stb. További tudnivalókért lásd: [gyakori riasztási séma](../azure-monitor/platform/alerts-common-schema.md)

Riasztás a runbookot hívja, a tényleges hívás esetén, a webhook HTTP POST-kérelmet. A POST-kérés törzse tartalmazza a JSON-formátumú objektum, amelynek hasznosak a riasztáshoz kapcsolódó tulajdonságok. Az alábbi táblázat az egyes riasztástípusok tartalom sémáját mutató hivatkozásokat:

|Riasztás  |Leírás|Adattartalom-séma  |
|---------|---------|---------|
|[Közös riasztás](../azure-monitor/platform/alerts-common-schema.md?toc=%2fazure%2fautomation%2ftoc.json)|Az általános riasztási sémát, amely szabványosítja a riasztási értesítéseket az Azure-beli használati élményét még ma.|[Gyakori riasztási adattartalom-séma](../azure-monitor/platform/alerts-common-schema-definitions.md?toc=%2fazure%2fautomation%2ftoc.json#sample-alert-payload)|
|[Tevékenységnapló-riasztás](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Értesítést küld minden olyan új esemény, az Azure tevékenységnaplójában megegyezik a megadott feltételeknek. Például, hogy amikor egy `Delete VM` művelethez az szükséges, **myProductionResourceGroup** , vagy ha egy új Azure Service Health-esemény az egy **aktív** állapot jelenik meg.| [Műveletnapló-adattartalom riasztási séma](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[Közel valós idejű metrikariasztás](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |Értesítés gyorsabban metrikákhoz kapcsolódó riasztások küldése, ha egy vagy több platform-szintű metrikákat megfelel egy bizonyos feltételnek. Például, hogy amikor értéke **processzorhasználatról** a virtuális gép értéke nagyobb, mint **90**, és az értéke **bejövő** nagyobb, mint **500 MB-os** az elmúlt 5 perc.| [Közel valós idejű metrikaalapú riasztási adattartalom-séma](../azure-monitor/platform/alerts-webhooks.md#payload-schema)          |

Mivel az adatok különböző típusú riasztás által biztosított különböző, az egyes riasztástípusok eltérően kell kezelni. A következő szakaszban megismerheti, hogyan kezelje a különböző típusú riasztásokat runbook létrehozása.

## <a name="create-a-runbook-to-handle-alerts"></a>Hozzon létre egy runbookot, hogy a riasztások kezelése

Automation használata riasztásokkal, egy runbookot, amely rendelkezik a logika, amely kezeli a riasztási JSON-adattartalom van átadva a runbooknak szüksége. A következő példa runbook Azure riasztásból kell meghívni.

Az előző szakaszban leírtak minden riasztás típusát különböző sémákkal rendelkeznek. A parancsfájlnak a webhook-adatokat a `WebhookData` forgatókönyv bemeneti paramétere a riasztást. A parancsfájl összeveti a JSON-adattartalom meghatározni, melyik riasztási típus lett megadva.

Ebben a példában egy virtuális gépről egy riasztást. Lekéri a virtuális gép adatait a hasznos-, és ezt az információt használja majd a virtuális gép leállítása. A kapcsolat kell beállítani az Automation-fiókban, a runbook futtatása. Riasztások runbookok való használatakor fontos a runbook, amely akkor aktiválódik a riasztás állapotának ellenőrzéséhez. A runbook minden alkalommal, amikor a riasztás állapota aktivál. Riasztások több állapota rendelkezzen, a két leggyakoribb állapotok `Activated` és `Resolved`. Ellenőrizze, hogy a runbook logikai győződjön meg arról, hogy a runbook nem futtatható többször, ebben az állapotban. Ebben a cikkben szereplő példa mutatja be a keresett `Activated` csak riasztást küld.

A runbook használja a **AzureRunAsConnection** [Futtatás mint fiók](automation-create-runas-account.md) ellen a virtuális Gépet a felügyeleti művelet végrehajtásához Azure-hitelesítésre.

Ez a példa használatával hozza létre a nevű runbook **Stop-AzureVmInResponsetoVMAlert**. Módosítása a PowerShell-parancsfájlt, és számos különböző erőforrásokat használhatja.

1. Nyissa meg az Azure Automation-fiók.
2. A **Folyamatautomatizálás**válassza **Runbookok**.
3. A runbookok listája tetején válassza **+ hozzon létre egy runbookot**.
4. Az a **Runbook hozzáadása** lap, adja meg **Stop-AzureVmInResponsetoVMAlert** a runbook neve. A forgatókönyv típusának kiválasztása **PowerShell**. Ezután kattintson a **Létrehozás** elemre.  
5. Másolja be a következő PowerShell-példa az **szerkesztése** lapot.

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

6. Válassza ki **közzététel** menti és közzéteszi a runbookot.

## <a name="create-the-alert"></a>A riasztás létrehozása

Riasztások használata a Műveletcsoportok, amely a riasztás által aktivált tevékenységek gyűjteményei. A forgatókönyvek olyan csak egyike a számos végrehajtandó művelet csoportokkal is használhatja.

1. Válassza ki az Automation-fiók **riasztások** alatt **figyelés**.
1. Válassza ki **+ Új riasztási szabály**.
1. Kattintson a **kiválasztása** alatt **erőforrás**. Az a **válasszon ki egy erőforrást** lapon válassza ki a riasztást a virtuális gép, és kattintson a **kész**.
1. Kattintson a **feltétel hozzáadása** alatt **feltétel**. Válassza ki a jel, például a használni kívánt **százalékos Processzorhasználat** kattintson **kész**.
1. Az a **jellogika konfigurálása** lap, adja meg a **küszöbérték** alatt **riasztási logika**, és kattintson a **kész**.
1. A **Műveletcsoportok**válassza **hozzon létre új**.
1. Az a **műveleti csoport hozzáadása** lapon, a műveletcsoport adjon egy nevet és a egy rövid nevet.
1. Nevezze el a műveletet. Válassza ki a művelettípus **Automation-Runbook**.
1. Válassza ki **részleteinek szerkesztése**. Az a **Runbook konfigurálása** lap **forgatókönyv forrása**válassza **felhasználói**.  
1. Válassza ki a **előfizetés** és **Automation-fiók**, majd válassza ki a **Stop-AzureVmInResponsetoVMAlert** runbook.  
1. Válassza ki **Igen** a **engedélyezze a közös riasztási séma**.
1. A műveletcsoport létrehozásához válassza **OK**.

    ![Adja hozzá a műveleti csoport oldalán](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Ez a művelet a csoport is használhatja a [tevékenységnapló-riasztások](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) és [közel valós idejű riasztások](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json) létrehozott.

1. A **riasztás részletei**, adjon hozzá egy riasztási szabály nevét és leírását, és kattintson a **riasztási szabály létrehozása**.

## <a name="next-steps"></a>További lépések

* Automation-runbook indítása egy webhook használatával kapcsolatos további információkért lásd: [runbook indítása webhookból](automation-webhooks.md).
* Módjai, indítson el egy runbookot, lásd: [runbook elindítása](automation-starting-a-runbook.md).
* Tevékenységnapló-riasztás létrehozása kapcsolatban lásd: [tevékenységnapló-riasztások létrehozása](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Riasztás létrehozása, közel valós idejű kezelésével kapcsolatos információkért lásd: [riasztási szabályt létrehozni az Azure Portalon](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json).
