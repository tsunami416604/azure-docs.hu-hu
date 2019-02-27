---
title: Lekérdezés erőforrásoknak az Azure Monitor szolgáltatással |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan végezhet lekérdezéseket erőforrásokat több munkaterületet és az App Insights-alkalmazás az előfizetésében.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: magoedte
ms.openlocfilehash: d69ddcd44fd947f3d1dc61ac960e7b55258c163e
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56872012"
---
# <a name="perform-cross-resource-log-queries-in-azure-monitor"></a>Erőforrások közötti log-lekérdezések végrehajtása az Azure monitorban  

Korábban az Azure monitorral, csak elemezheti az aktuális munkaterületen belüli adatokat, és az előfizetésben megadott többi munkaterület lekérdezését, korlátozott.  Ezenkívül csak keressen telemetriai elem az Application insights segítségével közvetlenül az Application Insights webes alkalmazását a, vagy a Visual Studióból.  Ez is tette a natív módon elemezheti a működési probléma és az alkalmazásadatok együtt.   

Most már lekérdezheti a nemcsak a több Log Analytics-munkaterületek, de is ugyanazt az erőforráscsoportot, egy másik erőforráscsoportot, vagy egy másik előfizetést az adott Application Insights alkalmazásból származó adatok között. Ez biztosít a rendszerre kiterjedően megtekintheti az adatokat.  Ezek a lekérdezéstípusok a főadatbázisról csak [Log Analytics](portals.md).

## <a name="cross-resource-query-limits"></a>Erőforrások közötti lekérdezési korlátok 

* Application Insights-erőforrások, amelyeket megadhat egyetlen lekérdezést számát 100-ra korlátozódik.
* Erőforrások közötti lekérdezés nem támogatott Az adatforrásnézet-tervezőből. A Log Analytics-lekérdezés létrehozásához, és rögzítheti az Azure-irányítópulton és [Naplókeresés ábrázolása](../../azure-monitor/learn/tutorial-logs-dashboards.md#visualize-a-log-search). 
* Az új riasztások az erőforrások közötti lekérdezési támogatott [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Alapértelmezés szerint az Azure Monitor használja a [örökölt Log Analytics-riasztás API](../platform/api-alerts.md) új naplófájl riasztási szabályok létrehozását az Azure Portalról, kivéve, ha átvált a [örökölt Log riasztások API](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). A váltás után az új API lesz az alapértelmezett új riasztási szabályok az Azure Portalon, és lehetővé teszi, hogy az erőforrások közötti lekérdezési napló riasztások, szabályok létrehozása. Hozhat létre erőforrások közötti lekérdezési napló riasztási szabályok anélkül, hogy a kapcsoló használatával a [scheduledQueryRules API az ARM-sablon](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) –, de ez a riasztási szabály kezelhető azonban [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) és nem az Azure Portalról.


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>A Log Analytics-munkaterületek között és az Application Insightsból
Hivatkozhat egy másik munkaterületet, a lekérdezés, használja a [ *munkaterület* ](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression) azonosítóját, és a egy alkalmazást az Application Insightsból, használja a [ *alkalmazás* ](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression)azonosítója.  

### <a name="identifying-workspace-resources"></a>A munkaterület erőforrásai azonosítása
Az alábbi példák bemutatják a lekérdezések összesített száma a naplók a frissítési tábla visszaadása nevű munkaterület a Log Analytics-munkaterületek között *contosoretail-it*. 

Munkaterület azonosítása, lehet, hogy számos módon elvégezhető az egyik:

* Erőforrás - név a munkaterület, más néven egy természetes nyelven olvasható neve *összetevőnév*. 

    `workspace("contosoretail-it").Update | count`
 
    >[!NOTE]
    >A munkaterület azonosítása név alapján feltételezi, hogy az egyedi-e az összes elérhető előfizetés. Ha több alkalmazás a megadott névvel, a lekérdezés a kétértelműség miatt nem sikerült. Ebben az esetben az egyéb azonosítókhoz kötött egyikét kell használnia.

* Minősített - név a "teljes neve" a munkaterületen mikroszolgáltatásokból álló, az előfizetés nevét, az erőforráscsoportot és az összetevő neve a következő formátumban: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count `

    >[!NOTE]
    >Azure-előfizetés nevek nem egyediek, mert lehet, hogy ez az azonosító nem egyértelmű. 
    >

* Munkaterület-azonosító: a munkaterület-azonosító hozzárendelve az egyes munkaterületeken egy globálisan egyedi azonosítóját (GUID) szerinti egyedi, nem módosítható, azonosító érték.

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Az Azure erőforrás-azonosító – az Azure által megadott egyedi identitása a munkaterületen. Erőforrás-Azonosítóját használnia, ha az erőforrás neve nem egyértelmű.  A munkaterületek, formátuma: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. Az OperationalInsights/munkaterületek/componentName*.  

    Példa:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Egy alkalmazás azonosítása
Az alábbi példák az alkalmazás neve kérelmekre összesített számát adja vissza *fabrikamapp* az Application Insightsban. 

Azonosító egy alkalmazást az Application Insights segítségével végezhető a *app(Identifier)* kifejezés.  A *azonosító* argumentum, adja meg az alkalmazást a használatával a következők egyikét:

* Erőforrás - név egy emberi olvasható nevet az alkalmazáshoz, más néven a *összetevőnév*.  

    `app("fabrikamapp")`

* Minősített - név a "teljes neve" az alkalmazás mikroszolgáltatásokból álló, az előfizetés nevét, az erőforráscsoportot és az összetevő neve a következő formátumban: *subscriptionName/resourceGroup/componentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Azure-előfizetés nevek nem egyediek, mert lehet, hogy ez az azonosító nem egyértelmű. 
    >

* Azonosító – az alkalmazás az alkalmazás GUID azonosítója.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Az Azure erőforrás-azonosító – az Azure által megadott egyedi az alkalmazás identitását. Erőforrás-Azonosítóját használnia, ha az erőforrás neve nem egyértelmű. A formátum: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. Az OperationalInsights/components/componentName*.  

    Példa:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>A lekérdezés végrehajtása több erőforrás között
Az erőforrás-példányok bármelyikével tetszőleges irányú lekérdezheti több erőforrást, ezek lehetnek, munkaterületek és alkalmazások kombinált.
    
Példa a munkaterületeket két lekérdezést:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Erőforrások közötti lekérdezéssel több erőforrás
Erőforrások közötti lekérdezések használatával több Log Analytics-munkaterületek és Application Insights-erőforrások adatait, amikor a lekérdezés összetett és nehezen fenntartható válhat. Támaszkodjon [funkciók az Azure monitorban lekérdezések naplózását](functions.md) hatókörére a lekérdezés erőforrásokat, amely egyszerűbbé teszi a lekérdezés szerkezete a lekérdezés logikai elkülönítése. A következő példa bemutatja, hogyan figyelheti több Application Insights-erőforrást, és jelenítheti meg a sikertelen kérelmek száma alkalmazásnév szerint. 

Hozzon létre egy lekérdezést, például a következőképpen, hogy az Application Insights-erőforrások a következő hatókörre hivatkozik. A `withsource= SourceApp` parancs hozzáad egy oszlopot, amely az alkalmazás nevét jelöli meg a napló küldött. [A lekérdezés Mentés másként funkció](functions.md#create-a-function) az aliasszal _applicationsScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Mostantól [ezzel a funkcióval](../../azure-monitor/log-query/functions.md#use-a-function) a következő erőforrások közötti lekérdezésben. A függvény aliasa _applicationsScoping_ kérelmek táblázat unióját adja vissza a meghatározott alkalmazások. A lekérdezés és a sikertelen kérelmek szűrése elérhetővé, és a trendek alkalmazás. A _elemezni_ operátor használata nem kötelező ebben a példában. Az alkalmazás nevét a bontja ki _SourceApp_ tulajdonság.

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```
![idődiagramját](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>További lépések

- Felülvizsgálat [naplóadatok elemzése az Azure monitorban](log-query-overview.md) naplólekérdezések, és hogyan épül fel az Azure Monitor naplóadatok áttekintését.
- Felülvizsgálat [Azure Monitor log-lekérdezések](query-language.md) összes erőforrást az Azure Monitor log-lekérdezések megtekintése.
