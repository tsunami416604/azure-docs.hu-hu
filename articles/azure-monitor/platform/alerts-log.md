---
title: Naplózási riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával | Microsoft Docs
description: Használja a Azure Monitor az Azure-beli naplózási riasztási szabályok létrehozásához, megtekintéséhez és kezeléséhez.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 07/29/2019
ms.subservice: alerts
ms.openlocfilehash: 25604bde3afbbef0d541bc21996b59e98b3090f4
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327497"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Naplózási riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával

## <a name="overview"></a>Áttekintés
Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet napló-riasztásokat a riasztások felületén a Azure Portalon belül. A riasztási szabályokat három összetevő határozza meg:
- Cél: A figyelni kívánt Azure-erőforrás
- Feltételek: az igazság kiértékelésére szolgáló feltétel vagy logika. Ha az értéke igaz, a riasztás tüzet vált.  
- Művelet: a rendszer adott hívást küld egy értesítési e-mail, SMS, webhook stb. fogadójának.

A **log riasztás** kifejezés azokat a riasztásokat ismerteti, amelyekben [Log Analytics munkaterület](../log-query/get-started-portal.md) vagy [Application Insights](../log-query/log-query-overview.md) naplózási lekérdezését kiértékeli a rendszer, és egy riasztás, ha az eredmény igaz. További információ a [naplózási riasztások](alerts-unified-log.md)funkcióival, terminológiával és típusaival kapcsolatban – áttekintés.

> [!NOTE]
> [Log Analytics munkaterületről](../log-query/get-started-portal.md) származó adatok naplózása a Azure monitor metrikák adatbázisába is elvégezhető. A metrikák riasztásai [eltérő viselkedéssel](alerts-metric-overview.md)rendelkeznek, ami kívánatosabb lehet a használt adatoktól függően.   A naplók mérőszámokra való átirányításával kapcsolatos információkért lásd: [metrika riasztás a naplókhoz](alerts-metric-logs.md).

## <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Napló-riasztási szabály létrehozása a Azure Portal

1. A [portálon](https://portal.azure.com/)kattintson a **figyelés**elemre. Ebben a szakaszban válassza a **riasztások**lehetőséget.

    ![Figyelés](media/alerts-log/AlertsPreviewMenu.png)

1. Kattintson az **új riasztási szabály**elemre. 

    ![Riasztás hozzáadása](media/alerts-log/AlertsPreviewOption.png)

1. Megjelenik a **riasztás létrehozása** panel. Négy részből áll: 
    - Az erőforrás, amelyre a riasztás vonatkozik
    - Az ellenőrzési feltétel
    - A feltétel teljesülésekor végrehajtandó művelet
    - A riasztások nevének és leírásának részletei. 

    ![Szabály létrehozása](media/alerts-log/AlertsPreviewAdd.png)

1. Adja meg a riasztási feltételt az **erőforrás kiválasztása** hivatkozásra kattintva, és adja meg a célt egy erőforrás kiválasztásával. A szűréshez válassza ki az *előfizetést*, az *erőforrástípust*és a szükséges *erőforrást*. 

   ![Erőforrás kiválasztása](media/alerts-log/Alert-SelectResourceLog.png)

1. Győződjön meg arról, hogy az **Erőforrás típusa** olyan elemzési forrás, mint a *Log Analytics* vagy a *Application Insights* és a jel típusa *log*. Kattintson a **Kész** gombra. Ezután a **feltételek hozzáadása** gomb használatával megtekintheti az erőforráshoz rendelkezésre álló jel-beállítások listáját. Keresse meg és válassza az **egyéni naplók keresése** lehetőséget *log Analytics* vagy *Application Insights*esetén, attól függően, hogy a rendszer hol tárolja a napló-riasztások adatait.

   ![Erőforrás kiválasztása – egyéni naplók keresése](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > A riasztások listája a fenti ábrán látható módon importálhatja az elemzési lekérdezést jel Type- **log (mentett lekérdezés)** néven. Így a felhasználók elérhetik a lekérdezéseket az elemzésekben, majd menthetik őket a riasztásokban való későbbi használatra. A mentett lekérdezések használatával kapcsolatos további információkért lásd: a [Azure monitor](../log-query/log-query-overview.md) és [a megosztott lekérdezés használata a Application Insights Analyticsben](../app/app-insights-overview.md).

1. A kiválasztást követően hozza létre a riasztási lekérdezést a **keresési lekérdezés** mezőben. Ha a lekérdezés szintaxisa helytelen, a mező piros színnel jelenik meg. 

1. Ha a lekérdezés szintaxisa helyes, akkor a lekérdezés korábbi adatok diagramként jelennek meg az időablak az elmúlt hat órában az utolsó héten való megcsípése lehetőséggel.

    ![Riasztási szabály konfigurálása](media/alerts-log/AlertsPreviewAlertLog.png)

   A korábbi adatvizualizációk csak akkor jelennek meg, ha a lekérdezés eredményeinek ideje részletes. Ha a lekérdezés összegzett vagy adott oszlop típusú értékeket eredményez, a kijelzőn egyetlen mintaterület látható.
  
   Az Application Insights vagy [a log Analytics API](/rest/api/monitor/scheduledqueryrules)-t használó metrikai mérések esetében megadhatja, hogy melyik változót szeretné csoportosítani az adatok **összesítése** lehetőség használatával. az itt látható módon: 
  
   ![Összesítés lehetőség szerint](media/alerts-log/aggregate-on.png)



1. Ezután válassza ki a **riasztási logika** feltételét, az összesítést és a küszöbértéket. 

1. Válassza ki azt az időszakot, amely alatt a megadott feltételt az **időtartam** lehetőség használatával értékeli. 

1. Válassza ki, hogy a riasztás milyen gyakran fusson a **gyakoriságban**. 

    A **naplózási riasztások** a következőeken alapulhatnak:
    - [Rekordok száma](./alerts-unified-log.md#number-of-results-alert-rules): a rendszer riasztást hoz létre, ha a lekérdezés által visszaadott rekordok száma nagyobb vagy kisebb, mint a megadott érték.
    - [Metrika mértéke](./alerts-unified-log.md#metric-measurement-alert-rules): létrejön egy riasztás, ha az eredményekben szereplő összes *összesített érték* meghaladja a megadott küszöbértéket, és a kiválasztott érték *szerint van csoportosítva* . A riasztások megszegésének száma a kiválasztott időszakra vonatkozó küszöbérték túllépése. Megadhatja az összes, az eredményekben vagy egymást követő szabálysértésben előforduló jogsértés kombinációjának teljes megszegését annak érdekében, hogy a szabálysértéseknek egymást követő mintákban is szerepelniük kell.


1. Kattintson a **Kész** gombra. 

1. Adjon meg egy nevet a riasztáshoz a **riasztási szabály neve** mezőben, valamint egy **leírást** , amely részletezi a riasztás és a **Súlyosság** értékét a megadott beállítások alapján. Ezek az adatok a Azure Monitor által végzett összes riasztási e-mailben, értesítésben vagy leküldésen újra felhasználhatók. Emellett dönthet úgy is, hogy azonnal aktiválja a riasztási szabályt a létrehozáshoz a **szabály engedélyezése a létrehozáskor**lehetőségre kattintva.

1. Válassza ki, hogy egy adott időszakra vonatkozóan kívánja-e **letiltani a riasztásokat** .  Ha bekapcsolja a riasztási szabály letiltását, a szabály műveletei le lesznek tiltva egy új riasztás létrehozása után meghatározott időtartamra. A szabály továbbra is fut, és riasztási rekordokat hoz létre, amelyeket a feltételek teljesítenek. Ez a beállítás lehetővé teszi, hogy az ismétlődő műveletek futtatása nélkül javítsa a problémát.

   ![Riasztások mellőzése a naplózási riasztásokhoz](media/alerts-log/AlertsPreviewSuppress.png)

    > [!TIP]
    > A riasztások gyakorisága nagyobb, mint az értesítéseknek az átfedések nélküli leállítását biztosító riasztási érték megadása

1. A harmadik és utolsó lépésként határozza meg, hogy a riasztási szabálynak ki kell-e indítania egy vagy több **műveleti csoportot** a riasztási feltétel teljesülése esetén. Kiválaszthat egy meglévő műveleti csoportot, vagy létrehozhat egy újat is. A műveleti csoportokkal számos műveletet küldhet, például e-mailek küldését, SMS-ek küldését, a webhook (ok) meghívását, a szervizelést az Azure Runbookok használatával, leküldheti a ITSM eszközt, és így tovább. További információ a [műveleti csoportokról](action-groups.md).

    > [!NOTE]
    > Tekintse meg az [Azure-előfizetési szolgáltatás](../../azure-resource-manager/management/azure-subscription-service-limits.md) korlátozásait az elvégezhető műveletek korlátain.  

    Néhány további funkció is elérhető az alapértelmezett műveletek felülbírálásához:

    - **E-mail-értesítés**: felülbírálja az e-mailek *tárgyát* a műveleti csoporton keresztül küldött e-mailben. Nem módosíthatja a levél törzsét, és ez a mező **nem** az e-mail-cím.
    - **Egyéni JSON-adattartalom belefoglalása**: felülbírálja a műveleti csoportok által használt WEBhook JSON-t, feltételezve, hogy a műveleti csoport webhook-típust tartalmaz. A webhook-formátumokkal kapcsolatos további információkért lásd: [webhook művelet a naplózási riasztásokhoz](./alerts-log-webhook.md). A webhook beállítás megadásával ellenőrizhető a minta JSON-adatként használt formátum.

        ![Műveletek felülbírálásai a naplók Értesítéseinél](media/alerts-log/AlertsPreviewOverrideLog.png)


1. Ha az összes mező érvényes, és a zöld pipa jelenik meg, a **riasztási szabály létrehozása** gomb rákattinthat, és a rendszer riasztást hoz létre Azure monitor-riasztásokban. Minden riasztás a riasztások irányítópultról tekinthető meg.

     ![Szabály létrehozása](media/alerts-log/AlertsPreviewCreate.png)

     Néhány percen belül a riasztás aktív, és a korábban leírt módon aktiválódik.

A felhasználók a [log Analyticsben](../log-query/log-query-overview.md) is véglegesítheti Analytics-lekérdezését, majd elküldhetik a riasztást a "riasztás beállítása" gomb használatával, majd a fenti oktatóanyag 6. lépésének utasításait követve.

 ![Log Analytics riasztás beállítása](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Tekintse meg & a naplózási riasztások kezelése Azure Portal

1. A [portálon](https://portal.azure.com/)kattintson a **figyelés** elemre, és a figyelés szakaszban válassza a **riasztások**lehetőséget.

1. Megjelenik a **riasztások irányítópultja** – amelyben az összes Azure-riasztás (beleértve a naplózási riasztásokat is) egy adott számú táblán jelenik meg. a napló riasztási szabályának minden példányát tartalmazza. További információ: [Alert Management](https://aka.ms/managealertinstances).
    > [!NOTE]
    > A naplózási riasztási szabályok a felhasználók által megadott egyéni lekérdezési logikák, és így feloldási állapot nélkül állnak. Mivel a riasztási szabályban megadott feltételek minden alkalommal teljesülnek, a rendszer kirúgták.

1. A felső sávon a **szabályok kezelése** gombra kattintva navigáljon a szabály kezelése szakaszhoz, ahol az összes létrehozott riasztási szabály fel van sorolva; a letiltott riasztásokat is beleértve.
    ![riasztási szabályok kezelése](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>A naplózási riasztások kezelése az Azure Resource template használatával

A Azure Monitor-beli naplózási riasztások erőforrás-típussal vannak társítva `Microsoft.Insights/scheduledQueryRules/` . További információ erről az erőforrás típusáról: [Azure monitor-ütemezett lekérdezési szabályok API-referenciája](/rest/api/monitor/scheduledqueryrules/). Application Insights vagy Log Analytics naplózási riasztásai az [ütemezett lekérdezési szabályok API](/rest/api/monitor/scheduledqueryrules/)használatával hozhatók létre.

> [!NOTE]
> Log Analytics esetén a naplózási riasztások a régi [log Analytics a riasztási API](api-alerts.md) -val és a [log Analytics mentett keresések és riasztások](../insights/solutions.md) örökölt sablonjaival is kezelhetők. Az új ScheduledQueryRules API alapértelmezés szerinti részletes használatával kapcsolatos további információkért lásd: [váltás új API-ra log Analytics riasztások](alerts-log-api-switch.md)esetén.


### <a name="sample-log-alert-creation-using-azure-resource-template"></a>Példa naplóbeli riasztás létrehozására az Azure Resource template használatával

Az alábbi példa az [ütemezett lekérdezési szabályok létrehozásán](/rest/api/monitor/scheduledqueryrules/createorupdate) alapuló erőforrás-sablonjának szerkezete, amely az [eredmények típusú naplóbeli riasztások számának](alerts-unified-log.md#number-of-results-alert-rules)szabványos naplóbeli keresési lekérdezését használja, a minta adatkészlete változóként.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "southcentralus",
        "alertName": "samplelogalert",
        "alertDescription": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"requests",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4"
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescription')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "aznsAction":{
                    "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]"
                }
            }
        }
    } ]
}

```

A fenti JSON-minta menthető a (z) sampleScheduledQueryRule.jsa (z) és a (z) [Azure Portal Azure Resource Manager](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)használatával.


### <a name="log-alert-with-cross-resource-query-using-azure-resource-template"></a>Riasztás naplózása az erőforrások közötti lekérdezéssel az Azure Resource template használatával

A következő példa az [ütemezett lekérdezési szabályok létrehozásán](/rest/api/monitor/scheduledqueryrules/createorupdate) alapuló erőforrás-sablonjának struktúrája a [metrikai mérőszám típusú log-riasztást](./alerts-unified-log.md#metric-measurement-alert-rules)használó [többerőforrásos naplóbeli keresési lekérdezéssel](../log-query/cross-workspace-query.md) , a mintavételi adatok változóként való beállításával.

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "Region Name for your Application Insights App or Log Analytics Workspace",
        "alertName": "sample log alert",
        "alertDescr": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"union workspace(\"servicews\").Update, app('serviceapp').requests | summarize AggregatedValue = count() by bin(TimeGenerated,1h), Classification",
            "Resource1": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Resource2": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/components/serviceapp",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4",
            "SuppressTimeinMin": 20
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "metricMeasurement": {
            "thresholdOperator": "Equal",
            "threshold": "1",
            "metricTriggerType": "Consecutive",
            "metricColumn": "Classification"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescr')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "authorizedResources": "[concat(array(variables('alertSource').Resource1), array(variables('alertSource').Resource2))]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "throttlingInMin": "[variables('alertActions').SuppressTimeinMin]",
                "aznsAction":{
                    "actionGroup": "[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]",
                    "metricTrigger":{
                        "thresholdOperator": "[variables('metricMeasurement').thresholdOperator]",
                        "threshold": "[variables('metricMeasurement').threshold]",
                        "metricColumn": "[variables('metricMeasurement').metricColumn]",
                        "metricTriggerType": "[variables('metricMeasurement').metricTriggerType]"
                    }
                }
            }
        }
    } ]
}

```

> [!IMPORTANT]
> Ha a naplózási riasztásban több erőforrást használó lekérdezést használ, a [authorizedResources](/rest/api/monitor/scheduledqueryrules/createorupdate#source) használata kötelező, és a felhasználónak hozzáféréssel kell rendelkeznie a megadott erőforrások listájához.

A fenti JSON-minta menthető a (z) sampleScheduledQueryRule.jsa (z) és a (z) [Azure Portal Azure Resource Manager](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)használatával.

## <a name="managing-log-alerts-using-powershell"></a>A naplózási riasztások kezelése a PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor – az [ütemezett lekérdezési szabályok API](/rest/api/monitor/scheduledqueryrules/) egy REST API, és teljes mértékben kompatibilis Azure Resource Manager Rest APIokkal. Az alább felsorolt PowerShell-parancsmagok az [ütemezett lekérdezési szabályok API](/rest/api/monitor/scheduledqueryrules/)kihasználása érdekében érhetők el.

- [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) : PowerShell-parancsmag új naplózási riasztási szabály létrehozásához.
- [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) : PowerShell-parancsmag egy meglévő naplózási riasztási szabály frissítéséhez.
- [New-AzScheduledQueryRuleSource](/powershell/module/az.monitor/new-azscheduledqueryrulesource) : PowerShell-parancsmag az objektum létrehozásához vagy frissítéséhez, amely megadja a naplózási riasztás forrásának paramétereit. A [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) és a [set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) parancsmag bemenetként használja.
- [New-AzScheduledQueryRuleSchedule](/powershell/module/az.monitor/new-azscheduledqueryruleschedule): PowerShell-parancsmag az objektum létrehozásához vagy frissítéséhez, amely meghatározza a naplózási riasztások ütemezett paramétereit. A [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) és a [set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) parancsmag bemenetként használja.
- [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) : PowerShell-parancsmag az objektum létrehozásához vagy frissítéséhez, amely a naplózási riasztás műveleti paramétereit határozza meg. A [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) és a [set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) parancsmag bemenetként használja.
- [New-AzScheduledQueryRuleAznsActionGroup](/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : PowerShell-parancsmag az objektum létrehozásához vagy frissítéséhez a műveleti csoportok paramétereinek megadása a naplózási riasztásokhoz. A [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) parancsmag bemenetként használja.
- [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : PowerShell-parancsmag az objektum létrehozásához vagy frissítéséhez, amely meghatározza a riasztási feltétel paramétereit. A [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) parancsmag bemenetként használja.
- [New-AzScheduledQueryRuleLogMetricTrigger](/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : PowerShell-parancsmag az objektum létrehozásához vagy frissítéséhez, amely metrikai trigger feltételi paramétereit adja meg a [metrikai mérték típusú napló riasztásához](./alerts-unified-log.md#metric-measurement-alert-rules). A [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) parancsmag bemenetként használja.
- [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule) : PowerShell-parancsmag a meglévő naplózási riasztási szabályok listázásához vagy egy adott napló riasztási szabályához
- [Update-AzScheduledQueryRule](/powershell/module/az.monitor/update-azscheduledqueryrule) : PowerShell-parancsmag a napló riasztási szabályának engedélyezéséhez vagy letiltásához
- [Remove-AzScheduledQueryRule](/powershell/module/az.monitor/remove-azscheduledqueryrule): PowerShell-parancsmag egy meglévő naplózási riasztási szabály törléséhez

> [!NOTE]
> A ScheduledQueryRules PowerShell-parancsmagok csak saját maguk által létrehozott és a Azure Monitor [ütemezett lekérdezési szabályok API](/rest/api/monitor/scheduledqueryrules/)-val kezelhetik a szabályokat. Az örökölt [log Analytics riasztási API](api-alerts.md) -val létrehozott és a [log Analytics mentett keresések és riasztások](../insights/solutions.md) örökölt sablonjaival létrehozott naplózási riasztási szabályok csak a ScheduledQueryRules PowerShell-parancsmagokkal kezelhetők, miután [a felhasználói kapcsolók API-beállításai log Analytics riasztásokhoz](alerts-log-api-switch.md).

A következő ábrán a scheduledQueryRules PowerShell-parancsmagok használatával végezhető el a minta napló riasztási szabályának létrehozása.

```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

## <a name="managing-log-alerts-using-cli-or-api"></a>A naplózási riasztások kezelése a parancssori felület vagy az API használatával

Azure Monitor – az [ütemezett lekérdezési szabályok API](/rest/api/monitor/scheduledqueryrules/) egy REST API, és teljes mértékben kompatibilis Azure Resource Manager Rest APIokkal. Ezért használhatja a PowerShellt a Resource Manager-parancsok használatával az Azure CLI-hez.


> [!NOTE]
> Log Analytics esetén a naplózási riasztások a régi [log Analytics a riasztási API](api-alerts.md) -val és a [log Analytics mentett keresések és riasztások](../insights/solutions.md) örökölt sablonjaival is kezelhetők. Az új ScheduledQueryRules API alapértelmezés szerinti részletes használatával kapcsolatos további információkért lásd: [váltás új API-ra log Analytics riasztások](alerts-log-api-switch.md)esetén.

A naplózási riasztások jelenleg nem rendelkeznek dedikált CLI-parancsokkal. az alábbi ábrán látható módon azonban Azure Resource Manager CLI-paranccsal használhatja az erőforrás-sablonban korábban megjelenő minta-erőforrás sablonját (sampleScheduledQueryRule.js):

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
```

Sikeres művelet esetén a rendszer visszaadja a 201 új riasztási szabály létrehozásához, vagy ha egy meglévő riasztási szabályt módosított, a 200 értéket adja vissza a rendszer.

## <a name="next-steps"></a>További lépések

* Tudnivalók a [riasztásokról az Azure-riasztásokban](./alerts-unified-log.md)
* [A log-riasztások webhook-műveleteinek](./alerts-log-webhook.md) ismertetése
* További információ a [Application Insights](../log-query/log-query-overview.md)
* További információ a [naplók lekérdezéséről](../log-query/log-query-overview.md).

