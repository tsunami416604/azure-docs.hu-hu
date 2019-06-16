---
title: A logic apps és a runbookok frissítésével az Azure Monitor klasszikus riasztások migrálásának előkészítése
description: Ismerje meg, hogyan lehet módosítani a webhookok, a logic apps és a runbookok önkéntes migrálást előkészítése.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: bdbd45c2b10dec8f1c0a85110747a470e818dbf9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66015614"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>A logic apps és a runbookok előkészítése a klasszikus riasztási szabályok az áttelepítéshez

Mint [azt korábban bejelentettük](monitoring-classic-retirement.md), klasszikus riasztások az Azure monitorban vannak vezetve az szeptember 2019 (eredetileg július 2019 volt). A migrálási eszköz érhető el az ügyfelek számára, akik klasszikus riasztási szabályok és ki szeretne aktiválása áttelepítési magukat az Azure Portalon.

> [!NOTE]
> Bevezetés az áttelepítési eszköz késéssel klasszikus riasztások az áttelepítéshez a kivezetési dátum most már elérhető 2019. augusztus 31-edik eredetileg közzétett dátumától számítva 2019. június 30.

Ha a klasszikus riasztási szabályok önkéntesen át Új riasztási szabályok mellett dönt, vegye figyelembe, hogy nincsenek-e a két rendszer közötti különbségeket. Ez a cikk ismerteti azokat a különbségeket, és hogyan készítheti elő a módosítás.

## <a name="api-changes"></a>API-módosítás

API-k létrehozása és kezelése a klasszikus riasztási szabályok (`microsoft.insights/alertrules`) különböznek az API-k létrehozásához és kezeléséhez új metrikákhoz kapcsolódó riasztások (`microsoft.insights/metricalerts`). Ha programozott módon létrehozása, és jelenleg a klasszikus riasztási szabályok kezelése, frissítése az új API-khoz az üzembehelyezési szkriptek.

Az alábbi táblázat eszköztáblára mutató hivatkozás a klasszikus és az új riasztások parancsvezérelt felületek:

|         |Klasszikus riasztások  |Új metrikákhoz kapcsolódó riasztások |
|---------|---------|---------|
|REST API     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [az a figyelő riasztást](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [az a figyelő metrika-riasztás](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Referencia](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [Referencia](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Azure Resource Manager-sablon | [Klasszikus riasztások](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Az új metrikákhoz kapcsolódó riasztások](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Értesítési hasznos módosítások

Az értesítési adattartalom formátuma kissé eltér, közötti [klasszikus riasztási szabályok](alerts-webhooks.md) és [új metrikákhoz kapcsolódó riasztások](alerts-metric-near-real-time.md#payload-schema). Ha bármilyen webhook, a logikai alkalmazásban vagy a klasszikus riasztási szabályok által elindított runbook-műveletek, frissítenie kell az új metrikákhoz kapcsolódó riasztások adattartalom formátuma fogadására notification végpontokhoz.

Használja az alábbi táblázat a webhook hasznos mezők, a klasszikus formátumból az új formátumra leképezése:

|  |Klasszikus riasztások  |Új metrikákhoz kapcsolódó riasztások |
|---------|---------|---------|
|A riasztás aktív vagy megoldott?    | **status**       | **data.status** |
|A riasztásra vonatkozó környezeti információk     | **context**        | **data.context**        |
|Időbélyeg, amelyen a riasztás aktív vagy megoldott     | **context.timestamp**       | **data.context.timestamp**        |
| Riasztási szabály azonosítója | **context.id** | **data.context.id** |
| Riasztási szabály neve | **context.name** | **data.context.name** |
| A riasztási szabály leírása | **context.description** | **data.context.description** |
| Riasztási szabály a feltétel | **context.condition** | **data.context.condition** |
| Metrika neve | **context.condition.metricName** | **data.context.condition.allOf[0].metricName** |
| Idő összesítése (hogyan a metrika összesített értéket jelenít meg az értékelés ablakban)| **data.context.condition.timeAggregation** | **data.context.condition.timeAggregation** |
| Kiértékelési időszak | **context.condition.windowSize** | **data.context.condition.windowSize** |
| Operátor (hogyan az összesített metrika értékét a rendszer összehasonlítja a küszöbértéket) | **context.condition.operator** | **data.context.condition.operator** |
| Küszöbérték | **context.condition.threshold** | **data.context.condition.allOf[0].threshold** |
| Metrikaérték | **context.condition.metricValue** | **data.context.condition.allOf[0].metricValue** |
| Előfizetés azonosítója | **context.subscriptionId** | **data.context.subscriptionId** |
| Az erőforráscsoport az érintett erőforrás | **context.resourceGroup** | **data.context.resourceGroup** |
| Az érintett erőforrás neve | **context.resourceName** | **data.context.resourceName** |
| Az érintett erőforrás típusa | **context.resourceType** | **data.context.resourceType** |
| Erőforrás-azonosító az érintett erőforrás | **context.resourceId** | **data.context.resourceId** |
| Portál erőforrás összegzés lapján mutató közvetlen hivatkozás | **context.portalLink** | **data.context.portalLink** |
| A webhook vagy a logikai alkalmazás átadandó egyéni adattartalom mezők | **Tulajdonságok** | **data.properties** |

A hasznos adat található hasonlóak, ahogy láthatjuk. A következő szakaszban a következőket kínálja:

- A logic apps, az új formátum használata módosításával kapcsolatos adatokat.
- Egy runbook például, hogy az új riasztások az értesítési tartalom elemzi.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Módosítsa a metrikaalapú riasztási értesítések fogadása a Logic Apps-alkalmazás

A klasszikus riasztások használata a logic apps, módosítania kell a logikaialkalmazás kód az új metrikákhoz kapcsolódó riasztások tartalom elemzése. Kövesse az alábbi lépéseket:

1. Hozzon létre egy új logikai alkalmazást.

1. Használja az "Azure Monitor – metrikák riasztási kezelő" sablont. Ez a sablon egy **HTTP-kérelem** eseményindító a meghatározott megfelelő sémáját.

    ![logikai alkalmazássablon](media/alerts-migration/logic-app-template.png "Metrikaalapú riasztási sablon")

1. A feldolgozó logika üzemeltetésére művelet hozzáadása lehetőséget.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Automation-runbook, amely megkapja a metrikaalapú riasztási értesítések használata

Az alábbi példa a PowerShell-kódot, ha a runbookban tartalmaz. Ez a kód a is észleltünk adattartalmakat. klasszikus metrikariasztási szabályokra és a metrikaalapú riasztási szabályok új értelmezni tudja.

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

Egy runbookot, amely a virtuális gép leáll, amikor a riasztás aktiválódik egy teljes példa: a [Azure Automation dokumentációja](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Partnerintegráció webhookokkal

A legtöbb [, amelyek integrálhatók a klasszikus riasztások partnereink](https://docs.microsoft.com/azure/azure-monitor/platform/partners) már támogatja az újabb metrikákhoz kapcsolódó riasztások azok Integrációk keresztül. Már fog tudni új metrikákhoz kapcsolódó riasztások ismert integrációi a következők:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Használata egy partnerekkel való integráció, amely nem szerepel itt, erősítse meg integration-szolgáltatóval, hogy működik-e az integráció az új metrikákhoz kapcsolódó riasztások.

## <a name="next-steps"></a>További lépések

- [Az áttelepítési eszköz használata](alerts-using-migration-tool.md)
- [Az áttelepítési eszköz működésének megismerése](alerts-understand-migration.md)
