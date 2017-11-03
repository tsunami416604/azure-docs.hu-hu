---
title: "A Service Fabric-fürt biztonsági: ügyfél szerepkörök |} Microsoft Docs"
description: "Ez a cikk ismerteti a két ügyfél szerepkörök és a szerepkörök számára biztosított engedélyeit."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: coreysa
editor: 
ms.assetid: 7bc808d9-3609-46a1-ac12-b4f53bff98dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 85935e60bba4b27972282700e2e9c9a22b403bdb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>A Service Fabric ügyfelek szerepköralapú hozzáférés-vezérlés
Azure Service Fabric két különböző hozzáférést vezérlő típusokat támogatja az ügyfelek csatlakoznak a Service Fabric-fürt: rendszergazdai és felhasználói. Hozzáférés-vezérlés lehetővé teszi, hogy a fürt rendszergazdája a különböző csoportok számára, így a fürt biztonságosabb bizonyos fürtműveletekben való hozzáférés korlátozásához.  

**A rendszergazdák** (beleértve az olvasási/írási képességek) felügyeleti képességek teljes hozzáféréssel rendelkeznek. Alapértelmezés szerint **felhasználók** csak rendelkezik olvasási hozzáférés kezelési képességek (például lekérdezési lehetőségek), és oldja meg az alkalmazások és szolgáltatások képességét.

A két ügyfél szerepkörök (a rendszergazda és az ügyfél) a fürt létrehozása idején adja meg az egyes különálló tanúsítványok megadásával. Lásd: [Service Fabric-fürt biztonsági](service-fabric-cluster-security.md) biztonságos Service Fabric-fürt beállításával kapcsolatos részleteket.

## <a name="default-access-control-settings"></a>Alapértelmezett hozzáférés-vezérlési beállításokkal
A rendszergazdai hozzáférés-vezérlő típusa minden FabricClient API teljes hozzáféréssel rendelkezik. Minden olvasási és írási végrehajtható, a Service Fabric-fürt, beleértve a következő műveleteket:

### <a name="application-and-service-operations"></a>Alkalmazás és a szolgáltatási műveletek
* **CreateService**: a szolgáltatás létrehozása                             
* **CreateServiceFromTemplate**: a szolgáltatás létrehozása sablonból                             
* **UpdateService**: frissítheti a szolgáltatást                             
* **DeleteService**: szolgáltatás törlése                             
* **ProvisionApplicationType**: alkalmazás típusa kiépítése                             
* **CreateApplication**: alkalmazás létrehozása                               
* **Deleteapplication függvényhez**: alkalmazás törlése                             
* **UpgradeApplication**: indítása és alkalmazásfrissítések megszakítása                             
* **UnprovisionApplicationType**: alkalmazás típus leépítése                             
* **MoveNextUpgradeDomain**: explicit frissítési tartománnyal alkalmazásfrissítések folytatása                             
* **ReportUpgradeHealth**: az aktuális frissítés állapota az alkalmazásfrissítések folytatása                             
* **ReportHealth**: reporting állapota                             
* **PredeployPackageToNode**: telepítés előtti API                            
* **CodePackageControl**: kód csomagok újraindítása                             
* **RecoverPartition**: egy partíció helyreállítása                             
* **RecoverPartitions**: partíciók helyreállítása                             
* **RecoverServicePartitions**: szolgáltatáspartíciók helyreállítása                             
* **RecoverSystemPartitions**: rendszer szolgáltatáspartíciók helyreállítása                             

### <a name="cluster-operations"></a>Fürt működését
* **ProvisionFabric**: MSI-fájl és/vagy a fürt manifest kiépítése                             
* **UpgradeFabric**: fürt frissítések indítása                             
* **UnprovisionFabric**: MSI-fájl és/vagy a fürt manifest kiépítés megszüntetésére                         
* **MoveNextFabricUpgradeDomain**: explicit frissítési tartománnyal fürt frissítések folytatása                             
* **ReportFabricUpgradeHealth**: fürt frissítések az aktuális frissítési folyamat folytatása                             
* **StartInfrastructureTask**: infrastruktúra feladatok indítása                             
* **FinishInfrastructureTask**: infrastruktúrakezelési feladatok befejezése                             
* **InvokeInfrastructureCommand**: infrastruktúra feladat parancsok                              
* **ActivateNode**: egy csomópont aktiválása                             
* **DeactivateNode**: a csomópont inaktiválása                             
* **DeactivateNodesBatch**: több csomópont inaktiválása                             
* **RemoveNodeDeactivations**: több csomóponton visszaállítási inaktiválása                             
* **GetNodeDeactivationStatus**: az inaktiválást állapotának ellenőrzése                             
* **NodeStateRemoved**: csomópont állapota reporting eltávolítása                             
* **ReportFault**: reporting hiba                             
* **FileContent**: image store ügyfél fájlátvitel (külső fürthöz)                             
* **FileDownload**: image store ügyfél fájl letöltése kezdeményezés (külső fürthöz)                             
* **InternalList**: image store ügyfél fájl list művelet (belső)                             
* **Törlés**: image store ügyfél delete művelet                              
* **Töltse fel**: image store ügyfél feltöltési művelete                             
* **NodeControl**: indítása, leállítása és újraindítása csomópontok                             
* **MoveReplicaControl**: replikák áthelyezése egyik csomópontról egy másikra                             

### <a name="miscellaneous-operations"></a>Különböző műveletekhez
* **Ping**: ügyfél pingelésre.                             
* **Lekérdezés**: engedélyezett összes lekérdezés
* **NameExists**: elnevezési URI meglétének ellenőrzése                             

A felhasználói hozzáférés-vezérlő típusa van, alapértelmezés szerint csak a következő műveleteket: 

* **EnumerateSubnames**: elnevezési URI felsorolása                             
* **EnumerateProperties**: elnevezési tulajdonság felsorolása                             
* **PropertyReadBatch**: elnevezési tulajdonság olvasási műveletek                             
* **GetServiceDescription**: szolgáltatás hosszú-lekérdezési értesítéseket és olvasási szolgáltatás leírása                             
* **ResolveService**: csatlakoztatásra-alapú szolgáltatás feloldása                             
* **ResolveNameOwner**: feloldó elnevezési URI tulajdonosa                             
* **ResolvePartition**: rendszerszolgáltatások feloldása                             
* **ServiceNotifications**: eseményalapú szolgáltatáshoz értesítést                             
* **GetUpgradeStatus**: lekérdezési alkalmazás frissítési állapot                             
* **GetFabricUpgradeStatus**: a fürt frissítési állapotának lekérdezése                             
* **InvokeInfrastructureQuery**: infrastruktúrakezelési feladatok lekérdezése                             
* **Lista**: image store ügyfél fájl list művelet                             
* **ResetPartitionLoad**: betöltése a failover Unit visszaállítása                             
* **ToggleVerboseServicePlacementHealthReporting**: való átváltással részletes elhelyezési állapotát szolgáltatásjelentési                             

A rendszergazdai hozzáférés-vezérlés is hozzáfér a fenti műveletekre.

## <a name="changing-default-settings-for-client-roles"></a>Ügyfél-szerepkörök alapértelmezett beállításainak módosítása
A fürt jegyzékfájl biztosíthat felügyeleti képességek az ügyfél szükség esetén. Címen bármikor módosíthatja az alapértelmezett érték a **Hálóbeállításokat** során lehetőséget [fürtlétrehozás](service-fabric-cluster-creation-via-portal.md), és az előző beállítások megadása a **neve**, **admin**, **felhasználói**, és **érték** mezők.

## <a name="next-steps"></a>Következő lépések
[A Service Fabric-fürt biztonsági](service-fabric-cluster-security.md)

[A Service Fabric-fürt létrehozása](service-fabric-cluster-creation-via-portal.md)

