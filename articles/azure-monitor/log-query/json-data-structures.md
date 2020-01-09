---
title: Karakterláncok használata Azure Monitor log-lekérdezésekben | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használhatók a Azure Monitor Log Analytics a Azure Portal a Azure Monitor naplózási adatai lekérdezéséhez és elemzéséhez.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 940c82e9ef7016639a3ab334040c408f83996e2b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365308"
---
# <a name="working-with-json-and-data-structures-in-azure-monitor-log-queries"></a>JSON-és adatstruktúrák használata Azure Monitor log-lekérdezésekben

> [!NOTE]
> A lecke elvégzése előtt fejezze be a [Azure Monitor log Analytics](get-started-portal.md) és a [Azure monitor log-lekérdezések első](get-started-queries.md) lépéseinek megismerését.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

A beágyazott objektumok olyan objektumok, amelyek egy tömb más objektumait vagy a kulcs-érték párok egy térképét tartalmazzák. Ezek az objektumok JSON-karakterláncként jelennek meg. Ez a cikk azt ismerteti, hogyan használható a JSON az adatlekérdezéshez és a beágyazott objektumok elemzéséhez.

## <a name="working-with-json-strings"></a>JSON-karakterláncok használata
A `extractjson` használatával férhet hozzá egy ismert elérési úton található adott JSON-elemhez. Ehhez a függvényhez olyan elérésiút-kifejezésre van szükség, amely az alábbi konvenciókat használja.

- _$_ a gyökérmappa
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
let hosts_report='{"location":"North_DC", "status":"running", "rate":5}';
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Objektumok használata

### <a name="parsejson"></a>parseJSON
A JSON-struktúra több elemének eléréséhez egyszerűbb a dinamikus objektumként való elérése. Használjon `parsejson` a szöveges és a dinamikus objektumokra való átküldéshez. A dinamikus típusra konvertálása után további függvények is használhatók az adatok elemzéséhez.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
Egy tömbben lévő elemek számának megszámlálásához használja a `arraylength`:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Az objektumok tulajdonságainak különálló sorokra való bontásához használja a `mvexpand`.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
A `buildschema` használatával kérheti le a sémát, amely elismeri egy objektum összes értékét:

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

## <a name="next-steps"></a>Következő lépések
Tekintse meg a Azure Monitor a naplók használatával kapcsolatos további leckéket:

- [Karakterlánc-műveletek](string-operations.md)
- [Dátum-és időműveletek](datetime-operations.md)
- [Összesítési függvények](aggregations.md)
- [Speciális összesítések](advanced-aggregations.md)
- [Speciális lekérdezés írása](advanced-query-writing.md)
- [Csatlakozik](joins.md)
- [Diagramok](charts.md)
