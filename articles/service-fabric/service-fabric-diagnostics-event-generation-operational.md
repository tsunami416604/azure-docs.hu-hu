---
title: Az Azure Service Fabric esemény lista |} Microsoft Docs
description: Események figyelése Azure Service Fabric által biztosított átfogó listáját a fürt.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 8426218cf8f0819f20c78d9d84e202541d025b60
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="list-of-service-fabric-events"></a>A Service Fabric-események 

A Service Fabric tesz elérhetővé, elsődleges fürthöz kapcsolódó események, hogy a fürt állapotának [Service Fabric események](service-fabric-diagnostics-events.md). Ezek a műveletek a Service Fabric végzi el a csomópontokon és a fürt vagy a fürt tulajdonos/operátor felügyeleti döntések alapulnak. Ezek az események lekérdezésével érhető el a [EventStore](service-fabric-diagnostics-eventstore.md) a fürtben, vagy a működési csatornán keresztül. A Windows-gépek a műveleti csatornát van is csatlakoztatnia kell az Eseménynapló -, a Service Fabric eseményeket az eseménynaplóban megjelenik. 

>[!NOTE]
>A Service Fabric események verziók < 6.2 fürtök listáját tekintse meg a következő részt. 

Íme az események a platform, az entitás, amelyek leképezik a szerint rendezve.

## <a name="cluster-events"></a>Fürtesemények

**Fürt frissítési események**

| EventId | Name (Név) | Forrás (feladat) | Szint | Verzió |
| --- | --- | --- | --- | --- |
| 29627 | ClusterUpgradeStartOperational | TANÚSÍTVÁNYKEZELŐ | Tájékoztató | C1 |
| 29628 | ClusterUpgradeCompleteOperational | TANÚSÍTVÁNYKEZELŐ | Tájékoztató | 1 |
| 29629 | ClusterUpgradeRollbackStartOperational | TANÚSÍTVÁNYKEZELŐ | Tájékoztató | 1 |
| 29630 | ClusterUpgradeRollbackCompleteOperational | TANÚSÍTVÁNYKEZELŐ | Tájékoztató | 1 |
| 29631 | ClusterUpgradeDomainCompleteOperational | TANÚSÍTVÁNYKEZELŐ | Tájékoztató | 1 |

**A fürt állapotának jelentési eseményei**

| EventId | Name (Név) | Forrás (feladat) | Szint | Verzió |
| --- | --- | --- | --- | --- |
| 54428 | ProcessClusterReportOperational | HM | Tájékoztató | 1 |
| 54437 | ExpiredClusterEventOperational | HM | Tájékoztató | 1 |

**Chaos eseményei**

| EventId | Name (Név) | Forrás (feladat) | Szint | Verzió |
| --- | --- | --- | --- | --- |
| 50021 | ChaosStartedEvent | Tesztelhetőségi | Tájékoztató | 1 |
| 50023 | ChaosStoppedEvent | Tesztelhetőségi | Tájékoztató | 1 |

## <a name="node-events"></a>Csomópont-események

**Csomópont életciklus-események** 

| EventId | Name (Név) | Forrás (feladat) | Szint | Verzió |
| --- | --- | --- | --- | --- |
| 18602 | DeactivateNodeCompletedOperational | FM | Tájékoztató | 1 |
| 18603 | NodeUpOperational | FM | Tájékoztató | 1 |
| 18604 | NodeDownOperational | FM | Tájékoztató | 1 |
| 18605 | NodeAddedOperational | FM | Tájékoztató | 1 |
| 18606 | NodeRemovedOperational | FM | Tájékoztató | 1 |
| 18607 | DeactivateNodeStartOperational | FM | Tájékoztató | 1 |
| 25620 | NodeOpening | FabricNode | Tájékoztató | 1 |
| 25621 | NodeOpenedSuccess | FabricNode | Tájékoztató | 1 |
| 25622 | NodeOpenedFailed | FabricNode | Tájékoztató | 1 |
| 25623 | NodeClosing | FabricNode | Tájékoztató | 1 |
| 25624 | NodeClosed | FabricNode | Tájékoztató | 1 |
| 25625 | NodeAborting | FabricNode | Tájékoztató | 1 |
| 25626 | NodeAborted | FabricNode | Tájékoztató | 1 |

**Csomópont állapota jelentési eseményei**

| EventId | Name (Név) | Forrás (feladat) | Szint | Verzió |
| --- | --- | --- | --- | --- |
| 54423 | ProcessNodeReportOperational | HM | Tájékoztató | 1 |
| 54432 | ExpiredNodeEventOperational | HM | Tájékoztató | 1 |

**Chaos csomópont események**

| EventId | Name (Név) | Forrás (feladat) | Szint | Verzió |
| --- | --- | --- | --- | --- |
| 50033 | ChaosRestartNodeFaultScheduledEvent | Tesztelhetőségi | Tájékoztató | 1 |
| 50087 | ChaosRestartNodeFaultCompletedEvent | Tesztelhetőségi | Tájékoztató | 1 |

## <a name="application-events"></a>Alkalmazásesemények

**Alkalmazás életciklusa események**

| EventId | Name (Név) | Forrás (feladat) | Szint | Verzió |
| --- | --- | --- | --- | --- |
| 29620 | ApplicationCreatedOperational | TANÚSÍTVÁNYKEZELŐ | Tájékoztató | 1 |
| 29625 | ApplicationDeletedOperational | TANÚSÍTVÁNYKEZELŐ | Tájékoztató | 1 |
| 23083 | ProcessExitedOperational | Hosting | Tájékoztató | 1 |

**Alkalmazás frissítési események**

| EventId | Name (Név) | Forrás (feladat) | Szint | Verzió |
| --- | --- | --- | --- | --- |
| 29621 | ApplicationUpgradeStartOperational | TANÚSÍTVÁNYKEZELŐ | Tájékoztató | 1 |
| 29622 | ApplicationUpgradeCompleteOperational | TANÚSÍTVÁNYKEZELŐ | Tájékoztató | 1 |
| 29623 | ApplicationUpgradeRollbackStartOperational | TANÚSÍTVÁNYKEZELŐ | Tájékoztató | 1 |
| 29624 | ApplicationUpgradeRollbackCompleteOperational | TANÚSÍTVÁNYKEZELŐ | Tájékoztató | 1 |
| 29626 | ApplicationUpgradeDomainCompleteOperational | TANÚSÍTVÁNYKEZELŐ | Tájékoztató | 1 |

**Alkalmazás állapotának jelentési eseményei**

| EventId | Name (Név) | Forrás (feladat) | Szint | Verzió |
| --- | --- | --- | --- | --- |
| 54425 | ProcessApplicationReportOperational | HM | Tájékoztató | 1 |
| 54426 | ProcessDeployedApplicationReportOperational | HM | Tájékoztató | 1 |
| 54427 | ProcessDeployedServicePackageReportOperational | HM | Tájékoztató | 1 |
| 54434 | ExpiredApplicationEventOperational | HM | Tájékoztató | 1 |
| 54435 | ExpiredDeployedApplicationEventOperational | HM | Tájékoztató | 1 |
| 54436 | ExpiredDeployedServicePackageEventOperational | HM | Tájékoztató | 1 |

**Chaos alkalmazásesemények**

| EventId | Name (Név) | Forrás (feladat) | Szint | Verzió |
| --- | --- | --- | --- | --- |
| 50053 | ChaosRestartCodePackageFaultScheduledEvent | Tesztelhetőségi | Tájékoztató | 1 |
| 50101 | ChaosRestartCodePackageFaultCompletedEvent | Tesztelhetőségi | Tájékoztató | 1 |

## <a name="service-events"></a>Szolgáltatás-események

**Szolgáltatási életciklus-események**

| EventId | Name (Név) | Forrás (feladat) | Szint | Verzió |
| --- | --- | --- | --- | --- |
| 18602 | ServiceCreatedOperational | FM | Tájékoztató | 1 |
| 18658 | ServiceDeletedOperational | FM | Tájékoztató | 1 |

**Állapotfigyelő szolgáltatás eseményei**

| EventId | Name (Név) | Forrás (feladat) | Szint | Verzió |
| --- | --- | --- | --- | --- |
| 54424 | ProcessServiceReportOperational | HM | Tájékoztató | 1 |
| 54433 | ExpiredServiceEventOperational | HM | Tájékoztató | 1 |

## <a name="partition-events"></a>Partíció események

**Partíció áthelyezés események**

| EventId | Name (Név) | Forrás (feladat) | Szint | Verzió |
| --- | --- | --- | --- | --- |
| 18940 | ReconfigurationCompleted | A HELYREÁLLÍTÁSI ÜGYNÖK | Tájékoztató | 1 |

**Partíció állapotfigyelő jelentési eseményei**

| EventId | Name (Név) | Forrás (feladat) | Szint | Verzió |
| --- | --- | --- | --- | --- |
| 54422 | ProcessPartitionReportOperational | HM | Tájékoztató | 1 |
| 54431 | ExpiredPartitionEventOperational | HM | Tájékoztató | 1 |

**Chaos partíció események**

| EventId | Name (Név) | Forrás (feladat) | Szint | Verzió |
| --- | --- | --- | --- | --- |
| 50069 | ChaosMovePrimaryFaultScheduledEvent | Tesztelhetőségi | Tájékoztató | 1 |
| 50077 | ChaosMoveSecondaryFaultScheduledEvent | Tesztelhetőségi | Tájékoztató | 1 |

**Partíció elemzési események**

| EventId | Name (Név) | Forrás (feladat) | Szint | Verzió |
| --- | --- | --- | --- | --- |
| 65003 | PrimaryMoveAnalysisEvent | Tesztelhetőségi | Tájékoztató | 1 |

## <a name="replica-events"></a>A replika események

**A replika állapota jelentési eseményei**

| EventId | Name (Név) | Forrás (feladat) | Szint | Verzió |
| --- | --- | --- | --- | --- |
| 54429 | ProcessStatefulReplicaReportOperational | HM | Tájékoztató | 1 |
| 54430 | ProcessStatelessInstanceReportOperational | HM | Tájékoztató | 1 |
| 54438 | ExpiredStatefulReplicaEventOperational | HM | Tájékoztató | 1 |
| 54439 | ExpiredStatelessInstanceEventOperational | HM | Tájékoztató | 1 |

**Chaos replika események**

| EventId | Name (Név) | Forrás (feladat) | Szint | Verzió |
| --- | --- | --- | --- | --- |
| 50047 | ChaosRestartReplicaFaultScheduledEvent | Tesztelhetőségi | Tájékoztató | 1 |
| 50051 | ChaosRemoveReplicaFaultScheduledEvent | Tesztelhetőségi | Tájékoztató | 1 |
| 50093 | ChaosRemoveReplicaFaultCompletedEvent | Tesztelhetőségi | Tájékoztató | 1 |

## <a name="container-events"></a>Tároló események

**Tároló életciklus-események** 

| EventId | Name (Név) | Forrás (feladat) | Szint | Verzió |
| --- | --- | --- | --- | --- |
| 23074 | ContainerActivatedOperational | Hosting | Tájékoztató | 1 |
| 23075 | ContainerDeactivatedOperational | Hosting | Tájékoztató | 1 |
| 23082 | ContainerExitedOperational | Hosting | Tájékoztató | 1 |

## <a name="other-events"></a>A további események

**Korrelációs események**

| EventId | Name (Név) | Forrás (feladat) | Szint | Verzió |
| --- | --- | --- | --- | --- |
| 65011 | CorrelationOperational | Tesztelhetőségi | Tájékoztató | 1 |

## <a name="events-prior-to-version-62"></a>6.2-es vagy korábbi események

Események 6.2-es vagy korábbi Service Fabric által biztosított átfogó listája itt található.

| EventId | Name (Név) | Forrás (feladat) | Szint |
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
| 23074 | ContainerActivated | Hosting | Tájékoztató |
| 23075 | ContainerDeactivated | Hosting | Tájékoztató |
| 29620 | ApplicationCreated | TANÚSÍTVÁNYKEZELŐ | Tájékoztató |
| 29621 | ApplicationUpgradeStart | TANÚSÍTVÁNYKEZELŐ | Tájékoztató |
| 29622 | ApplicationUpgradeComplete | TANÚSÍTVÁNYKEZELŐ | Tájékoztató |
| 29623 | ApplicationUpgradeRollback | TANÚSÍTVÁNYKEZELŐ | Tájékoztató |
| 29624 | ApplicationUpgradeRollbackComplete | TANÚSÍTVÁNYKEZELŐ | Tájékoztató |
| 29625 | ApplicationDeleted | TANÚSÍTVÁNYKEZELŐ | Tájékoztató |
| 29626 | ApplicationUpgradeDomainComplete | TANÚSÍTVÁNYKEZELŐ | Tájékoztató |
| 18566 | ServiceCreated | FM | Tájékoztató |
| 18567 | ServiceDeleted | FM | Tájékoztató |

## <a name="next-steps"></a>További lépések

* További általános információ [platform szintjén az eseménygenerálás](service-fabric-diagnostics-event-generation-infra.md) a Service Fabric
* Módosítja a [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) konfigurációját, és további naplófájlok összegyűjtése
* [Az Application Insights beállítása](service-fabric-diagnostics-event-analysis-appinsights.md) a műveleti naplókat csatorna megjelenítéséhez
