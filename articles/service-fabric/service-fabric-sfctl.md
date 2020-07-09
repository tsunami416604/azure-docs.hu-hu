---
title: Azure Service Fabric CLI – sfctl
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. A parancsok és alcsoportok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 56efa15a7de3414f9c535e66bd80c94594cd5038
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76906220"
---
# <a name="sfctl"></a>sfctl
Parancsok Service Fabric fürtök és entitások kezeléséhez. Ez a verzió Service Fabric 7,0 futtatókörnyezettel kompatibilis.

A parancsok a főnévi művelet mintáját követik. További információért tekintse meg az alcsoportokat.

## <a name="subgroups"></a>Alcsoportok
|Alcsoport|Description|
| --- | --- |
| [alkalmazás](service-fabric-sfctl-application.md) | Alkalmazások és alkalmazások típusainak létrehozása, törlése és kezelése. |
| [Chaos](service-fabric-sfctl-chaos.md) | Indítsa el, állítsa le és jelentse a Chaos test Service-t. |
| [fürt](service-fabric-sfctl-cluster.md) | Service Fabric fürtök kiválasztása, kezelése és üzemeltetése. |
| [Compose](service-fabric-sfctl-compose.md) | Docker-összeállítási alkalmazások létrehozása, törlése és kezelése. |
| [tároló](service-fabric-sfctl-container.md) | Tárolóval kapcsolatos parancsok futtatása fürtcsomóponton. |
| [események](service-fabric-sfctl-events.md) | Események beolvasása az események tárolójából (ha a EventStore szolgáltatás már telepítve van). |
| [van](service-fabric-sfctl-is.md) | Parancsok lekérdezése és küldése az infrastruktúra-szolgáltatásnak. |
| [háló](service-fabric-sfctl-mesh.md) | Service Fabric Mesh-alkalmazások törlése és kezelése. |
| [csomópont](service-fabric-sfctl-node.md) | A fürtöt alkotó csomópontok kezelése. |
| [partíció](service-fabric-sfctl-partition.md) | Bármely szolgáltatás partícióinak lekérdezése és kezelése. |
| [tulajdonság](service-fabric-sfctl-property.md) | Tulajdonságok tárolása és lekérdezése Service Fabric neve alatt. |
| [replika](service-fabric-sfctl-replica.md) | Kezelheti a szolgáltatási partíciókhoz tartozó replikákat. |
| [rpm](service-fabric-sfctl-rpm.md) | Parancsok lekérdezése és küldése a Repair Manager szolgáltatásnak. |
| [SA-cluster](service-fabric-sfctl-sa-cluster.md) | Önálló Service Fabric-fürtök kezelése. |
| [szolgáltatás](service-fabric-sfctl-service.md) | Szolgáltatás, szolgáltatások típusai és szolgáltatási csomagok létrehozása, törlése és kezelése. |
| [beállítások](service-fabric-sfctl-settings.md) | Konfigurálja a helyi beállításokat a sfctl ezen példánya számára. |
| [áruház](service-fabric-sfctl-store.md) | Hajtsa végre az alapszintű fájl szintű műveleteket a fürt rendszerkép-tárolóján. |

## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.