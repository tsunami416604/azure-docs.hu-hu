---
title: Erőforrások lekérdezése az Azure Monitorhasználatával | Microsoft dokumentumok
description: Ez a cikk azt ismerteti, hogyan kérdezheti le az előfizetésben több munkaterületés az App Insights-alkalmazás erőforrásait.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/05/2019
ms.openlocfilehash: 4740034bd970f42833125fa43bfdf72f710ac147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249606"
---
# <a name="perform-cross-resource-log-queries-in-azure-monitor"></a>Erőforrások közötti naplólekérdezések végrehajtása az Azure Monitorban  

Korábban az Azure Monitor, csak az aktuális munkaterületről származó adatokat elemezhet, és korlátozta a lekérdezési több munkaterületen az előfizetésben meghatározott.  Emellett csak a webalapú alkalmazásból gyűjtött telemetriai elemek et kereshet, közvetlenül az Application Insightsban vagy a Visual Studióban az Application Insightsban vagy a Visual Studióban. Ez azt is kihívást jelentett, hogy natívan elemezze a működési és alkalmazásadatokat együtt.

Most már lekérdezheti nem csak több Log Analytics-munkaterületeken, hanem az adatokat egy adott Application Insights-alkalmazás ugyanabban az erőforráscsoportban, egy másik erőforráscsoport, vagy egy másik előfizetés. Ez az adatok rendszerszintű áttekintését teszi lehetővé. Ilyen típusú lekérdezéseket csak a [Log Analytics](portals.md)szolgáltatásban hajthat végre.

## <a name="cross-resource-query-limits"></a>Erőforrásközi lekérdezési korlátok 

* Az Application Insights-erőforrások és a Log Analytics-munkaterületek száma, amelyek egyetlen lekérdezésben szerepelhetnek, 100-ra korlátozódik.
* A Nézettervező nem támogatja az erőforrásközi lekérdezést. A Log Analytics szolgáltatásban létrehozhat egy lekérdezést, és rögzítheti azt az Azure irányítópultján [a naplólekérdezés megjelenítéséhez.](../learn/tutorial-logs-dashboards.md) 
* Az új [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)támogatja az erőforrásközi lekérdezést a naplóriasztásokban. Alapértelmezés szerint az Azure Monitor az [örökölt Log Analytics riasztási API-t](../platform/api-alerts.md) használja az azure portalúj naplóriasztási szabályainak létrehozásához, kivéve, ha átvált [az örökölt naplóriasztások API-ról.](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) A váltás után az új API lesz az alapértelmezett az új riasztási szabályok az Azure Portalon, és lehetővé teszi, hogy hozzon létre több erőforrásközötti lekérdezési napló riasztások szabályok. Az erőforrások közötti lekérdezési napló riasztási szabályokat anélkül hozhat létre, hogy a kapcsolót az [Azure Resource Manager sablon takarásban scheduledQueryRules API-val](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) hozlétre – de ez a riasztási szabály kezelhető az [ütemezettQueryRules API-val,](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) és nem az Azure Portalról.


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Lekérdezés a Log Analytics-munkaterületeken és az Application Insights-ból
Ha egy másik munkaterületre szeretne hivatkozni a lekérdezésben, használja a [*munkaterület*](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression) azonosítóját, és az Application Insights alkalmazásához használja az [*alkalmazásazonosítót.*](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression)  

### <a name="identifying-workspace-resources"></a>Munkaterületi erőforrások azonosítása
Az alábbi példák bemutatják a Log Analytics munkaterületeiközötti lekérdezéseket, amelyek a *contosoretail-it*nevű munkaterület Frissítés táblájából származó naplók összesített számát adják vissza. 

A munkaterület azonosítása többféleképpen is elvégezhető:

* Erőforrás neve - a munkaterület ember által olvasható neve, amelyet *néha összetevőnévnek*is neveznek. 

    `workspace("contosoretail-it").Update | count`

* Minősített név - a munkaterület "teljes neve", amely az előfizetés nevéből, erőforráscsoportjából és összetevőnevéből áll ebben a formátumban: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Mivel az Azure-előfizetés nevei nem egyediek, ez az azonosító kétértelmű lehet. 
    >

* Munkaterület-azonosító – A munkaterület-azonosító az egyes munkaterülethez globálisan egyedi azonosítóként (GUID) rendelt egyedi, nem módosítható azonosító.

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure Resource ID – a munkaterület Azure által definiált egyedi identitása. Az erőforrás-azonosítót akkor használja, ha az erőforrás neve nem egyértelmű.  Munkaterületek esetén a formátum a következő: */subscriptions/subscriptionId/resourceGroups/resourceGroup/providers/microsoft. OperationalInsights/workspaces/componentName*.  

    Példa:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Alkalmazás azonosítása
Az alábbi példák egy *fabrikamapp* nevű alkalmazással szemben idozett kérelmek összesített számát adják vissza az Application Insightsban. 

Egy alkalmazás azonosítása az Application Insights ban az *alkalmazás(azonosító)* kifejezéssel végezhető el.  Az *Azonosító* argumentum az alkalmazást az alábbi műveletek egyikével adja meg:

* Erőforrás neve - az alkalmazás emberileg olvasható neve, amelyet *néha összetevőnévnek*is neveznek.  

    `app("fabrikamapp")`

    >[!NOTE]
    >Az alkalmazás név szerint történő azonosítása az összes elérhető előfizetés egyediségét feltételezi. Ha több alkalmazás a megadott nevet, a lekérdezés sikertelen, mert a kétértelműség. Ebben az esetben a többi azonosító egyikét kell használnia.

* Minősített név - az alkalmazás "teljes neve", amely az előfizetés nevéből, erőforráscsoportjából és összetevőnevéből áll ebben a formátumban: *subscriptionName/resourceGroup/componentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Mivel az Azure-előfizetés nevei nem egyediek, ez az azonosító kétértelmű lehet. 
    >

* ID - az alkalmazás GUID az alkalmazás.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure Resource ID - az alkalmazás Azure által definiált egyedi identitása. Az erőforrás-azonosítót akkor használja, ha az erőforrás neve nem egyértelmű. A formátum: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/components/componentName*.  

    Példa:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Lekérdezés végrehajtása több erőforrás között
Lekérdezhet több erőforrás-példányt bármelyik erőforráspéldányból, ezek lehetnek munkaterületek és alkalmazások kombinálva.
    
Példa két munkaterületen lévő lekérdezésre:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Erőforrásközi lekérdezés használata több erőforráshoz
Ha több Log Analytics-munkaterület és az Application Insights-erőforrások adatainak korrelációjára használja az erőforrások közötti lekérdezéseket, a lekérdezés bonyolulttá és nehezen karbantarthatóvá válhat. Az [Azure Monitor naplólekérdezéseinek függvényeit](functions.md) kell használnia a lekérdezési logika elkülönítéséhez a lekérdezési erőforrások hatókörétől, ami leegyszerűsíti a lekérdezési struktúrát. A következő példa bemutatja, hogyan figyelheti több Application Insights-erőforrások és a sikertelen kérelmek száma az alkalmazás neve. 

Hozzon létre egy lekérdezést, mint a következő, amely hivatkozik az Application Insights-erőforrások hatókörét. A `withsource= SourceApp` parancs hozzáad egy oszlopot, amely a naplót küldő alkalmazásnevet jelöli. [Mentse a lekérdezést függvényként](functions.md#create-a-function) az alias _alkalmazásokkalScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Most már [használhatja ezt](../../azure-monitor/log-query/functions.md#use-a-function) a függvényt egy erőforrásközi lekérdezésben, mint például a következő. A függvény alias _applicationsScoping_ visszaadja a kérelmek tábla az összes meghatározott alkalmazások. A lekérdezés ezután szűri a sikertelen kérelmeket, és alkalmazásonként megjeleníti a trendeket. Ebben a példában az _elemzési_ operátor nem kötelező. Kinyeri az alkalmazás nevét a _SourceApp_ tulajdonságból.

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
>Ez a módszer nem használható naplóriasztások, mert a hozzáférési szabály erőforrásainak, beleértve a munkaterületeket és az alkalmazásokat, a riasztás létrehozása kor történik. Új erőforrások hozzáadása a függvényhez a riasztás létrehozása után nem támogatott. Ha a függvényt a naplóriasztások erőforrás-hatókörének hatóköréhez szeretné használni, a hatókörrel rendelkező erőforrások frissítéséhez a portálon vagy egy Erőforrás-kezelő sablonnal kell szerkesztenie a riasztási szabályt. Azt is megteheti, hogy az erőforrások listáját a naplóriasztási lekérdezés.


![Időábra](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>További lépések

- [Tekintse át a naplóadatok elemzése az Azure Monitorban](log-query-overview.md) a naplólekérdezések áttekintését és az Azure Monitor naplóadatainak felépítését.
- Tekintse át az [Azure Monitor naplólekérdezéseit](query-language.md) az Azure Monitor naplólekérdezései összes erőforrásának megtekintéséhez.
