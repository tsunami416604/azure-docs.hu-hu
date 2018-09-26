---
title: Az Azure Service Fabric eseménylista |} A Microsoft Docs
description: Fürtök figyelése az Azure Service Fabric által biztosított események átfogó listája.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: da4a779bca806fe6aa392db96eafc6c20f8ddcf6
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182161"
---
# <a name="list-of-service-fabric-events"></a>A Service Fabric-események listája 

A Service Fabric megjelenít egy elsődleges Fürtesemények tájékoztatja, mint a fürt állapotának [Service Fabric-események](service-fabric-diagnostics-events.md). Ezek a csomópontok és a fürt Service Fabric által végrehajtott műveletek vagy a fürt tulajdonosa vagy operátor által végzett kezelési döntést alapulnak. Ezek az események lekérdezésével érhető el a [EventStore](service-fabric-diagnostics-eventstore.md) a fürtben, vagy a működési csatornán keresztül. Windows-gépeken a műveleti csatorna van is csatlakoztatnia kell az eseménynaplóba – így láthatja a Service Fabric-események az eseménynaplóban. 

>[!NOTE]
>Service Fabric-fürtök verziói < 6.2 események listája tekintse meg a következő szakaszt. 

Itt érhető el az összes esemény listáját a platformon, az entitást, amelyek leképezik a szerint rendezve.

## <a name="cluster-events"></a>Fürtesemények

**A fürt frissítési események**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | --- | --- | --- | --- |
| 29627 | ClusterUpgradeStarted | Fürt frissítése megkezdődött. | CM | Tájékoztató | 1 |
| 29628 | ClusterUpgradeCompleted | Fürt frissítése befejeződött| CM | Tájékoztató | 1 |
| 29629 | ClusterUpgradeRollbackStarted | Fürt frissítése megkezdődött a visszaállítás | CM | Tájékoztató | 1 |
| 29630 | ClusterUpgradeRollbackCompleted | Fürt frissítése befejeződött, visszaállítása | CM | Tájékoztató | 1 |
| 29631 | ClusterUpgradeDomainCompleted | Fürt frissítése során egy tartomány frissítése befejeződött | CM | Tájékoztató | 1 |

## <a name="node-events"></a>Csomópont-események

**Csomópont életciklussal kapcsolatos események** 

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 18602 | NodeDeactivateCompleted | A csomópont inaktiválása befejeződött | AZ FM | Tájékoztató | 1 |
| 18603 | NodeUp | A fürt észlelt egy csomópont van elindítva | AZ FM | Tájékoztató | 1 |
| 18604 | NodeDown | A fürt észlelt egy csomópont le lett állítva. |  AZ FM | Tájékoztató | 1 |
| 18605 | NodeAddedToCluster | Új csomóponttal bővült a fürt | AZ FM | Tájékoztató | 1 |
| 18606 | NodeRemovedFromCluster | A csomópont el lett távolítva a fürtből | AZ FM | Tájékoztató | 1 |
| 18607 | NodeDeactivateStarted | A csomópont inaktiválása megkezdődött | AZ FM | Tájékoztató | 1 |
| 25620 | NodeOpening | Egy csomópont indítása folyamatban van. Első szakasz a csomópont-életciklus | FabricNode | Tájékoztató | 1 |
| 25621 | NodeOpenSucceeded | Egy csomópont sikeresen elindult | FabricNode | Tájékoztató | 1 |
| 25622 | NodeOpenFailed | A csomópont nem indult el | FabricNode | Tájékoztató | 1 |
| 25623 | NodeClosing | Egy csomópont leáll. Az utolsó fáziséit, a csomópont-életciklus kezdete | FabricNode | Tájékoztató | 1 |
| 25624 | NodeClosed | A csomópont leállítása sikeresen megtörtént | FabricNode | Tájékoztató | 1 |
| 25625 | NodeAborting | Egy csomópontja megkezdi ungracefully leállítása | FabricNode | Tájékoztató | 1 |
| 25626 | NodeAborted | Egy csomópont ungracefully le lett állítva | FabricNode | Tájékoztató | 1 |

## <a name="application-events"></a>Alkalmazásesemények

**Alkalmazások életciklus-események**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 29620 | ApplicationCreated | Új alkalmazás létrehozása | CM | Tájékoztató | 1 |
| 29625 | ApplicationDeleted | Egy meglévő alkalmazás törölve lett | CM | Tájékoztató | 1 |
| 23083 | ApplicationProcessExited | Az alkalmazáson belül a folyamat kilépett. | Hosting | Tájékoztató | 1 |

**Frissítési alkalmazásesemények**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 29621 | ApplicationUpgradeStarted | Egy alkalmazás frissítése megkezdődött. | CM | Tájékoztató | 1 |
| 29622 | ApplicationUpgradeCompleted | Egy alkalmazás frissítése befejeződött | CM | Tájékoztató | 1 |
| 29623 | ApplicationUpgradeRollbackStarted | Egy alkalmazás frissítése megkezdődött a visszaállítás |CM | Tájékoztató | 1 |
| 29624 | ApplicationUpgradeRollbackCompleted | Egy alkalmazás frissítése befejeződött, visszaállítása | CM | Tájékoztató | 1 |
| 29626 | ApplicationUpgradeDomainCompleted | Tartomány frissítése befejeződött, egy alkalmazás frissítése során | CM | Tájékoztató | 1 |

## <a name="service-events"></a>Szolgáltatás-események

**Service-életciklusesemények**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | Egy új szolgáltatás létrehozása | AZ FM | Tájékoztató | 1 |
| 18658 | ServiceDeleted | Egy meglévő szolgáltatás törlése | AZ FM | Tájékoztató | 1 |

## <a name="partition-events"></a>Partíció események

**Partíció áthelyezési események**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | A partíció újrakonfigurálása befejeződött | RA | Tájékoztató | 1 |

## <a name="container-events"></a>Tárolóesemények

**Tároló életciklus-események** 

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | A tároló elindult | Hosting | Tájékoztató | 1 |
| 23075 | ContainerDeactivated | Egy tároló leállt | Hosting | Tájékoztató | 1 |
| 23082 | ContainerExited | Egy tároló kilépett – ellenőrizze a UnexpectedTermination jelző | Hosting | Tájékoztató | 1 |

## <a name="health-reports"></a>Rendszerállapot-jelentések

**A fürt jelentés állapotesemények**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Új fürt egészségügyi jelentés érhető el | HM | Tájékoztató | 1 |
| 54437 | ClusterHealthReportExpired | Meglévő fürt egészségügyi jelentés érvényessége lejárt | HM | Tájékoztató | 1 |

**Csomópont jelentés állapotesemények**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Egy új csomópont jelentés érhető el | HM | Tájékoztató | 1 |
| 54432 | NodeHealthReportExpired | Meglévő csomópont egészségügyi jelentés érvényessége lejárt | HM | Tájékoztató | 1 |

**Alkalmazásesemények egészségügyi jelentés**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | Egy alkalmazás új jelentés létrehozása. Ez a nem telepített alkalmazásokat. | HM | Tájékoztató | 1 |
| 54426 | DeployedApplicationNewHealthReport | Egy üzembe helyezett alkalmazás új jelentés létrehozása | HM | Tájékoztató | 1 |
| 54427 | DeployedServicePackageNewHealthReport | Egy telepített szolgáltatást új állapotjelentés létrehozása | HM | Tájékoztató | 1 |
| 54434 | ApplicationHealthReportExpired | Meglévő alkalmazás egészségügyi jelentés érvényessége lejárt | HM | Tájékoztató | 1 |
| 54435 | DeployedApplicationHealthReportExpired | Egy meglévő üzembe helyezett alkalmazás egészségügyi jelentés érvényessége lejárt | HM | Tájékoztató | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Egy meglévő üzemelő szolgáltatás egészségügyi jelentés érvényessége lejárt | HM | Tájékoztató | 1 |

**A Szolgáltatásállapot-események jelentés**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Service health új jelentés létrehozása | HM | Tájékoztató | 1 |
| 54433 | ServiceHealthReportExpired | Service health meglévő jelentés érvényessége lejárt | HM | Tájékoztató | 1 |

**Partíció jelentés állapotesemények**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Új partíció egészségügyi jelentés létrehozása | HM | Tájékoztató | 1 |
| 54431 | PartitionHealthReportExpired | Meglévő partíció egészségügyi jelentés érvényessége lejárt | HM | Tájékoztató | 1 |

**Replika jelentés állapotesemények**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Egy állapot-nyilvántartó replika egészségügyi jelentés létrehozása | HM | Tájékoztató | 1 |
| 54430 | StatelessInstanceNewHealthReport | Állapot nélküli példány állapotának új jelentés létrehozása | HM | Tájékoztató | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Egy meglévő állapot-nyilvántartó replika egészségügyi jelentés érvényessége lejárt | HM | Tájékoztató | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Egy meglévő állapotmentes példány egészségügyi jelentés érvényessége lejárt | HM | Tájékoztató | 1 |

## <a name="chaos-testing-events"></a>A Chaos tesztelési események 

**A Chaos munkamenet eseményei**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | A Chaos tesztelése a munkamenet megkezdése | Testability | Tájékoztató | 1 |
| 50023 | ChaosStopped | A Chaos tesztelési munkamenetet leállt | Testability | Tájékoztató | 1 |

**A Chaos csomópont események**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Egy csomópont újraindítását a Chaos tesztelés munkamenet részeként van ütemezve. | Testability | Tájékoztató | 1 |
| 50087 | ChaosNodeRestartCompleted | Csomópont újraindítása a Chaos tesztelés munkamenet részeként befejeződött | Testability | Tájékoztató | 1 |

**A Chaos alkalmazásesemények**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | Kód csomag újraindításra van ütemezve egy káosz munkamenet tesztelése során | Testability | Tájékoztató | 1 |
| 50101 | ChaosCodePackageRestartCompleted | Kód csomag újraindítás befejeződött, a Chaos munkamenet tesztelése során | Testability | Tájékoztató | 1 |

**A Chaos partíció események**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | Elsődleges partíció van ütemezve egy tesztelési munkamenetet káosz részeként áthelyezése | Testability | Tájékoztató | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Egy másodlagos partíció van ütemezve egy tesztelési munkamenetet káosz részeként áthelyezése | Testability | Tájékoztató | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | Az elsődleges partíció áthelyezési mélyebb elemzésre érhető el | Testability | Tájékoztató | 1 |

**A Chaos replika események**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | Replika újraindításra van ütemezve egy tesztelési munkamenetet káosz részeként | Testability | Tájékoztató | 1 |
| 50051 | ChaosReplicaRemovalScheduled | Egy replika eltávolítása a rendszer ütemezte a Chaos tesztelés munkamenet részeként | Testability | Tájékoztató | 1 |
| 50093 | ChaosReplicaRemovalCompleted | A Chaos tesztelés munkamenet részeként befejeződött egy replika eltávolítása | Testability | Tájékoztató | 1 |

## <a name="other-events"></a>Egyéb események

**Korrelációs események**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | Korreláció detacted lett | Testability | Tájékoztató | 1 |

## <a name="events-prior-to-version-62"></a>6.2 verziónál régebbi események

A következő események 6.2 verzió előtt a Service Fabric által biztosított átfogó listája.

| EventId | Name (Név) | Forrás (feladat) | Szint |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Tájékoztató |
| 25621 | NodeOpenedSuccess | FabricNode | Tájékoztató |
| 25622 | NodeOpenedFailed | FabricNode | Tájékoztató |
| 25623 | NodeClosing | FabricNode | Tájékoztató |
| 25624 | NodeClosed | FabricNode | Tájékoztató |
| 25625 | NodeAborting | FabricNode | Tájékoztató |
| 25626 | NodeAborted | FabricNode | Tájékoztató |
| 29627 | ClusterUpgradeStart | CM | Tájékoztató |
| 29628 | ClusterUpgradeComplete | CM | Tájékoztató |
| 29629 | ClusterUpgradeRollback | CM | Tájékoztató |
| 29630 | ClusterUpgradeRollbackComplete | CM | Tájékoztató |
| 29631 | ClusterUpgradeDomainComplete | CM | Tájékoztató |
| 23074 | ContainerActivated | Hosting | Tájékoztató |
| 23075 | ContainerDeactivated | Hosting | Tájékoztató |
| 29620 | ApplicationCreated | CM | Tájékoztató |
| 29621 | ApplicationUpgradeStart | CM | Tájékoztató |
| 29622 | ApplicationUpgradeComplete | CM | Tájékoztató |
| 29623 | ApplicationUpgradeRollback | CM | Tájékoztató |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Tájékoztató |
| 29625 | ApplicationDeleted | CM | Tájékoztató |
| 29626 | ApplicationUpgradeDomainComplete | CM | Tájékoztató |
| 18566 | ServiceCreated | AZ FM | Tájékoztató |
| 18567 | ServiceDeleted | AZ FM | Tájékoztató |

## <a name="next-steps"></a>További lépések

* További tudnivalók a teljes [események létrehozása a platform szintjén](service-fabric-diagnostics-event-generation-infra.md) a Service Fabricben
* Módosítása a [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) konfigurációját, és további naplók összegyűjtése
* [Az Application Insights beállítása](service-fabric-diagnostics-event-analysis-appinsights.md) a műveleti naplók channel megtekintéséhez
