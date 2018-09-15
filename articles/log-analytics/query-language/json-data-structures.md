---
title: Az Azure Log Analytics-lekérdezéseket a karakterláncokkal való munka |} A Microsoft Docs
description: Ez a cikk nyújt segítséget az Analytics-portál használatával kell lekérdezéseket írni a Log Analyticsben.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 1b9a8e4a8706dea43e33331cd196fbe2ad877a3a
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45605555"
---
# <a name="working-with-json-and-data-structures-in-log-analytics-queries"></a>A JSON és használatához adatok struktúrák a Log Analytics-lekérdezések

> [!NOTE]
> Hajtsa végre [az Analytics-portál – első lépések](get-started-analytics-portal.md) és [Ismerkedés a lekérdezések](get-started-queries.md) ebben a leckében befejezése előtt.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Beágyazott objektumok olyan objektumok, amelyek tartalmaznak egy tömb, vagy egy térképet, kulcs-érték párokat az objektumokat. Ezek az objektumok JSON karakterláncként vannak megadva. Ez a cikk bemutatja, hogyan JSON adatok lekéréséhez és a beágyazott objektumok elemzésére szolgál.

## <a name="working-with-json-strings"></a>JSON-sztringek használata
Használat `extractjson` egy adott JSON-elemeiről ismert elérési út elérésére. A függvény csak egy elérési út kifejezés, amely a következő használja.

- _$_ a gyökérmappa hivatkozik
- A szögletes zárójelet vagy ponttal jelölés használatával tekintse meg az indexekhez és elemeket, a következő példákban szemléltetett módon.


Elemek elválasztásához használjon szögletes indexek és pontra:

```KQL
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Ez a ugyanaz az eredmény csak zárójelek jelölés használatával:

```KQL
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Ha csak egy elem, csak pont jelölés is használhatja:

```KQL
let hosts_report='{"location":"North_DC", "status":"running", "rate":5}';
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Objektumok használata

### <a name="parsejson"></a>parsejson
A json struktúrában több elemek eléréséhez egyszerűbb legyen a dinamikus objektumként férni. Használat `parsejson` szöveges adatot dinamikus-objektumba. Ha egy dinamikus típus konvertálva, további funkciók segítségével elemezheti az adatokat.

```KQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
Használat `arraylength` tömbben szereplő elemek számát:

```KQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Használat `mvexpand` az objektum tulajdonságainak felosztása külön sorokat.

```KQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
Használat `buildschema` beolvasni a sémát, amely elismeri az objektum összes értéket:

```KQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

A kimenet a sémát a JSON formátumban:
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
Ez a kimenet a objektum mezőket és azok egyező adattípusok nevét írja le. 

Beágyazott objektumok különböző sémákkal például előfordulhat, hogy rendelkezik az alábbi példában:

```KQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Séma létrehozása](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>További lépések
Tekintse meg a Log Analytics lekérdezési nyelv segítségével a többi leckék:

- [Karakterlánc-műveletek](string-operations.md)
- [Dátum és idő műveletek](datetime-operations.md)
- [Összesítésfüggvények](aggregations.md)
- [Speciális összesítések](advanced-aggregations.md)
- [Speciális lekérdezés írása](advanced-query-writing.md)
- [Illesztés](joins.md)
- [Diagramok](charts.md)