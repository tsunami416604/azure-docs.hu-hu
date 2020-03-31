---
title: Az Azure Service Fabric CLI- sfctl káosz ütemezése
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. Tartalmazza a káosz ütemezéséhez szükséges parancsok listáját.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 29b365c48e405830e238945f1d94156f477c15b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906184"
---
# <a name="sfctl-chaos-schedule"></a>sfctl-káosz ütemterv
Szerezd meg és állítsd be a káosz menetrendjét.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| get | Szerezd meg a Chaos Schedule határozza meg, mikor és hogyan kell futtatni Chaos. |
| halmaz | Állítsa be a Chaos által használt ütemezést. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl káosz menetrend kap
Szerezd meg a Chaos Schedule határozza meg, mikor és hogyan kell futtatni Chaos.

Beszerzi a Chaos Schedule verzióját használatban, és a Chaos Schedule, amely meghatározza, hogy mikor és hogyan kell futtatni a Chaos-t.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl káosz menetrend készlet
Állítsa be a Chaos által használt ütemezést.

Chaos automatikusan ütemezi fut alapján a Chaos Schedule. A Chaos Schedule frissül, ha a megadott verzió megegyezik a kiszolgálón lévő verzióval. A Chaos Schedule frissítésekor a kiszolgálón lévő verzió 1-tel növekszik. A kiszolgálón lévő verzió nagy szám elérése után 0-ra nyúlik vissza. Ha a Chaos fut a hívás meghívásakor, a hívás sikertelen lesz.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| ---chaos-paraméterek - szótár | JSON kódolású lista, amely a karakterláncnevek ChaosParameters-hez való hozzárendelését jelöli a Jobs számára. |
| --lejárati dátum-utc | A dátum és az idő, amikor le kell állítani a Chaos ütemezésének ütemezése ütemezését.  Alapértelmezett\: 9999-12-31T23\:\:59 59.999Z. |
| --munkahelyek | JSON kódolt listáját ChaosScheduleJobs, amely a Chaos futtatásának időpontja, és milyen paraméterekkel futtatni Chaos. |
| --kezdő-dátum-utc | A dátum és az idő, amikor elkezdi használni a Menetrend ütemezése Chaos.  Alapértelmezett\: 1601-01-01T00\:\:00 00.000Z. |
| --idő-out -t | Alapértelmezett\: 60. |
| --verzió | Az Ütemezés verziószáma. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

### <a name="examples"></a>Példák

A következő parancs beállít egy ütemezést (feltéve, hogy az aktuális ütemezés 0-as verzióval rendelkezik), amely 2016-01-01-en kezdődik és 2038-01-01-en jár le, amely a Káoszt a nap 24 órájában, a hét 7 napján futtatja.
Káosz lesz ütemezve a fürtön az adott időpontban.
```
sfctl chaos schedule set --version 0 --start-date-utc "2016-01-01T00:00:00.000Z" --expiry-date-utc "2038-01-01T00:00:00.000Z"
    --chaos-parameters-dictionary
    [
    {
        "Key":"adhoc",
        "Value":{
            "MaxConcurrentFaults":3,
            "EnableMoveReplicaFaults":true,
            "ChaosTargetFilter":{
                "NodeTypeInclusionList":[
                "N0010Ref",
                "N0020Ref",
                "N0030Ref",
                "N0040Ref",
                "N0050Ref"
                ]
            },
            "MaxClusterStabilizationTimeoutInSeconds":60,
            "WaitTimeBetweenIterationsInSeconds":15,
            "WaitTimeBetweenFaultsInSeconds":30,
            "TimeToRunInSeconds":"600",
            "Context":{
                "Map":{
                "test":"value"
                }
            },
            "ClusterHealthPolicy":{
                "MaxPercentUnhealthyNodes":0,
                "ConsiderWarningAsError":true,
                "MaxPercentUnhealthyApplications":0
            }
        }
    }
    ]
    --jobs
    [
    {
        "ChaosParameters":"adhoc",
        "Days":{
            "Sunday":true,
            "Monday":true,
            "Tuesday":true,
            "Wednesday":true,
            "Thursday":true,
            "Friday":true,
            "Saturday":true
        },
        "Times":[
            {
                "StartTime":{
                "Hour":0,
                "Minute":0
                },
                "EndTime":{
                "Hour":23,
                "Minute":59
                }
            }
        ]
    }
    ]
```



## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI.Set up the Service Fabric CLI.
- Ismerje meg, hogyan használhatja a Service Fabric CLI-t a [mintaparancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.
