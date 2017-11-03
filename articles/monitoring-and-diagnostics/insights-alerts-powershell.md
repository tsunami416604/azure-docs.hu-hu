---
title: "Hozzon létre Azure-szolgáltatások - PowerShell riasztásokat |} Microsoft Docs"
description: "Eseményindító e-mailek, értesítések, a megadott feltételek teljesülnek webhely URL-címek (webhookok), vagy az automation hívni."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d26ab15b-7b7e-42a9-81c8-3ce9ead5d252
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: robb
ms.openlocfilehash: d3fca8675c1f15b8fd0f952cfbf520f5c68478b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-metric-alerts-in-azure-monitor-for-azure-services---powershell"></a>Hozzon létre metrika riasztások Azure figyelése az Azure-szolgáltatások - PowerShell
> [!div class="op_single_selector"]
> * [Portál](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [Parancssori felület](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan PowerShell használata Azure metrika riasztások beállítása.  

A figyelési metrikákat, vagy események, az Azure-szolgáltatások alapuló riasztást kaphat.

* **Metrika értékek** -a riasztás elindítja a megadott metrika értékét ebbe a küszöbérték mindkét irányban rendel. Ez azt jelenti, hogy elindítja a mindkét Ha először a feltétel teljesül, és majd ezt követően, hogy a feltétel mikor van már nem teljesül.    
* **Tevékenység naplóeseményeket** -riasztást aktiválhatók *minden* esemény, vagy csak akkor, ha egy bizonyos események következik be. További információt a naplófájl tevékenységriasztásokat [kattintson ide](monitoring-activity-log-alerts.md)

A metrika riasztások tegye a következőket, amikor elindítja a konfigurálhatja:

* e-mail értesítések küldéséhez a szolgáltatás-rendszergazda és a társadminisztrátorok
* e-mail küldéséhez megadott további e-maileket.
* A webhook hívása
* egy Azure-runbook (csak az Azure portálról) végrehajtásának elindítása

Konfigurálhatja, és a riasztási szabályok használatával adatainak beolvasása

* [Azure Portal](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [parancssori felület (CLI)](insights-alerts-command-line-interface.md)
* [Az Azure figyelő REST API-n](https://msdn.microsoft.com/library/azure/dn931945.aspx)

További információkért mindig beírhatja ```Get-Help``` és majd a keresett PowerShell-parancsot.

## <a name="create-alert-rules-in-powershell"></a>A riasztási szabályok létrehozása a PowerShell
1. Jelentkezzen be az Azure-bA.   

    ```PowerShell
    Login-AzureRmAccount

    ```
2. Listáját, az előfizetéssel elérhető rendelkezik. Győződjön meg arról, hogy a megfelelő előfizetés dolgozik. Ha nem, állítsa be a megfelelőt kimenete használatával `Get-AzureRmSubscription`.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. Egy erőforráscsoportot a meglévő szabályok listájában, használja a következő parancsot:

   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. Olyan szabály létrehozására, először rendelkezik néhány fontos adatot kell.

  * A **erőforrás-azonosító** be szeretné állítani egy riasztást az erőforrás
  * A **metrikai meghatározásainak** az adott erőforrás érhető el

     Egy az erőforrás-azonosító eléréséhez módja az Azure-portálon. Ha az erőforrás létrehozása már be van állítva, válassza ki azt a portálon. A következő paneljén válassza *tulajdonságok* alatt a *beállítások* szakasz. **ERŐFORRÁS-azonosító** mező a következő panelen. Egy másik módja a [Azure erőforrás-kezelő](https://resources.azure.com/).

     A webes alkalmazás például az erőforrás-azonosító

     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Használhat `Get-AzureRmMetricDefinition` egy adott erőforrás minden metrikadefiníciót listájának megtekintéséhez.

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```

     A következő példa olyan táblát, amely a mérték neve és az adott metrika egységet hoz létre.

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

     ```
     A Get-AzureRmMetricDefinition elérhető lehetőségek teljes listáját érhető futtatásával `Get-Help Get-AzureRmMetricDefinition -Detailed`.
5. Az alábbi példa állít be egy webhely erőforráson riasztást. A riasztási eseményindítók, ha 5 percig, majd újra amikor megkapja sincs forgalom 5 percig következetesen kap minden forgalom.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```
6. Webhook létrehozása vagy e-mailt küld, ha elindítja a riasztást, először létre kell hoznia az e-mailek és/vagy a webhook. Majd azonnal a szabály létrehozása után a - műveletek címkével, és az alábbi példában látható. Nem társítható webhook vagy e-mailek már hozott létre a szabályokat PowerShell segítségével.

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```

7. Ellenőrizheti, hogy a riasztások elkészültek megfelelően az egyes szabályok alapján.

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```
8. A riasztások törlése. Ezek a parancsok törölje az ebben a cikkben korábban létrehozott szabályokat.

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>Következő lépések
* [Az Azure Figyelés áttekintése](monitoring-overview.md) többek között a adattípusok összegyűjtheti, és figyelje.
* További információ [konfigurálása webhookokkal a riasztások](insights-webhooks-alerts.md).
* További információ [riasztások konfigurálása a naplózási eseményeket](monitoring-activity-log-alerts.md).
* További információ [Azure Automation-forgatókönyveket](../automation/automation-starting-a-runbook.md).
* Első egy [diagnosztikai naplók gyűjtésére áttekintése](monitoring-overview-of-diagnostic-logs.md) nagyon gyakori gyűjtéséhez részletes a a szolgáltatásban.
* Első egy [metrikák gyűjtemény áttekintése](insights-how-to-customize-monitoring.md) ellenőrizze, hogy a szolgáltatás elérhető, és a gyors.
