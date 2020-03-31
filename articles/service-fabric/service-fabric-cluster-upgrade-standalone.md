---
title: Önálló Azure Service Fabric-fürt frissítése
description: Ismerje meg az Azure Service Fabric önálló fürt verziójának vagy konfigurációjának frissítését.  T
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 6da9b4c6890895141ecc419382f05f667614fb31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451828"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>A Service Fabric önálló fürtjének frissítése és frissítése

Minden modern rendszer számára a felbonthatóság tervezése kulcsfontosságú a termék hosszú távú sikerének eléréséhez. Az Azure Service Fabric önálló fürt egy saját erőforrás. Ez a cikk azt ismerteti, hogy mit lehet frissíteni vagy frissíteni.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>A fürtön futó hálóverzió vezérlése
Győződjön meg arról, hogy a fürt mindig egy [támogatott Service Fabric-verziót](service-fabric-versions.md)futtat. Amikor a Microsoft bejelenti a Service Fabric új verziójának kiadását, az előző verzió a bejelentés dátumától számított legalább 60 nap elteltével a támogatás megszűnése után a támogatás megszűnése jelzővel van ellátva. Új kiadások vannak jelentették be [a Service Fabric csapat blog](https://blogs.msdn.microsoft.com/azureservicefabric/). Az új kiadás ezen a ponton választható.

Beállíthatja, hogy a fürt automatikus hálófrissítéseket kapjon, amint azok a Microsoft által kiadott, vagy manuálisan kiválaszthatja a támogatott hálóverziót, amelyen a fürthöz be szeretne érkezni. További információért olvassa el [a fürtön futó Service Fabric-verzió frissítése](service-fabric-cluster-upgrade-windows-server.md)című olvasni.

## <a name="customize-configuration-settings"></a>Konfigurációs beállítások testreszabása

A *ClusterConfig.json* fájlban számos különböző [konfigurációs beállítás](service-fabric-cluster-manifest.md) állítható be, például a fürt és a csomópont tulajdonságainak megbízhatósági szintje.  További információ: [Önálló fürt konfigurációjának frissítése](service-fabric-cluster-config-upgrade-windows-server.md).  Sok más, fejlettebb, beállításokat is testre szabható.  További információért olvassa el a [Service Fabric fürtháló beállításait.](service-fabric-cluster-fabric-settings.md)

## <a name="define-node-properties"></a>Csomóponttulajdonságok definiálása
Előfordulhat, hogy biztosítani szeretné, hogy bizonyos számítási feladatok csak bizonyos típusú csomópontokon fussanak a fürtben. Előfordulhat például, hogy egyes munkaterhelések GPU-kat vagy SSD-ket igényelnek, míg mások nem. A fürt minden csomóponttípusához egyéni csomóponttulajdonságokat adhat hozzá a fürtcsomópontokhoz. Az elhelyezési megkötések az egyes szolgáltatásokhoz csatolt utasítások, amelyek egy vagy több csomóponttulajdonsághoz választanak ki. Az elhelyezési megkötések határozzák meg, hogy a szolgáltatások nak hol kell futniuk.

Az elhelyezési megkötések, a csomóponttulajdonságok használatáról és azok meghatározásáról, a [csomópont tulajdonságainak és az elhelyezési kényszereknek](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)a használatával kapcsolatos részletekért olvassa el a csomópont tulajdonságait és az elhelyezési kényszereket.
 

## <a name="add-capacity-metrics"></a>Kapacitásmérők hozzáadása
Az egyes csomóponttípusok egyéni kapacitásmetrikákat adhat hozzá, amelyeket az alkalmazásokban a betöltés jelentéséhez szeretne használni. A terhelés jelentéséhez szükséges kapacitásmérők használatával kapcsolatos részleteket a Fürt és [metrikák](service-fabric-cluster-resource-manager-metrics.md) [leírásáról](service-fabric-cluster-resource-manager-cluster-description.md) szóló Service Fabric-fürterőforrás-kezelő dokumentumok című dokumentumai ban találja.

## <a name="patch-the-os-in-the-cluster-nodes"></a>Az operációs rendszer javítása a fürtcsomópontokban
A patch vezénylési alkalmazás (POA) egy Service Fabric-alkalmazás, amely automatizálja az operációs rendszer javítását egy Service Fabric-fürtön állásidő nélkül. A [Windows patch vezénylési alkalmazás](service-fabric-patch-orchestration-application.md) telepíthető a fürtre a javítások hangszerelt módon történő telepítéséhez, miközben a szolgáltatások mindig elérhetőek maradnak. 


## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan szabhatja testre a [szolgáltatásháló fürthálózatának néhány beállítását](service-fabric-cluster-fabric-settings.md)
* További információ [a fürt méretezése és méretezése](service-fabric-cluster-scale-up-down.md)
* További információ [az alkalmazásfrissítésekről](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
