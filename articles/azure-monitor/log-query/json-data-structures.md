---
title: Karakterláncok kezelése az Azure Monitor naplólekérdezéseiben | Microsoft dokumentumok
description: Ez a cikk egy oktatóanyag ot tartalmaz az Azure Monitor Log Analytics használatával az Azure Portalon a naplóadatok lekérdezéséhez és elemzéséhez az Azure Monitorban.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: f792820b7b0dff20e647031410ba87ac26c2495a
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672975"
---
# <a name="working-with-json-and-data-structures-in-azure-monitor-log-queries"></a>JSON- és adatstruktúrák kal való együttműködés az Azure Monitor naplólekérdezéseiben

> [!NOTE]
> A lecke befejezése előtt el kell [végeznie az Azure Monitor loganalytics szolgáltatásának](get-started-portal.md) első lépéseit és [az Azure Monitor naplólekérdezéseinek első lépéseit.](get-started-queries.md)

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

A beágyazott objektumok olyan objektumok, amelyek egy tömb ben lévő más objektumokat vagy kulcsértékpárok térképét tartalmazzák. Ezek az objektumok JSON-karakterláncként jelennek meg. Ez a cikk azt ismerteti, hogy a JSON hogyan használható az adatok beolvasására és a beágyazott objektumok elemzésére.

## <a name="working-with-json-strings"></a>JSON-karakterláncok kal való együttműködés
Egy `extractjson` ismert elérési út egy adott JSON-elemének elérésére használható. Ehhez a függvényhez olyan görbekifejezésszükséges, amely a következő konvenciókat használja.

- _$_ a gyökérmappára való hivatkozás
- A zárójel vagy pont jelölés segítségével az alábbi példákban bemutatott indexek és elemek hivatkozhatnak.


Az indexek és a poklelemek szögletes zárójeleit különelemekkel választhatja el:

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

Ha csak egy elem van, csak a dot jelölést használhatja:

```Kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Objektumok használata

### <a name="parsejson"></a>parsejson között
A jsonstruktúra több elemének eléréséhez könnyebb dinamikus objektumként hozzáférni. Szövegadatok `parsejson` dinamikus objektumra való levetítése. Miután dinamikus típussá konvertálta, további függvények használhatók az adatok elemzésére.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>tömbhossz
A `arraylength` tömb elemeinek megszámlálására használható:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Az `mvexpand` objektumok tulajdonságainak külön sorokra bontása.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
Az `buildschema` objektum összes értékét felismerő séma leésének leése:

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
Ez a kimenet az objektummezők nevét és egyező adattípusait írja le. 

A beágyazott objektumok sémája eltérő lehet, például a következő példában:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Séma létrehozása](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>További lépések
Tekintse meg a naplólekérdezések azure Monitor ban való használatának egyéb leckéit:

- [Sztringműveletek](string-operations.md)
- [Dátum és idő típusú adatokkal végzett műveletek](datetime-operations.md)
- [Aggregátumfüggvények](aggregations.md)
- [Speciális aggregátumok](advanced-aggregations.md)
- [Továbbfejlesztett lekérdezésírás](advanced-query-writing.md)
- [Illesztések](joins.md)
- [Diagramok](charts.md)
