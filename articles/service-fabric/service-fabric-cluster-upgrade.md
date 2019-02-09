---
title: Egy Azure Service Fabric-fürt frissítése |} A Microsoft Docs
description: További információ az, illetve Azure Service Fabric-fürt konfigurációjának frissítése.  Ez a cikk ismerteti a beállítás fürt frissítési mód, frissítése, tanúsítványok, hozzáadás, application portok, operációsrendszer-javítások, és hogy mire számíthat, ha a frissítés el kell végezni
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: aljo
ms.openlocfilehash: f19693853672b6274265c95b851f478b4f3ef4a2
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55961890"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>A frissítés és az Azure Service Fabric-fürt frissítése

Minden modern rendszeren bővíthetőség for designing fontos a termék hosszú távú sikert eléréséhez. Azure Service Fabric-fürt egy erőforrás, amelynek a tulajdonosa, de részben a Microsoft felügyeli. Ez a cikk bemutatja, milyen automatikusan kezeli, és amit konfigurálhat saját magának.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>A fabric-verziót a fürtön futó szabályozása

Győződjön meg arról, hogy a fürtöt, egy [fabric verziója támogatott](service-fabric-versions.md) mindig. És hogy jelentjük be az új verzióját a service fabric, az előző verzió után 60 napon, hogy legalább támogatásuk van megjelölve. Az új kiadásokkal teszi közzé a service fabric blogján. Az új kiadásban érhető el, majd válassza ki.

a fürt fut, a kiadás lejárta előtt 14 nappal állapottal kapcsolatos esemény jön létre, amely a fürt beteszi a figyelmeztetési állapot. A fürt figyelmeztetési állapotban marad, amíg nem támogatott fabric-verzióra frissít.

Beállíthatja, hogy a fürt automatikus hálófrissítések fogadásához, azok a Microsoft által kiadott, vagy kiválaszthat egy azt szeretné, hogy a rendszer a fürt támogatott fabric verziója.  További tudnivalókért olvassa el a [a fürt Service Fabric-verziójának frissítése](service-fabric-cluster-upgrade-version-azure.md).

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>Fabric frissítési viselkedést automatikus frissítések során
A Microsoft fenntartja a fabric-kódra, és a egy Azure-fürtön futó configuration. A szoftver figyelt automatikus frissítései révén igény szerint elvégzése. Ezek a frissítések lehet kódot, konfigurációs vagy mindkettőt. Ahhoz, hogy az alkalmazás romlik a, hogy ezek a frissítések miatt csak minimális hatással van, vagy nem gyakorolt hatás, a következő szakaszában, a Microsoft végzi a frissítéseket:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>1. fázis: Frissítés az összes fürt állapotházirendeket használatával történik
Ebben a fázisban a frissítéseket a egyszerre több frissítési tartományt folytatja, és az alkalmazások, a fürtben futó tovább állásidő nélkül. A fürt állapotházirendeket (csomópont állapotát, az kombinációja a fürtben futó összes alkalmazás) a frissítés során betartását.

Ha a fürt állapotházirendeket nem teljesülnek, a frissítés vissza lesz állítva. Ezután e-mailt küldeni az előfizetés tulajdonosa van. Az e-mailt a következő információkat tartalmazza:

* Értesítés az, hogy a fürtfrissítések visszavonásához kellett.
* Helyreigazító műveletek végrehajtásához, akkor javasolt, ha van ilyen.
* (N), amíg nem tudjuk hajtsa végre a 2. fázis napok száma.

Próbálja meg hajtsa végre ugyanezt a frissítést néhány alkalommal abban az esetben a megjelenjenek infrastruktúra miatt nem sikerült. Az n nap elteltével a rendszer elküldte az e-mail dátumától számítva hogy folytassa a 2. fázis.

Ha a fürt állapotházirendeket teljesülnek, a frissítés sikeresnek minősül és befejezettként. Ez akkor fordulhat elő, a kezdeti frissítés, vagy a frissítési ismétlések során ebben a fázisban. Nincs nem sikeres Futtatás megerősítő e-mailt. Ez a ne küldjön túl sok e-mailek--fogad e-mailt, a szokásos kivételt kell tekinteni. Terveink szerint már a legtöbb a fürtfrissítések sikeres végrehajtásához az rendelkezésre állásának befolyásolása nélkül.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>2. fázis: Frissítés alapértelmezett egészségügyi házirendek segítségével történik
Ebben a fázisban a házirendek úgy, hogy voltak a frissítés kezdetén kifogástalan állapotú alkalmazások száma továbbra is ugyanazt a frissítési folyamat idejére vannak beállítva. 1. fázis, mint a 2. fázis verziófrissítések több frissítési tartományt folytatható egy időben, és az alkalmazások, a fürtben futó tovább állásidő nélkül. A fürt állapotházirendeket (csomópont állapotát, az kombinációja a fürtben futó összes alkalmazás) betartsák a frissítés idejére.

Ha a fürt állapotának házirendek érvényben nem teljesülnek, a frissítés vissza lesz állítva. Ezután e-mailt küldeni az előfizetés tulajdonosa van. Az e-mailt a következő információkat tartalmazza:

* Értesítés az, hogy a fürtfrissítések visszavonásához kellett.
* Helyreigazító műveletek végrehajtásához, akkor javasolt, ha van ilyen.
* (N), amíg nem tudjuk hajtsa végre a 3. fázis napok száma.

Próbálja meg hajtsa végre ugyanezt a frissítést néhány alkalommal abban az esetben a megjelenjenek infrastruktúra miatt nem sikerült. Emlékeztető e-mailt néhány nap alatt, mielőtt n napig mentése zajlik. Az n nap elteltével a rendszer elküldte az e-mail dátumától számítva hogy folytassa a 3. fázis. Az e-mailt küldünk, 2. fázis komolyan kell venni, és kell tenni a helyreigazító műveletek végrehajtásához.

Ha a fürt állapotházirendeket teljesülnek, a frissítés sikeresnek minősül és befejezettként. Ez akkor fordulhat elő, a kezdeti frissítés, vagy a frissítési ismétlések során ebben a fázisban. Nincs nem sikeres Futtatás megerősítő e-mailt.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>3. fázis: Frissítés agresszív állapotházirendeket használatával történik
Ebben a fázisban a health házirendek helyett az alkalmazások állapotát a frissítés felé egyszerűsítését szolgálja. Ebben a fázisban végül néhány fürtfrissítések. Ha a fürt lekérdezi az ebben a fázisban, van esély arra, hogy az alkalmazás akkor kerül sérült, és/vagy elveszíti a rendelkezésre állás.

A két fázisban hasonlóan, 3. fázis verziófrissítések folytatható, több frissítési tartományt egyszerre.

Ha a fürt állapotházirendeket nem teljesülnek, a frissítés vissza lesz állítva. Próbálja meg hajtsa végre ugyanezt a frissítést néhány alkalommal abban az esetben a megjelenjenek infrastruktúra miatt nem sikerült. Ezt követően a fürt ki van tűzve, így már nem kap támogatást és/vagy a frissítéseket.

Ezt az információt egy e-mailt az előfizetés tulajdonosa a helyreigazító műveletek együtt érkezik. Bármely olyan állapotba, ahol a 3. fázis nem sikerült beolvasni a fürtök várhatóan nem lesz.

Ha a fürt állapotházirendeket teljesülnek, a frissítés sikeresnek minősül és befejezettként. Ez akkor fordulhat elő, a kezdeti frissítés, vagy a frissítési ismétlések során ebben a fázisban. Nincs nem sikeres Futtatás megerősítő e-mailt.

## <a name="manage-certificates"></a>Tanúsítványok kezelése
A Service Fabric használ [X.509 kiszolgálói tanúsítványok](service-fabric-cluster-security.md) fürtcsomópontok közötti kommunikáció biztonságossá tételéhez és-ügyfelek hitelesítésére a fürt létrehozásakor megadott. Hozzáadása, módosítása és törlése a fürt és az ügyfél tanúsítványai a [az Azure portal](https://portal.azure.com) vagy a PowerShell vagy az Azure CLI használatával.  További tudnivalókért olvassa el a [hozzáadni vagy eltávolítani a tanúsítványokat](service-fabric-cluster-security-update-certs-azure.md)

## <a name="open-application-ports"></a>Portok megnyitása
Alkalmazásportok typ uzlu társított terheléselosztó erőforrás-tulajdonságok módosításával módosíthatja. Az Azure Portalon is használhatja, vagy használhatja a PowerShell vagy az Azure parancssori felület. További információkért olvassa el [nyissa meg a fürt alkalmazásportok](create-load-balancer-rule.md).

## <a name="define-node-properties"></a>Csomópont-tulajdonságok meghatározása
Néha érdemes győződjön meg arról, hogy bizonyos munkaterhelések csak bizonyos típusú, a fürtben található csomópontok futtassa. Például néhány számítási feladat lehet szükség gpu-kat vagy SSD-k, míg mások nem. A fürtben csomóponttípusok mindegyikéhez fürtcsomópontok egyéni csomópont tulajdonságokat adhat hozzá. Elhelyezési korlátozások az egyes szolgáltatásai, válassza ki egy vagy több csomópont-tulajdonságok csatolt utasításokat. Elhelyezési korlátozások határozza meg, ahol szolgáltatásainak futnia kell.

Elhelyezési korlátozások, a csomópont-tulajdonságok és hogyan adhat meg hozzájuk használatára vonatkozó további tudnivalókért olvassa el [csomópont-tulajdonságok és elhelyezési korlátozások](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="add-capacity-metrics"></a>Adja hozzá a kapacitási mérőszámot
Minden egyes csomóponttípus betöltésének jelentéséhez az alkalmazásban használni kívánt egyéni kapacitási mérőszámokat adhat hozzá. Betöltésének jelentéséhez a kapacitási mérőszámot használatának részletes ismertetéséért tekintse meg a Service Fabric-fürt Resource Manager-dokumentumok a [a fürt leíró](service-fabric-cluster-resource-manager-cluster-description.md) és [metrikák és a terhelés](service-fabric-cluster-resource-manager-metrics.md).

## <a name="set-health-policies-for-automatic-upgrades"></a>Az automatikus frissítésekre házirendek beállítása
Egyéni házirendek a fabric frissítése is megadhat. Ha a fürt automatikus hálófrissítések értékre állította, ezek a szabályzatok a – 1. fázisa az automatikus hálófrissítések legyenek alkalmazva.
Ha a manuális fabric-fürtöt verziófrissítések állított be, ezeket a szabályzatokat alkalmazva minden alkalommal, amikor egy új verzió indítása a rendszer a fabric frissítése a fürt elindítson választja. Ha nem bírálja felül a szabályzatokat, az alapértelmezett értékeket használják.

Adja meg az egyéni állapotházirendeket, vagy tekintse át a jelenlegi beállítások szerint a "fabric frissítése" panel a speciális frissítési beállítások kiválasztásával. Tekintse át a következő képen látható, hogyan. 

![Egyéni házirendek kezelése][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>A fürt háló beállításainak testreszabása
Számos különböző konfigurációs beállítások testre szabható a megbízhatósági szint a fürt és a csomópont tulajdonságait, például egy fürtön. További információkért olvassa el [Service Fabric-fürt hálóbeállítások](service-fabric-cluster-fabric-settings.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Az operációs rendszer, a fürtcsomópontok javítása
A patch orchestration application (POA) a Service Fabric-alkalmazás, amely automatizálja az operációs rendszer javításának Service Fabric-fürt, üzemkimaradás nélkül. A [Patch Orchestration Application for Windows](service-fabric-patch-orchestration-application.md) vagy [Patch Orchestration Application linuxos](service-fabric-patch-orchestration-application-linux.md) is telepíthetők a javítások telepítése összehangolt módon miközben gondoskodik a szolgáltatásokat a fürt rendelkezésre álló kapacitását. 

## <a name="os-upgrades-on-the-vms-that-make-up-the-cluster"></a>Operációs rendszer verziófrissítései a fürtöt alkotó virtuális gépeken
Ha frissítenie kell a virtuális gépek a fürt operációsrendszer-lemezkép, akkor kell megtennie, egy virtuális gép egyszerre. Az Ön felelőssége lesz a frissítés – jelenleg nincs automatizálva a.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan szabhatja testre az egyes a [service fabric-fürt hálóbeállítások](service-fabric-cluster-fabric-settings.md)
* Ismerje meg, hogyan [fürt kétirányú méretezése](service-fabric-cluster-scale-up-down.md)
* Ismerje meg [alkalmazásfrissítések](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
