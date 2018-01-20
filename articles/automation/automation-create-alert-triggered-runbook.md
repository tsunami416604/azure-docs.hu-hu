---
title: "Azure automation-runbook riasztások válaszolni |} Microsoft Docs"
description: "Információ kezdeményezi egy runbook Azure riasztásokról értesítő futtatásához."
services: automation
keywords: 
author: georgewallace
ms.author: gwallace
ms.date: 01/11/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 486a3c80c9d6c19ca0a7ba7d4e3cdcde927053b9
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="respond-to-azure-alerts-with-an-automation-runbook"></a>Azure automation-runbook riasztások válaszolni

[Az Azure figyelő](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md?toc=%2fazure%2fautomation%2ftoc.json) base szintű metrikák és a naplók biztosít a legtöbb szolgáltatás a Microsoft Azure-ban. Azure automation-forgatókönyv használatával hívható [művelet csoportok](../monitoring-and-diagnostics/monitoring-action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) vagy a klasszikus riasztásokat a riasztások alapján feladatok automatizálására. Ez a cikk bemutatja, hogyan lehet konfigurálni és futtatni egy runbookot ki a riasztások alapján.

## <a name="alert-types"></a>Riasztástípusok

A forgatókönyvek olyan riasztások összes három típusú elvégezhető műveletek. Riasztás meghívja a runbook, a tényleges hívás esetén a webhook HTTP POST-kérelmet. A POST kérelem törzse a riasztással kapcsolatban hasznos tulajdonságokat tartalmazó JSON-formátumú objektumot tartalmaz. A következő táblázat az egyes riasztási a hasznos séma hivatkozásokat tartalmaz:

|Riasztás  |Leírás|Hasznos séma  |
|---------|---------|---------|
|[Klasszikus metrika riasztás](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |Értesítést kapnak, ha minden platform szintű metrika megfelel-e egy adott feltétel (például a virtuális gép CPU % a nagyobb, mint 90 az elmúlt 5 perc).| [Hasznos séma](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[Figyelmeztetés a napló](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Értesítést küld az Azure tevékenységnapló bármely új esemény megfelel a megadott feltételek (például a "Virtuális gép törlése" művelet esetén myProductionResourceGroup vagy ha egy új szolgáltatás állapotesemény, amelynél "Active", mint az állapot akkor jelenik meg).| [Hasznos séma](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)        |
|[Valós idejű metrika figyelmeztetés közelében](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Gyorsabb, mint az metrika riasztások kap értesítést, ha egy vagy több platform szintekhez tartozó metrikákat a megadott feltételeknek eleget (például a virtuális gép CPU % 90-nél több és hálózati a nem 500 MB-nál nagyobb az elmúlt 5 percig).| [Hasznos séma](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

Mivel az egyes riasztások által biztosított adatokat különböző, a minden riasztás kell eltérően kell kezelni. A következő szakaszban megismerheti, hogyan ezek különböző típusú riasztások kezelésének runbook létrehozása.

## <a name="create-a-runbook-to-handle-alerts"></a>Hozzon létre egy runbookot, a riasztások kezelése

A riasztások automation használatához, amelyen kezelheti a riasztás a runbook átadott JSON-adattartalmat logika runbook kell létrehoznia. A következő példa runbook az Azure-riasztás alapján kell meghívni. Az előző szakaszban leírtak a különböző típusú riasztási típus rendelkezik másik sémát. A parancsfájl a webhook adatai fogadja el a `WebhookData` runbook bemeneti paraméter riasztásból és kiértékeli a JSON-adattartalmat határozza meg, melyik riasztási típus lett megadva. A következő példa a riasztás egy virtuális géptől használni. A virtuális gép adatai lekéri a tartalmat, és ezt az információt használja a virtuális gép leállítása. A kapcsolat az automatizálási fiók, amelyen a runbook állapota futott kell beállítani.

A runbook felhasználja a **AzureRunAsConnection** [futtató fiók](automation-create-runas-account.md) hitelesítéséhez az Azure-ral ahhoz, hogy a virtuális gép elleni felügyeleti művelet végrehajtása.

A következő PowerShell-parancsfájlt is módosítható, hogy számos különböző erőforrások használatára.

Az alábbi példa eltarthat, és hozzon létre egy nevű runbookot **Stop-AzureVmInResponsetoVMAlert** PowerShell mintával.

1. Nyissa meg az Automation-fiókját.
1. Kattintson a **FOLYAMATOK AUTOMATIZÁLÁSA** területen lévő **Runbookok** lehetőségre. Megjelenik a runbookok listája.
1. Kattintson a lista tetején található **Runbook hozzáadása** gombra. Az a **hozzáadása Runbook lap**, jelölje be **Gyorslétrehozás**.
1. Adja meg a "Stop-AzureVmInResponsetoVMAlert" a runbook **neve**, és válassza ki **PowerShell** a **runbooktípusba**. Kattintson a **Create** (Létrehozás) gombra.
1. Másolja a következő PowerShell-példa a Szerkesztés ablaktáblára. Kattintson a **közzététel** mentéséhez és közzétenni a runbookot.

```powershell-interactive
<#
.SYNOPSIS
  This runbook will stop a resource management VM in response to an Azure alert trigger.

.DESCRIPTION
  This runbook will stop an resource management VM in response to an Azure alert trigger.
  Input is alert data with information needed to identify which VM to stop.
  
  DEPENDENCIES
  - The runbook must be called from an Azure alert via a webhook.
  
  REQUIRED AUTOMATION ASSETS
  - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
  - An Automation certificate asset called "AzureRunAsCertificate".

.PARAMETER WebhookData
   Optional (user does not need to enter anything, but the service will always pass an object)
   This is the data that is sent in the webhook that is triggered from the alert.

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
    # Get the data object from WebhookData
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Get the info needed to identify the VM (depends on the payload schema)
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
        # Schema not supported
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

        # Do work only if this is a resource management VM
        if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
        {
            # This is the VM
            Write-Verbose "This is a resource management VM." -Verbose

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

            # Stop the VM
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
        # The alert status was not 'Activated' so no action taken
        Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
    }
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="create-an-action-group"></a>A művelet-csoport létrehozása

Egy művelet csoport gyűjteménye ki riasztást alapján végzett műveleteket. A forgatókönyvek olyan csak a közül sok elérhető művelet csoportokkal.

1. Válassza a portál **figyelő**.

1. Válassza ki **művelet csoportok** alatt **beállítások**.

1. Válassza ki **művelet csoport hozzáadása**, és töltse ki a mezőket.

1. A művelet csoport neve mezőben adjon meg egy nevet, és a rövid név mezőben adjon meg egy nevet. A rövid nevét használja a teljes műveletet csoport neve helyett amikor ez a csoport értesítések küldése.

1. Az előfizetés mezőben autofills az aktuális előfizetéshez. Ez az előfizetés, amelyben a művelet csoport mentett lesz.
Válassza ki az erőforráscsoportot, amelyben a művelet csoport mentett.

Ez a példa létrehoz két műveletet, a runbook műveletet és egy értesítési műveletet.

### <a name="runbook-action"></a>Runbook-művelet

Az alábbi lépéseket hozzon létre egy runbook műveletet a művelet csoporton belül.

1. A **műveletek**, nevezze el a műveletet.

1. Válassza ki **Automation-Runbook** a a **művelettípus**.

1. A **részletek** kijelöléséhez **részletes Szerkesztés**

1. Az a **konfigurálása Runbook** lapon jelölje be **felhasználói** alatt **Runbook forrás**.

1. Válassza ki a **előfizetés** és **Automation-fiók**, és végül válassza ki a runbookot, "Stop-AzureVmInResponsetoVMAlert" nevű az előző lépésben létrehozott.

1. Ha befejezte, kattintson a **OK**.

### <a name="notification-action"></a>Értesítési művelet

Az alábbi lépéseket hozzon létre egy értesítési műveletet, a művelet csoporton belül.

1. A **műveletek**, nevezze el a műveletet.

1. Válassza ki **E-mail** a a **művelettípus**.

1. A **részletek** kijelöléséhez **részletes Szerkesztés**

1. Az a **E-mail** lapján adja meg az e-mail cím értesíti, és kattintson a **OK**. Az e-mail címet, valamint a runbook műveletet hozzáadása akkor hasznos, értesítés jelenik meg a runbook indításakor. A művelet csoport a következő kép kell hasonlítania:

   ![A művelet egy kiszolgálócsoport lapját hozzáadása](./media/automation-create-alert-triggered-runbook/add-action-group.png)

1. Kattintson a **OK** a művelet csoport létrehozásához.

A létrehozott művelet csoport hozhat létre [napló tevékenységriasztásokat](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) vagy [majdnem valós idejű riasztások](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal) és a létrehozott művelet csoportot használjon.

## <a name="classic-alert"></a>Klasszikus riasztás

Klasszikus riasztások metrikák alapulnak és nem használható művelet csoportok tegye, de a runbook műveletet kell ezek alapján. A klasszikus riasztások létrehozásához használja az alábbi lépéseket:

1. Válassza ki **+ Hozzáadás metrika riasztás**.

1. A metrika riasztás "myVMCPUAlert" nevet, és adjon meg egy rövid leírást a riasztás.

1. A metrika riasztás, mint a "Nagyobb, mint" feltétel, a küszöbérték beállítása "10", és beállítása a időszak szerint "az elmúlt 5 percben".

1. A **hajtsa végre a műveletet**, jelölje be **ezt a riasztást a runbook futtatása**

1. Az a **konfigurálása Runbook** lapon jelölje be **felhasználói** a **Runbook forrás**. Válassza ki az automation-fiók, és válassza ki a **Stop-AzureVmInResponsetoVMAlert** runbook. Kattintson a **OK**, és kattintson a **OK** újra, hogy a riasztási szabály mentése.

## <a name="next-steps"></a>További lépések

* Az automatizálási runbookok kezdődő, és webhookokkal további információkért lásd: [runbook indítása a webhook](automation-webhooks.md)
* A runbook-indítási különböző módokon részletekért lásd: [Runbook indítása](automation-starting-a-runbook.md).
* Tevékenység napló riasztás létrehozása, lásd: [napló riasztások tevékenység létrehozása](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)
* Lásd: how to közel valós idejű riasztás létrehozása, látogasson el a [riasztási szabályt létrehozni és az Azure portál](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal).