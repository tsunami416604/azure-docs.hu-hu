---
title: Azure Service Fabric eseménylista
description: Az Azure Service Fabric által a fürtök figyeléséhez biztosított események átfogó listája.
author: srrengar
ms.topic: reference
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: e69b407bc7d58a83616daa44272ec008ccff9fad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258537"
---
# <a name="list-of-service-fabric-events"></a>A Service Fabric-események listája 

A Service Fabric fürtesemények elsődleges készletét teszi elérhetővé, hogy tájékoztassa a fürt [állapotát Service Fabric-eseményekként.](service-fabric-diagnostics-events.md) Ezek a Service Fabric által a csomópontokon végrehajtott műveleteken, valamint a fürt tulajdonosa/üzemeltetője által hozott fürt- vagy felügyeleti döntéseken alapulnak. Ezek az események számos módon érhetők el, például konfigurálva az [Azure Monitor-naplókat a fürttel,](service-fabric-diagnostics-oms-setup.md)vagy lekérdezve az [EventStore-t.](service-fabric-diagnostics-eventstore.md) Windows-gépeken ezek az események bekerülnek az Eseménynaplóba, így láthatja a Service Fabric-eseményeket az Eseménynaplóban. 

Íme néhány jellemzője ezeknek az eseményeknek
* Minden esemény a fürt egy adott entitásához van kötve, például alkalmazás, szolgáltatás, csomópont, replika.
* Minden esemény közös mezőket tartalmaz: EventInstanceId, EventName és Category.
* Minden esemény olyan mezőket tartalmaz, amelyek az eseményt ahhoz az entitáshoz kötik, amelyhez társítva van. Például az ApplicationCreated esemény volna mezőket, amelyek azonosítják a létrehozott alkalmazás nevét.
* Az események úgy vannak felépítve, hogy a további elemzéshez számos eszközben felhasználhatók legyenek. Emellett az esemény releváns részletei külön tulajdonságokként vannak definiálva, nem pedig egy hosszú karakterlánc. 
* Az eseményeket a Service Fabric különböző alrendszerei írják a Forrás(Feladat) alatt. Ezekről az alrendszerekről további információ található a [Service Fabric architektúrájában](service-fabric-architecture.md) és a [Service Fabric műszaki áttekintésében.](service-fabric-technical-overview.md)

Az alábbi, entitás szerint szervezett Service Fabric-események listája látható.

## <a name="cluster-events"></a>Fürtesemények

**Fürtfrissítési események**

További részletek a fürtfrissítésekről [itt](service-fabric-cluster-upgrade-windows-server.md)találhatók.

| Napszállta | Név | Kategória | Leírás |Forrás (tevékenység) | Szint | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | Fürtfrissítés elindult | Frissítés | Megkezdődött a fürtfrissítése | CM | Tájékoztató |
| 29628 | ClusterUpgrade Kész | Frissítés | A fürtfrissítése befejeződött | CM | Tájékoztató | 
| 29629 | ClusterUpgradeRollbackbackStarted Started | Frissítés | A fürtfrissítése megkezdődött a visszaállításhoz  | CM | Figyelmeztetés | 
| 29630 | ClusterUpgradeRollbackCompleted | Frissítés | A fürtfrissítés befejeződött a visszaállítássorán | CM | Figyelmeztetés | 
| 29631 | ClusterUpgradeDomain Kész | Frissítés | A frissítési tartomány frissítése a fürt frissítése során befejeződött | CM | Tájékoztató | 

## <a name="node-events"></a>Csomópontesemények

**Csomópont életciklus-eseményei** 

| Napszállta | Név | Kategória | Leírás |Forrás (tevékenység) | Szint |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivate Kész | Állapotátmenet | Egy csomópont inaktiválása befejeződött | FM | Tájékoztató | 
| 18603 | NodeUp között | Állapotátmenet | A fürt csomópontot észlelt | FM | Tájékoztató | 
| 18604 | NodeDown között | Állapotátmenet | A fürt olyan csomópontot észlelt, amely leállt. A csomópont újraindítása során egy NodeDown-esemény, majd egy NodeUp-esemény jelenik meg |  FM | Hiba | 
| 18605 | NodeAddedToCluster | Állapotátmenet |  Új csomópont lett hozzáadva a fürthöz, és a Service Fabric alkalmazásokat telepíthet erre a csomópontra | FM | Tájékoztató | 
| 18606 | NodeRemovedFromCluster | Állapotátmenet |  Egy csomópont ot eltávolítottak a fürtből. A Service Fabric már nem telepít alkalmazásokat erre a csomópontra | FM | Tájékoztató | 
| 18607 | NodeDeactivateStarted | Állapotátmenet |  Egy csomópont inaktiválása megkezdődött | FM | Tájékoztató | 
| 25621 | NodeOpenSucceeded | Állapotátmenet |  Egy csomópont sikeresen elindult | FabricNode között | Tájékoztató | 
| 25622 | NodeOpenFailed | Állapotátmenet |  Egy csomópont nem tudott elindulni és csatlakozni a gyűrűhöz | FabricNode között | Hiba | 
| 25624 | NodeClosed (NodeClosed) | Állapotátmenet |  Egy csomópont leállítása sikeresen megtörtént | FabricNode között | Tájékoztató | 
| 25626 | NodeAmegszakadt | Állapotátmenet |  Egy csomópont nem kecsesen leállt | FabricNode között | Hiba | 

## <a name="application-events"></a>Alkalmazásesemények

**Alkalmazás életciklus-eseményei**

| Napszállta | Név | Kategória | Leírás |Forrás (tevékenység) | Szint | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | Alkalmazás létrehozva | Életciklus | Új alkalmazás jött létre | CM | Tájékoztató | 
| 29625 | Alkalmazástörölve | Életciklus | Egy meglévő alkalmazás törölve | CM | Tájékoztató | 
| 23083 | ApplicationProcessLekilépett | Életciklus | Egy alkalmazáson belüli folyamat kilépett | Hosting | Tájékoztató | 

**Alkalmazásfrissítési események**

További részletek az alkalmazás [frissítéseket](service-fabric-application-upgrade.md)itt található .

| Napszállta | Név | Kategória | Leírás |Forrás (tevékenység) | Szint | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | Frissítés | Egy alkalmazásfrissítés elindult | CM | Tájékoztató | 
| 29622 | ApplicationUpgrade Completed | Frissítés | Egy alkalmazásfrissítés befejeződött | CM | Tájékoztató | 
| 29623 | ApplicationUpgradeRollbackStartedStarted | Frissítés | Az alkalmazásfrissítése megkezdődött a visszaállításhoz |CM | Figyelmeztetés | 
| 29624 | ApplicationUpgradeRollbackCompleted | Frissítés | Az alkalmazásfrissítés befejeződött a visszaállítássorán | CM | Figyelmeztetés | 
| 29626 | ApplicationUpgradeDomain Completed | Frissítés | Egy frissítési tartomány frissítése az alkalmazás frissítése során befejeződött | CM | Tájékoztató | 

## <a name="service-events"></a>Szolgáltatási események

**Szolgáltatási életciklus-események**

| Napszállta | Név | Kategória | Leírás |Forrás (tevékenység) | Szint | 
| --- | --- | ---| --- | --- | --- |
| 18657 | Létrehozva a szolgáltatás | Életciklus | Új szolgáltatás jött létre | FM | Tájékoztató | 
| 18658 | Szolgáltatástörölve | Életciklus | Egy meglévő szolgáltatás törölve | FM | Tájékoztató | 

## <a name="partition-events"></a>Partíciós események

**Partícióáthelyezési események**

| Napszállta | Név | Kategória | Leírás |Forrás (tevékenység) | Szint | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartícióÚjrakonfigurálva | Életciklus | A partíció újrakonfigurálása befejeződött | RA | Tájékoztató | 

## <a name="replica-events"></a>Replikaesemények

**A replika életciklus-eseményei**

| Napszállta | Név | Kategória | Leírás |Forrás (tevékenység) | Szint |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOpened | Életciklus | Megnyitott a Megbízható szótár | DistributedDictionary (Elosztott szótár) | Tájékoztató |
| 61702 | ReliableDictionaryClosed | Életciklus | A megbízható szótár bezárt | DistributedDictionary (Elosztott szótár) | Tájékoztató |
| 61703 | ReliableDictionaryCheckpointRecovered | Életciklus | A megbízható szótár visszaszerezte ellenőrzőpontját | DistributedDictionary (Elosztott szótár) | Tájékoztató |
| 61704 | ReliableDictionaryCheckpointFilesSent | Életciklus | A Replika megbízható szótár ellenőrzőpont-fájljait küldte el | DistributedDictionary (Elosztott szótár) | Tájékoztató |
| 61705 | ReliableDictionaryCheckpointFilesBeérkezett | Életciklus | A Replika megbízható szótárellenőrzőpont-fájljait kapta | DistributedDictionary (Elosztott szótár) | Tájékoztató |
| 61963 | ReliableQueueOpened | Életciklus | A megbízható várólista megnyílt | Elosztott várólista | Tájékoztató |
| 61964 | ReliableQueueZárva | Életciklus | A megbízható várólista bezárt | Elosztott várólista | Tájékoztató |
| 61965 | ReliableQueueCheckpoint Újranyert | Életciklus | A megbízható várólista helyreállt az ellenőrzőpontja | Elosztott várólista | Tájékoztató |
| 61966 | ReliableQueueCheckpointFilesSent | Életciklus | A Replika megbízható várólista ellenőrzőpont-fájljait küldte el | Elosztott várólista | Tájékoztató |
| 63647 | ReliableQueueCheckpointFilesbeérkezett | Életciklus | A Replika megbízható várólista ellenőrzőpont-fájljait kapta meg | Elosztott várólista | Tájékoztató |
| 63648 | ReliableConcurrentQueueOpened | Életciklus | Megbízható egyidejű várólista megnyílt | ReliableConcurrentQueue | Tájékoztató |
| 63649 | ReliableConcurrentQueueLezárt | Életciklus | Megbízható egyidejű várólista bezárult | ReliableConcurrentQueue | Tájékoztató |
| 63650 | ReliableConcurrentQueueCheckpointÚjranyert | Életciklus | A megbízható egyidejű várólista helyreállt az ellenőrzőpont | ReliableConcurrentQueue | Tájékoztató |
| 61687 | TStoreError (Tárhiba) | Hiba | A megbízható gyűjtés váratlan hibát kapott | TÁruház | Hiba |
| 63831 | PrimaryFullCopyinitiated | Életciklus | Az elsődleges kópia teljes másolatot kezdeményezett | TReplicator (TReplicator) | Tájékoztató |
| 63832 | PrimaryPartialCopyinitiated | Életciklus | Az elsődleges kópia részleges másolatot kezdeményezett | TReplicator (TReplicator) | Tájékoztató |
| 16831 | BuildIdleReplicaStarted | Életciklus | Az elsődleges replika megkezdte az alapjárati kópia létrehozásának megkezdését | Replikáció | Tájékoztató |
| 16832 | BuildIdleReplicaCompleted | Életciklus | Az elsődleges replika befejezte az épület alapjárati replikájának befejezését | Replikáció | Tájékoztató |
| 16833 | BuildIdleReplicaFailed | Életciklus | Az elsődleges replika nem sikerült az épület alapjárati replikájában | Replikáció | Figyelmeztetés |
| 16834 | PrimaryReplicationQueueFull | Állapot | Az elsődleges replika replikációs várólistája megtelt | Replikáció | Figyelmeztetés |
| 16835 | PrimaryReplicationQueue – figyelmeztetés | Állapot | Az elsődleges replika replikációs várólistája közel van a teljes | Replikáció | Figyelmeztetés |
| 16836 | PrimaryReplicationQueueWarningTeljesdesztika | Állapot | Az elsődleges replika replikációs várólistája rendben van | Replikáció | Tájékoztató |
| 16837 | SecondaryReplicationQueueFull | Állapot | A másodlagos replika replikációs várólistája megtelt | Replikáció | Figyelmeztetés |
| 16838 | SecondaryReplicationQueueFigyelmeztetés | Állapot | A másodlagos replika replikációs várólistája közel van a teljes | Replikáció | Figyelmeztetés |
| 16839 | SecondaryReplicationQueueWarningAmab-eltagért | Állapot | A másodlagos replika replikációs várólistája rendben van | Replikáció | Tájékoztató |
| 16840 | PrimaryFaultedSlowMásodlagos | Állapot | Az elsődleges replika hibás egy lassú másodlagos replika | Replikáció | Figyelmeztetés |
| 16841 | Replikátorhibás | Állapot | Hibás a replika | Replikáció | Figyelmeztetés |

## <a name="container-events"></a>Tárolóesemények

**Tároló életciklus-eseményei** 

| Napszállta | Név | Leírás |Forrás (tevékenység) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 23074 | Konténeraktiválva | Egy tároló elindult | Hosting | Tájékoztató | 1 |
| 23075 | ContainerDeactivated | Egy tároló leállt | Hosting | Tájékoztató | 1 |
| 23082 | ContainerKilépett | Kilépett egy tároló - Ellenőrizze a UnexpectedTermination jelzőt | Hosting | Tájékoztató | 1 |

## <a name="health-reports"></a>Egészségügyi jelentések

A [Service Fabric egészségügyi modell](service-fabric-health-introduction.md) gazdag, rugalmas és bővíthető állapotértékelést és -jelentést biztosít. A Service Fabric 6.2-es verziójának indításakor az állapotadatok platformeseményekként vannak megírva, hogy az állapotelőzményeit megadják. Az állapotesemények mennyiségének alacsony a csökkentése érdekében csak a következőt írjuk Service Fabric-eseményekként:

* Minden `Error` `Warning` vagy egészségügyi jelentés
* `Ok`egészségügyi jelentések az átmenetek során
* Amikor `Error` egy `Warning` vagy egészségügyi esemény lejár. Ezzel meghatározható, hogy egy entitás mennyi ideig volt egészségtelen.

**Fürtállapot-jelentés eseményei**

| Napszállta | Név | Leírás |Forrás (tevékenység) | Szint | Verzió |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Új fürtállapot-jelentés érhető el | Hm | Tájékoztató | 1 |
| 54437 | ClusterHealthReportExpired (Fürtállapotjelentés lejárta) | Lejárt egy meglévő fürtállapot-jelentés | Hm | Tájékoztató | 1 |

**Csomópont állapotjelentési eseményei**

| Napszállta | Név | Leírás |Forrás (tevékenység) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Elérhető egy új csomópontállapot-jelentés | Hm | Tájékoztató | 1 |
| 54432 | NodeHealthReportExpired | Lejárt egy meglévő csomópontállapot-jelentés | Hm | Tájékoztató | 1 |

**Alkalmazásállapot-jelentés eseményei**

| Napszállta | Név | Leírás |Forrás (tevékenység) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 54425 | AlkalmazásÚj-egészségügyi jelentés | Új alkalmazásállapot-jelentés készült. Ez az el nem helyezett alkalmazások. | Hm | Tájékoztató | 1 |
| 54426 | DeployedApplicationNewHealthReport | Új telepített alkalmazásállapot-jelentés készült | Hm | Tájékoztató | 1 |
| 54427 | DeployedServicePackageNewHealthReport | Új üzembe helyezett szolgáltatásállapot-jelentés készült | Hm | Tájékoztató | 1 |
| 54434 | ApplicationHealthReportExpired | Lejárt egy meglévő alkalmazásállapot-jelentés | Hm | Tájékoztató | 1 |
| 54435 | DeployedApplicationHealthReportExpired | Lejárt egy meglévő telepített alkalmazásállapot-jelentés | Hm | Tájékoztató | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Lejárt egy meglévő üzembe helyezett szolgáltatásállapot-jelentés | Hm | Tájékoztató | 1 |

**Szolgáltatásállapot-jelentés eseményei**

| Napszállta | Név | Leírás |Forrás (tevékenység) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Új szolgáltatásállapot-jelentés készült | Hm | Tájékoztató | 1 |
| 54433 | ServiceHealthReportExpired | Lejárt egy meglévő szolgáltatásállapot-jelentés | Hm | Tájékoztató | 1 |

**Partíció állapotjelentési eseményei**

| Napszállta | Név | Leírás |Forrás (tevékenység) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Új partícióállapot-jelentés jött létre | Hm | Tájékoztató | 1 |
| 54431 | PartitionHealthReportExpired | Lejárt egy meglévő partícióállapot-jelentés | Hm | Tájékoztató | 1 |

**Állapotjelentés-események replika**

| Napszállta | Név | Leírás |Forrás (tevékenység) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Létrehozták az állapotalapú replika állapotjelentését | Hm | Tájékoztató | 1 |
| 54430 | StatelessInstanceNewHealthReport | Új állapotnélküli példány állapotjelentés készült | Hm | Tájékoztató | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Lejárt egy meglévő állapotalapú replika állapotjelentése | Hm | Tájékoztató | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Lejárt egy meglévő állapotnélküli példány állapotjelentése | Hm | Tájékoztató | 1 |

## <a name="chaos-testing-events"></a>Káosz tesztelési események 

**Káosz munkamenet események**

| Napszállta | Név | Leírás |Forrás (tevékenység) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 50021 | Káosz Elindult | Elkezdődött a Chaos tesztelési munkamenete | Tesztelhetőség | Tájékoztató | 1 |
| 50023 | ChaosStopped (Káoszleállítva) | A Chaos tesztelési munkamenetleállt | Tesztelhetőség | Tájékoztató | 1 |

**Káosz csomópontesemények**

| Napszállta | Név | Leírás |Forrás (tevékenység) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Egy csomópont újraindításra van ütemezve a Chaos tesztelési munkamenet részeként | Tesztelhetőség | Tájékoztató | 1 |
| 50087 | ChaosNodeRestart Kész | Egy csomópont újraindítása befejeződött a Chaos tesztelési munkamenet részeként | Tesztelhetőség | Tájékoztató | 1 |

**Káosz alkalmazásesemények**

| Napszállta | Név | Leírás |Forrás (tevékenység) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | A chaos tesztelési munkamenet során a kódcsomag újraindítása ütemezve van | Tesztelhetőség | Tájékoztató | 1 |
| 50101 | ChaosCodePackageRestart kész | A kódcsomag újraindítása befejeződött a Chaos tesztelési munkamenet során | Tesztelhetőség | Tájékoztató | 1 |

**Chaos partícióesemények**

| Napszállta | Név | Leírás |Forrás (tevékenység) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | Egy elsődleges partíció a Chaos tesztelési munkamenet részeként van ütemezve | Tesztelhetőség | Tájékoztató | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Egy másodlagos partíció a Chaos tesztelési munkamenet részeként van ütemezve | Tesztelhetőség | Tájékoztató | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | Az elsődleges partícióáthelyezés mélyebb elemzése elérhető | Tesztelhetőség | Tájékoztató | 1 |

**Káosz replika események**

| Napszállta | Név | Leírás |Forrás (tevékenység) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | A replika újraindítása a Chaos tesztelési munkamenet részeként lett ütemezve | Tesztelhetőség | Tájékoztató | 1 |
| 50051 | ChaosReplicaRemovalÜtemezettd | A replika eltávolítása egy Chaos tesztelési munkamenet részeként lett ütemezve | Tesztelhetőség | Tájékoztató | 1 |
| 50093 | ChaosReplicaRemoval Kész | A replika eltávolítása befejeződött a Chaos tesztelési munkamenet részeként | Tesztelhetőség | Tájékoztató | 1 |

## <a name="other-events"></a>Egyéb események

**Korrelációs események**

| Napszállta | Név | Leírás |Forrás (tevékenység) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 65011 | KorrelációMűködés | Korrelációt észleltak | Tesztelhetőség | Tájékoztató | 1 |

## <a name="events-prior-to-version-62"></a>A 6.2-es verzió előtti események

Itt van egy átfogó lista a Service Fabric által a 6.2-es verzió előtt biztosított eseményekről.

| Napszállta | Név | Forrás (tevékenység) | Szint |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode között | Tájékoztató |
| 25621 | NodeOpenedSuccess | FabricNode között | Tájékoztató |
| 25622 | CsomópontmegnyitásaSikertelenül | FabricNode között | Tájékoztató |
| 25623 | NodeClosing (Csomópontlezárás) | FabricNode között | Tájékoztató |
| 25624 | NodeClosed (NodeClosed) | FabricNode között | Tájékoztató |
| 25625 | NodeAmegszakítás | FabricNode között | Tájékoztató |
| 25626 | NodeAmegszakadt | FabricNode között | Tájékoztató |
| 29627 | ClusterUpgradeStart | CM | Tájékoztató |
| 29628 | Fürtfrissítésteljesítés | CM | Tájékoztató |
| 29629 | ClusterUpgradeRollbackback | CM | Tájékoztató |
| 29630 | ClusterUpgradeRollbackComplete | CM | Tájékoztató |
| 29631 | ClusterUpgradeDomainComplete | CM | Tájékoztató |
| 23074 | Konténeraktiválva | Hosting | Tájékoztató |
| 23075 | ContainerDeactivated | Hosting | Tájékoztató |
| 29620 | Alkalmazás létrehozva | CM | Tájékoztató |
| 29621 | ApplicationUpgradeStart alkalmazás | CM | Tájékoztató |
| 29622 | Alkalmazásfrissítéseteljes | CM | Tájékoztató |
| 29623 | ApplicationUpgradeRollback | CM | Tájékoztató |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Tájékoztató |
| 29625 | Alkalmazástörölve | CM | Tájékoztató |
| 29626 | ApplicationUpgradeDomainComplete | CM | Tájékoztató |
| 18566 | Létrehozva a szolgáltatás | FM | Tájékoztató |
| 18567 | Szolgáltatástörölve | FM | Tájékoztató |

## <a name="next-steps"></a>További lépések

* A Service [Fabric diagnosztikájának](service-fabric-diagnostics-overview.md) áttekintése
* További információ az EventStore in [Service Fabric Eventstore áttekintéséről](service-fabric-diagnostics-eventstore.md)
* Az [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) konfigurációjának módosítása további naplók gyűjtéséhez
* [Az Application Insights beállítása](service-fabric-diagnostics-event-analysis-appinsights.md) az operatív csatornanaplók megtekintéséhez
