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
ms.openlocfilehash: c01cdb967fd7f9516b4403aa4f0c76f2577d5050
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294722"
---
# <a name="standard-properties-in-azure-monitor-log-records"></a>Az Azure Monitor naplórekordok standard tulajdonságok
Naplóadatok az Azure monitorban [tárolt rekordkészletet és](../log-query/log-query-overview.md), amelyek mindegyike egy adott adattípus, amely egy egyedi tulajdonságkészlettel rendelkezik. Számos adattípusok, amelyek közösek a több típus több alapvető tulajdonságainak lesz. Ez a cikk ismerteti ezeket a tulajdonságokat és példákat, hogyan használhatja ezeket a lekérdezéseket.

Ezek a tulajdonságok néhány létrehozás folyamatban van megvalósítva, így előfordulhat, hogy megjelennek az egyes adattípusok, de még nincs a többi.

## <a name="timegenerated"></a>TimeGenerated
A **TimeGenerated** tulajdonság tartalmazza a dátum és idő, hogy a rekord létrehozásának. Szűrés vagy időpontig összefoglalójához közös tulajdonság biztosít. Amikor kiválaszt egy időtartományt, nézetben vagy Irányítópulton az Azure Portalon, az eredmények szűréséhez TimeGenerated használ.

### <a name="examples"></a>Példák

Az alábbi lekérdezés minden nap az előző héten létrehozott események számát adja vissza.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

## <a name="type"></a>Typo
A **típus** tulajdonság nevét tárolja a tábla, hogy a rekord, amelyből beolvasták is is értelmezhetők, a rekord típusa. Ez a tulajdonság akkor hasznos, a lekérdezések, amelyek több táblából, például rekordok a `search` operátor szerinti szűrése, megkülönböztetni a különböző típusú rekordok. **$table** helyén használható **típus** egyes helyeken.

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

Ha meg szeretné tekinteni a számlázható adattípusok darabszáma adatot küldenek, adott számítógéphez, használja a következő lekérdezést:

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
