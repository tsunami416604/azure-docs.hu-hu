---
title: Speciális lekérdezések az Azure Monitorban | Microsoft dokumentumok
description: Ez a cikk egy oktatóanyagot tartalmaz az Analytics-portál használatával lekérdezések írásához az Azure Monitorban.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2018
ms.openlocfilehash: 3d228c62cd2d1bcb7f4515cd698186e2ebcbe929
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670287"
---
# <a name="writing-advanced-queries-in-azure-monitor"></a>Speciális lekérdezések írása az Azure Monitorban

> [!NOTE]
> A lecke befejezése előtt el kell [végeznie az Azure Monitor loganalytics szolgáltatásának](get-started-portal.md) első lépéseit és [a lekérdezések](get-started-queries.md) első lépéseit.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>Kód újrafelhasználása a let segítségével
Segítségével `let` eredményeket rendelhet egy változóhoz, és később hivatkozhat rá a lekérdezésben:

```Kusto
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

Állandó értékeket is rendelhet a változókhoz. Ez egy olyan módszert támogat, amely a lekérdezés minden egyes végrehajtásakor módosítani kívánt mezők paramétereit határozza meg. Szükség szerint módosítsa ezeket a paramétereket. Például a szabad lemezterület és a szabad memória kiszámításához (percentilisekben) egy adott időablakban:

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

Ez megkönnyíti a lekérdezés következő futtatásakor a befejezési időpont kezdetének módosítását.

### <a name="local-functions-and-parameters"></a>Helyi függvények és paraméterek
Az `let` utasítások segítségével olyan függvényeket hozhat létre, amelyek ugyanabban a lekérdezésben használhatók. Definiáljon például egy olyan függvényt, amely egy datetime mezőt (UTC formátumban) vesz fel, és azt szabványos amerikai formátumra konvertálja. 

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
`print`egy oszlopot és egy sort tartalmazó táblázatot ad vissza, amely egy számítás eredményét mutatja. Ezt gyakran használják olyan esetekben, amikor egyszerű számításra van szükség. Például az aktuális idő keresése pst-ben, és egy oszlop hozzáadása EST-vel:

```Kusto
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>Datatable
`datatable`lehetővé teszi egy adathalmaz definiálását. Megad egy sémát és egy értékkészletet, majd a táblát bármely más lekérdezési elembe. Például a RAM-használat táblázatának létrehozásához és az óránkénti átlagos érték kiszámításához:

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

Az adattábla-konstrukciók is nagyon hasznosak a kerestitábla létrehozásakor. Ha például táblaadatokat, például eseményazonosítókat szeretne leképezni a _SecurityEvent_ táblából, a máshol `datatable` felsorolt eseménytípusokhoz, hozzon létre egy olyan keresmet, amelynek használata az eseménytípusokat használja, és csatlakozzon ehhez az adattáblához a _SecurityEvent_ adataival:

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
Tekintse meg a [Kusto lekérdezési nyelv](/azure/kusto/query/) ének használatát az Azure Monitor naplóadataival:

- [Sztringműveletek](string-operations.md)
- [Dátum és idő típusú adatokkal végzett műveletek](datetime-operations.md)
- [Aggregátumfüggvények](aggregations.md)
- [Speciális aggregátumok](advanced-aggregations.md)
- [JSON és adatstruktúrák](json-data-structures.md)
- [Illesztések](joins.md)
- [Diagramok](charts.md)
