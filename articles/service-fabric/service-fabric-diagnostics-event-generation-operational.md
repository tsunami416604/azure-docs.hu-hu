---
title: "Az Azure Service Fabric működési csatorna |} Microsoft Docs"
description: "A működési csatorna az Azure Service Fabric-fürtök létrehozott naplók átfogó listáját."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: 712679b8fae9059df602881f28a1b74f7244fca3
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="operational-channel"></a>Működési csatorna 

A műveleti csatornát naplók végzi el a Service Fabric a csomópontokon és a fürt magas szintű műveletekből áll. Ha "diagnosztika" fürt engedélyezve van, az Azure diagnosztikai ügynök telepítve van a fürt, és úgy konfigurálva, hogy olvassa a műveleti csatornát a naplók alapértelmezés szerint ki van. További információk konfigurálása a [Azure diagnosztikai ügynök](service-fabric-diagnostics-event-aggregation-wad.md) a fürt további naplóit vagy teljesítményszámlálóit átvételéhez diagnosztika konfigurációjának módosítása. 

## <a name="operational-channel-logs"></a>Működési csatorna naplók 

A naplók a műveleti csatornát a Service Fabric által biztosított átfogó listája itt található. 

| Eseményazonosító | Név | Forrás (feladat) | Szint |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Tájékoztató |
| 25621 | NodeOpenedSuccess | FabricNode | Tájékoztató |
| 25622 | NodeOpenedFailed | FabricNode | Tájékoztató |
| 25623 | NodeClosing | FabricNode | Tájékoztató |
| 25624 | NodeClosed | FabricNode | Tájékoztató |
| 25625 | NodeAborting | FabricNode | Tájékoztató |
| 25626 | NodeAborted | FabricNode | Tájékoztató |
| 29627 | ClusterUpgradeStart | TANÚSÍTVÁNYKEZELŐ | Tájékoztató |
| 29628 | ClusterUpgradeComplete | TANÚSÍTVÁNYKEZELŐ | Tájékoztató |
| 29629 | ClusterUpgradeRollback | TANÚSÍTVÁNYKEZELŐ | Tájékoztató |
| 29630 | ClusterUpgradeRollbackComplete | TANÚSÍTVÁNYKEZELŐ | Tájékoztató |
| 29631 | ClusterUpgradeDomainComplete | TANÚSÍTVÁNYKEZELŐ | Tájékoztató |
| 23074 | ContainerActivated | Üzemeltetési | Tájékoztató |
| 23075 | ContainerDeactivated | Üzemeltetési | Tájékoztató |
| 29620 | ApplicationCreated | TANÚSÍTVÁNYKEZELŐ | Tájékoztató |
| 29621 | ApplicationUpgradeStart | TANÚSÍTVÁNYKEZELŐ | Tájékoztató |
| 29622 | ApplicationUpgradeComplete | TANÚSÍTVÁNYKEZELŐ | Tájékoztató |
| 29623 | ApplicationUpgradeRollback | TANÚSÍTVÁNYKEZELŐ | Tájékoztató |
| 29624 | ApplicationUpgradeRollbackComplete | TANÚSÍTVÁNYKEZELŐ | Tájékoztató |
| 29625 | ApplicationDeleted | TANÚSÍTVÁNYKEZELŐ | Tájékoztató |
| 29626 | ApplicationUpgradeDomainComplete | TANÚSÍTVÁNYKEZELŐ | Tájékoztató |
| 18566 | ServiceCreated | FM | Tájékoztató |
| 18567 | ServiceDeleted | FM | Tájékoztató |

## <a name="next-steps"></a>Következő lépések

* További általános információ [platform szintjén az eseménygenerálás](service-fabric-diagnostics-event-generation-infra.md) a Service Fabric
* Módosítja a [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) konfigurációját, és további naplófájlok összegyűjtése
* [Az Application Insights beállítása](service-fabric-diagnostics-event-analysis-appinsights.md) a műveleti naplókat csatorna megjelenítéséhez
