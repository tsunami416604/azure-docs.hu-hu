---
title: Logikai alkalmazások & runbookok frissítése a riasztások áttelepítéséhez
description: Ismerje meg, hogyan módosíthatja a webhookokat, a logikai alkalmazásokat és a runbookokat az önkéntes áttelepítéselőkészítéséhez.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: f31fcc07bed0287c2f86ca4fe52bf02a2a1d2a71
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114410"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Logikai alkalmazások és runbookok előkészítése a klasszikus riasztási szabályok migrálására

Ahogy [azt korábban bejelentették](monitoring-classic-retirement.md), az Azure Monitor klasszikus riasztásai 2019 szeptemberében (eredetileg 2019 júliusa) megszűnnek. Az Azure Portalon elérhető egy áttelepítési eszköz azoknak az ügyfeleknek, akik klasszikus riasztási szabályokat használnak, és akik maguk szeretnék elindítani az áttelepítést.

> [!NOTE]
> A migrációs eszköz bevezetésének késedelmi ideje miatt a klasszikus riasztások áttelepítésének nyugdíjazási dátumát 2019. június 30-án, az eredetileg bejelentett időponttól 2019. augusztus 31-ig meghosszabbították.

Ha úgy dönt, hogy önként áttelepíti a klasszikus riasztási szabályokat az új riasztási szabályokba, vegye figyelembe, hogy vannak bizonyos különbségek a két rendszer között. Ez a cikk ismerteti ezeket a különbségeket, és hogyan készülhet fel a változásra.

## <a name="api-changes"></a>API-módosítások

A klasszikus riasztási szabályokat (`microsoft.insights/alertrules`) létrehozó és kezelő API-k eltérnek`microsoft.insights/metricalerts`az új metrikariasztásokat ( ) létrehozó és kezelő API-któl. Ha még ma programozott módon hoz létre és kezel klasszikus riasztási szabályokat, frissítse a központi telepítési parancsfájlokat az új API-kkal való együttműködésre.

Az alábbi táblázat a klasszikus és az új riasztások programozott felületeire mutató hivatkozást tartalmaz:

|         |Klasszikus riasztások  |Új metrikariasztások |
|---------|---------|---------|
|REST API     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [az monitor riasztása](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [az monitor mérőszámai riasztás](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Referencia](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [Referencia](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Azure Resource Manager-sablon | [Klasszikus riasztások esetén](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Új metrikariasztások esetén](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Értesítési hasznos adat módosításai

Az értesítési hasznos adat formátuma némileg eltér a [klasszikus riasztási szabályok](alerts-webhooks.md) és az [új metrikariasztások](alerts-metric-near-real-time.md#payload-schema)között. Ha rendelkezik olyan webhook, logikai alkalmazás vagy runbook-műveletek, amelyek a klasszikus riasztási szabályok által kiváltott, frissítenie kell ezeket az értesítési végpontokat, hogy elfogadja az új metrikariasztások hasznos formátumát.

Az alábbi táblázat segítségével a webhook hasznos mezőit a klasszikus formátumról az új formátumra képezze le:

|  |Klasszikus riasztások  |Új metrikariasztások |
|---------|---------|---------|
|Aktiválták vagy feloldották a riasztást?    | **Állapot**       | **data.status** |
|A riasztással kapcsolatos kontextuális információk     | **Összefüggésben**        | **data.context**        |
|Az az időbélyegző, amelyen a riasztást aktiválták vagy feloldották     | **context.timestamp**       | **data.context.timestamp**        |
| Riasztási szabály azonosítója | **context.id** | **data.context.id** |
| Riasztási szabály neve | **context.name** | **data.context.name** |
| A riasztási szabály leírása | **context.description** | **data.context.description** |
| Riasztási szabály feltétele | **context.condition** | **data.context.condition** |
| Metrika neve | **context.condition.metricName** | **data.context.condition.allOf[0].metricName** |
| Időösszesítés (a mérőszám összesítésének a próbaidőszakon keresztül)| **context.condition.timeAggregation** | **context.condition.timeAggregation** |
| Értékelési időszak | **context.condition.windowSize** | **data.context.condition.windowSize** |
| Operátor (az összesített metrikaérték összehasonlítása a küszöbértékkel) | **context.condition.operator** | **data.context.condition.operator** |
| Küszöbérték | **context.condition.threshold** | **data.context.condition.allOf[0].threshold** |
| Metrikus érték | **context.condition.metricValue** | **data.context.condition.allOf[0].metricValue** |
| Előfizetés azonosítója | **context.subscriptionId** | **data.context.subscriptionId** |
| Az érintett erőforrás erőforráscsoportja | **context.resourceCsoport** | **data.context.resourceCsoport** |
| Az érintett erőforrás neve | **context.resourceName** | **data.context.resourceName** |
| Az érintett erőforrás típusa | **context.resourceType** | **data.context.resourceType** |
| Az érintett erőforrás erőforrásazonosítója | **context.resourceId** | **data.context.resourceId** |
| Közvetlen hivatkozás a portálerőforrás-összefoglaló lapra | **context.portalLink** | **data.context.portalLink** |
| A webhooknak vagy logikai alkalmazásnak átadandó egyéni hasznos adatmezők | **Tulajdonságok** | **data.properties** |

A rakományok hasonlóak, amint látod. A következő rész a következőket kínálja:

- A logikai alkalmazások nak az új formátummal való használata érdekében történő módosításának részletei.
- Egy runbook példa, amely elemzi az értesítési hasznos adat az új riasztások.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Logikai alkalmazás módosítása metrikariasztási értesítés fogadásához

Ha a logikai alkalmazások klasszikus riasztások, módosítania kell a logikai alkalmazás kódját az új metrika riasztások hasznos teher. Kövesse az alábbi lépéseket:

1. Hozzon létre egy új logikai alkalmazást.

1. Használja az "Azure Monitor – Metrikák riasztáskezelő" sablont. Ez a sablon rendelkezik egy **HTTP-kérelem** eseményindító a megfelelő séma definiálva.

    ![logikai alkalmazássablon](media/alerts-migration/logic-app-template.png "Metrikariasztási sablon")

1. Adjon hozzá egy műveletet a feldolgozási logika üzemeltetéséhez.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Metrikariasztási értesítést kapó automatizálási runbook használata

A következő példa a Runbookban használható PowerShell-kódot tartalmazza. Ez a kód a klasszikus metrikariasztási szabályok és az új metrikariasztási szabályok hasznos terhelését is elemezheti.

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

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

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

Egy teljes példa egy runbook, amely leállítja a virtuális gép, ha egy riasztás aktiválódik, tekintse meg az [Azure Automation dokumentációt.](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)

## <a name="partner-integration-via-webhooks"></a>Partnerintegráció webhookokon keresztül

A [legtöbb partnerünk, amely integrálja a klasszikus riasztások](https://docs.microsoft.com/azure/azure-monitor/platform/partners) már támogatja az újabb metrika riasztások integrációkon keresztül. Az új metrikariasztásokkal már működik ismert integrációk a következők:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4 (Jelzés)](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Ha olyan partnerintegrációt használ, amely nem szerepel az itt felsorolt, erősítse meg az integrációs szolgáltatóval, hogy az integráció új metrikariasztásokkal működik.

## <a name="next-steps"></a>További lépések

- [A migrálási eszköz használata](alerts-using-migration-tool.md)
- [A migrálási eszköz működésének ismertetése](alerts-understand-migration.md)
