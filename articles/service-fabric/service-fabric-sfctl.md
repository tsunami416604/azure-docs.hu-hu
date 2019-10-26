---
title: Azure Service Fabric CLI – sfctl | Microsoft Docs
description: Ismerteti a CLI-sfctl parancsokat Service Fabric.
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
ms.openlocfilehash: 7e7fc7bbc65e92960d7839f6531ef1f7c1935ed3
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900882"
---
# <a name="sfctl"></a>sfctl
Parancsok Service Fabric fürtök és entitások kezeléséhez. Ez a verzió Service Fabric 6,5 futtatókörnyezettel kompatibilis.

A parancsok a főnévi művelet mintáját követik. További információért tekintse meg az alcsoportokat.

## <a name="subgroups"></a>Alcsoportok
|Alcsoport|Leírás|
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
| [Store](service-fabric-sfctl-store.md) | Hajtsa végre az alapszintű fájl szintű műveleteket a fürt rendszerkép-tárolóján. |

## <a name="next-steps"></a>Következő lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.