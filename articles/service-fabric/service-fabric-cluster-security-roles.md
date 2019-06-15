---
title: 'A Service Fabric-Fürtbiztonság: ügyfélszerepkörök |} A Microsoft Docs'
description: Ez a cikk ismerteti a két ügyfél szerepköröket és a szerepkörök számára biztosított engedélyeket.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: coreysa
editor: ''
ms.assetid: 7bc808d9-3609-46a1-ac12-b4f53bff98dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: ed000dc4be1ae45382d688d4a596ec745c69d0bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60711158"
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>Szerepköralapú hozzáférés-vezérlés a Service Fabric-ügyfelek
Az Azure Service Fabric támogatja a Service Fabric-fürt csatlakozó ügyfelek különböző hozzáférés-vezérlő kétféle: rendszergazdai és felhasználói. Hozzáférés-vezérlés lehetővé teszi, hogy a fürt rendszergazdája a különböző csoportok számára, így több biztonságos a fürt egyes fürtműveletek való hozzáférés korlátozásához.  

**A rendszergazdák** (beleértve az olvasási/írási képességeket) felügyeleti képességek teljes hozzáféréssel rendelkezik. Alapértelmezés szerint **felhasználók** csak rendelkezik olvasási hozzáférés a felügyeleti funkciók (például a lekérdezési képességek), valamint az alkalmazások és szolgáltatások megoldásához.

A két ügyfél szerepkörök (a rendszergazda és az ügyfél) a fürt létrehozása idején adjon meg egyedi tanúsítványt azáltal, hogy az egyes. Lásd: [Service Fabric-Fürtbiztonság](service-fabric-cluster-security.md) biztonságos Service Fabric-fürt beállítása részleteiért.

## <a name="default-access-control-settings"></a>Alapértelmezett hozzáférés-vezérlési beállításokkal
A rendszergazdai hozzáférés-vezérlő típusa a FabricClient API-teljes hozzáféréssel rendelkezik. Minden olvasási és írási végrehajtható, a Service Fabric-fürthöz, többek között a következő műveleteket:

### <a name="application-and-service-operations"></a>Alkalmazás- és szolgáltatási műveletek
* **CreateService**: szolgáltatás létrehozása                             
* **CreateServiceFromTemplate**: szolgáltatás létrehozása sablonból                             
* **UpdateService**: szolgáltatási hírek                             
* **DeleteService**: szolgáltatás törlése                             
* **ProvisionApplicationType**: alkalmazások típus üzembe helyezése                             
* **CreateApplication**: alkalmazás létrehozása                               
* **Deleteapplication függvényhez**: alkalmazás törlése                             
* **UpgradeApplication**: indítása vagy alkalmazásfrissítések megszakítása                             
* **UnprovisionApplicationType**: alkalmazás típus leépítése                             
* **MoveNextUpgradeDomain**: explicit frissítési tartomány az alkalmazásfrissítések folytatása                             
* **ReportUpgradeHealth**: a jelenlegi frissítési folyamat állapotát az alkalmazásfrissítések folytatása                             
* **ReportHealth**: állapotfigyelő jelentési                             
* **PredeployPackageToNode**: üzembe helyezés előtti API                            
* **CodePackageControl**: kódcsomagok újraindítása                             
* **RecoverPartition**: egy partíció helyreállítása                             
* **RecoverPartitions**: partíciók helyreállítása                             
* **RecoverServicePartitions**: szolgáltatás partícióinak helyreállítása                             
* **RecoverSystemPartitions**: rendszer szolgáltatáspartíciók helyreállítása                             

### <a name="cluster-operations"></a>Fürtműveletek
* **ProvisionFabric**: MSI és/vagy a fürt manifest kiépítése                             
* **UpgradeFabric**: fürtfrissítések indítása                             
* **UnprovisionFabric**: MSI és/vagy a fürt telepítésének visszavonását manifest                         
* **MoveNextFabricUpgradeDomain**: explicit frissítési tartomány a fürtfrissítések folytatása                             
* **ReportFabricUpgradeHealth**: a jelenlegi frissítési folyamat állapotát a fürtfrissítések folytatása                             
* **StartInfrastructureTask**: infrastruktúra feladatok indítása                             
* **FinishInfrastructureTask**: feladatok befejezése                             
* **InvokeInfrastructureCommand**: infrastruktúra feladat felügyeleti parancsok                              
* **ActivateNode**: egy csomópont aktiválása                             
* **DeactivateNode**: egy csomópont inaktiválása                             
* **DeactivateNodesBatch**: több csomópont inaktiválása                             
* **RemoveNodeDeactivations**: több csomóponton visszaállítása inaktiválása                             
* **GetNodeDeactivationStatus**: inaktiválási állapot ellenőrzése                             
* **NodeStateRemoved**: jelentéskészítő csomópont állapota eltávolítva                             
* **ReportFault**: tartalék reporting                             
* **FileContent**: image store ügyfél fájlátvitel (fürt külső)                             
* **FileDownload**: image store ügyfél fájl letöltése kezdeményezés (fürt külső)                             
* **InternalList**: image store ügyfél file list művelet (belső)                             
* **Törlés**: image store ügyfél-törlési művelet                              
* **Töltse fel**: image store ügyfél feltöltési művelet                             
* **NodeControl**: indítása, leállítása és újraindítása a csomópontok                             
* **MoveReplicaControl**: replikák áthelyezése egyik csomópontról a másikra                             

### <a name="miscellaneous-operations"></a>Egyéb műveletek
* **Ping**: ügyfél pingelésre.                             
* **Lekérdezés**: az összes lekérdezés engedélyezett
* **NameExists**: elnevezési URI meglétének ellenőrzése                             

A felhasználói hozzáférés-vezérlő típusa van, csak a következő műveleteket alapértelmezés szerint: 

* **EnumerateSubnames**: elnevezési URI-enumerálás                             
* **EnumerateProperties**: elnevezési számbavétele                             
* **PropertyReadBatch**: vlastnost elnevezési olvasási műveletek                             
* **GetServiceDescription**: hosszú-lekérdezési szolgáltatási értesítések és olvasása szolgáltatás leírása                             
* **ResolveService**: panasz-alapú megoldás                             
* **ResolveNameOwner**: feloldó elnevezési URI tulajdonosa                             
* **ResolvePartition**: rendszerszolgáltatások feloldása                             
* **ServiceNotifications**: eseményalapú szolgáltatási értesítések                             
* **GetUpgradeStatus**: lekérdezési alkalmazás frissítésének állapota                             
* **GetFabricUpgradeStatus**: a fürt frissítési állapotának lekérdezése                             
* **InvokeInfrastructureQuery**: feladatok lekérdezése                             
* **Lista**: image store ügyfél file list művelet                             
* **ResetPartitionLoad**: alaphelyzetbe állítása a terhelés egy feladatátvételi egység                             
* **ToggleVerboseServicePlacementHealthReporting**: részletes szolgáltatás elhelyezési állapotfigyelő jelentési vizualizációtól                             

A rendszergazdai hozzáférés-vezérlés is hozzáfér, a fenti műveletek.

## <a name="changing-default-settings-for-client-roles"></a>Ügyfél-szerepkörök alapértelmezett beállításainak módosítása
A fürt jegyzékfájlban megadhat rendszergazdai lehetőségek az ügyfélnek szükség esetén. Módosíthatja az alapértelmezett értékeket fogja a **Hálóbeállítások** során lehetőség [fürtlétrehozás](service-fabric-cluster-creation-via-portal.md), és az a fenti beállítások megadása a **neve**,  **rendszergazdai**, **felhasználói**, és **érték** mezőket.

## <a name="next-steps"></a>További lépések
[A Service Fabric-Fürtbiztonság](service-fabric-cluster-security.md)

[A Service Fabric-fürt létrehozása](service-fabric-cluster-creation-via-portal.md)

