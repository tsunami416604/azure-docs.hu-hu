---
title: Naplózási riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával | Microsoft Docs
description: Használja a Azure Monitor az Azure-beli naplózási riasztási szabályok létrehozásához, megtekintéséhez és kezeléséhez.
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: d540912d280302d2ae2166b75fb2d163458d7a61
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677828"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Naplózási riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával

## <a name="overview"></a>Áttekintés
Ebből a cikkből megtudhatja, hogyan állíthatja be a naplózási riasztásokat az Azure Portal riasztások felületének használatával. A riasztási szabályok definíciója három részből áll:
- Cél: A figyelni kívánt Azure-erőforrás
- Feltételek: A jelet a művelet elindítására szolgáló konkrét feltételnek vagy logikának kell megjelennie.
- Művelet: Értesítés küldése e-mailben, SMS-ben, webhookban stb.

A naplókban szereplő riasztások olyan riasztások leírására szolgálnak, amelyekben a jel egy [log Analytics](../learn/tutorial-viewdata.md) munkaterületen vagy [Application Insights](../app/analytics.md). További információ a naplózási riasztások funkcióival, terminológiával és típusaival kapcsolatban [– Áttekintés](alerts-unified-log.md).

> [!NOTE]
> A [log Analytics munkaterület](../../azure-monitor/learn/tutorial-viewdata.md) népszerű naplózási adatai mostantól a Azure monitor metrika platformján is elérhetők. Részletek nézetben a [naplók metrikai riasztása](alerts-metric-logs.md)

## <a name="managing-log-alerts-from-the-azure-portal"></a>A naplózási riasztások kezelése a Azure Portalból

A következő részletes útmutató a log-riasztások Azure Portal felülettel való használatának lépései.

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Napló-riasztási szabály létrehozása a Azure Portal

1. A [portálon](https://portal.azure.com/)kattintson a **figyelés** elemre, és a figyelés szakaszban válassza a **riasztások**lehetőséget.

    ![Figyelés](media/alerts-log/AlertsPreviewMenu.png)

1. Az új riasztási **szabály** gomb kiválasztásával új riasztást hozhat létre az Azure-ban.

    ![Riasztás hozzáadása](media/alerts-log/AlertsPreviewOption.png)

1. A riasztás létrehozása szakasz az alábbi három részből áll: A *riasztási feltétel meghatározása*, a *riasztás részleteinek*meghatározása és a *műveleti csoport*definiálása.

    ![Szabály létrehozása](media/alerts-log/AlertsPreviewAdd.png)

1. Adja meg a riasztási feltételt az **erőforrás kiválasztása** hivatkozásra kattintva, és adja meg a célt egy erőforrás kiválasztásával. A szűréshez válasszaki az előfizetést, az _erőforrástípust_és a szükséges _erőforrást_.

   > [!NOTE]
   > A naplózási riasztások létrehozásához – ellenőrizze, hogy a naplózási jel elérhető-e a kiválasztott erőforráshoz a folytatás előtt.
   >  ![Erőforrás kiválasztása](media/alerts-log/Alert-SelectResourceLog.png)

1. *Naplók riasztásai*: Győződjön meg arról, hogy az **Erőforrás típusa** egy elemzési forrás, például *log Analytics* vagy *Application Insights* és a jel típusa **naplóként**, majd a megfelelő **erőforrás** választása után kattintson a *kész*gombra. Ezután a **feltételek hozzáadása** gomb használatával megtekintheti az erőforráshoz rendelkezésre álló jel-beállításokat, valamint a Szignál **lista egyéni naplók keresése** lehetőségét a kiválasztott naplózási figyelő szolgáltatáshoz (például *log Analytics* vagy *Application Insights*).

   ![Erőforrás kiválasztása – egyéni naplók keresése](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > A riasztások listája a fenti ábrán látható módon importálhatja az elemzési lekérdezést jel Type- **log (mentett lekérdezés)** néven. Így a felhasználók tökéletesítheti a lekérdezést az elemzésekben, majd a riasztások későbbi használatba vételével megtakaríthatja őket – további részletek a lekérdezés az Application ininsights Analyticsben [való Azure monitor](../log-query/log-query-overview.md) vagy [megosztott lekérdezésben](../app/app-insights-overview.md)való használatával történő mentéséről.

1. *Naplók riasztásai*: A kiválasztást követően a riasztás lekérdezése a **keresési lekérdezés** mezőben adható meg; Ha a lekérdezés szintaxisa helytelen, a mező piros színnel jeleníti meg a hibát. Ha a lekérdezés szintaxisa helyes – a megadott lekérdezéshez tartozó korábbi adatok hivatkozása diagramként jelenik meg, amely az utolsó hat óra és a múlt hét közötti időintervallumot mutatja.

    ![Riasztási szabály konfigurálása](media/alerts-log/AlertsPreviewAlertLog.png)

   > [!NOTE]
   > 
   > A korábbi adatmegjelenítés csak akkor jeleníthető meg, ha a lekérdezés eredményeinek ideje részletes. Ha a lekérdezés összegzett vagy adott oszlopos értékeket eredményez, akkor az azonos számú mintaterületként jelenik meg.
   > A naplózási riasztások metrikai típusához Application Insights vagy [az új API](alerts-log-api-switch.md)-ra való áttéréskor megadhatja, hogy melyik változót kell az adatok csoportosítására használni az **Összesítés** lehetőség használatával. az alábbi ábrán látható módon:
   > 
   > ![Összesítés lehetőség szerint](media/alerts-log/aggregate-on.png)

1. *Naplók riasztásai*: A vizualizáció a helyén a **riasztási logikát** kiválaszthatja a feltételnek, az összesítésnek és a végső küszöbértéknek a megjelenített lehetőségei közül. Végül adja meg a logikát a megadott feltételnek az **időtartam** beállítással való értékeléséhez szükséges idő alapján. A **gyakoriság**lehetőség kiválasztásával együtt, hogy milyen gyakran kell futtatni a riasztást. A naplózási riasztások a következőeken alapulhatnak:
    - [Rekordok száma](../../azure-monitor/platform/alerts-unified-log.md#number-of-results-alert-rules): A rendszer riasztást hoz létre, ha a lekérdezés által visszaadott rekordok száma nagyobb vagy kisebb, mint a megadott érték.
    - [Metrika mértékegysége](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules): A rendszer riasztást hoz létre, ha az eredményekben szereplő összes *összesített érték* meghaladja a megadott küszöbértéket, és a kiválasztott érték *szerint* van csoportosítva. A riasztások megszegésének száma a kiválasztott időszakra vonatkozó küszöbérték túllépése. Megadhatja az összes, az eredményekben vagy egymást követő szabálysértésben előforduló jogsértés kombinációjának teljes megszegését annak érdekében, hogy a szabálysértéseknek egymást követő mintákban is szerepelniük kell.


1. Második lépésként adjon meg egy nevet a riasztáshoz a riasztási **szabály neve** mezőben, valamint egy leírást, amely részletesen **ismerteti** a riasztásra vonatkozó jellemzőket, valamint a **súlyossági** értéket a megadott beállítások alapján. Ezeket az adatokat a rendszer minden riasztási e-mailben, értesítésben vagy Azure Monitor által végzett küldéssel újra felhasználja. A felhasználó emellett dönthet úgy is, hogy azonnal aktiválja a riasztási szabályt a létrehozáshoz, ha a létrehozási beállítással megfelelően bekapcsolja az engedélyezési **szabályt** .

    Csak **naplózási riasztások** esetén a riasztás részleteiben további funkciók érhetők el:

    - **Riasztások mellőzése**: Ha bekapcsolja a riasztási szabály letiltását, a szabály műveletei le lesznek tiltva egy új riasztás létrehozása után meghatározott időtartamra. A szabály továbbra is fut, és riasztási rekordokat hoz létre, amelyeket a feltételek teljesítenek. Lehetővé teszi, hogy az ismétlődő műveletek futtatása nélkül javítsa a problémát.

        ![Riasztások mellőzése a naplózási riasztásokhoz](media/alerts-log/AlertsPreviewSuppress.png)

        > [!TIP]
        > A riasztás gyakorisága nagyobb, mint az értesítéseknek az átfedések nélküli leállítását biztosító riasztási érték megadása

1. A harmadik és utolsó lépésként határozza meg, hogy a riasztási szabályhoz kell-e indítani valamilyen **műveleti csoportot** , ha a riasztási feltétel teljesül. Bármelyik meglévő műveleti csoportot kiválaszthatja riasztással, vagy létrehozhat egy új műveleti csoportot. A kiválasztott műveleti csoport szerint a riasztás indításakor az Azure a következő lesz: e-mailek küldése, SMS-ek küldése, hívás webhook (ok), szervizelés az Azure Runbookok, leküldés a ITSM eszközre stb. További információ a [műveleti csoportokról](action-groups.md).

    > [!NOTE]
    > Tekintse meg az [Azure-előfizetési szolgáltatási](../../azure-subscription-service-limits.md) korlátokat az Azure-műveleti csoportokon keresztül a naplózási riasztásokra váltott Runbook-hasznos adatokra

    A **naplózási riasztások** esetében néhány további funkció is elérhető az alapértelmezett műveletek felülbírálásához:

    - **Értesítő e-mail**: Felülbírálja az *e-mail tárgyát* az e-mailben, a műveleti csoporton keresztül elküldve; Ha egy vagy több e-mail művelet szerepel az említett műveleti csoportban. Nem módosíthatja a levél törzsét, és ez a mező **nem** az e-mail-cím.
    - **Egyéni JSON**-adattartalom belefoglalása: Felülbírálja a műveleti csoportok által használt webhook JSON-t. Ha egy vagy több webhook-művelet létezik az említett műveleti csoportban. A felhasználó megadhatja a JSON formátumát, amelyet a társított műveleti csoportban konfigurált összes webhookhoz használni kíván. a webhook-formátumokkal kapcsolatos további információkért lásd: [webhook művelet a naplózási riasztásokhoz](../../azure-monitor/platform/alerts-log-webhook.md). A webhook beállítás megadásával ellenőrizhető a minta JSON-adatként használt formátum.

        ![Műveletek felülbírálásai a naplók Értesítéseinél](media/alerts-log/AlertsPreviewOverrideLog.png)


1. Ha az összes mező érvényes, és a zöld pipa jelenik meg, a **riasztási szabály létrehozása** gomb rákattinthat, és a rendszer riasztást hoz létre Azure monitor-riasztásokban. Minden riasztás a riasztások irányítópultról tekinthető meg.

     ![Szabály létrehozása](media/alerts-log/AlertsPreviewCreate.png)

     Néhány percen belül a riasztás aktív, és a korábban leírt módon aktiválódik.

A felhasználók a [log Analyticsben](../log-query/portals.md) is véglegesítették Analytics-lekérdezését, majd a riasztások beállítása gomb használatával elküldhetik a riasztást, majd a fenti oktatóanyag 6. lépésében szereplő utasításokat követve.

 ![Log Analytics riasztás beállítása](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Tekintse meg & a naplózási riasztások kezelése Azure Portal

1. A [portálon](https://portal.azure.com/)kattintson a **figyelés** elemre, és a figyelés szakaszban válassza a **riasztások**lehetőséget.

1. Megjelenik a riasztások irányítópultja – amelyben az összes Azure-riasztás (beleértve a naplózási riasztásokat is) egy adott számú táblán jelenik meg. a napló riasztási szabályának minden példányát tartalmazza. További információ: [Alert Management](https://aka.ms/managealertinstances).
    > [!NOTE]
    > A naplózási riasztási szabályok a felhasználók által megadott egyéni lekérdezési logikák, és így feloldási állapot nélkül állnak. Mivel a riasztási szabályban megadott feltételek minden alkalommal teljesülnek, a rendszer kirúgták.

1. A felső sávon a **szabályok kezelése** gombra kattintva navigáljon a szabály kezelése szakaszhoz, ahol az összes létrehozott riasztási szabály fel van sorolva; a letiltott riasztásokat is beleértve.
    ![riasztási szabályok kezelése](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>A naplózási riasztások kezelése az Azure Resource template használatával

A Azure Monitor-beli naplózási riasztások erőforrás `Microsoft.Insights/scheduledQueryRules/`-típussal vannak társítva. További információ erről az erőforrás típusáról: [Azure monitor-ütemezett lekérdezési szabályok API-referenciája](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/). Application Insights vagy Log Analytics naplózási riasztásai az [ütemezett lekérdezési szabályok API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)használatával hozhatók létre.

> [!NOTE]
> Log Analytics esetén a naplózási riasztások a régi [log Analytics a riasztási API](api-alerts.md) -val és a [log Analytics mentett keresések és riasztások](../insights/solutions-resources-searches-alerts.md) örökölt sablonjaival is kezelhetők. Az új ScheduledQueryRules API alapértelmezés szerinti részletes használatával kapcsolatos további információkért lásd: [váltás új API](alerts-log-api-switch.md)-ra log Analytics riasztások esetén.


### <a name="sample-log-alert-creation-using-azure-resource-template"></a>Példa naplóbeli riasztás létrehozására az Azure Resource template használatával

Az alábbi példa az [ütemezett lekérdezési szabályok létrehozásán](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) alapuló erőforrás-sablonjának szerkezete, amely az [eredmények típusú naplóbeli riasztások számának](alerts-unified-log.md#number-of-results-alert-rules)szabványos naplóbeli keresési lekérdezését használja, a minta adatkészlete változóként.

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

A fenti JSON-minta menthető a (Say) sampleScheduledQueryRule. JSON néven, és a [Azure Resource Manager Azure Portal](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)használatával is üzembe helyezhető.


### <a name="log-alert-with-cross-resource-query-using-azure-resource-template"></a>Riasztás naplózása az erőforrások közötti lekérdezéssel az Azure Resource template használatával

A következő példa az [ütemezett lekérdezési szabályok létrehozásán](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) alapuló erőforrás-sablonjának struktúrája a metrikai [mérőszám típusú log](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)-riasztást használó többerőforrásos [naplóbeli keresési lekérdezéssel](../../azure-monitor/log-query/cross-workspace-query.md) , a mintavételi adatok változóként való beállításával.

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
> Ha a naplózási riasztásban több erőforrást használó lekérdezést használ, a [authorizedResources](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate#source) használata kötelező, és a felhasználónak hozzáféréssel kell rendelkeznie a megadott erőforrások listájához.

A fenti JSON-minta menthető a (Say) sampleScheduledQueryRule. JSON néven, és a [Azure Resource Manager Azure Portal](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)használatával is üzembe helyezhető.

## <a name="managing-log-alerts-using-powershell"></a>A naplózási riasztások kezelése a PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor – az [ütemezett lekérdezési szabályok API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) egy REST API, és teljes mértékben kompatibilis Azure Resource Manager Rest APIokkal. Az alább felsorolt PowerShell-parancsmagok az [ütemezett lekérdezési szabályok API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)kihasználása érdekében érhetők el.

1. [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) : PowerShell-parancsmag új naplózási riasztási szabály létrehozásához.
1. [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) : PowerShell-parancsmag egy meglévő naplózási riasztási szabály frissítéséhez.
1. [New-AzScheduledQueryRuleSource](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulesource) : PowerShell-parancsmag olyan objektum létrehozásához vagy frissítéséhez, amely megadja a naplózási riasztás forrásának paramétereit. A [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) és a [set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) parancsmag bemenetként használja.
1. [New-AzScheduledQueryRuleSchedule](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleSchedule): PowerShell-parancsmag az objektum létrehozásához vagy frissítéséhez, amely meghatározza a naplózási riasztások ütemezett paramétereit. A [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) és a [set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) parancsmag bemenetként használja.
1. [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) : PowerShell-parancsmag olyan objektum létrehozásához vagy frissítéséhez, amely a naplózási riasztás műveleti paramétereit határozza meg. A [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) és a [set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) parancsmag bemenetként használja.
1. [New-AzScheduledQueryRuleAznsActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : PowerShell-parancsmag olyan objektum létrehozásához vagy frissítéséhez, amely megadja a műveleti csoportok paramétereit a naplózási riasztásokhoz. A [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) parancsmag bemenetként használja.
1. [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : PowerShell-parancsmag az objektum létrehozásához vagy frissítéséhez, amely megadja a riasztási feltétel paramétereit a naplózási riasztáshoz. A [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) parancsmag bemenetként használja.
1. [New-AzScheduledQueryRuleLogMetricTrigger](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : PowerShell-parancsmag olyan objektum létrehozásához vagy frissítéséhez, amely metrika-trigger feltételi paramétereit adja meg a [metrika mértékének típusú napló riasztásához](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules). A [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) parancsmag bemenetként használja.
1. [Get-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/get-azscheduledqueryrule) : PowerShell-parancsmag meglévő naplózási riasztási szabályok vagy egy adott napló-riasztási szabály listázásához
1. [Update-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/update-azscheduledqueryrule) : PowerShell-parancsmag a napló riasztási szabályának engedélyezéséhez vagy letiltásához
1. [Remove-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/remove-azscheduledqueryrule): PowerShell-parancsmag meglévő naplózási riasztási szabály törléséhez

> [!NOTE]
> A ScheduledQueryRules PowerShell-parancsmagok csak saját maguk által létrehozott és a Azure Monitor [ütemezett lekérdezési szabályok API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)-val kezelhetik a szabályokat. A örökölt [log Analytics riasztási API](api-alerts.md) -val és [log Analytics a mentett keresések és](../insights/solutions-resources-searches-alerts.md) a riasztások örökölt sablonjaival létrehozott naplózási riasztási szabályok csak a ScheduledQueryRules PowerShell-parancsmagokkal kezelhetők, csak a bejelentkezéshez használt felhasználói kapcsolók API-beállításai után [ Elemzési riasztások](alerts-log-api-switch.md).

A következő ábrán a scheduledQueryRules PowerShell-parancsmagok használatával végezhető el a minta napló riasztási szabályának létrehozása.
```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ \"alert\":\"#alertrulename\", \"IncludeSearchResults\":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

## <a name="managing-log-alerts-using-cli-or-api"></a>A naplózási riasztások kezelése a parancssori felület vagy az API használatával

Azure Monitor – az [ütemezett lekérdezési szabályok API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) egy REST API, és teljes mértékben kompatibilis Azure Resource Manager Rest APIokkal. Ezért használhatja a PowerShellt a Resource Manager-parancsok használatával az Azure CLI-hez.


> [!NOTE]
> Log Analytics esetén a naplózási riasztások a régi [log Analytics a riasztási API](api-alerts.md) -val és a [log Analytics mentett keresések és riasztások](../insights/solutions-resources-searches-alerts.md) örökölt sablonjaival is kezelhetők. Az új ScheduledQueryRules API alapértelmezés szerinti részletes használatával kapcsolatos további információkért lásd: [váltás új API](alerts-log-api-switch.md)-ra log Analytics riasztások esetén.

A naplózási riasztások jelenleg nem rendelkeznek dedikált CLI-parancsokkal. az alábbi ábrán látható módon azonban Azure Resource Manager CLI-paranccsal használhatja a korábban (sampleScheduledQueryRule. JSON) nevű minta-erőforrást az erőforrás-sablon szakaszban:

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
```

Sikeres művelet esetén a rendszer visszaadja a 201 új riasztási szabály létrehozásához, vagy ha egy meglévő riasztási szabályt módosított, a 200 értéket adja vissza a rendszer.

## <a name="next-steps"></a>További lépések

* Tudnivalók a [riasztásokról az Azure-riasztásokban](../../azure-monitor/platform/alerts-unified-log.md)
* [A log](../../azure-monitor/platform/alerts-log-webhook.md) -riasztások webhook-műveleteinek ismertetése
* További információ a [Application Insights](../../azure-monitor/app/analytics.md)
* További információ a [naplók lekérdezéséről](../log-query/log-query-overview.md).
