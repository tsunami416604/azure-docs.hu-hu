---
title: Karakterláncok használata Azure Monitor log-lekérdezésekben | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használhatók a Azure Monitor Log Analytics a Azure Portal a Azure Monitor naplózási adatai lekérdezéséhez és elemzéséhez.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: f792820b7b0dff20e647031410ba87ac26c2495a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80672975"
---
# <a name="working-with-json-and-data-structures-in-azure-monitor-log-queries"></a>JSON-és adatstruktúrák használata Azure Monitor log-lekérdezésekben

> [!NOTE]
> A lecke elvégzése előtt fejezze be a [Azure Monitor log Analytics](get-started-portal.md) és a [Azure monitor log-lekérdezések első](get-started-queries.md) lépéseinek megismerését.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

A beágyazott objektumok olyan objektumok, amelyek egy tömb más objektumait vagy a kulcs-érték párok egy térképét tartalmazzák. Ezek az objektumok JSON-karakterláncként jelennek meg. Ez a cikk azt ismerteti, hogyan használható a JSON az adatlekérdezéshez és a beágyazott objektumok elemzéséhez.

## <a name="working-with-json-strings"></a>JSON-karakterláncok használata
Egy `extractjson` ismert elérési úton található adott JSON-elem elérésére használható. Ehhez a függvényhez olyan elérésiút-kifejezésre van szükség, amely az alábbi konvenciókat használja.

- _$_ a gyökérmappa átirányítása
- A szögletes zárójel vagy a pont jelölés használatával az alábbi példákban látható módon hivatkozhat az indexekre és az elemekre.


Az indexek és a pontok szögletes zárójeleit az elemek elkülönítéséhez használhatja:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Ez ugyanaz az eredmény, amely csak a zárójelek jelölését használja:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Ha csak egy elem van, csak a pont jelölését használhatja:

```Kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Objektumok használata

### <a name="parsejson"></a>parseJSON
A JSON-struktúra több elemének eléréséhez egyszerűbb a dinamikus objektumként való elérése. `parsejson`A használatával szöveges és dinamikus objektumokra is felhasználható. A dinamikus típusra konvertálása után további függvények is használhatók az adatok elemzéséhez.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
`arraylength`Egy tömb elemei számának megszámlálásához használja a következőt:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
`mvexpand`Egy objektum tulajdonságainak különálló sorokra való tördelésére használható.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
`buildschema`A paranccsal lekérheti az objektum összes értékét azonosító sémát:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

A kimenet JSON formátumú séma:
```json
{
    "hosts":
    {
        "indexer":
        {
            "location": "string",
            "rate": "int",
            "status": "string"
        }
    }
}
```
Ez a kimenet az objektum mezőinek nevét és a hozzájuk tartozó adattípusokat ismerteti. 

A beágyazott objektumok különböző sémákkal rendelkezhetnek, például a következő példában:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Séma létrehozása](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>További lépések
Tekintse meg a Azure Monitor a naplók használatával kapcsolatos további leckéket:

- [Sztringműveletek](string-operations.md)
- [Dátum és idő típusú adatokkal végzett műveletek](datetime-operations.md)
- [Aggregátumfüggvények](aggregations.md)
- [Speciális aggregátumok](advanced-aggregations.md)
- [Továbbfejlesztett lekérdezésírás](advanced-query-writing.md)
- [Illesztések](joins.md)
- [Diagramok](charts.md)
