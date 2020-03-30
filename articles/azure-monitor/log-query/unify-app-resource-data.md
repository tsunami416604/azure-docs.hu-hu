---
title: Több Azure Monitor Application Insights-erőforrás egyesítése | Microsoft dokumentumok
description: Ez a cikk részletesen ismerteti, hogyan használhatja a függvényt az Azure Monitor naplók több Application Insights-erőforrások lekérdezésére és az adatok megjelenítésére.
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: ce58aae3b1db1f0f338d353025d4f277aeb6944f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137494"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Több Azure-figyelő alkalmazáselemzési erőforrásának egyesítése 
Ez a cikk ismerteti, hogyan lehet lekérdezni és megtekinteni az Összes Application Insights-naplóadatok egy helyen, még akkor is, ha azok a különböző Azure-előfizetések, az Application Insights-összekötő evesztésének helyettesítésére. Az Application Insights-erőforrások száma, amelyek egyetlen lekérdezésben szerepelhetnek, 100-ra korlátozódik.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Több Application Insights-erőforrás lekérdezésének ajánlott megközelítése 
Több Application Insights-erőforrás listázása egy lekérdezésben nehézkes és nehezen karbantartható lehet. Ehelyett a függvény takarásában elkülönítheti a lekérdezési logikát az alkalmazások hatókörétől.  

Ez a példa bemutatja, hogyan figyelheti több Application Insights-erőforrások és a sikertelen kérelmek száma az alkalmazás neve.

Hozzon létre egy függvényt az alkalmazáslistát tartalmazó szakszervezeti operátorral, majd mentse a lekérdezést a munkaterületre függvényként az *aliasalkalmazásokkalScoping*. 

A felsorolt alkalmazásokat bármikor módosíthatja a portálon, ha a munkaterületlekérdezés-kezelőjéhez navigál, és kiválasztja a szerkesztésre, majd mentésre szolgáló függvényt, vagy használja a `SavedSearch` PowerShell-parancsmagát. 

>[!NOTE]
>Ez a módszer nem használható naplóriasztások, mert a hozzáférési szabály erőforrásainak, beleértve a munkaterületeket és az alkalmazásokat, a riasztás létrehozása kor történik. Új erőforrások hozzáadása a függvényhez a riasztás létrehozása után nem támogatott. Ha a függvényt a naplóriasztások erőforrás-hatókörének hatóköréhez szeretné használni, a hatókörrel rendelkező erőforrások frissítéséhez a portálon vagy egy Erőforrás-kezelő sablonnal kell szerkesztenie a riasztási szabályt. Azt is megteheti, hogy az erőforrások listáját a naplóriasztási lekérdezés.

A `withsource= SourceApp` parancs egy oszlopot ad hozzá a naplót küldő alkalmazást jelölő eredményekhez. Az elemzési operátor nem kötelező ebben a példában, és az alkalmazás nevét a SourceApp tulajdonságból nyeri ki. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

Most már készen áll az alkalmazásokhasználatáraScoping függvény az erőforrás-közi lekérdezésben:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

A lekérdezés Az Application Insights-sémát használja, bár a lekérdezés a munkaterületen kerül végrehajtásra, mivel az applicationsScoping függvény az Application Insights adatstruktúráját adja vissza. A függvényalias az összes definiált alkalmazás kérelmeinek egyesítését adja vissza. A lekérdezés ezután szűri a sikertelen kérelmeket, és alkalmazásonként megjeleníti a trendeket.

![Lekérdezések közötti eredmények – példa](media/unify-app-resource-data/app-insights-query-results.png)

>[!NOTE]
>Az új [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)támogatja az [erőforrásközi lekérdezést](../log-query/cross-workspace-query.md) a naplóriasztásokban. Alapértelmezés szerint az Azure Monitor az [örökölt Log Analytics riasztási API-t](../platform/api-alerts.md) használja az azure portalúj naplóriasztási szabályainak létrehozásához, kivéve, ha átvált [az örökölt naplóriasztások API-ról.](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) A váltás után az új API lesz az alapértelmezett az új riasztási szabályok az Azure Portalon, és lehetővé teszi, hogy hozzon létre több erőforrásközötti lekérdezési napló riasztások szabályok. Az erőforrások közötti lekérdezési napló riasztási szabályokat anélkül is létrehozhatja, hogy a kapcsolót az [SCHEDULEDQueryRules API ARM sablonjával](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) hozná létre – de ez a riasztási szabály [az ütemezettQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) [API-val](../log-query/cross-workspace-query.md) kezelhető, és nem az Azure Portalról.

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Az Application Insights és a Log Analytics-munkaterületi sémakülönbségek
Az alábbi táblázat a Log Analytics és az Application Insights közötti sémakülönbségeket mutatja be.  

| A Log Analytics munkaterületének tulajdonságai| Az Application Insights erőforrás tulajdonságai|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName alkalmazásneve|
| ApplicationTypeVersion | application_Version |
| Elérhetőségi szám | itemCount (tételszám) |
| Elérhetőségidőtartama | duration |
| AvailabilityMessage | message |
| AvailabilityRunLocation | location |
| ElérhetőségId azonosító | id |
| AvailabilityTestname | név |
| ElérhetőségIdőbélyeg | időbélyeg |
| Böngésző | client_browser |
| Város | client_city |
| ÜgyfélIP-címe | client_IP |
| Computer | cloud_RoleInstance | 
| Ország | client_CountryOrRegion | 
| Egyéni eseményszám | itemCount (tételszám) | 
| CustomEventDimensions | egyéni dimenziók |
| Egyéni eseményneve | név | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| Kivételszáma | itemCount (tételszám) | 
| ExceptionHandledAt | kezelni |
| ExceptionMessage | message | 
| ExceptionType (Kivételtípus) | type |
| OperationID azonosító | operation_id |
| OperationName | operation_Name | 
| Operációs rendszer | client_OS | 
| PageViewCount (PageViewCount) | itemCount (tételszám) |
| PageViewDuration | duration | 
| PageViewName (Lapnézetneve) | név | 
| ParentOperationID azonosító | operation_Id | 
| Kérésszáma | itemCount (tételszám) | 
| RequestDuration (Kérésidőtartama) | duration | 
| RequestID | id | 
| Kérelemnév | név | 
| Kéréssiker | sikeres | 
| ResponseCode (Válaszkód) | resultCode | 
| Szerepkör | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| Munkamenet | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemettryTYpe | type |
| URL-cím | url |
| UserAccountId azonosító | user_AccountId |

## <a name="next-steps"></a>További lépések

A [Naplókeresés](../../azure-monitor/log-query/log-query-overview.md) segítségével megtekintheti az Application Insights-alkalmazások részletes adatait.
