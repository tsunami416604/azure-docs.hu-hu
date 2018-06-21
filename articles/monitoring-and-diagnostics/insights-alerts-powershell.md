---
title: Klasszikus Azure-szolgáltatások figyelmeztetések létrehozása a PowerShell használatával |} Microsoft Docs
description: Indítás, e-maileket vagy értesítések, vagy a webhely URL-címek (webhookok) vagy az automation hívni a megadott feltételek teljesülnek.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: b08a8f66add45d64085ac05605fe3c7d7f91b705
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36286199"
---
# <a name="use-powershell-to-create-alerts-for-azure-services"></a>Azure-szolgáltatások figyelmeztetések létrehozása a PowerShell használatával
> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [Parancssori felület](insights-alerts-command-line-interface.md)
>
>

> [!NOTE]
> Ez a cikk ismerteti, hogyan régebbi klasszikus metrika riasztások létrehozásához. Az Azure figyelő most támogatja [újabb, metrika riasztások jobb](monitoring-near-real-time-metric-alerts.md). Ezek a riasztások több metrikát, és lehetővé teszik a dimenzionális metrikák riasztást küld. Újabb metrika riasztások PowerShell támogatása hamarosan elérhető.
>
>

Ez a cikk bemutatja, hogyan Azure klasszikus metrika riasztások beállítása a PowerShell használatával.  

Az Azure-szolgáltatások metrikáját alapján riasztásokat fogadhat, vagy az Azure-ban is fogadhatja az eseményekre vonatkozó riasztást.

* **Metrika értékek**: A riasztás elindítja a megadott metrika értékét ebbe a küszöbérték mindkét irányban rendel. Ez azt jelenti, hogy elindítja a mindkét Ha először a feltétel teljesül, majd amikor ez a feltétel már nem van teljesül.    
* **Tevékenység naplóeseményeket**: riasztást aktiválhatók *minden* esemény vagy bizonyos események esetén. Napló tevékenységriasztásokat kapcsolatos további információkért lásd: [tevékenység létrehozása (klasszikus) napló riasztások](monitoring-activity-log-alerts.md).

A következő feladatokat hajthatnak végre, amikor elindítja a klasszikus metrika riasztások konfigurálhatja:

* A szolgáltatás-rendszergazda és a társadminisztrátorok e-mail értesítések küldéséhez.
* E-mail küldése további e-mail-címek.
* A webhook hívja.
* Egy Azure-runbook (csak az Azure portálról) végrehajtásának elindítása.

Konfigurálhatja, és a riasztási szabályok adatainak beolvasása a következő helyekről: 

* [Azure Portal](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Azure parancssori felület (CLI)](insights-alerts-command-line-interface.md)
* [Az Azure figyelő REST API-n](https://msdn.microsoft.com/library/azure/dn931945.aspx)

További információkért mindig beírhatja ```Get-Help``` követ, amelyekre szüksége van segítségre a PowerShell-parancsot.

## <a name="create-alert-rules-in-powershell"></a>A riasztási szabályok létrehozása a PowerShell
1. Jelentkezzen be az Azure-ba.   

    ```PowerShell
    Connect-AzureRmAccount

    ```
2. Az előfizetéseket, amelyek elérhetők a listájának lekérése. Győződjön meg arról, hogy a megfelelő előfizetés dolgozunk. Ha nem, segítségével könnyebben nyerhet a megfelelő előfizetéshez kimenete `Get-AzureRmSubscription`.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. Meglévő erőforráscsoport szabályok listán a következő paranccsal:

   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. Olyan szabály létrehozására, először rendelkezik néhány fontos adatot kell.

    - A **erőforrás-azonosító** az erőforrás be szeretné állítani egy riasztást.
    - A **metrikai meghatározásainak** , amelyek az adott erőforrás érhető el.

     Egy az erőforrás-azonosító eléréséhez módja az Azure-portálon. Feltételezve, hogy az erőforrás már létezik, válassza ki azt a portálon. A következő panelen, majd a a **beállítások** szakaszban jelölje be **tulajdonságok**. **ERŐFORRÁS-azonosító** mező a következő panelen. 
     
     Az erőforrás-azonosítója használatával is megkapható [Azure erőforrás-kezelő](https://resources.azure.com/).

     Az alábbiakban olvashat egy példa egy webalkalmazást az erőforrás-azonosító:

     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Használhat `Get-AzureRmMetricDefinition` egy adott erőforrás minden metrikadefiníciót listájának megtekintéséhez:

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```

     Az alábbi példa létrehoz egy táblát a metrika neve és az adott metrika egység:

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

     ```
     Futtassa a Get-AzureRmMetricDefinition elérhető lehetőségek teljes listájának, `Get-Help Get-AzureRmMetricDefinition -Detailed`.
5. Az alábbi példa állít be egy webhely erőforráson riasztást. A riasztási eseményindítók, ha 5 percig, majd újra amikor megkapja sincs forgalom 5 percig következetesen kap minden forgalom.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```
6. A webhook létrehozása vagy e-mailt küld, ha elindítja a riasztást, először létre kell hoznia az e-mailben vagy webhook. Ezután hozzon létre azonnal a szabályt ezt követően a címkével - műveleteket az alábbi példában látható módon. Már létrehozott szabályok webhookok vagy e-mailek nem lehet társítani.

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```

7. Tekintse meg az egyes szabályokat, hogy a riasztás létrehozása megfelelően.

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```
8. A riasztások törlése. Ezek a parancsok a cikkben korábban létrehozott szabályok törlése.

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>További lépések
* [Az Azure Figyelés áttekintése](monitoring-overview.md), beleértve a adattípusok összegyűjtheti, és figyelje.
* Ismerje meg, hogy [riasztások konfigurálása webhookokkal](insights-webhooks-alerts.md).
* Ismerje meg, hogy [állítsa be a riasztásokat tevékenység naplóeseményeket](monitoring-activity-log-alerts.md).
* További információ [Azure Automation-runbook](../automation/automation-starting-a-runbook.md).
* Első egy [diagnosztikai naplók gyűjtésére áttekintése](monitoring-overview-of-diagnostic-logs.md) nagyon gyakori gyűjtéséhez részletes a a szolgáltatásban.
* Első egy [metrikák gyűjtemény áttekintése](insights-how-to-customize-monitoring.md) ellenőrizze, hogy a szolgáltatás elérhető, és a gyors.
