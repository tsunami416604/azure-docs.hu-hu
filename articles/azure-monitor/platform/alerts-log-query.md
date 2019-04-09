---
title: Riasztási lekérdezések jelentkezzen be az Azure Monitor |} A Microsoft Docs
description: Az Azure Monitor frissítéseket és a egy folyamatot a meglévő lekérdezéseket konvertálása naplóriasztások hatékony lekérdezések írásáról ajánlásokkal.
author: yossi-y
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: bwren
ms.subservice: alerts
ms.openlocfilehash: 429770b7651a93473c03f5e386d8f7b72692c161
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006099"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Az Azure Monitor riasztási lekérdezések naplózása
[Riasztási szabályok alapján az Azure Monitor naplóira](alerts-unified-log.md) futtatása rendszeres időközönként, ezért győződjön meg arról, hogy azok írt terhelést és a késés minimalizálása érdekében. Ez a cikk naplóriasztások hatékony lekérdezések és a egy folyamat alakítása a meglévő lekérdezések írásáról javaslatokat nyújt. 

## <a name="types-of-log-queries"></a>Napló a lekérdezések típusai
[Lekérdezések jelentkezzen be az Azure Monitor](../log-query/log-query-overview.md) kezdődnie vagy egy táblát vagy egy [keresési](/azure/kusto/query/searchoperator) vagy [union](/azure/kusto/query/unionoperator) operátor.

Ha például a következő lekérdezés hatókörét a _SecurityEvent_ tábla- és megkeresi az adott esemény azonosítóját. Ez az egyetlen tábla, amely a lekérdezést kell feldolgozni.

``` Kusto
SecurityEvent | where EventID == 4624 
```

Lekérdezések kezdődő `search` vagy `union` lehetővé teszi, hogy egyszerre több oszlopra egy tábla vagy még több tábla kereshet. Az alábbi példák bemutatják a kifejezés keresése több módszer _memória_:

```Kusto
search "Memory"
search * | where == "Memory"
search ObjectName: "Memory"
search ObjectName == "Memory"
union * | where ObjectName == "Memory"
```

Bár a `search` és `union` is hasznosak során adatfeltárás keres a feltételek teljes adatmodellben kevésbé hatékony, mint egy tábla használata, mivel azok több különböző táblázat kell vizsgálnia. A riasztási szabályok lekérdezések rendszeres időközönként futnak, mivel ez túlzott többletterhelést késés ad hozzá a riasztás eredményezhet. Miatt a terhelés a lekérdezések az Azure-beli naplóriasztási szabály mindig kell kezdődnie, ami javítja a lekérdezési teljesítmény és az eredmények a relevancia alapján végzett egyértelmű hatókör meghatározása az tábla.

## <a name="unsupported-queries"></a>Nem támogatott lekérdezések
Január indítása 11,2019, létrehozásakor vagy módosításakor naplóriasztási szabály használó `search`, vagy `union` operátorok nem támogatott az Azure Portalon. Ezen operátorok segítségével a riasztási szabály hibaüzenetet ad vissza. Ez a változás nem érinti a meglévő riasztási szabályait, és a riasztási szabályok hozhatók létre és szerkeszthetők a Log Analytics API-val. Továbbra is érdemes szabályok módosítása rá egy riasztásra, amely segítségével ezek a lekérdezéstípusok, ha a hatékonyság növelése.  

Naplóriasztási szabályok használatával [erőforrások közötti lekérdezések](../log-query/cross-workspace-query.md) óta erőforrások közötti lekérdezések használata nem ez a változás által érintett `union`, ami korlátozza az adott erőforrásokhoz a lekérdezés hatókörébe. Ez a nem megfelelő `union *` nem használható.  Az alábbi példa érvényes egy riasztási szabály a következő lesz:

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>[Erőforrások közötti lekérdezési](../log-query/cross-workspace-query.md) naplóban riasztások az új támogatott [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Alapértelmezés szerint az Azure Monitor használja a [örökölt Log Analytics-riasztás API](api-alerts.md) új naplófájl riasztási szabályok létrehozását az Azure Portalról, kivéve, ha átvált a [örökölt Log riasztások API](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). A váltás után az új API lesz az alapértelmezett új riasztási szabályok az Azure Portalon, és lehetővé teszi, hogy az erőforrások közötti lekérdezési napló riasztások, szabályok létrehozása. Létrehozhat [erőforrások közötti lekérdezési](../log-query/cross-workspace-query.md) riasztási szabályok jelentkezzen anélkül, hogy a kapcsoló használatával a [scheduledQueryRules API az ARM-sablon](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) –, de ez a riasztási szabály kezelhető azonban [ scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) és nem az Azure Portalról.

## <a name="examples"></a>Példák
Az alábbi példákban olyan használó naplólekérdezések `search` és `union` , és adja meg a lépéseket követve módosíthatja ezeket a lekérdezéseket a riasztási szabályok segítségével.

### <a name="example-1"></a>1. példa
Szeretne létrehozni egy használja a következő lekérdezést, amely lekéri a teljesítmény az adatokat a riasztási szabály `search`: 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

Módosíthatja a lekérdezést, indítsa el a következő lekérdezést a táblához tartozó tulajdonságok:

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

A lekérdezés eredménye a következő jelenik meg, amely a _CounterName_ tulajdonság honnan származnak a _Teljesítményoptimalizált_ tábla. 

Használhatja ezt az eredményt a következő lekérdezést, amelyek használhatók a riasztási szabály létrehozása:

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>2. példa
Szeretne létrehozni egy használja a következő lekérdezést, amely lekéri a teljesítmény az adatokat a riasztási szabály `search`: 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
  

Módosíthatja a lekérdezést, indítsa el a következő lekérdezést a táblához tartozó tulajdonságok:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize by $table 
```
 

A lekérdezés eredménye a következő jelenik meg, amely a _ObjectName_ és _CounterName_ tulajdonság honnan származnak a _Teljesítményoptimalizált_ tábla. 

Használhatja ezt az eredményt a következő lekérdezést, amelyek használhatók a riasztási szabály létrehozása:

``` Kusto
Perf 
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
 

### <a name="example-3"></a>3. példa

Használja a következő lekérdezést, amely is használ egy naplózási riasztási szabályt létrehozni kívánt `search` és `union` teljesítményadatok lekéréséhez: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

Módosíthatja a lekérdezést, indítsa el a következő lekérdezést a tábla első részében a lekérdezés tulajdonságai tartozó azonosításához használatával: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

A lekérdezés eredménye a következő jelenik meg, hogy ezek a Tulajdonságok honnan származnak a _Teljesítményoptimalizált_ tábla. 

Mostantól `union` a `withsource` melyik forrástábla azonosításához parancsot minden egyes sor hozzájárult.

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
| summarize by table 
```
 

A lekérdezés eredménye a következő jelenik meg, hogy ezek a tulajdonságok is honnan származnak a _Teljesítményoptimalizált_ tábla. 

Ezekkel az eredményekkel segítségével a következő lekérdezést, amelyek használhatók a riasztási szabály létrehozása: 

``` Kusto
Perf 
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total" 
| where Computer !in ( 
    (Perf 
    | where CounterName == "% Processor Utility" 
    | summarize by Computer)) 
| summarize Avg_Idle_Time = avg(CounterValue) by Computer 
| count 
``` 

### <a name="example-4"></a>4. példa
Szeretne létrehozni egy használja a következő lekérdezést, amely csatlakozik az eredmények két riasztási szabály `search` lekérdezéseket:

```Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by Computer, Hour = bin(TimeGenerated, 1h) 
| join kind = leftouter ( 
    search in (Heartbeat) OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
```
 

Módosítsa a lekérdezést, indítsa el a következő lekérdezést a tábla, amely tartalmazza azokat a tulajdonságokat az illesztés bal oldalán lévő azonosításához használatával: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by $table 
```
 

Az eredmény azt jelzi, hogy az illesztés bal oldalán tulajdonság tartozik _SecurityEvent_ tábla. 

Most már használja a következő lekérdezést a tábla, amelyben a tulajdonságok a join jobb oldalán lévő azonosításához: 

 
``` Kusto
search in (Heartbeat) OSType == 'Windows' 
| summarize by $table 
```

 
Az eredmény azt jelzi, hogy a tulajdonságokat a join jobb oldalán a szívverés táblához tartozik. 

Ezekkel az eredményekkel segítségével a következő lekérdezést, amelyek használhatók a riasztási szabály létrehozása: 


``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat  
    | where OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
```

## <a name="next-steps"></a>További lépések
- Ismerje meg [naplóriasztások](alerts-log.md) az Azure monitorban.
- Ismerje meg [lekérdezések naplózását](../log-query/log-query-overview.md).

