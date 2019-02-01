---
title: Használja őket egységes előtérrendszerként több Azure Monitor az Application Insights-erőforrást |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure Monitor naplóira függvény használatával több Application Insights-erőforrások lekérdezése és megjelenítése az adatokat.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: magoedte
ms.openlocfilehash: e0f305d8200a6b78eb138d5a3c6d9cd99a095dbe
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55486521"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Használja őket egységes előtérrendszerként több Azure Monitor az Application Insights-erőforrást 
Ez a cikk bemutatja, hogyan lekérdezéséhez és az összes Application Insights alkalmazás log adatok megtekintése az egyik helyen, akkor is, ha azokat az Azure-előfizetések, az Application Insights-összekötő elavulása helyettesítője.  

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Ajánlott megközelítést több Application Insights-erőforrások lekérdezése 
A lekérdezés több Application Insights-erőforrások listázása lehet nehézkes és nehezen fenntartható. Ehelyett használhatja a lekérdezés logikai elkülönítése az alkalmazások hatókörének függvény.  

Ez a példa bemutatja, hogyan figyelheti több Application Insights-erőforrást, és jelenítheti meg a sikertelen kérelmek száma alkalmazásnév szerint. Mielőtt elkezdené, futtassa ezt a lekérdezést a munkaterületet, amely csatlakozik egymáshoz kapcsolódó alkalmazások listájának az Application Insights-erőforrások: 

```
ApplicationInsights
| summarize by ApplicationName
```

Hozzon létre union operátor használata az elérhető alkalmazások listája egy függvényt, majd mentse a lekérdezés az aliasszal függvény *applicationsScoping*.  

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

>[!NOTE]
>Módosíthatja a listán szereplő alkalmazások bármikor ellenőrizheti, hogy a naplók portálon és a függvény szerkesztése, vagy a Query explorer a `SavedSearch` PowerShell-parancsmagot. A `withsource= SourceApp` parancs hozzáadja az eredményeket egy oszlopot, amely az alkalmazás azt jelzi a napló küldött. 
>
>A lekérdezés az Application Insights-sémát, használja, bár a lekérdezés végrehajtása a munkaterületen, mivel a applicationsScoping függvényt ad vissza az Application Insights-adatok struktúrája. 
>
>Az elemzési operátor használata nem kötelező ebben a példában, SourceApp tulajdonságból bontja ki az alkalmazás nevét. 

Most már készen áll az erőforrások közötti lekérdezési applicationsScoping függvénnyel:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

A függvény aliasa, a kérelmek unióját adja vissza a meghatározott alkalmazások. A lekérdezés és a sikertelen kérelmek szűrése elérhetővé, és a trendek alkalmazás.

![Példa a kereszt-lekérdezés eredményei](media/unify-app-resource-data/app-insights-query-results.png)

## <a name="query-across-application-insights-resources-and-workspace-data"></a>Application Insights-erőforrások és a munkaterület adatainak lekérdezése 
Amikor leállítja az összekötő, és meg kell keresztül, amely által az Application Insights-adatok megőrzése (90 nap) volt vágott időtartomány-lekérdezések végrehajtásához, kell elvégeznie [erőforrások közötti lekérdezések](../../azure-monitor/log-query/cross-workspace-query.md) a munkaterület és Application Insights erőforrások egy köztes időszakban. Ez nem lesz, amíg a fent említett új Application Insights-adatmegőrzés / gyűlnek az alkalmazások adatokat. A lekérdezés néhány feldolgozás van szükség, mivel a sémák az Application Insights és a munkaterület különböző. Lásd a jelen szakasz későbbi kiemelése a séma különbségek a táblát. 

Például ha az összekötő leállt a 2018-11-01, amikor, naplók az Application Insights-erőforrások és alkalmazások adatok lekérdezése a munkaterületen, a lekérdezés lenne kell kialakítani, az alábbi példához hasonlóan:

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

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Az Application Insights és a Log Analytics munkaterület között
Az alábbi táblázat a Log Analytics és az Application Insights séma különbségeit.  

| Log Analytics-munkaterület tulajdonságai| Application Insights erőforrás-tulajdonságok|
|------------|------------| 
| AnonUserId | user_id|
| Alkalmazásazonosító | appId|
| Alkalmazásnév | Alkalmazásnév|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | az elemek száma |
| AvailabilityDuration | időtartam |
| AvailabilityMessage | message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | név |
| AvailabilityTimestamp | időbélyeg |
| Böngésző | client_browser |
| Város | client_city |
| ClientIP | client_IP |
| Computer | cloud_RoleInstance | 
| Ország | client_CountryOrRegion | 
| CustomEventCount | az elemek száma | 
| CustomEventDimensions | customDimensions |
| CustomEventName | név | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | az elemek száma | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| ExceptionType | type |
| Műveletazonosító: | operation_id |
| OperationName | operation_Name | 
| Operációs rendszer | client_OS | 
| PageViewCount | az elemek száma |
| PageViewDuration | időtartam | 
| PageViewName | név | 
| ParentOperationID | operation_Id | 
| RequestCount | az elemek száma | 
| RequestDuration | időtartam | 
| Kérelemazonosító: | id | 
| RequestName | név | 
| RequestSuccess | sikeres | 
| ResponseCode | Eredménykód | 
| Szerepkör | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| munkamenet-azonosító | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| URL-cím | _url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>További lépések

Használat [naplóbeli keresés](../../azure-monitor/log-query/log-query-overview.md) részletes információk az Application Insights-alkalmazások megtekintéséhez.
