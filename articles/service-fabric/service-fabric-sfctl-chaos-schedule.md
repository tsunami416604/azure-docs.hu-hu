---
title: Azure Service Fabric CLI – sfctl Chaos-ütemterv | Microsoft Docs
description: Ismerteti a Service Fabric CLI sfctl Chaos Schedule parancsait.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 443a8186ac6e57360105e59e30f84db997cd2251
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72897543"
---
# <a name="sfctl-chaos-schedule"></a>sfctl-káosz ütemterv
A Chaos-ütemterv beolvasása és beállítása.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| Get | Szerezze be a káosz-ütemtervet, amely meghatározza, hogy mikor és hogyan kell futtatni a káoszt. |
| halmaz | Állítsa be a káosz által használt ütemtervet. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl Chaos-ütemterv Get
Szerezze be a káosz-ütemtervet, amely meghatározza, hogy mikor és hogyan kell futtatni a káoszt.

Beolvassa a használatban lévő Chaos-ütemterv verzióját, valamint azt a káosz-ütemtervet, amely meghatározza, hogy mikor és hogyan kell futtatni a káoszt.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl Chaos-ütemterv beállítása
Állítsa be a káosz által használt ütemtervet.

A Chaos automatikusan ütemezni fogja a futtatásokat a Chaos-ütemterv alapján. A Chaos-ütemterv akkor frissül, ha a megadott verzió megegyezik a kiszolgálón található verzióval. A Chaos-ütemterv frissítésekor a kiszolgálón lévő verzió eggyel nő. A kiszolgáló verziószáma 0 lesz, miután elérte a nagy számot. Ha a káosz fut a híváskor, a hívás sikertelen lesz.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Chaos-Parameters-Dictionary | A JSON-kódolású lista a feladatok által használandó ChaosParameters karakterlánc-nevek hozzárendelését jelképezi. |
| --lejárat dátuma – UTC | Az a dátum és idő, ameddig a rendszer leállítja a káoszt az ütemterv használatával.  Alapértelmezett\: 9999-12-31T23\:59\:59.999 Z. |
| – feladatok | JSON-kódolású ChaosScheduleJobs-lista, amely azt jelképezi, hogy mikor fusson a káosz, és hogy milyen paraméterekkel kell futtatni a Chaos-t |
| --Start-Date-UTC | Annak a dátumnak és időpontnak a dátuma és ideje, hogy mikor kell elkezdeni az ütemtervet ütemezni.  Alapértelmezett\: 1601-01-01T00\:00\:00.000 Z. |
| --időtúllépés-t | Alapértelmezett\: 60. |
| --Version | Az ütemezett verzió verziószáma. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

### <a name="examples"></a>Példák

A következő parancs egy ütemtervet állít be (feltételezve, hogy az aktuális ütemezett verzió 0), amely 2016-01-01-kor kezdődik, és 2038-01-01 a Chaos 24 órányi nap, a hét 7 napján fut le. A káosz a fürtön lesz ütemezve.

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


## <a name="next-steps"></a>Következő lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.
