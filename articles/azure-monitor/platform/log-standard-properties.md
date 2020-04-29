---
title: Azure Monitor naplóbejegyzések szabványos tulajdonságai | Microsoft Docs
description: A Azure Monitor-naplók több adattípusának gyakori tulajdonságait ismerteti.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 252ddeb372744986df0b8ba9b742d0462a4e8202
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79274475"
---
# <a name="standard-properties-in-azure-monitor-logs"></a>Azure Monitor naplók standard tulajdonságai
Azure Monitor naplókban lévő adatokat egy [log Analytics munkaterületen vagy Application Insights alkalmazásban található rekordok halmaza tárolja](../log-query/logs-structure.md), amelyek mindegyike egy adott adattípussal rendelkezik, amely egyedi tulajdonságokkal rendelkezik. Számos adattípushoz általános tulajdonságok tartoznak, amelyek több típusra is jellemzőek. Ez a cikk ismerteti ezeket a tulajdonságokat, és példákat tartalmaz arra, hogyan használhatja őket a lekérdezésekben.

> [!NOTE]
> A standard tulajdonságok némelyike nem jelenik meg a séma nézetben vagy az IntelliSense Log Analyticsban, és a lekérdezési eredményekben nem jelennek meg, kivéve, ha explicit módon megadja a tulajdonságot a kimenetben.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated és időbélyeg
A **TimeGenerated** (log Analytics munkaterület) és az **időbélyeg** (Application Insights alkalmazás) tulajdonságai azt a dátumot és időpontot tartalmazzák, ameddig a rekordot az adatforrás hozta létre. További részletekért lásd: a [naplózási adatok betöltési ideje Azure monitorban](data-ingestion-time.md) .

A **TimeGenerated** és az **időbélyeg** közös tulajdonságot biztosít a szűréshez, illetve az időbeli összesítéshez. Amikor kiválaszt egy időtartományt egy nézethez vagy egy irányítópulthoz a Azure Portalban, a TimeGenerated vagy időbélyeg használatával szűri az eredményeket. 

### <a name="examples"></a>Példák

A következő lekérdezés az előző hét egyes napjaihoz létrehozott hibák eseményeinek számát adja vissza.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

A következő lekérdezés az előző hét egyes napjaihoz létrehozott kivételek számát adja vissza.

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_TimeReceived
A ** \_TimeReceived** tulajdonság azt a dátumot és időpontot tartalmazza, ameddig a rekord az Azure-felhőben lévő Azure monitor betöltési ponttól érkezett. Ez hasznos lehet az adatforrás és a felhő közötti késési problémák azonosításához. Ilyen például egy hálózati probléma, ami késlelteti az ügynöktől küldött adatok késését. További részletekért lásd: a [naplózási adatok betöltési ideje Azure monitorban](data-ingestion-time.md) .

Az alábbi lekérdezés átlagos késést biztosít egy ügynöktől származó esemény-rekordok esetében óránként. Ez magában foglalja az ügynök és a felhő közötti időt, valamint azt, hogy a rekord teljes ideje elérhető legyen a naplók lekérdezéséhez.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Típus-és itemType
A **típus** (log Analytics munkaterület) és a **itemtype** (Application Insights alkalmazás) tulajdonságai megtartják annak a táblának a nevét, amelyből a rekordot beolvasták, amelyből a rekord típusa is lehet. Ez a tulajdonság olyan lekérdezésekben hasznos, amelyek több tábla rekordjait egyesítik, például a `search` kezelőt használó rekordokat a különböző típusú rekordok megkülönböztetéséhez. a **$Table** egyes helyeken a **típus** helyett lehet használni.

### <a name="examples"></a>Példák
A következő lekérdezés a rekordok számát adja vissza az elmúlt órában összegyűjtött típusok alapján.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_Elemazonosító
A ** \_elemazonosító** tulajdonság a rekord egyedi azonosítóját tárolja.


## <a name="_resourceid"></a>\_ResourceId
A ** \_ResourceId** tulajdonság az erőforrás egyedi azonosítóját tartalmazza, amelyhez a rekord társítva van. Ez egy szabványos tulajdonságot biztosít, amellyel a lekérdezés hatókörét csak egy adott erőforrás rekordjaira, illetve a kapcsolódó adatok több táblába való csatlakoztatására használhatja.

Az Azure-erőforrások esetében **_ResourceId** értéke az [Azure-erőforrás azonosítójának URL-címe](../../azure-resource-manager/templates/template-functions-resource.md). A tulajdonság jelenleg Azure-erőforrásokra korlátozódik, de az Azure-on kívüli erőforrásokra, például a helyszíni számítógépekre is kiterjeszthető.

> [!NOTE]
> Egyes adattípusok már rendelkeznek olyan mezőkkel, amelyek tartalmazzák az Azure Resource ID-t, vagy legalább egy részét, például az előfizetés-azonosítót. Habár ezek a mezők a visszamenőleges kompatibilitás érdekében is megmaradnak, javasoljuk, hogy a _ResourceId használatával végezze el a korrelációt, mivel az konzisztensebb lesz.

### <a name="examples"></a>Példák
A következő lekérdezés összekapcsolja az egyes számítógépek teljesítmény-és esemény-adatait. Az _101_ -es azonosítójú és a processzor kihasználtsága 50%-nál több eseményt mutat be.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

A következő lekérdezés összekapcsolja a _AzureActivity_ -rekordokat a _SecurityEvent_ -rekordokkal. Megjeleníti az összes olyan tevékenység-műveletet, amelynek a felhasználóinak bejelentkezett a gépre.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

Az alábbi lekérdezés az Azure-előfizetések által **_ResourceId** és összesített számlázott adatmennyiséget elemez.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

Ezeket `union withsource = tt *` a lekérdezéseket takarékosan használhatja az adattípusok megkereséséhez.

## <a name="_isbillable"></a>\_Számlázható
A ** \_számlázható** tulajdonság azt határozza meg, hogy a betöltött adatmennyiség számlázható-e. A ** \_számlázható** megegyező, _hamis_ értékű adatok gyűjtése ingyenes, és nem számítunk fel az Azure-fiókra.

### <a name="examples"></a>Példák
A számlázott adattípusokat küldő számítógépek listájának lekéréséhez használja a következő lekérdezést:

> [!NOTE]
> A lekérdezések használatával `union withsource = tt *` takarékosan végezheti el az adattípusok megkeresését. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Ezt kiterjesztheti a számlázott adattípusokat küldő számítógépek száma óránként:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_BilledSize
A ** \_BilledSize** tulajdonság azt az adatmennyiséget adja meg bájtban, amely az Azure-fiókba kerül ** \_** , ha a számlázható értéke igaz.


### <a name="examples"></a>Példák
Ha meg szeretné tekinteni a számítógépen betöltött számlázható események méretét, használja `_BilledSize` a méretet bájtban megadó tulajdonságot:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Az előfizetéshez tartozó számlázható események méretének megtekintéséhez használja a következő lekérdezést:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

Ha szeretné megtekinteni, hogy mekkora a számlázható események száma egy erőforráscsoport esetében, használja a következő lekérdezést:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


A számítógépen betöltött események számának megtekintéséhez használja a következő lekérdezést:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

A számítógépeken betöltött számlázható események számának megtekintéséhez használja a következő lekérdezést: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Egy adott számítógépről származó számlázható adattípusok számának megtekintéséhez használja a következő lekérdezést:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>További lépések

- További információ a [Azure monitor naplózási adatainak tárolásáról](../log-query/log-query-overview.md).
- Vegyen fel egy leckét a [naplók írásához](../../azure-monitor/log-query/get-started-queries.md).
- Bemutatjuk [, hogyan csatlakozhatnak táblákhoz a naplók lekérdezésében](../../azure-monitor/log-query/joins.md).
