---
title: 'A Service Fabric fürtjének biztonsága: ügyfélszerepkörök'
description: Ez a cikk ismerteti a két ügyfélszerepkörök és a szerepkörök nek biztosított engedélyeket.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: abca19e686d39338fcaa2e0b0c8126913135170b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451900"
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>Szerepköralapú hozzáférés-vezérlés Service Fabric-ügyfelek számára
Az Azure Service Fabric két különböző hozzáférés-vezérlési típust támogat a Service Fabric-fürthöz kapcsolódó ügyfelek számára: a rendszergazdát és a felhasználót. A hozzáférés-vezérlés lehetővé teszi a fürtrendszergazdának, hogy korlátozza a hozzáférést bizonyos fürtműveletekhez a felhasználók különböző csoportjai számára, így a fürt biztonságosabbá téve azt.  

**A rendszergazdák** teljes hozzáféréssel rendelkeznek a felügyeleti képességekhez (beleértve az olvasási/írási képességeket is). Alapértelmezés szerint a **felhasználók** csak olvasási hozzáféréssel rendelkeznek a felügyeleti képességekhez (például a lekérdezési képességekhez), és képesek az alkalmazások és szolgáltatások feloldására.

A fürt létrehozásakor a két ügyfélszerepkört (rendszergazda és ügyfél) külön tanúsítványok at adhat meg mindegyikhez. A biztonságos Service Fabric-fürt beállításával kapcsolatos részletekért tekintse meg a [Service Fabric fürtbiztonságát.](service-fabric-cluster-security.md)

## <a name="default-access-control-settings"></a>Alapértelmezett hozzáférés-vezérlési beállítások
A rendszergazdai hozzáférés-vezérlési típus teljes hozzáféréssel rendelkezik az összes FabricClient API-hoz. Bármilyen olvasási és írási műveletet elvégezhet a Service Fabric-fürtön, beleértve a következő műveleteket is:

### <a name="application-and-service-operations"></a>Alkalmazási és szolgáltatási műveletek
* **CreateService**: szolgáltatás létrehozása                             
* **CreateServiceFromTemplate**: szolgáltatás létrehozása sablonból                             
* **UpdateService**: szolgáltatásfrissítések                             
* **DeleteService**: szolgáltatás törlése                             
* **ProvisionApplicationType**: alkalmazástípus-kiépítés                             
* **CreateApplication**: alkalmazás létrehozása                               
* **DeleteApplication**: alkalmazás törlése                             
* **UpgradeApplication**: alkalmazásfrissítések indítása vagy megszakítása                             
* **UnprovisionApplicationType**: alkalmazástípus kiépítés nélkül                             
* **MoveNextUpgradeDomain**: az alkalmazásfrissítések folytatása explicit frissítési tartománnyal                             
* **ReportUpgradeHealth**: az alkalmazásfrissítések folytatása az aktuális frissítési folyamattal                             
* **ReportHealth**: az egészség jelentése                             
* **PredeployPackageToNode**: telepítés előtti API                            
* **CodePackageControl**: kódcsomagok újraindítása                             
* **RecoverPartition**: partíció helyreállítása                             
* **RecoverPartitions**: partíciók helyreállítása                             
* **RecoverServicePartitions**: szolgáltatáspartíciók helyreállítása                             
* **RecoverSystemPartitions**: rendszerszolgáltatás-partíciók helyreállítása                             

### <a name="cluster-operations"></a>Fürtműveletek
* **ProvisionFabric**: MSI és/vagy fürtjegyzék-kiépítés                             
* **UpgradeFabric**: fürtfrissítések indítása                             
* **UnprovisionFabric**: MSI és/vagy fürtjegyzék kiépítés                         
* **MoveNextFabricUpgradeDomain**: fürtfrissítések folytatása explicit frissítési tartománnyal                             
* **ReportFabricUpgradeHealth**: fürtfrissítések folytatása az aktuális frissítési folyamattal                             
* **StartInfrastructureTask**: infrastrukturális feladatok indítása                             
* **FinishInfrastructureTask**: infrastrukturális feladatok befejezése                             
* **InvokeInfrastructureCommand**: infrastruktúrafeladat-kezelési parancsok                              
* **ActivateNode**: csomópont aktiválása                             
* **DeactivateNode**: csomópont inaktiválása                             
* **DeactivateNodesBatch**: több csomópont kikapcsolása                             
* **RemoveNodeDeactivations**: az inaktiválás visszaállítása több csomóponton                             
* **GetNodeDeactivationStatus**: inaktiválási állapot ellenőrzése                             
* **NodeStateRemoved**: a csomópont állapotának jelentése eltávolítva                             
* **ReportFault**: jelentési hiba                             
* **FileContent**: lemezképtároló ügyfélfájl átvitele (a fürtön kívül)                             
* **FileDownload**: image store ügyfélfájl letöltési kezdeményezése (a fürtön kívül)                             
* **InternalList**: lemezképtároló ügyfélfájllista-művelet (belső)                             
* **Törlés**: lemezképtároló ügyfél törlési művelete                              
* **Feltöltés**: image store ügyfél feltöltési művelet                             
* **NodeControl**: csomópontok indítása, leállítása és újraindítása                             
* **MoveReplicaControl**: replikák áthelyezése egyik csomópontról a másikra                             

### <a name="miscellaneous-operations"></a>Egyéb műveletek
* **Ping**: ügyfél ping                             
* **Lekérdezés**: minden lekérdezés engedélyezett
* **NameExists**: uri létezési ellenőrzésének elnevezése                             

A felhasználói hozzáférés-vezérlés típusa alapértelmezés szerint a következő műveletekre korlátozódik: 

* **SzámbavételUtónevek:** URI-számbavétel elnevezése                             
* **EnumerálniTulajdonságok:** tulajdonság számbavétele                             
* **PropertyReadBatch**: névadási tulajdonság olvasási műveletek                             
* **GetServiceDescription**: hosszú szavazással kapcsolatos szolgáltatásértesítések és olvasási szolgáltatások leírásai                             
* **ResolveService**: panaszalapú szolgáltatásmegoldás                             
* **ResolveNameOwner**: az URI-tulajdonos elnevezésének feloldása                             
* **ResolvePartition**: rendszerszolgáltatások feloldása                             
* **ServiceNotifications**: eseményalapú szolgáltatásértesítések                             
* **GetUpgradeStatus**: alkalmazásfrissítési állapot lekérdezése                             
* **GetFabricUpgradeStatus**: fürtfrissítési állapot lekérdezése                             
* **InvokeInfrastructureQuery**: infrastruktúra-feladatok lekérdezése                             
* **Lista**: lemezképtároló ügyfélfájllista-művelete                             
* **ResetPartitionLoad**: feladatátvevő egység terhelése visszaállítása                             
* **ToggleVerboseServicePlacementHealthReporting**: részletes szolgáltatáselhelyezési állapotjelentés váltása                             

A rendszergazdai hozzáférés-vezérlés is hozzáfér az előző műveletekhez.

## <a name="changing-default-settings-for-client-roles"></a>Az ügyfélszerepkörök alapértelmezett beállításainak módosítása
A fürtjegyzékfájlban szükség esetén rendszergazdai lehetőségeket biztosíthat az ügyfélnek. Az alapértelmezett értékeket úgy módosíthatja, hogy a [fürt létrehozása](service-fabric-cluster-creation-via-portal.md)során a **Fabric-beállítások** lehetőséget választja, és megadja az előző beállításokat a **névben,** **a rendszergazda**, a **felhasználó**és az **értékmezőkben.**

## <a name="next-steps"></a>További lépések
[A Service Fabric fürtjének biztonsága](service-fabric-cluster-security.md)

[A Szolgáltatásháló-fürt létrehozása](service-fabric-cluster-creation-via-portal.md)

