---
title: Naplóriasztások létrehozása, megtekintése és kezelése az Azure Monitor használatával | Microsoft dokumentumok
description: Az Azure Monitor használatával naplóriasztási szabályokat hoz, tekinthet meg és kezelhet az Azure-ban.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 07/29/2019
ms.subservice: alerts
ms.openlocfilehash: 96b1bd86576f8cf34428eb60e2d3f476312311c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249424"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Naplóriasztások létrehozása, megtekintése és kezelése az Azure Monitor használatával

## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan állíthat be naplóriasztásokat az Azure Portalon belüli riasztások felülethasználatával. A riasztási szabály meghatározása három részből áll:
- Cél: Adott Azure-erőforrás, amelyet figyelni kell
- Feltételek: Konkrét feltétel vagy logika, hogy ha látható a Jel, meg kell indítani a cselekvés
- Művelet: Adott hívás, amelyet egy értesítés címzettjének küldenek - e-mail, SMS, webhook stb.

A **Naplóriasztások** kifejezés olyan riasztások leírására, ahol a jel naplólekérdezés egy [Log Analytics-munkaterületen](../learn/tutorial-viewdata.md) vagy [az Application Insightsban.](../app/analytics.md) További információ a funkciókról, a terminólógiáról és a típusokról a [Naplóriasztások – Áttekintés című témakörben.](alerts-unified-log.md)

> [!NOTE]
> A Log [Analytics-munkaterület](../../azure-monitor/learn/tutorial-viewdata.md) népszerű naplóadatai már az Azure Monitor metrikaplatformján is elérhetők. A részletekért nézet, [Metrika riasztás naplók](alerts-metric-logs.md)

## <a name="managing-log-alerts-from-the-azure-portal"></a>Naplóriasztások kezelése az Azure Portalról

A következő részletes útmutató a naplóriasztások használatával az Azure Portal felület használatával részletesútmutató.

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Naplóriasztási szabály létrehozása az Azure Portalon

1. A [portálon](https://portal.azure.com/)válassza a **Monitor** lehetőséget, és a MONITOR csoportban válassza a **Riasztások**lehetőséget.

    ![Figyelés](media/alerts-log/AlertsPreviewMenu.png)

1. Az **Új riasztási szabály** gombra kattintva hozzon létre egy új riasztást az Azure-ban.

    ![Riasztás hozzáadása](media/alerts-log/AlertsPreviewOption.png)

1. A Riasztás létrehozása szakasz a következő három részből áll: *Riasztási feltétel meghatározása*, *Riasztás részleteinek meghatározása*és *Műveletcsoport meghatározása*.

    ![Szabály létrehozása](media/alerts-log/AlertsPreviewAdd.png)

1. Adja meg a riasztási feltételt az **Erőforrás kiválasztása** hivatkozással, és adja meg a célt egy erőforrás kiválasztásával. A szűrést az _Előfizetés_, _Az Erőforrás típusa_és a Kötelező Erőforrás lehetőség _kiválasztásával szűrheti._

   > [!NOTE]
   > A naplóriasztás létrehozásához ellenőrizze, hogy a **naplójel** elérhető-e a kiválasztott erőforráshoz a folytatás előtt.
   >  ![Erőforrás kiválasztása](media/alerts-log/Alert-SelectResourceLog.png)

1. *Naplóriasztások:* Győződjön meg arról, hogy az **erőforrástípus** olyan elemzési forrás, mint *a Log Analytics* vagy az Application *Insights* és a jeltípus **naplóként,** majd a megfelelő **erőforrás** kiválasztása után kattintson a *Kész*gombra. Ezután használja a **Feltételek hozzáadása** gombot az erőforrás számára elérhető jelbeállítások listájának megtekintéséhez, valamint a jellistából a kiválasztott naplófigyelő szolgáltatás, például a *Log Analytics* vagy az Application *Insights* **egyéni naplókeresési** beállításából.

   ![Erőforrás kiválasztása - egyéni naplókeresés](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > A riasztási listák az elemzési lekérdezést jeltípusként importálhatják – **Napló (Mentett lekérdezés),** ahogy az a fenti ábrán látható. Így a felhasználók tökéletessé tehetik a lekérdezést az Analytics szolgáltatásban, majd menthetik őket későbbi használatra a riasztásokban – további részletek a [naplólekérdezés azure Monitorban vagy](../log-query/log-query-overview.md) [az alkalmazáselemzési elemzésben megosztott lekérdezések](../app/app-insights-overview.md)használatával elérhető lekérdezés mentéséről.

1. *Naplóértesítések:* Miután kiválasztotta, a riasztási lekérdezés megadható a **Keresési lekérdezés** mezőben; ha a lekérdezés szintaxisa helytelen, a mező piros mezőben jelenik meg. Ha a lekérdezés szintaxisa helyes - A megadott lekérdezés korábbi adatainak hivatkozása grafikonként jelenik meg, és az időablak ot az elmúlt hat órától a múlt hétig finomíthat.

    ![Riasztási szabály konfigurálása](media/alerts-log/AlertsPreviewAlertLog.png)

   > [!NOTE]
   > 
   > Az előzményadatok vizualizációja csak akkor jeleníthető meg, ha a lekérdezés eredményei időadatokat tartalmaznak. Ha a lekérdezés összesített adatokat vagy adott oszlopértékeket eredményez , akkor ez egyes számú telekként jelenik meg.
   > Az Application Insights használatával vagy az [új API-ra átváltott](alerts-log-api-switch.md)naplóriasztások metrikamérési típusához megadhatja, hogy melyik adott változó csoportosítsa az adatokat az **Összesítés on** beállítással; az alábbiakban bemutatottak szerint:
   > 
   > ![összesítés opción](media/alerts-log/aggregate-on.png)

1. *Naplóriasztások:* A vizualizáció helyben, **Alert Logic** lehet kiválasztani a megjelenített beállítások feltétel, összesítés és végül küszöb. Végül adja meg a logikában, a megadott feltétel felmérésének idejét a **Period** beállítás használatával. Együtt, hogy milyen gyakran Alert kell futtatni kiválasztásával **Gyakorisága**. **A naplóriasztások** a következőkön alapulhatnak:
    - [Rekordok száma:](../../azure-monitor/platform/alerts-unified-log.md#number-of-results-alert-rules)Riasztás jön létre, ha a lekérdezés által visszaadott rekordok száma nagyobb vagy kisebb, mint a megadott érték.
    - [Metrikamérés:](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)Riasztás jön létre, ha az eredményekben szereplő minden *egyes összesített érték* meghaladja a megadott küszöbértéket, és a kiválasztott érték szerint van *csoportosítva.* A riasztás megsértéseinek száma a küszöbérték túllépésének száma a kiválasztott időszakban. Megadhatja a teljes megsértése bármilyen kombinációja a szabályszegések az eredményhalmazban, vagy egymást követő megsértése, hogy a jogsértések kell történnie az egymást követő mintákban.


1. Második lépésként adja meg a riasztás nevét a **Riasztási szabály neve** mezőben, valamint egy **Leírást,** amely részletezi a riasztás és a **Súlyossági** érték részleteit a megadott beállításokból. Ezeket az adatokat az Azure Monitor minden riasztási e-mailben, értesítésben vagy leküldéses szolgáltatásban újra felhasználja a rendszer. Emellett a felhasználó dönthet úgy, hogy azonnal aktiválja a riasztási szabályt a létrehozáskor a **szabály engedélyezése a létrehozási** lehetőség megfelelő en.

    Csak **a naplóriasztások** esetén néhány további funkció érhető el a Riasztás részleteiben:

    - **Riasztások letiltása**: Ha bekapcsolja a riasztási szabály letiltását, a szabályhoz szükséges műveletek meghatározott ideig le vannak tiltva az új riasztás létrehozása után. A szabály még mindig fut, és riasztási rekordokat hoz létre, feltéve, hogy a feltételek teljesülnek. Így időt ad a probléma kijavítására anélkül, hogy ismétlődő műveleteket futtatna.

        ![A naplóértesítések riasztásának letiltása](media/alerts-log/AlertsPreviewSuppress.png)

        > [!TIP]
        > A riasztás gyakoriságától nagyobb letiltási riasztási érték megadása annak biztosításához, hogy az értesítések átfedés nélkül leálljanak

1. A harmadik és egyben utolsó lépésként adja meg, hogy szükség van-e egy **műveletcsoport** aktiválására a riasztási szabályhoz, ha a riasztási feltétel teljesül. Bármelyik meglévő műveletcsoportot kiválaszthatja riasztással, vagy létrehozhat egy új műveletcsoportot. A kiválasztott műveletcsoport szerint, ha a riasztás eseményindító, az Azure: küldjön e-mailt(ek), küldjön SMS-eket, hívja webhook(oka)t, orvosolja az Azure Runbookok használatával, nyomja meg az ITSM eszközt stb. További információ a [műveletcsoportokról.](action-groups.md)

    > [!NOTE]
    > Tekintse meg az [Azure-előfizetési szolgáltatás korlátait](../../azure-resource-manager/management/azure-subscription-service-limits.md) a Runbook-rakományok az Azure műveletcsoportokon keresztüli naplóriasztásokhoz kiváltott korlátozásairól

    A **Naplóriasztások** esetében néhány további funkció is elérhető az alapértelmezett műveletek felülbírálásához:

    - **E-mail értesítés:** Felülbírálja *az e-mail tárgyát* az e-mailben, amelyet az Action Group-on keresztül küld; ha egy vagy több e-mail művelet létezik az említett műveletcsoportban. Az e-mail törzse nem módosítható, és ez a mező **nem** e-mail címre van kialakítva.
    - **Egyéni Json-tartalom belefoglalása:** Felülbírálja a műveletcsoportok által használt webhook JSON-t; ha egy vagy több webhook-művelet létezik az említett műveletcsoportban. A felhasználó megadhatja a JSON formátumát, amelyet a társított műveletcsoportban konfigurált összes webhookhoz használni kell; A webhook-formátumokról a [Webhook-műveletekről a Naplóriasztások című témakörben talál](../../azure-monitor/platform/alerts-log-webhook.md)további információt. A View Webhook beállítás a formátum ellenőrzéséhez szolgál a Minta JSON-adatok használatával.

        ![Műveletfelülbírálás a naplóriasztásokhoz](media/alerts-log/AlertsPreviewOverrideLog.png)


1. Ha minden mező érvényes, és zöld pipával a **riasztási szabály létrehozása** gombra kattinthat, és riasztás jön létre az Azure Monitor – Riasztások. Az összes riasztás megtekinthető a riasztások irányítópultján.

     ![Szabály létrehozása](media/alerts-log/AlertsPreviewCreate.png)

     Néhány percen belül a riasztás aktív, és a korábban leírt eseményindítók.

A felhasználók is véglegesíthetik az elemzési [lekérdezést a log analytics,](../log-query/portals.md) majd nyomja meg, hogy hozzon létre egy riasztást a "Set Alert" gomb - majd a következő utasításokat a 6 lépéstől kezdve a fenti bemutató.

 ![Log Analytics - Riasztás beállítása](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Naplóriasztások megtekintése & kezelése az Azure Portalon

1. A [portálon](https://portal.azure.com/)válassza a **Monitor** lehetőséget, és a MONITOR csoportban válassza a **Riasztások**lehetőséget.

1. Megjelenik a **Riasztások irányítópult** , amelyben az összes Azure-riasztás (beleértve a naplóriasztásokat is) egy egyes táblán jelenik meg; beleértve minden olyan példányt, amikor a naplóriasztási szabály aktiválódott. További információ: [Alert Management](https://aka.ms/managealertinstances).
    > [!NOTE]
    > A naplóriasztási szabályok a felhasználók által biztosított egyéni lekérdezésalapú logikát tartalmazzák, így a feloldott állapot nélkül. Ezért minden alkalommal, amikor a naplóriasztási szabályban megadott feltételek teljesülnek, a rendszer aktiválódik.

1. A felső **sávon** a Szabályok kezelése gombra kattintva keresse meg a szabálykezelési szakaszt, ahol az összes létrehozott riasztási szabály megjelenik; beleértve a letiltott riasztásokat is.
    ![riasztási szabályok kezelése](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Naplóriasztások kezelése az Azure Resource Template használatával

Az Azure Monitor naplóriasztásai `Microsoft.Insights/scheduledQueryRules/`az erőforrástípushoz vannak társítva. Az erőforrástípusról az [Azure Monitor – Ütemezett lekérdezési szabályok API-hivatkozás című](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)témakörben talál további információt. Az Application Insights vagy a Log Analytics naplóriasztásai [az Ütemezett lekérdezési szabályok API használatával hozhatók](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)létre.

> [!NOTE]
> A Log Analytics naplóriasztásai a korábbi [Log Analytics-riasztási API](api-alerts.md) és a Log Analytics mentett keresések és riasztások örökölt sablonjai használatával is [kezelhetők.](../insights/solutions-resources-searches-alerts.md) Az itt részletezett új ScheduledQueryRules API használatával kapcsolatos további információkért lásd: Váltás az [új API-ra a Log Analytics-riasztásokhoz.](alerts-log-api-switch.md)


### <a name="sample-log-alert-creation-using-azure-resource-template"></a>Mintanapló-riasztás létrehozása az Azure Resource Template használatával

Az alábbiakban az [ütemezett lekérdezési szabályok létrehozásán](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) alapuló erőforrássablon szerkezete látható, amely az [eredménytípusú naplóriasztás](alerts-unified-log.md#number-of-results-alert-rules)szabványos naplókeresési lekérdezését használja, és a mintaadatkészlet változóként szerepel.

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

A fenti minta json menthető (mondjuk) mintaScheduledQueryRule.json e séta céljából, és az Azure Resource Manager használatával telepíthető [az Azure Portalon.](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)


### <a name="log-alert-with-cross-resource-query-using-azure-resource-template"></a>Naplózási riasztás erőforrásközi lekérdezéssel az Azure Resource Template használatával

Az alábbiakban az [ütemezett lekérdezési szabályok létrehozása](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) alapú erőforrássablon szerkezete, amely a [metrikamérési típusú naplóriasztás imetrikatípusú naplóriasztási](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)lekérdezésével használja az [erőforrásközi naplókeresési lekérdezést,](../../azure-monitor/log-query/cross-workspace-query.md) és a mintaadatkészlet változóként van beállítva.

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
> Erőforrásközi lekérdezés használata a naplóriasztásban kötelező az [engedélyezett erőforrások használata,](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate#source) és a felhasználónak hozzáféréssel kell rendelkeznie a megadott erőforrások listájához

A fenti minta json menthető (mondjuk) mintaScheduledQueryRule.json e séta céljából, és az Azure Resource Manager használatával telepíthető [az Azure Portalon.](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)

## <a name="managing-log-alerts-using-powershell"></a>Naplóriasztások kezelése a PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor – [Ütemezett lekérdezési szabályok API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) egy REST API-t, és teljes mértékben kompatibilis az Azure Resource Manager REST API-val. Az alább felsorolt PowerShell-parancsmagok pedig elérhetők az [ütemezett lekérdezési szabályok API-hoz.](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)

1. [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) : Powershell-parancsmag új naplóriasztási szabály létrehozásához.
1. [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) : Powershell-parancsmag egy meglévő naplóriasztási szabály frissítéséhez.
1. [New-AzScheduledQueryRuleSource](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulesource) : Powershell-parancsmag a naplóriasztás forrásparamétereit megadva objektum létrehozásához vagy frissítéséhez. A [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) és a [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) parancsmag bemenetként használja.
1. [New-AzScheduledQueryRuleSchedule](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleSchedule): Powershell-parancsmag a naplóriasztás ütemezési paramétereit megadva objektum létrehozásához vagy frissítéséhez. A [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) és a [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) parancsmag bemenetként használja.
1. [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) : Powershell-parancsmag a naplóriasztás műveletparamétereit megadva objektum létrehozásához vagy frissítéséhez. A [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) és a [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) parancsmag bemenetként használja.
1. [New-AzScheduledQueryRuleAznsActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : Powershell-parancsmag a naplóriasztás műveletcsoportok paramétereit megadva objektum létrehozásához vagy frissítéséhez. A [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) parancsmag bemenetként használja.
1. [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : Powershell-parancsmag a naplóriasztás eseményindítófeltétel-paramétereit megadva objektum létrehozásához vagy frissítéséhez. A [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) parancsmag bemenetként használja.
1. [New-AzScheduledQueryRuleLogMetricTrigger](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : Powershell-parancsmag a [metrikamérési típusnapló](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)riasztásmetrikatrigger feltételparamétereit megíró objektum létrehozásához vagy frissítéséhez. A [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) parancsmag bemenetként használja.
1. [Get-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/get-azscheduledqueryrule) : Powershell-parancsmag a meglévő naplóriasztási szabályok vagy egy adott naplóriasztási szabály listázásához
1. [Update-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/update-azscheduledqueryrule) : Powershell-parancsmag a naplóriasztási szabály engedélyezéséhez vagy letiltásához
1. [Remove-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/remove-azscheduledqueryrule): Powershell-parancsmag meglévő naplóriasztási szabály törléséhez

> [!NOTE]
> Az ScheduledQueryRules PowerShell-parancsmagok csak magát a parancsmamot vagy az Azure Monitor – [Ütemezett lekérdezési szabályok API-t](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)képesek kezelni. A korábbi Log [Analytics riasztási API-val](api-alerts.md) létrehozott naplóriasztási szabályok és a [Log Analytics mentett kereséseiés riasztásainak](../insights/solutions-resources-searches-alerts.md) örökölt sablonjai csak akkor kezelhetők a ScheduledQueryRules PowerShell-parancsmagokkal, ha a felhasználó [átváltja a Log Analytics-riasztások API-preferenciáját.](alerts-log-api-switch.md)

A következő szemléltetése a mintanapló-riasztási szabály létrehozásának lépései az scheduledQueryRules PowerShell-parancsmagok használatával.
```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

## <a name="managing-log-alerts-using-cli-or-api"></a>Naplóriasztások kezelése CLI vagy API használatával

Azure Monitor – [Ütemezett lekérdezési szabályok API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) egy REST API-t, és teljes mértékben kompatibilis az Azure Resource Manager REST API-val. Ezért a Powershell en keresztül az Azure CLI Resource Manager parancsait használhatja.


> [!NOTE]
> A Log Analytics naplóriasztásai a korábbi [Log Analytics-riasztási API](api-alerts.md) és a Log Analytics mentett keresések és riasztások örökölt sablonjai használatával is [kezelhetők.](../insights/solutions-resources-searches-alerts.md) Az itt részletezett új ScheduledQueryRules API használatával kapcsolatos további információkért lásd: Váltás az [új API-ra a Log Analytics-riasztásokhoz.](alerts-log-api-switch.md)

A naplóriasztások jelenleg nem rendelkeznek dedikált CLI-parancsokkal; de az alábbiakban bemutatott módon használható az Azure Resource Manager CLI parancs segítségével a korábban látható erőforrássablon mintasablonhoz (sampleScheduledQueryRule.json) az Erőforrássablon szakaszban:

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
```

Sikeres működés esetén a 201-es rendszer visszakerül az új riasztási szabály létrehozásához, vagy 200 lesz visszaadva, ha egy meglévő riasztási szabályt módosítottak.

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplóriasztásokról az Azure-riasztásokban](../../azure-monitor/platform/alerts-unified-log.md)
* [Webhook-műveletek ismertetése a naplóriasztásokhoz](../../azure-monitor/platform/alerts-log-webhook.md)
* További információ az [Application Insightsról](../../azure-monitor/app/analytics.md)
* További információ a [naplólekérdezésekről.](../log-query/log-query-overview.md)
