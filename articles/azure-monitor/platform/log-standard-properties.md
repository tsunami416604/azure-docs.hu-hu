---
title: Az Azure monitorban alapvető tulajdonságainak naplórekordok |} A Microsoft Docs
description: Az Azure Monitor naplóira adattípusok közös tulajdonságokat ismerteti.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/20/2019
ms.author: bwren
ms.openlocfilehash: 50804e1f6ab4f352239d3f405e5b41e4e0c58d14
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67292821"
---
# <a name="standard-properties-in-azure-monitor-logs"></a>Az Azure Monitor naplóira standard tulajdonságok
Adatai az Azure Monitor naplóira [a rekordok egy Log Analytics-munkaterületen vagy az Application Insights alkalmazást egy készletben tárolt](../log-query/logs-structure.md), amelyek mindegyike egy adott adattípus, amely egy egyedi tulajdonságkészlettel rendelkezik. Számos adattípusok, amelyek közösek a több típus több alapvető tulajdonságainak lesz. Ez a cikk ismerteti ezeket a tulajdonságokat és példákat, hogyan használhatja ezeket a lekérdezéseket.

Ezek a tulajdonságok néhány létrehozás folyamatban van megvalósítva, így előfordulhat, hogy megjelennek az egyes adattípusok, de még nincs a többi.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated és időbélyeg
A **TimeGenerated** (Log Analytics-munkaterület) és **időbélyeg** (Application Insights-alkalmazás) tulajdonságok tartalmaznak dátumát és időpontját, hogy a rekord létrejött. Szűrés vagy időpontig összefoglalójához közös tulajdonság biztosít. Amikor kiválaszt egy időtartományt, nézetben vagy Irányítópulton az Azure Portalon, használ a TimeGenerated, illetve időbélyeg, az eredmények szűréséhez.

### <a name="examples"></a>Példák

Az alábbi lekérdezés minden nap az előző héten létrehozott események számát adja vissza.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

A következő lekérdezést kivételek létrehozott minden egyes nap az előző hét számát adja vissza.

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="type-and-itemtype"></a>Írja be és itemType
A **típus** (Log Analytics-munkaterület) és **itemType** (Application Insights-alkalmazás) tulajdonságok céllal, hogy a rekord való lekérdezés melyik is is a táblázat neve értelmezhetők, a rekord Írja be. Ez a tulajdonság akkor hasznos, a lekérdezések, amelyek több táblából, például rekordok a `search` operátor szerinti szűrése, megkülönböztetni a különböző típusú rekordok. **$table** helyén használható **típus** egyes helyeken.

### <a name="examples"></a>Példák
Az alábbi lekérdezés a bejegyzések száma az elmúlt egy órában gyűjtött típus szerint adja vissza.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type
```

## <a name="resourceid"></a>\_ResourceId
A  **\_ResourceId** a tulajdonság tárolja az erőforrást, amelyhez társítva van a rekord egyedi azonosítója. Ez lehetővé teszi a lekérdezés hatókörét, csak a rekordok egy adott erőforrásból, vagy a kapcsolódó adatok join több különböző táblázat standard tulajdonságot.

Az Azure-erőforrásokhoz, értékét **_ResourceId** van a [Azure-erőforrást azonosító URL-cím](../../azure-resource-manager/resource-group-template-functions-resource.md). A tulajdonság jelenleg csak Azure-erőforrások, de például a helyi számítógépek Azure-on kívüli erőforrások terjeszteni.

> [!NOTE]
> Egyes adattípusok már rendelkezik mezőkkel, amelyek tartalmazzák az Azure erőforrás-azonosító, vagy legalább részeit, például az előfizetés-azonosítójára. Bár ezek a mezők őrzi meg az előző verziókkal való kompatibilitás, javasoljuk, hogy használja a _ResourceId közötti korreláció végrehajtásához, mivel a egységesebb lesz.

### <a name="examples"></a>Példák
A következő lekérdezés teljesítmény- és esemény adatokat minden számítógéphez csatlakozik. Összes esemény azonosítóval jelenik meg _101_ és 50 % feletti processzorhasználat.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

Az alábbi lekérdezés illesztések _AzureActivity_ rögzíti a _SecurityEvent_ rögzíti. A felhasználókkal, hogy ezek a gépek naplózott összes tevékenység műveleteket jeleníti meg.

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

Az alábbi lekérdezés elemzi **_ResourceId** és összesítések az adatkötetek Azure-előfizetésenként külön számlázzuk.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

E `union withsource = tt *` , különböző adattípusok keresésekre végrehajtása költséges takarékosan kérdezi le.

## <a name="isbillable"></a>\_IsBillable
A  **\_IsBillable** tulajdonság határozza meg, hogy a feldolgozott adatok számlázható. Az adatok  **\_IsBillable** egyenlő _hamis_ gyűjtött ingyenesen, és nem számítunk fel az Azure-fiókjával.

### <a name="examples"></a>Példák
Számlázott adattípusok küldő számítógépek listájának lekéréséhez használja a következő lekérdezést:

> [!NOTE]
> A lekérdezések használata `union withsource = tt *` takarékosan vizsgálatok különböző adattípusok drágák végrehajtásához. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Ez is kiterjeszthető való visszatéréshez, a küldő számítógépek óránkénti száma számlázzuk adattípusok:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="billedsize"></a>\_BilledSize
A  **\_BilledSize** tulajdonság határozza meg a mérete (bájt), amelyek számítjuk fel az Azure-fiókjával, ha  **\_IsBillable** igaz.

### <a name="examples"></a>Példák
Számítógép betöltött üzenetnek méretének megtekintéséhez használja a `_BilledSize` -tulajdonsággal, amely biztosítja a mérete (bájt):

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Előfizetésenként betöltött üzenetnek méretének megtekintéséhez használja a következő lekérdezést:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

Az erőforráscsoport betöltött üzenetnek méretének megtekintéséhez használja a következő lekérdezést:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


A számítógép betöltött események száma megtekintéséhez használja a következő lekérdezést:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

A számítógép betöltött számlázható események száma megtekintéséhez használja a következő lekérdezést: 

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

- További információk [tárolt naplóadatokat az Azure Monitor](../log-query/log-query-overview.md).
- Gyorsan egy leckében a [log-lekérdezések írása](../../azure-monitor/log-query/get-started-queries.md).
- Gyorsan egy leckében a [a táblázatok összekapcsolása a naplólekérdezések](../../azure-monitor/log-query/joins.md).
