---
title: Speciális lekérdezések a Azure Monitorban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használható az elemzési portál a Azure Monitor lekérdezések írásához.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2018
ms.openlocfilehash: 3d228c62cd2d1bcb7f4515cd698186e2ebcbe929
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "77670287"
---
# <a name="writing-advanced-queries-in-azure-monitor"></a>Speciális lekérdezések írása a Azure Monitorban

> [!NOTE]
> A lecke elvégzése előtt fejezze be [a Azure Monitor log Analytics](get-started-portal.md) és [a lekérdezések első](get-started-queries.md) lépéseit.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>Kód újrafelhasználása a Let
A paranccsal az `let` eredményeket egy változóhoz rendelheti hozzá, és a lekérdezésben később is megtekintheti:

```Kusto
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

A változókhoz állandó értékeket is hozzárendelhet. Ez a módszer lehetővé teszi, hogy beállítsa a paramétereket a lekérdezés minden egyes futtatásakor módosítani kívánt mezőkhöz. Szükség szerint módosítsa a paramétereket. Például a szabad lemezterület és a szabad memória (százalékban) kiszámításához egy adott időablakban:

```Kusto
let startDate = datetime(2018-08-01T12:55:02);
let endDate = datetime(2018-08-02T13:21:35);
let FreeDiskSpace =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Logical Disk" and CounterName=="Free Megabytes"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
let FreeMemory =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Memory" and CounterName=="Available MBytes Memory"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
union FreeDiskSpace, FreeMemory
```

Így egyszerűen megváltoztathatja a befejezési idő kezdetét a lekérdezés következő futtatásakor.

### <a name="local-functions-and-parameters"></a>Helyi függvények és paraméterek
`let`Az ugyanabban a lekérdezésben használható függvények létrehozásához használjon utasításokkal. Definiáljon például egy olyan függvényt, amely egy datetime típusú mezőt (UTC formátumban) fogad, és átalakítja a standard US formátumra. 

```Kusto
let utc_to_us_date_format = (t:datetime)
{
  strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
  bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
};
Event 
| where TimeGenerated > ago(1h) 
| extend USTimeGenerated = utc_to_us_date_format(TimeGenerated)
| project TimeGenerated, USTimeGenerated, Source, Computer, EventLevel, EventData 
```

## <a name="print"></a>Nyomtatás
`print` egy olyan táblát ad vissza, amely egyetlen oszlopból és egy sorból áll, amely a számítás eredményét jeleníti meg. Ezt gyakran használják olyan esetekben, amikor egyszerű számításra van szükség. Ha például az aktuális időt szeretné megkeresni a PST-ben, és hozzá szeretne adni egy oszlopot az EST használatával:

```Kusto
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>DataTable
`datatable` lehetővé teszi az adathalmazok meghatározását. Adjon meg egy sémát és egy értéket, majd a táblázatot a többi lekérdezési elembe. Például a RAM-használati táblázat létrehozásához és az átlagos érték óránkénti kiszámításához:

```Kusto
datatable (TimeGenerated: datetime, usage_percent: double)
[
  "2018-06-02T15:15:46.3418323Z", 15.5,
  "2018-06-02T15:45:43.1561235Z", 20.2,
  "2018-06-02T16:16:49.2354895Z", 17.3,
  "2018-06-02T16:46:44.9813459Z", 45.7,
  "2018-06-02T17:15:41.7895423Z", 10.9,
  "2018-06-02T17:44:23.9813459Z", 24.7,
  "2018-06-02T18:14:59.7283023Z", 22.3,
  "2018-06-02T18:45:12.1895483Z", 25.4
]
| summarize avg(usage_percent) by bin(TimeGenerated, 1h)
```

A DataTable-szerkezetek a keresési tábla létrehozásakor is hasznosak. Ha például a _SecurityEvent_ táblából származó táblázatos adatok (például eseményazonosító) leképezése a máshol felsorolt események típusaira, hozzon létre egy keresési táblázatot az eseménytípus használatával, `datatable` és csatlakozzon ehhez a DataTable adattáblához _SecurityEvent_ -adatokkal:

```Kusto
let eventCodes = datatable (EventID: int, EventType:string)
[
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4672, "Access",
    4624, "Account activity",
    4799, "Access management",
    4798, "Access management",
    5059, "Key operation",
    4648, "A logon was attempted using explicit credentials",
    4768, "Access management",
    4662, "Other",
    8002, "Process action",
    4904, "Security event management",
    4905, "Security event management",
];
SecurityEvent
| where TimeGenerated > ago(1h) 
| join kind=leftouter (
  eventCodes
) on EventID
| project TimeGenerated, Account, AccountType, Computer, EventType
```

## <a name="next-steps"></a>További lépések
Tekintse meg a [Kusto lekérdezési nyelv](/azure/kusto/query/) használatát ismertető további leckéket a Azure monitor naplózási adataival:

- [Sztringműveletek](string-operations.md)
- [Dátum és idő típusú adatokkal végzett műveletek](datetime-operations.md)
- [Aggregátumfüggvények](aggregations.md)
- [Speciális aggregátumok](advanced-aggregations.md)
- [JSON és adatstruktúrák](json-data-structures.md)
- [Illesztések](joins.md)
- [Diagramok](charts.md)
