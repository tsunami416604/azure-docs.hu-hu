---
title: Több Azure Monitor Application Insights erőforrás egységesítése | Microsoft Docs
description: Ez a cikk részletesen ismerteti, hogyan használható függvények a Azure Monitor naplókban több Application Insights erőforrás lekérdezéséhez és az adatok megjelenítéséhez.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: magoedte
ms.openlocfilehash: d441b72b34da6146eba523563a09c2908cdcbbf4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650131"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Több Azure Monitor Application Insights erőforrás egységesítése 
Ez a cikk azt ismerteti, hogyan lehet lekérdezni és megtekinteni az összes Application Insights naplózási adatait egy helyen, még akkor is, ha azok különböző Azure-előfizetésekben találhatók, a Application Insights Connector elavulttá tételének pótlására. Az egyetlen lekérdezésben felvehető Application Insights erőforrások száma a 100-ra korlátozódik.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Több Application Insights erőforrás lekérdezésének ajánlott módszere 
A lekérdezésekben több Application Insights-erőforrás felsorolása nehézkes és nehéz fenntartani. Ehelyett a függvényt kihasználva elkülönítheti a lekérdezési logikát az alkalmazások hatókörében.  

Ez a példa azt mutatja be, hogyan figyelhető meg több Application Insights erőforrás, és Hogyan jeleníthető meg a sikertelen kérelmek száma az alkalmazás neve alapján. Mielőtt elkezdené, futtassa ezt a lekérdezést a Application Insights erőforrásokhoz csatlakozó munkaterületen a csatlakoztatott alkalmazások listájának lekéréséhez: 

```
ApplicationInsights
| summarize by ApplicationName
```

Hozzon létre egy függvényt a Union operátor használatával az alkalmazások listájával, majd mentse a lekérdezést a munkaterületen a *applicationsScoping*alias függvényként. 

A felsorolt alkalmazásokat bármikor módosíthatja a portálon, ha megnyitja a lekérdezési tallózót a munkaterületen, majd kiválasztja a függvényt a szerkesztéshez, majd a PowerShell- `SavedSearch` parancsmag használatával vagy a mentéshez. 

>[!NOTE]
>Ez a metódus nem használható a naplózási riasztásokkal, mert a riasztási szabály erőforrásainak, köztük a munkaterületeknek és az alkalmazásoknak a hozzáférés-ellenőrzését a riasztás létrehozási ideje végzi. Új erőforrások hozzáadása a függvényhez a riasztás létrehozása után nem támogatott. Ha az erőforrás-hatókör függvényét szeretné használni a naplózási riasztásokban, szerkesztenie kell a riasztási szabályt a portálon, vagy egy Resource Manager-sablonnal a hatókörrel rendelkező erőforrások frissítéséhez. Azt is megteheti, hogy felveszi az erőforrások listáját a napló riasztási lekérdezésében.

A `withsource= SourceApp` parancs hozzáadja a naplót küldő alkalmazást jelölő oszlopokat az eredményekhez. Ebben a példában az elemzési operátor nem kötelező, és az alkalmazás nevét kinyeri az SourceApp tulajdonságból. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

Most már készen áll a applicationsScoping függvény használatára a több erőforrást használó lekérdezésben:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

A lekérdezés Application Insights sémát használ, bár a lekérdezés végrehajtása a munkaterületen történik, mivel a applicationsScoping függvény a Application Insights adatstruktúrát adja vissza. A függvény aliasa az összes definiált alkalmazástól érkező kérések unióját adja vissza. A lekérdezés ezután szűri a sikertelen kérelmeket, és alkalmazás szerint jeleníti meg a trendeket.

![Példa több lekérdezés eredményére](media/unify-app-resource-data/app-insights-query-results.png)

## <a name="query-across-application-insights-resources-and-workspace-data"></a>Lekérdezés Application Insights-erőforrások és a munkaterület-információk között 
Ha leállítja az összekötőt, és olyan időtartományon keresztül kell lekérdezéseket végeznie, amelyet Application Insights adatmegőrzéssel (90 nap) nyírtak, [](../../azure-monitor/log-query/cross-workspace-query.md) akkor a munkaterületen több erőforrással kapcsolatos lekérdezéseket kell végrehajtania, és Application Insights egy köztes erőforrást. időszak. Ez addig tart, amíg az alkalmazások adatai a fent említett új Application Insights adatmegőrzési értékkel halmozódnak. A lekérdezéshez bizonyos manipulációk szükségesek, mivel a Application Insights sémái és a munkaterület eltérő. Tekintse meg a séma eltéréseit a szakasz későbbi részében található táblázatban. 

>[!NOTE]
>Az új [SCHEDULEDQUERYRULES API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)támogatja a naplózási riasztásokban lévő [erőforrás-lekérdezések közötti lekérdezést](../log-query/cross-workspace-query.md) . Alapértelmezés szerint a Azure Monitor az [örökölt log Analytics riasztási API](../platform/api-alerts.md) -t használja az új naplózási riasztási szabályok létrehozásához Azure Portalból, kivéve, ha az [örökölt naplózási riasztások API](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api)-ból vált. A kapcsoló után az új API lesz az új riasztási szabályok alapértelmezett értéke Azure Portalban, és lehetővé teszi az erőforrások közötti lekérdezési napló riasztási szabályainak létrehozását. A [SCHEDULEDQUERYRULES API ARM](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) -sablonjának használata nélkül hozhat létre [erőforrás](../log-query/cross-workspace-query.md) -lekérdezési napló-riasztási szabályokat, de ez a riasztási szabály kezelhető, bár a [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) nem Azure Portal.

Ha például az összekötő leállt a 2018-11-01-on, amikor lekérdezi a naplókat Application Insights erőforrások és alkalmazások adatai között a munkaterületen, a lekérdezés a következő példához hasonlóan fog kialakítani:

```
applicationsScoping //this brings data from Application Insights resources 
| where timestamp between (datetime("2018-11-01") .. now()) 
| where success == 'False' 
| where duration > 1000 
| union ( 
    ApplicationInsights //this is Application Insights data in Log Analytics workspace 
    | where TimeGenerated < (datetime("2018-12-01") 
    | where RequestSuccess == 'False' 
    | where RequestDuration > 1000 
    | extend duration = RequestDuration //align to Application Insights schema 
    | extend timestamp = TimeGenerated //align to Application Insights schema 
    | extend name = RequestName //align to Application Insights schema 
    | extend resultCode = ResponseCode //align to Application Insights schema 
    | project-away RequestDuration , RequestName , ResponseCode , TimeGenerated 
) 
| project timestamp , duration , name , resultCode 
```

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Application Insights és Log Analytics munkaterület sémájának eltérései
Az alábbi táblázat a Log Analytics és Application Insights közötti sémák közötti különbségeket mutatja be.  

| Log Analytics munkaterület tulajdonságai| Erőforrás-tulajdonságok Application Insights|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duration |
| AvailabilityMessage | message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp | timestamp |
| Browser | client_browser |
| City | client_city |
| ClientIP | client_IP |
| Számítógép | cloud_RoleInstance | 
| Country | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| ExceptionType | type |
| OperationID | operation_id |
| OperationName | operation_Name | 
| OS | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duration | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duration | 
| RequestID | id | 
| RequestName | name | 
| RequestSuccess | success | 
| ResponseCode | resultCode | 
| Role | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| URL | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>További lépések

Használat [naplóbeli keresés](../../azure-monitor/log-query/log-query-overview.md) részletes információk az Application Insights-alkalmazások megtekintéséhez.
