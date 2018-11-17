---
title: Keresés erőforrásoknak az Azure Log Analyticsszel |} A Microsoft Docs
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
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 9c7a1ec33f82239a5b95e9bf116fe35694d9df36
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852860"
---
# <a name="perform-cross-resource-log-searches-in-log-analytics"></a>Hajtsa végre az erőforrások közötti naplókeresések a Log Analyticsben  

Korábban az Azure Log Analytics, akkor csak elemezheti az aktuális munkaterületen belüli adatokat, és az előfizetésben megadott többi munkaterület lekérdezését, korlátozott.  Ezenkívül csak keressen telemetriai elem az Application insights segítségével közvetlenül az Application Insights webes alkalmazását a, vagy a Visual Studióból.  Ez is tette a natív módon elemezheti a működési probléma és az alkalmazásadatok együtt.   

Most már lekérdezheti a nemcsak a több Log Analytics-munkaterületek, de is ugyanazt az erőforráscsoportot, egy másik erőforráscsoportot, vagy egy másik előfizetést az adott Application Insights alkalmazásból származó adatok között. Ez biztosít a rendszerre kiterjedően megtekintheti az adatokat.  Ezek a lekérdezéstípusok a főadatbázisról csak [Log Analytics](log-analytics-log-search-portals.md#log-analytics-page). Az erőforrások (Log Analytics-munkaterületek és Application Insights-alkalmazás), amelyeket megadhat egyetlen lekérdezést számát 100-ra korlátozódik. 

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
Erőforrások közötti lekérdezések korrelációját, ha több Log Analytics és az Application Insights-erőforrások adatainak használatakor a lekérdezés összetett és nehezen fenntartható válhat. Támaszkodjon [függvények a Log Analytics](query-language/functions.md) hatókörére a lekérdezés erőforrásokat, amely egyszerűbbé teszi a lekérdezés szerkezete a lekérdezés logikai elkülönítése. A következő példa bemutatja, hogyan figyelheti több Application Insights-erőforrást, és jelenítheti meg a sikertelen kérelmek száma alkalmazásnév szerint. 

Hozzon létre egy lekérdezést, például a következőképpen, hogy az Application Insights-erőforrások a következő hatókörre hivatkozik. A `withsource= SourceApp` parancs hozzáad egy oszlopot, amely az alkalmazás nevét jelöli meg a napló küldött. [A lekérdezés Mentés másként funkció](query-language/functions.md#create-a-function) az aliasszal _applicationsScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Mostantól [ezzel a funkcióval](query-language/functions.md#use-a-function) a következő erőforrások közötti lekérdezésben. A függvény aliasa _applicationsScoping_ kérelmek táblázat unióját adja vissza a meghatározott alkalmazások. A lekérdezés és a sikertelen kérelmek szűrése elérhetővé, és a trendek alkalmazás. A _elemezni_ operátor használata nem kötelező ebben a példában. Az alkalmazás nevét a bontja ki _SourceApp_ tulajdonság.

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| sort by count_ desc 
| render timechart
```
![idődiagramját](media/log-analytics-cross-workspace-search/chart.png)

## <a name="next-steps"></a>További lépések

Tekintse át a [Log Analytics naplóbeli keresési referencia](https://docs.microsoft.com/azure/log-analytics/query-language/kusto) összes elérhető a Log Analytics lekérdezési szintaxis beállítások megtekintéséhez.    
