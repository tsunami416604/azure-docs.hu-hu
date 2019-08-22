---
title: Erőforrások lekérdezése Azure Monitorsal | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan lehet lekérdezéseket lekérdezni több munkaterületről és az App betekintő alkalmazásból származó erőforrásokról az előfizetésben.
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
ms.date: 06/05/2019
ms.author: magoedte
ms.openlocfilehash: a1ea4012b7cda5b5deab82027e5547a9c9ef786f
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650157"
---
# <a name="perform-cross-resource-log-queries-in-azure-monitor"></a>Erőforrás-naplózási lekérdezések végrehajtása Azure Monitor  

Korábban a Azure Monitor használatával csak az aktuális munkaterületről elemezheti az adatait, és korlátozhatja az előfizetésben definiált több munkaterületre való lekérdezés lehetőségét.  Ezen kívül csak a webalapú alkalmazásból gyűjtött telemetria-elemeket keresheti meg Application Insights közvetlenül Application Insights vagy a Visual studióból. Ezzel a megoldással az operatív és az alkalmazásadatok natív elemzése is megtörtént.

Most már nem csak több Log Analytics-munkaterületre is lekérdezheti, hanem egy adott Application Insights alkalmazásból is egy adott erőforráscsoport, egy másik erőforráscsoport vagy egy másik előfizetés adatait. Ez teljes körű áttekintést nyújt az adatairól. Az ilyen típusú lekérdezéseket csak [log Analytics](portals.md)lehet elvégezni.

## <a name="cross-resource-query-limits"></a>Erőforrások közötti lekérdezési korlátok 

* Az egyetlen lekérdezésben felvehető Application Insights-erőforrások és Log Analytics-munkaterületek száma legfeljebb 100.
* Az erőforrások közötti lekérdezés nem támogatott a Tervező nézetében. Létrehozhat egy lekérdezést a Log Analyticsban, és rögzítheti az Azure-irányítópulton [egy napló lekérdezésének megjelenítéséhez](../learn/tutorial-logs-dashboards.md). 
* Az új [SCHEDULEDQUERYRULES API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)támogatja a naplózási riasztásokban lévő erőforrás-lekérdezések közötti lekérdezést. Alapértelmezés szerint a Azure Monitor az [örökölt log Analytics riasztási API](../platform/api-alerts.md) -t használja az új naplózási riasztási szabályok létrehozásához Azure Portalból, kivéve, ha az [örökölt naplózási riasztások API](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api)-ból vált. A kapcsoló után az új API lesz az új riasztási szabályok alapértelmezett értéke Azure Portalban, és lehetővé teszi az erőforrások közötti lekérdezési napló riasztási szabályainak létrehozását. Az erőforrás-lekérdezési napló riasztási szabályait anélkül hozhatja létre, hogy a kapcsolót a [SCHEDULEDQUERYRULES API Azure Resource Manager sablonnal](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) használja – ez a riasztási szabály azonban kezelhető, bár a [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) nem Azure Portal .


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Lekérdezés Log Analytics munkaterületeken és a Application Insights
Ha a lekérdezésben egy másik munkaterületre szeretne hivatkozni, használja a [*munkaterület*](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression) -azonosítót, és Application Insights alkalmazásból származó alkalmazáshoz használja az [*alkalmazás*](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression) azonosítóját.  

### <a name="identifying-workspace-resources"></a>Munkaterület-erőforrások azonosítása
Az alábbi példák bemutatják Log Analytics munkaterületek lekérdezéseit, hogy a *contosoretail*nevű munkaterület frissítési táblájában lévő naplók összesített számát adja vissza. 

A munkaterület azonosításához többféleképpen is elvégezhető:

* Erőforrás neve – a munkaterület egy ember által olvasható neve, más néven az *összetevő neve*. 

    `workspace("contosoretail-it").Update | count`

* Minősített név – a munkaterület "teljes neve", amely az előfizetés nevét, az erőforráscsoportot és az összetevő nevét alkotja ebben a formátumban: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Mivel az Azure-előfizetések nevei nem egyediek, lehet, hogy ez az azonosító nem egyértelmű. 
    >

* Munkaterület-azonosító – a munkaterület-azonosító a globálisan egyedi azonosítóként (GUID) jelölt minden munkaterülethez rendelt egyedi, nem módosítható azonosító.

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure Resource ID – a munkaterület Azure által meghatározott egyedi identitása. Az erőforrás-azonosítót akkor használja, ha az erőforrás neve nem egyértelmű.  A munkaterületek formátuma a következő: */Subscriptions/subscriptionId/resourcegroups/resourceGroup/Providers/Microsoft. OperationalInsights/munkaterületek/componentName*.  

    Példa:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Alkalmazás azonosítása
Az alábbi példák egy *fabrikamapp* nevű alkalmazásra vonatkozó kérelmek összesített számát adják vissza Application Insights. 

Application Insights alkalmazásának azonosítására az alkalmazás *(azonosító)* kifejezés használható.  Az *azonosító* argumentum a következők egyikét használja az alkalmazáshoz:

* Erőforrás neve – az alkalmazás egy ember által olvasható neve, más néven az *összetevő neve*.  

    `app("fabrikamapp")`

    >[!NOTE]
    >Az alkalmazások név szerinti azonosítása feltételezi az összes elérhető előfizetés egyediségét. Ha több alkalmazás is van a megadott névvel, a lekérdezés a kétértelműség miatt meghiúsul. Ebben az esetben a többi azonosító egyikét kell használnia.

* Minősített név – az alkalmazás "teljes neve", amely az előfizetés neve, az erőforráscsoport és az összetevő neve, ebben a formátumban: *subscriptionName/resourceGroup/componentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Mivel az Azure-előfizetések nevei nem egyediek, lehet, hogy ez az azonosító nem egyértelmű. 
    >

* AZONOSÍTÓ – az alkalmazás alkalmazás-GUID azonosítója.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure Resource ID – az alkalmazás Azure-ban meghatározott egyedi identitása. Az erőforrás-azonosítót akkor használja, ha az erőforrás neve nem egyértelmű. A formátum: */Subscriptions/subscriptionId/resourcegroups/resourceGroup/Providers/Microsoft. OperationalInsights/Components/componentName*.  

    Példa:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Lekérdezés végrehajtása több erőforrás között
Bármelyik erőforrás-példányból több erőforrást is lekérdezheti, ezek lehetnek munkaterületek és alkalmazások együttes használata.
    
Példa két munkaterületre vonatkozó lekérdezésre:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Több erőforrás közötti lekérdezés használata több erőforráshoz
Ha több Log Analytics munkaterületről származó adatok összekapcsolására és Application Insights erőforrásokra vonatkozó több erőforrás-lekérdezést használ, a lekérdezés bonyolult és nehézkesen kezelhető lesz. A függvények kihasználása [Azure monitor napló lekérdezésekben](functions.md) a lekérdezési logika elkülönítése a lekérdezési erőforrások hatóköréről, ami leegyszerűsíti a lekérdezési struktúrát. Az alábbi példa bemutatja, hogyan figyelhető meg több Application Insights erőforrás, és Hogyan jeleníthető meg a sikertelen kérelmek száma az alkalmazás neve alapján. 

Hozzon létre egy, az alábbihoz hasonló lekérdezést, amely a Application Insights erőforrások hatókörére hivatkozik. A `withsource= SourceApp` parancs egy oszlopot hoz létre, amely kijelöli a naplót küldő alkalmazás nevét. [Mentse a lekérdezést függvényként](functions.md#create-a-function) az alias _applicationsScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Ezt a függvényt mostantól egy több erőforrást tartalmazó lekérdezésben is [használhatja](../../azure-monitor/log-query/functions.md#use-a-function) , például a következőhöz. A _applicationsScoping_ függvény alias a kérelmek tábla unióját adja vissza az összes megadott alkalmazásból. A lekérdezés ezután szűri a sikertelen kérelmeket, és alkalmazás szerint jeleníti meg a trendeket. Ebben a példában az _elemzési_ operátor nem kötelező. Kibontja az alkalmazás nevét a _SourceApp_ tulajdonságból.

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
>Ez a metódus nem használható a naplózási riasztásokkal, mert a riasztási szabály erőforrásainak, köztük a munkaterületeknek és az alkalmazásoknak a hozzáférés-ellenőrzését a riasztás létrehozási ideje végzi. Új erőforrások hozzáadása a függvényhez a riasztás létrehozása után nem támogatott. Ha az erőforrás-hatókör függvényét szeretné használni a naplózási riasztásokban, szerkesztenie kell a riasztási szabályt a portálon, vagy egy Resource Manager-sablonnal a hatókörrel rendelkező erőforrások frissítéséhez. Azt is megteheti, hogy felveszi az erőforrások listáját a napló riasztási lekérdezésében.


![Idődiagramját](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>További lépések

- Tekintse át a naplófájlok [elemzését Azure monitor](log-query-overview.md) a naplók áttekintését, valamint a Azure monitor naplózási adatai strukturált módját.
- Tekintse át [Azure monitor a napló lekérdezéseit](query-language.md) , és tekintse meg az összes erőforrást a Azure monitor log lekérdezésekhez.
