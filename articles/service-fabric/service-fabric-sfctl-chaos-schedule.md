---
title: Azure Service Fabric CLI sfctl chaos ütemezést |} Microsoft Docs
description: Ismerteti a Service Fabric CLI sfctl chaos ütemezés parancsok.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: 0d09338f71d71d07ab0e037d4736cfaa1f3cff85
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34764017"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos ütemezése
Szerezze be, és a chaos ütemezés beállításához.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| Get | A meghatározása, mikor és hogyan futtathat Chaos Chaos ütemezés lekérése. |
| Állítsa be | Adja meg a Chaos ütemezést Chaos által használható. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl chaos ütemezés beolvasása
A meghatározása, mikor és hogyan futtathat Chaos Chaos ütemezés lekérése.

Lekérdezi a verzióját használja az Chaos ütemezés és a Chaos ütemezés szerint határozza meg, mikor és hogyan futtathat Chaos.

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos ütemezés beállítása
Adja meg a Chaos ütemezést Chaos által használható.

Az ütemezés beállítása, Chaos jelenleg használatban Chaos által. Chaos automatikusan beütemezett Chaos ütemezés szerint futtatja. Az a megadott bemeneti ütemezésben meg kell egyeznie a Chaos ütemezését, a kiszolgáló verziója. Ha a megadott verzió nem egyezik meg a verziót a kiszolgálón, a Chaos ütemezés frissítése nem történik meg. Ha a megadott ugyanolyan verziójúak, a kiszolgálón, a Chaos ütemezése frissül, és az a kiszolgáló Chaos ütemezés rendszer eggyel be egyet, majd burkolja 0-ra 2 147 483 647 után. Ha Chaos futása közben a rendszer telefonhívást indít, a hívás sikertelen lesz.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --chaos-paraméterek-könyvtár | JSON kódolású karakterlánc nevek leképezéseket képviselő feladatok által használt ChaosParameters lista. |
| --lejárati-dátum-UTC szerint | A dátum és idő az ütemezés használata Chaos ütemezése leállítására vonatkozó.  Alapértelmezett\: 9999-12-31T23\:59\:59.999Z. |
| – feladatok | JSON-kódolású lista képviselő Chaos futási időpontjának ChaosScheduleJobs és milyen paraméterekkel rendelkező Chaos futtatásához. |
| --start-dátum-UTC szerint | A dátum és idő, mikor kell elkezdeni az Chaos ütemezni a ütemezés használata a.  Alapértelmezett\: 1601-01-01T00\:00\:00.000Z. |
| --verziója | Az ütemezés verziószámát. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

### <a name="examples"></a>Példák

A következő parancsot (feltéve, hogy az aktuális ütemezés 0 verzióval rendelkezik) ütemezés szerint 2016-01-01 kezdése és lejárati ideje 2038-01-01 Chaos 24 óra, nap, a hét futtató beállítása. Chaos arra az időpontra ütemezi a fürtön.

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

## <a name="next-steps"></a>További lépések
- [Állítson be](service-fabric-cli.md) a Service Fabric CLI.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).