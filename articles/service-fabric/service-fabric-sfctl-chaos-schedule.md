---
title: Az Azure sfctl káosz ütemezés Service Fabric parancssori felület - |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl káosz ütemezés parancsokat.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: dc3dd06b5feac1f66598cd65fa79f447a1bbd9be
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668313"
---
# <a name="sfctl-chaos-schedule"></a>sfctl-káosz ütemterv
GET, és a chaos ütemezés beállításához.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| Get | A Chaos ütemezését, mikor és hogyan futtathat a Chaos meghatározása beolvasása. |
| halmaz | A Chaos által használt ütemezés beállításához. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl-káosz ütemezés lekérése
A Chaos ütemezését, mikor és hogyan futtathat a Chaos meghatározása beolvasása.

Lekérdezi a verzióját használja a Chaos ütemezés és a Chaos ütemezés, amely meghatározza, mikor és hogyan futtathat a Chaos.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl-káosz beállítva ütemezés
A Chaos által használt ütemezés beállításához.

A Chaos káosz ütemezés szerint fut automatikusan beütemezett. A megadott bemeneti ütemezés szerint a verziót meg kell egyeznie a Chaos ütemezését, az a kiszolgáló verziója. Ha a megadott verzió nem egyezik meg a verziót a kiszolgálón, a Chaos ütemezés nem frissül. Ha verziója megegyezik a verzió a kiszolgálón, a Chaos ütemezése frissül, és a Chaos ütemezését, az a kiszolgáló verziójának értéke akkor növekszik, mentése és 0-ra burkolja 2 147 483 647 után. Ha a kérés érkezett, a Chaos fut, a hívás sikertelen lesz.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --chaos-parameters-dictionary | JSON kódolású jelölő karakterlánc nevek leképezése a feladatok által használt ChaosParameters listája. |
| --expiry-date-utc | A dátum és idő az ütemezés használata a Chaos ütemezése leállítására vonatkozó.  Alapértelmezett\: 9999-12-31T23\:59\:59.999Z. |
| --feladatok | JSON-kódolású lista ChaosScheduleJobs jelölő mikor fusson a káosz, és a Chaos futtatásához paramétereket. |
| --start – dátum-(UTC) | A dátum és idő kiválasztásakor az ütemezés használatához a Chaos ütemezése.  Alapértelmezett\: 1601-01-01T00\:00\:00.000Z. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |
| --version | A verziószám az ütemezés. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

### <a name="examples"></a>Példák

Az alábbi parancsot (feltéve, hogy az aktuális ütemezés 0 verzióval rendelkezik) ütemezés, amely elindítja a 2016-01-01, és érvényessége lejár a 2038-01-01, amely a nap, a hét káosz 24 órányi állítja be. A Chaos arra az időpontra lesz ütemezve a fürtön.

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
- [Állítsa be a](service-fabric-cli.md) a Service Fabric parancssori felület.
- Ismerje meg, hogyan használható a Service Fabric parancssori felület használatával a [-szkript minták](/azure/service-fabric/scripts/sfctl-upgrade-application).
