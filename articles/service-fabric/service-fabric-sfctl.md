---
title: Azure Service Fabric CLI – sfctl | Microsoft Docs
description: Ismerteti a CLI-sfctl parancsokat Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 35b881268ca21a840836c96388a4562a54d17d3b
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035804"
---
# <a name="sfctl"></a>sfctl
Parancsok Service Fabric fürtök és entitások kezeléséhez. Ez a verzió Service Fabric 6,4 futtatókörnyezettel kompatibilis.

A parancsok a főnévi művelet mintáját követik. További információért tekintse meg az alcsoportokat.

## <a name="subgroups"></a>Alcsoportok
|Alcsoport|Leírás|
| --- | --- |
| [alkalmazás](service-fabric-sfctl-application.md) | Alkalmazások és alkalmazások típusainak létrehozása, törlése és kezelése. |
| [Chaos](service-fabric-sfctl-chaos.md) | Indítsa el, állítsa le és jelentse a Chaos test Service-t. |
| [cluster](service-fabric-sfctl-cluster.md) | Service Fabric fürtök kiválasztása, kezelése és üzemeltetése. |
| [compose](service-fabric-sfctl-compose.md) | Docker-összeállítási alkalmazások létrehozása, törlése és kezelése. |
| [container](service-fabric-sfctl-container.md) | Tárolóval kapcsolatos parancsok futtatása fürtcsomóponton. |
| [van](service-fabric-sfctl-is.md) | Parancsok lekérdezése és küldése az infrastruktúra-szolgáltatásnak. |
| [háló](service-fabric-sfctl-mesh.md) | Service Fabric Mesh-alkalmazások törlése és kezelése. |
| [csomópont](service-fabric-sfctl-node.md) | A fürtöt alkotó csomópontok kezelése. |
| [partition](service-fabric-sfctl-partition.md) | Bármely szolgáltatás partícióinak lekérdezése és kezelése. |
| [tulajdonság](service-fabric-sfctl-property.md) | Tulajdonságok tárolása és lekérdezése Service Fabric neve alatt. |
| [replica](service-fabric-sfctl-replica.md) | Kezelheti a szolgáltatási partíciókhoz tartozó replikákat. |
| [rpm](service-fabric-sfctl-rpm.md) | Parancsok lekérdezése és küldése a Repair Manager szolgáltatásnak. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Önálló Service Fabric-fürtök kezelése. |
| [service](service-fabric-sfctl-service.md) | Szolgáltatás, szolgáltatások típusai és szolgáltatási csomagok létrehozása, törlése és kezelése. |
| [beállítások](service-fabric-sfctl-settings.md) | Konfigurálja a helyi beállításokat a sfctl ezen példánya számára. |
| [store](service-fabric-sfctl-store.md) | Hajtsa végre az alapszintű fájl szintű műveleteket a fürt rendszerkép-tárolóján. |

## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.