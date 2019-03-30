---
title: A logic apps és a runbookok frissítésével az Azure Monitor klasszikus riasztások migrálásának előkészítése
description: Ismerje meg, hogyan lehet módosítani a webhookot, a logikai alkalmazás, és a runbookok önkéntes migrálást előkészítése.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 3c47404826d5055d4a82d4842523f790fb11f000
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632260"
---
# <a name="prepare-your-logic-apps-and-run-books-for-classic-alert-rules-migration"></a>Készítse elő a logic apps, és futtassa a könyvek klasszikus riasztási szabályok az áttelepítéshez

Mint [azt korábban bejelentettük](monitoring-classic-retirement.md), klasszikus riasztások az Azure monitorban vannak vezetve a július 2019. Az áttelepítési eszköz való áttelepítés önkéntesen érhető el az Azure Portalon, és ügyfeleink, akik klasszikus riasztási szabályok jelennek meg.

A klasszikus riasztási szabályok önkéntesen át Új riasztási szabályok választja, van-e, amely akkor célszerű tisztában lennie a két rendszer közötti különbségeket. Ez a cikk végigvezeti a két rendszer, és hogyan készítheti elő a módosítás közötti különbségeket.

## <a name="api-changes"></a>API-módosítás

Az API-k segítségével a klasszikus riasztási szabályok létrehozása/kezelése (`microsoft.insights/alertrules`) különbözik az új metrikákhoz kapcsolódó riasztások létrehozásához és kezeléséhez használt API-k (`microsoft.insights/metricalerts`). Programozott módon létrehozása/kezelése klasszikus riasztási szabályok ma esetén frissítse az üzembehelyezési szkriptek új API-khoz.

Az alábbi táblázat egy hivatkozást parancsvezérelt felületek biztosít klasszikus és az új riasztásokat.

|         |Klasszikus riasztások  |Új metrikákhoz kapcsolódó riasztások |
|---------|---------|---------|
|REST API     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [az a figyelő riasztást](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [az a figyelő metrika-riasztás](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Referencia](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |      |
| Azure Resource Manager-sablon | [Klasszikus riasztások](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Az új metrikákhoz kapcsolódó riasztások](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Értesítési hasznos módosítások

Az értesítési adattartalom formátuma kissé eltér, közötti [klasszikus riasztási szabályok](alerts-webhooks.md) és [új metrikákhoz kapcsolódó riasztások](alerts-metric-near-real-time.md#payload-schema). Ha bármilyen webhook, a logikai alkalmazás vagy a klasszikus riasztási szabály aktiválása a runbook-műveletek, fogadja el az új metrikákhoz kapcsolódó riasztások adattartalom formátuma notification végpontokhoz frissíteni kell.

Az alábbi táblázat segítségével a klasszikus riasztási szabályt a webhook hasznos és az új metrikaalapú riasztási webhook hasznos adatai között a mezők leképezése.

|  |Klasszikus riasztások  |Új metrikákhoz kapcsolódó riasztások |
|---------|---------|---------|
|A riasztás aktív vagy megoldott     | status       | Data.status |
|A riasztásra vonatkozó környezeti információk     | Környezet        | Data.Context        |
|Időbélyeg, amelyen a riasztás aktív vagy megoldott      | Context.Timestamp       | data.context.timestamp        |
| Riasztási szabály azonosítója | Context.ID | Data.Context.ID |
| Riasztási szabály neve | Context.Name | data.context.name |
| A riasztási szabály leírása | Context.Description | data.context.description |
| Riasztási szabály a feltétel | context.condition | data.context.condition|
| Metrika neve | context.condition.metricName| data.context.condition.allOf[0].metricName|
| Idő összesítése (hogyan a metrika összesített értéket jelenít meg az értékelés ablakban)|data.context.condition.timeAggregation|data.context.condition.timeAggregation|
| Kiértékelési időszak | context.condition.windowSize | data.context.condition.windowSize|
| Operátor (hogyan az összesített metrika értékét a rendszer összehasonlítja a küszöbértéket) | context.condition.operator | data.context.condition.operator|
| Küszöbérték | Context.condition.threshold| data.context.condition.allOf[0].threshold|
| Metrikaérték | context.condition.metricValue | data.context.condition.allOf[0].metricValue|
| Előfizetés azonosítója | context.subscriptionId | data.context.subscriptionId|
| Az erőforráscsoport az érintett erőforrás | context.resourceGroup | data.context.resourceGroup|
| Az érintett erőforrás neve | context.resourceName | data.context.resourceName |
| Az érintett erőforrás típusa | context.resourceType | data.context.resourceType |
|  Erőforrás-azonosító az érintett erőforrás | context.resourceId | data.context.resourceId |
| Portál erőforrás összegzés lapján közvetlen hivatkozás | context.portalLink | data.context.portalLink|
| Webhook vagy a logikai alkalmazás átadandó egyéni adattartalom mezők | properties |Data.Properties |

Amint láthatja, mind a is észleltünk adattartalmakat hasonlóak. Részletek a minta a logic apps és a egy minta runbook elemezni az új riasztások az értesítési tartalom következő szakaszban rendelkezik.

## <a name="using-a-logic-app-that-receives-a-metric-alert-notification"></a>Egy logikai alkalmazást, amely megkapja a metrikaalapú riasztási értesítések segítségével

A logic apps használata klasszikus riasztások, ha szüksége lesz a új metrikákhoz kapcsolódó riasztások hasznos adatainak elemzése a logikai alkalmazás módosításához.

1. Hozzon létre egy új logikai alkalmazást.

2. Használja az "Azure Monitor – metrikák riasztási kezelő" sablont. Ez a sablon egy **HTTP-kérelem** eseményindító a meghatározott megfelelő sémáját

    ![logikai alkalmazássablon](media/alerts-migration/logic-app-template.png "Metrikaalapú riasztási sablon")

3. A feldolgozó logika üzemeltetésére művelet hozzáadása lehetőséget.

## <a name="using-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Automation-runbook, amely megkapja a metrikaalapú riasztási értesítések segítségével

A következő mintát biztosít, amely a is észleltünk adattartalmakat. klasszikus metrikariasztási szabályokra és a metrikaalapú riasztási szabályok új értelmezni tudja a runbookban használható PowerShell-kódot.

```PS
## Sample PowerShell code to be used in a runbook to handle parsing of both classic and new metric alerts

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

    # Identify if the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new Metric Alert schema
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic Metric Alert schema
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is not either a classic metric alert or a new metric alert
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    #parse fields related to resource affected
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

Egy teljes példa, amely a virtuális gép leáll, amikor a riasztás akkor aktiválódik, a runbook [Azure Automation dokumentációja](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Partnerintegráció webhookokkal

A legtöbb [, amelyek integrálhatók a klasszikus riasztások partnereink](https://docs.microsoft.com/azure/azure-monitor/platform/partners) már támogatja az újabb metrikákhoz kapcsolódó riasztások azok Integrációk keresztül. Ismert Integrációk már fog tudni új metrikákhoz kapcsolódó riasztások az alábbiakban láthatók.

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Ha-partnerekkel való integráció, amely nem szerepel itt használ, győződjön meg arról integrációs-szolgáltatóval, hogy működik-e az integráció az új metrikákhoz kapcsolódó riasztások.

## <a name="next-steps"></a>További lépések

- [Az áttelepítési eszköz használata](alerts-using-migration-tool.md)
- [Az áttelepítési eszköz működésének megismerése](alerts-understand-migration.md)
