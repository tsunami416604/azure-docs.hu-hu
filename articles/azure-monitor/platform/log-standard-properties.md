---
title: Szabványos tulajdonságok az Azure Monitor naplórekordjaiban | Microsoft dokumentumok
description: Ismerteti azokat a tulajdonságokat, amelyek az Azure Monitor naplóiban több adattípusközösek.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 252ddeb372744986df0b8ba9b742d0462a4e8202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274475"
---
# <a name="standard-properties-in-azure-monitor-logs"></a>Szabványos tulajdonságok az Azure Figyelő naplókban
Az Azure Monitor naplókban lévő adatok [rekordok készleteként tárolódnak a Log Analytics-munkaterületben vagy az Application Insights-alkalmazásban,](../log-query/logs-structure.md)amelyek mindegyike egyedi tulajdonságokkal rendelkezik. Számos adattípus nak lesz szabványos tulajdonsága, amelyek több típusban is gyakoriak. Ez a cikk ismerteti ezeket a tulajdonságokat, és példákat, hogyan lehet használni őket a lekérdezések.

> [!NOTE]
> A szabványos tulajdonságok egy része nem jelenik meg a sémanézetben vagy az intellisense-ben a Log Analytics-ben, és nem jelennek meg a lekérdezési eredményekben, hacsak nem adja meg kifejezetten a tulajdonságot a kimenetben.

## <a name="timegenerated-and-timestamp"></a>TimeGenerált és időbélyeg
A **TimeGenerated** (Log Analytics-munkaterület) és **az időbélyeg** (Application Insights-alkalmazás) tulajdonságok tartalmazzák a dátumot és az időt, amikor a rekord az adatforrás által létrehozott. További részletekért [tekintse meg az Adatok betöltési idejének naplózása az Azure Monitorban.](data-ingestion-time.md)

**A TimeGenerated** és **az időbélyeg** egy közös tulajdonságot biztosít az idő szerinti szűréshez vagy összegzéshez. Amikor kiválaszt egy időtartományt egy nézethez vagy irányítópulthoz az Azure Portalon, timegenerated vagy időbélyeg használatával szűri az eredményeket. 

### <a name="examples"></a>Példák

A következő lekérdezés az előző hét egyes napjaihoz létrehozott hibaesemények számát adja vissza.

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

## <a name="_timereceived"></a>\_Beérkezett idő
A ** \_TimeReceived** tulajdonság tartalmazza azt a dátumot és időpontot, amikor a rekordot az Azure Monitor betöltési pontja az Azure-felhőben megkapta. Ez hasznos lehet az adatforrás és a felhő közötti késési problémák azonosításához. Erre példa egy hálózati probléma, amely késlelteti az ügynöktől küldött adatokat. További részletekért [tekintse meg az Adatok betöltési idejének naplózása az Azure Monitorban.](data-ingestion-time.md)

A következő lekérdezés az ügynök eseményrekordjainak átlagos késését adja meg. Ez magában foglalja az ügynök től a felhőbe, és a teljes idő a rekord elérhető naplólekérdezések.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Típus és itemType
A **Type** (Log Analytics workspace) és **az itemType** (Application Insights-alkalmazás) tulajdonságok annak a táblának a nevét tartalmazják, amelyből a rekord lelett, és amelyből rekordtípusként is fellehet fogni. Ez a tulajdonság olyan lekérdezéseknél hasznos, amelyek több `search` táblából származó rekordokat egyesítnek, például az operátort használó lekérdezéseknél a különböző típusú rekordok megkülönböztetésére. **$table** egyes **helyeken** típus helyett használható.

### <a name="examples"></a>Példák
A következő lekérdezés az elmúlt órában összegyűjtött rekordok számát adja vissza.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_Itemid
A ** \_ItemId** tulajdonság a rekord egyedi azonosítójával rendelkezik.


## <a name="_resourceid"></a>\_Erőforrásazonosító
A ** \_ResourceId** tulajdonság egyedi azonosítóval rendelkezik ahhoz az erőforráshoz, amelyhez a rekord társítva van. Ez egy szabványos tulajdonságot biztosít a lekérdezés hatókörének hatóköréhez, hogy csak egy adott erőforrás rekordjaijelenjenek meg, vagy hogy a kapcsolódó adatokat több táblához egyesítse.

Az Azure-erőforrások esetében **a _ResourceId** értéke az [Azure-erőforrás-azonosító URL-címe.](../../azure-resource-manager/templates/template-functions-resource.md) A tulajdonság jelenleg az Azure-erőforrásokra korlátozódik, de az Azure-on kívüli erőforrásokra, például a helyszíni számítógépekre is kiterjed.

> [!NOTE]
> Egyes adattípusok már rendelkeznek olyan mezőkkel, amelyek Tartalmazzák az Azure-erőforrás-azonosítót, vagy legalább annak egy részét, például az előfizetés-azonosítót. Bár ezek a mezők a visszamenőleges kompatibilitás érdekében megmaradnak, ajánlott a _ResourceId keresztkorreláció végrehajtására használni, mivel az következetesebb lesz.

### <a name="examples"></a>Példák
A következő lekérdezés egyesíti az egyes számítógépek teljesítmény- és eseményadatait. Az összes _101-es_ azonosítóval és 50%ot megnem használó processzorkihasználtsú eseményekkel jeleníti meg.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

A következő lekérdezés csatlakozik _az AzureActivity_ rekordok _SecurityEvent_ rekordok. Ez azt mutatja, az összes tevékenységi műveletek et a felhasználók, amelyek be voltak jelentkezve ezekre a gépekre.

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

A következő lekérdezés elemzi **_ResourceId** és összesíti a számlázott adatkötetek Azure-előfizetésenként.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

Használja `union withsource = tt *` ezeket a lekérdezéseket takarékosan, mint a vizsgálatok az adattípusok között költséges végrehajtani.

## <a name="_isbillable"></a>\_IsBillable között
Az ** \_IsBillable** tulajdonság azt határozza meg, hogy a bevitt adatok számlázhatók-e. Az ** \_IsBillable** értékű _hamis_ adatokat ingyenesen gyűjtjük, és nem számlázunk az Azure-fiókjába.

### <a name="examples"></a>Példák
A számlázott adattípusokat küldő számítógépek listájának lekérdezéséhez használja a következő lekérdezést:

> [!NOTE]
> A lekérdezések `union withsource = tt *` takarékosan használhatók, mivel az adattípusok közötti vizsgálatok végrehajtása költséges. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Ez kiterjeszthető a számlázott adattípusokat küldő számítógépek óránkénti számának visszaadása érdekében:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_Számlázott méret
A ** \_BilledSize** tulajdonság megadja az Azure-fióknak számlázott adatok bájtjainak méretét, ha ** \_az IsBillable** igaz.


### <a name="examples"></a>Példák
A számítógépenként bevitt számlázható események méretének `_BilledSize` megtekintéséhez használja a méretet bájtban megadott tulajdonságot:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Az előfizetésenként bevitt számlázható események méretének megtekintéséhez használja a következő lekérdezést:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

Az erőforráscsoportonként bevitt számlázható események méretének megtekintéséhez használja a következő lekérdezést:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


A számítógépenként bevitt események számának megtekintéséhez használja a következő lekérdezést:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

A számítógépenként bevitt számlázható események számának megtekintéséhez használja a következő lekérdezést: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Egy adott számítógép ről származó számlázható adattípusok számának megtekintéséhez használja a következő lekérdezést:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>További lépések

- További információ [az Azure Monitor naplóadatainak tárolásáról.](../log-query/log-query-overview.md)
- A [naplólekérdezések írásával](../../azure-monitor/log-query/get-started-queries.md)kapcsolatos lecke.
- A [naplólekérdezések tábláinak összehívásával](../../azure-monitor/log-query/joins.md)kapcsolatos lecke.
