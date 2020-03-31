---
title: Azure Service Fabric-fürt frissítése
description: Ismerje meg az Azure Service Fabric-fürt verziójának vagy konfigurációjának frissítését – fürtfrissítési mód beállítását, a tanúsítványok frissítését, az alkalmazásportok hozzáadását, az operációs rendszer javításait, valamint azt, hogy mire számíthat a frissítések végrehajtásakor.
ms.topic: conceptual
ms.date: 11/12/2018
ms.custom: sfrev
ms.openlocfilehash: 6897854820339fc78dd9083c82147dce95ab68b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258654"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>Azure Service Fabric-fürt frissítése

Minden modern rendszer számára a felbonthatóság tervezése kulcsfontosságú a termék hosszú távú sikerének eléréséhez. Az Azure Service Fabric-fürt egy olyan erőforrás, amely az Ön tulajdonában van, de részben a Microsoft kezeli. Ez a cikk ismerteti, hogy mi a felügyelt automatikusan, és mit lehet beállítani saját maga.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>A fürtön futó hálóverzió vezérlése

Győződjön meg arról, hogy a fürt mindig [támogatott hálóverziót](service-fabric-versions.md)futtat. Minden alkalommal, amikor a Microsoft bejelenti a Service Fabric új verziójának kiadását, az előző verzió a támogatás megszűnése után legalább 60 nap elteltével van megjelölve. Új kiadások vannak jelentették be a [Service Fabric csapat blog](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).

14 nappal a kiadás lejárta előtt a fürt fut, egy állapotesemény jön létre, amely a fürt egy figyelmeztető állapotba helyezi. A fürt figyelmeztetési állapotban marad, amíg nem frissít egy támogatott hálóverzióra.

Beállíthatja, hogy a fürt automatikus hálófrissítéseket kapjon, amint azok a Microsoft által kiadott, vagy kiválaszthatja a támogatott hálóverziót, amelyen a fürthöz be kell lépnie.  További információért olvassa el [a fürt Service Fabric-verziójának frissítése](service-fabric-cluster-upgrade-version-azure.md)című részét.

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>A háló frissítésének viselkedése az automatikus frissítések során

A Microsoft fenntartja a hálókódot és konfigurációt, amely egy Azure-fürtben fut. A szoftver automatikus, figyelt frissítéseit szükség szerint végezzük. Ezek a frissítések lehetnek kód, konfiguráció, vagy mindkettő. Annak érdekében, hogy az alkalmazás ne szenvedjen semmilyen hatást, vagy minimális hatást gyakoroljon a frissítések miatt, a frissítések a következő fázisokban hajtják végre:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>1. fázis: A frissítés az összes fürtállapot-házirend használatával történik

Ebben a fázisban a frissítések egyszerre csak egy frissítési tartományt folytatnak, és a fürtben futó alkalmazások továbbra is állásidő nélkül futnak. A fürt állapotházirendjei (a csomópont állapota és az alkalmazás állapota) a frissítés során betartják.

Ha a fürt állapotházirendjei nem teljesülnek, a frissítés visszalesz állítva, és egy e-mailt küld az előfizetés tulajdonosának. Az e-mail a következő információkat tartalmazza:

* Értesítés arról, hogy vissza kellett állítja a fürtfrissítést.
* Javasolt javító intézkedések, ha vannak ilyenek.
* A 2.*n*

Megpróbáljuk végrehajtani ugyanazt a frissítést még néhány alkalommal, ha a frissítések infrastrukturális okokmiatt sikertelenek voltak. Az *e-mail* elküldésétől számított n nap után a 2.

Ha a fürt állapotházirendjei teljesülnek, a frissítés sikeresnek és teljesnek minősül. Ez a kezdeti frissítés során vagy a frissítési ismétlések bármelyike során fordulhat elő ebben a fázisban. Nincs e-mail visszaigazolása a sikeres futtatásról. Ez azért van, hogy ne küldjön túl sok e-mailt; az e-mail fogadását a normális tól való kivételnek kell tekinteni. Arra számítunk, hogy a fürtfrissítések többsége sikeres lesz anélkül, hogy befolyásolná az alkalmazás rendelkezésre állását.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>2. fázis: A frissítés csak az alapértelmezett állapotházirendek használatával történik

Az állapotházirendek ebben a fázisban vannak beállítva oly módon, hogy a frissítés kezdetén kifogástalan állapotú alkalmazások száma ugyanaz marad a frissítési folyamat időtartama alatt. Az 1. A fürt állapotházirendjei (a csomópont állapotának és a fürtben futó összes alkalmazás állapotának kombinációja) a frissítés időtartama alatt be vannak tartva.

Ha a fürt állapotházirendjei nem teljesülnek, a frissítés visszalesz állítva. Ezután egy e-mailt küld a tulajdonos az előfizetés. Az e-mail a következő információkat tartalmazza:

* Értesítés arról, hogy vissza kellett állítja a fürtfrissítést.
* Javasolt javító intézkedések, ha vannak ilyenek.
* A 3.

Megpróbáljuk végrehajtani ugyanazt a frissítést még néhány alkalommal, ha a frissítések infrastrukturális okokmiatt sikertelenek voltak. Egy emlékeztető e-mailt küld egy pár nappal az n nap alatt. Az e-mail elküldésétől számított n nap után a 3. A 2.

Ha a fürt állapotházirendjei teljesülnek, a frissítés sikeresnek és teljesnek minősül. Ez a kezdeti frissítés során vagy a frissítési ismétlések bármelyike során fordulhat elő ebben a fázisban. Nincs e-mail visszaigazolása a sikeres futtatásról.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>3. fázis: A frissítés agresszív állapotházirendek használatával történik

Ezek az állapotházirendek ebben a fázisban a frissítés befejezésére irányulnak, nem pedig az alkalmazások állapotára. Kevés fürtfrissítés végződik ebben a fázisban. Ha a fürt ebben a fázisban, jó esély van arra, hogy az alkalmazás nem megfelelő állapotúlesz, és/vagy elveszíti a rendelkezésre állást.

A másik két fázishoz hasonlóan a 3.

Ha a fürt állapotházirendjei nem teljesülnek, a frissítés visszalesz állítva. Megpróbáljuk végrehajtani ugyanazt a frissítést még néhány alkalommal, ha a frissítések infrastrukturális okokmiatt sikertelenek voltak. Ezt követően a fürt rögzítve van, így a továbbiakban nem kap támogatást és/vagy frissítéseket.

Az adatokkal kapcsolatos e-mailt a rendszer elküldi az előfizetés tulajdonosának, valamint a javítási műveleteket. Nem számítunk arra, hogy a fürtök olyan állapotba kerülnek, ahol a 3.

Ha a fürt állapotházirendjei teljesülnek, a frissítés sikeresnek és teljesnek minősül. Ez a kezdeti frissítés során vagy a frissítési ismétlések bármelyike során fordulhat elő ebben a fázisban. Nincs e-mail visszaigazolása a sikeres futtatásról.

## <a name="manage-certificates"></a>Tanúsítványok kezelése

A Service Fabric [X.509-es kiszolgálói tanúsítványokat](service-fabric-cluster-security.md) használ, amelyeket akkor ad meg, amikor fürtöt hoz létre a fürtcsomópontok közötti kommunikáció biztonságossá tétele és az ügyfelek hitelesítése érdekében. Hozzáadhat, frissíthet vagy törölhet tanúsítványokat a fürthöz és az ügyfélhez az [Azure Portalon](https://portal.azure.com) vagy a PowerShell/Azure CLI használatával.  További információ: [Tanúsítványok hozzáadása vagy eltávolítása](service-fabric-cluster-security-update-certs-azure.md)

## <a name="open-application-ports"></a>Alkalmazásportok megnyitása

Az alkalmazásportok módosításával módosíthatja a csomóponttípushoz társított terheléselosztó erőforrás-tulajdonságait. Használhatja az Azure Portalon, vagy használhatja a PowerShell/Azure CLI. További információért olvassa el [a Fürt alkalmazásportjainak megnyitása](create-load-balancer-rule.md)című olvassa el.

## <a name="define-node-properties"></a>Csomóponttulajdonságok definiálása

Előfordulhat, hogy biztosítani szeretné, hogy bizonyos számítási feladatok csak bizonyos típusú csomópontokon fussanak a fürtben. Előfordulhat például, hogy egyes munkaterhelések GPU-kat vagy SSD-ket igényelnek, míg mások nem. A fürt minden csomóponttípusához egyéni csomóponttulajdonságokat adhat hozzá a fürtcsomópontokhoz. Az elhelyezési megkötések az egyes szolgáltatásokhoz csatolt utasítások, amelyek egy vagy több csomóponttulajdonsághoz választanak ki. Az elhelyezési megkötések határozzák meg, hogy a szolgáltatások nak hol kell futniuk.

Az elhelyezési megkötések, a csomóponttulajdonságok használatáról és azok meghatározásáról, a [csomópont tulajdonságainak és az elhelyezési kényszereknek](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)a használatával kapcsolatos részletekért olvassa el a csomópont tulajdonságait és az elhelyezési kényszereket.

## <a name="add-capacity-metrics"></a>Kapacitásmérők hozzáadása

Az egyes csomóponttípusok egyéni kapacitásmetrikákat adhat hozzá, amelyeket az alkalmazásokban a betöltés jelentéséhez szeretne használni. A terhelés jelentéséhez szükséges kapacitásmérők használatával kapcsolatos részleteket a Fürt és [metrikák](service-fabric-cluster-resource-manager-metrics.md) [leírásáról](service-fabric-cluster-resource-manager-cluster-description.md) szóló Service Fabric-fürterőforrás-kezelő dokumentumok című dokumentumai ban találja.

## <a name="set-health-policies-for-automatic-upgrades"></a>Állapotházirendek beállítása az automatikus frissítésekhez

Megadhatja az egyéni állapotházirendek a háló frissítése. Ha a fürt automatikus háló frissítések, majd ezek a házirendek az automatikus háló frissítések 1 fázisa alkalmazza.
Ha beállította a fürt manuális háló frissítések, majd ezek a házirendek minden alkalommal, amikor kiválaszt egy új verziót, amely elindítja a rendszert, hogy indítsa el a háló frissítése a fürtben. Ha nem bírálja felül a házirendeket, a rendszer az alapértelmezett értékeket használja.

Megadhatja az egyéni állapotházirendeket, vagy tekintse át az aktuális beállításokat a "háló frissítési" panel alatt, a speciális frissítési beállítások kiválasztásával. Tekintse át az alábbi képet arról, hogyan kell.

![Egyéni állapotházirendek kezelése][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>A fürt hálóbeállításainak testreszabása

Számos különböző konfigurációs beállítás testreszabható egy fürtön, például a fürt megbízhatósági szintje és a csomópont tulajdonságai. További információért olvassa el a [Service Fabric fürtháló beállításait.](service-fabric-cluster-fabric-settings.md)

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
