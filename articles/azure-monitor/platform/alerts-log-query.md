---
title: Riasztási lekérdezések naplózása az Azure Monitorban | Microsoft dokumentumok
description: Javaslatokat tartalmaz a naplóriasztások hatékony lekérdezéseinek írásához az Azure Monitor frissítéseiben és a meglévő lekérdezések konvertálásának folyamatában.
author: yossi-y
ms.author: yossiy
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: alerts
ms.openlocfilehash: fdf492b8f103e725046b9b1cbbd079c4d249664a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667788"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Riasztási lekérdezések naplózása az Azure Monitorban
[Az Azure Monitor-naplókon alapuló riasztási szabályok](alerts-unified-log.md) rendszeres időközönként futnak, ezért győződjön meg arról, hogy azok írásra vannak írva a terhelés és a késés minimalizálása érdekében. Ez a cikk javaslatokat tartalmaz a naplóriasztások hatékony lekérdezéseinek és a meglévő lekérdezések konvertálásának folyamatához. 

## <a name="types-of-log-queries"></a>A naplólekérdezések típusai
[Az Azure Monitor ban naplólekérdezések](../log-query/log-query-overview.md) egy táblával, illetve [egy kereséssel](/azure/kusto/query/searchoperator) vagy [szakszervezeti](/azure/kusto/query/unionoperator) operátorral kezdődnek.

Például a következő lekérdezés hatóköre a _SecurityEvent_ tábla, és megkeresi az adott eseményazonosítót. Ez az egyetlen tábla, amelyet a lekérdezésnek fel kell dolgoznia.

``` Kusto
SecurityEvent | where EventID == 4624 
```

Olyan lekérdezések, `union` amelyek `search` egy tábla vagy akár több tábla több oszlopában is kereshetnek. A következő példák a _Memória_kifejezésben való keresés több metódusát mutatják be:

```Kusto
search "Memory"
search * | where == "Memory"
search ObjectName: "Memory"
search ObjectName == "Memory"
union * | where ObjectName == "Memory"
```

Bár `search` `union` az adatok feltárása során hasznos, a teljes adatmodellen keresztül imitált kifejezések kevésbé hatékonyak, mint a tábla használata, mivel több tábla között kell beszkutatniuk. Mivel a riasztási szabályok lekérdezései rendszeres időközönként futnak, ez azt eredményezheti, hogy túlzott terhelési késés térhet hozzá a riasztáshoz. A terhelés miatt az Azure-ban a naplóriasztási szabályok lekérdezéseinek mindig egy táblával kell kezdődniük egy egyértelmű hatókör meghatározásához, amely javítja a lekérdezési teljesítményt és az eredmények relevanciáját.

## <a name="unsupported-queries"></a>Nem támogatott lekérdezések
2019. január 11-től kezdődően a naplóriasztási szabályok létrehozása vagy módosítása, amelyek a használni `search`, vagy `union` operátorok nem támogatott az Azure Portalon. Ha ezeket az operátorokat figyelmeztető szabályban használja, hibaüzenet jelenik meg. A Log Analytics API-val létrehozott és szerkesztett meglévő riasztási szabályokat és riasztási szabályokat ez a változás nem érinti. Továbbra is érdemes módosítani az ilyen típusú lekérdezéseket használó riasztási szabályokat a hatékonyságuk javítása érdekében.  

Ez a módosítás nem érinti az [erőforrásközi lekérdezéseket](../log-query/cross-workspace-query.md) használó `union`naplózási riasztási szabályokat, mivel az erőforrások közötti lekérdezések használata , amely a lekérdezéshatókört meghatározott erőforrásokra korlátozza. Ez nem egyenértékű `union *` azzal, amely nem használható.  A következő példa érvényes lenne egy naplóriasztási szabályban:

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>Az új [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)támogatja az [erőforrásközi lekérdezést](../log-query/cross-workspace-query.md) a naplóriasztásokban. Alapértelmezés szerint az Azure Monitor az [örökölt Log Analytics riasztási API-t](api-alerts.md) használja az azure portalúj naplóriasztási szabályainak létrehozásához, kivéve, ha átvált [az örökölt naplóriasztások API-ról.](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) A váltás után az új API lesz az alapértelmezett az új riasztási szabályok az Azure Portalon, és lehetővé teszi, hogy hozzon létre több erőforrásközötti lekérdezési napló riasztások szabályok. Az erőforrások közötti lekérdezési napló riasztási szabályokat anélkül is létrehozhatja, hogy a kapcsolót az [SCHEDULEDQueryRules API ARM sablonjával](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) hozná létre – de ez a riasztási szabály [az ütemezettQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) [API-val](../log-query/cross-workspace-query.md) kezelhető, és nem az Azure Portalról.

## <a name="examples"></a>Példák
A következő példák közé `search` `union` tartoznak a naplólekérdezések, amelyek használata, és a lépéseket, amelyekkel módosíthatja ezeket a lekérdezéseket a riasztási szabályok használatával.

### <a name="example-1"></a>1. példa
Naplóriasztási szabályt szeretne létrehozni a következő lekérdezéssel, `search`amely a teljesítményadatokat a következő használatával olvassa be: 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

A lekérdezés módosításához kezdje a következő lekérdezéssel annak a táblának a azonosítására, amelyhez a tulajdonságok tartoznak:

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

A lekérdezés eredménye azt mutatja, hogy a _CounterName_ tulajdonság a _Perf_ táblából származik. 

Ezzel az eredménnyel a következő lekérdezést hozhatja létre, amelyet a riasztási szabályhoz használna:

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>2. példa
Naplóriasztási szabályt szeretne létrehozni a következő lekérdezéssel, `search`amely a teljesítményadatokat a következő használatával olvassa be: 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
  

A lekérdezés módosításához kezdje a következő lekérdezéssel annak a táblának a azonosítására, amelyhez a tulajdonságok tartoznak:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize by $table 
```
 

A lekérdezés eredménye azt mutatja, hogy az _ObjectName_ és a _CounterName_ tulajdonság a _Perf_ táblából származik. 

Ezzel az eredménnyel a következő lekérdezést hozhatja létre, amelyet a riasztási szabályhoz használna:

``` Kusto
Perf 
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
 

### <a name="example-3"></a>3. példa

Naplóriasztási szabályt szeretne létrehozni a következő lekérdezéssel, amely mindkettőt használja, `search` és `union` a teljesítményadatok lekéréséhez: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

A lekérdezés módosításához először az alábbi lekérdezéssel azonosítsa azt a táblát, amelyhez a lekérdezés első részében szereplő tulajdonságok tartoznak: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

A lekérdezés eredménye azt mutatja, hogy ezek a tulajdonságok a _Perf_ táblából származnak. 

Most `union` használja `withsource` a parancsot, hogy azonosítsa, hogy melyik forrástábla járult hozzá az egyes sorokhoz.

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
| summarize by table 
```
 

A lekérdezés eredménye azt mutatja, hogy ezek a tulajdonságok is származnak a _Perf_ tábla. 

Ezekkel az eredményekkel a következő lekérdezést hozhatja létre, amelyet a riasztási szabályhoz használna: 

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
Naplóriasztási szabályt szeretne létrehozni a következő lekérdezéssel, amely `search` két lekérdezés eredményéhez csatlakozik:

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
 

A lekérdezés módosításához kezdje a következő lekérdezéssel az illesztés bal oldalán lévő tulajdonságokat tartalmazó tábla azonosítására: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by $table 
```
 

Az eredmény azt jelzi, hogy az illesztés bal oldalán lévő tulajdonságok a _SecurityEvent_ táblához tartoznak. 

Most a következő lekérdezéssel azonosíthatja azt a táblát, amely az illesztés jobb oldalán lévő tulajdonságokat tartalmazza: 

 
``` Kusto
search in (Heartbeat) OSType == 'Windows' 
| summarize by $table 
```

 
Az eredmény azt jelzi, hogy az illesztés jobb oldalán lévő tulajdonságok a Szívverés táblához tartoznak. 

Ezekkel az eredményekkel a következő lekérdezést hozhatja létre, amelyet a riasztási szabályhoz használna: 


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
- Ismerje meg a [naplóriasztások az](alerts-log.md) Azure Monitorban.
- További információ a [naplólekérdezésekről](../log-query/log-query-overview.md).

