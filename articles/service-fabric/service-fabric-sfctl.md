---
title: Azure Service Fabric CLI- sfctl
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. A parancsok és alcsoportok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 56efa15a7de3414f9c535e66bd80c94594cd5038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906220"
---
# <a name="sfctl"></a>sfctl között
A Service Fabric-fürtök és entitások kezelésére vonatkozó parancsok. Ez a verzió kompatibilis a Service Fabric 7.0 futásidejű.

A parancsok a főnévi ige mintát követik. További információt az alcsoportokban talál.

## <a name="subgroups"></a>Alcsoportok
|Alcsoport|Leírás|
| --- | --- |
| [alkalmazás](service-fabric-sfctl-application.md) | Alkalmazások és alkalmazástípusok létrehozása, törlése és kezelése. |
| [Káosz](service-fabric-sfctl-chaos.md) | Indítsa el, állítsa le és jelentse a káosz teszt szolgáltatást. |
| [Fürt](service-fabric-sfctl-cluster.md) | Válassza ki, kezelje és kezelje a Service Fabric-fürtöket. |
| [Össze](service-fabric-sfctl-compose.md) | Hozzon létre, töröljön és kezelje a Docker Compose alkalmazásokat. |
| [tároló](service-fabric-sfctl-container.md) | Tárolóval kapcsolatos parancsok futtatása fürtcsomóponton. |
| [események](service-fabric-sfctl-events.md) | Események lekérése az eseménytárolóból (ha az EventStore szolgáltatás már telepítve van). |
| [van](service-fabric-sfctl-is.md) | Parancsok lekérdezése és küldése az infrastruktúra-szolgáltatásnak. |
| [háló](service-fabric-sfctl-mesh.md) | A Service Fabric Mesh-alkalmazások törlése és kezelése. |
| [Csomópont](service-fabric-sfctl-node.md) | A fürtöt alkotó csomópontok kezelése. |
| [Partíció](service-fabric-sfctl-partition.md) | Bármely szolgáltatás partícióinak lekérdezése és kezelése. |
| [Tulajdonság](service-fabric-sfctl-property.md) | A Service Fabric-nevek alatt tárolja és lekérdezi a tulajdonságokat. |
| [Replika](service-fabric-sfctl-replica.md) | A szolgáltatáspartíciókhoz tartozó replikák kezelése. |
| [Rpm](service-fabric-sfctl-rpm.md) | Parancsok lekérdezése és küldése a javításkezelő szolgáltatásnak. |
| [sa-fürt](service-fabric-sfctl-sa-cluster.md) | Önálló Service Fabric-fürtök kezelése. |
| [Szolgáltatás](service-fabric-sfctl-service.md) | Szolgáltatások, szolgáltatástípusok és szolgáltatáscsomagok létrehozása, törlése és kezelése. |
| [beállítások](service-fabric-sfctl-settings.md) | Az sfctl ezen példányához helyi beállítások konfigurálása. |
| [áruház](service-fabric-sfctl-store.md) | Alapvető fájlszintű műveletek végrehajtása a fürtképtárolón. |

## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI.Set up the Service Fabric CLI.
- Ismerje meg, hogyan használhatja a Service Fabric CLI-t a [mintaparancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.