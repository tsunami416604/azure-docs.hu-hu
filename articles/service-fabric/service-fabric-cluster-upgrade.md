---
title: Egy Azure Service Fabric-fürt frissítése |} A Microsoft Docs
description: Frissítés a Service Fabric-kód és/vagy a Service Fabric-fürt, beleértve a fürt frissítési mód, frissítése, tanúsítványok, az alkalmazás portok, operációsrendszer-javítások, ezzel hozzáadása beállítása futtató konfiguráció, és így tovább. Mi a számíthat, ha a frissítés történik?
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
ms.date: 8/10/2017
ms.author: aljo
ms.openlocfilehash: 2fd62f8709bddfd981f4b1358c97d0acbaf7f12d
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2018
ms.locfileid: "48269103"
---
# <a name="upgrade-an-azure-service-fabric-cluster"></a>Egy Azure Service Fabric-fürt frissítése
> [!div class="op_single_selector"]
> * [Azure-fürtben](service-fabric-cluster-upgrade.md)
> * [Önálló fürt](service-fabric-cluster-upgrade-windows-server.md)
> 
> 

Minden modern rendszeren bővíthetőség for designing fontos a termék hosszú távú sikert eléréséhez. Azure Service Fabric-fürt egy erőforrás, amelynek a tulajdonosa, de részben a Microsoft felügyeli. Ez a cikk bemutatja, milyen automatikusan kezeli, és amit konfigurálhat saját magának.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>A fabric-verziót a fürtön futó szabályozása
Beállíthatja, hogy a fürt automatikus hálófrissítések fogadásához, azok a Microsoft által kiadott, vagy kiválaszthat egy azt szeretné, hogy a rendszer a fürt támogatott fabric verziója.

Ehhez az "upgrademode tulajdonság" fürtkonfiguráció beállítása a portálon, vagy a létrehozás vagy később egy élő fürthöz idején a Resource Manager használatával 

> [!NOTE]
> Győződjön meg arról, hogy a fürt támogatott fabric verziója mindig működik. És hogy jelentjük be az új verzióját a service fabric, az előző verzió után 60 napon, hogy legalább támogatásuk van megjelölve. Az új kiadásokkal már bejelentettünk [a service fabric blogján](https://blogs.msdn.microsoft.com/azureservicefabric/). Az új kiadásban érhető el, majd válassza ki. 
> 
> 

a fürt fut, a kiadás lejárta előtt 14 nappal állapottal kapcsolatos esemény jön létre, amely a fürt beteszi a figyelmeztetési állapot. A fürt figyelmeztetési állapotban marad, amíg nem támogatott fabric-verzióra frissít.

### <a name="setting-the-upgrade-mode-via-portal"></a>A portálon keresztül frissítési mód beállítása
A fürt és megadható az automatikus vagy kézi-e a fürt létrehozásakor.

![Create_Manualmode][Create_Manualmode]

Beállíthatja a fürt automatikus vagy manuális egy élő fürthöz, a kezelés tapasztalatok használatával. 

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Egy fürtöt, amely a portálon keresztül manuális módra van beállítva az új verzióra frissíti.
Az új verzióra frissít, ehhez szüksége a rendelkezésre álló verzió válassza a legördülő listából, majd mentse. A Fabric frissítés automatikusan lekéri kezdődjön. A fürt állapotházirendeket (csomópont állapotát, az kombinációja a fürtben futó összes alkalmazás) a frissítés során betartását.

Ha a fürt állapotházirendeket nem teljesülnek, a frissítés vissza lesz állítva. Görgessen lefelé a dokumentum további ezeket egyéni házirendek beállításával. 

Miután javította a problémákat, amelyek a visszaállítás eredményezett, a frissítés ismét kezdeményezése a következő ugyanazokat a lépéseket előtt kell.

![Manage_Automaticmode][Manage_Automaticmode]

### <a name="setting-the-upgrade-mode-via-a-resource-manager-template"></a>A frissítési mód használatával a Resource Manager-sablon beállítása
Az "upgrademode tulajdonság" konfigurációkat adhat a Microsoft.ServiceFabric/clusters erőforrás-definíció és a "clusterCodeVersion" értékre az alább látható módon támogatott fabric-verziók valamelyikét, és ezután helyezze üzembe a sablont. Az érvényes értékek az upgrademode "tulajdonság": "Kézi" vagy "Automatikus"

![ARMUpgradeMode][ARMUpgradeMode]

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Egy fürtön, amelyek egy Resource Manager-sablon használatával manuális módra van beállítva egy új verziójára való frissítését.
A fürt manuális módban van, amikor egy új verzióra frissít, módosítsa a "clusterCodeVersion" egy támogatott verziójára, és telepítheti. A sablon központi telepítését, a Fabric frissítési megrúgni lekérdezi kezdődjön automatikusan. A fürt állapotházirendeket (csomópont állapotát, az kombinációja a fürtben futó összes alkalmazás) a frissítés során betartását.

Ha a fürt állapotházirendeket nem teljesülnek, a frissítés vissza lesz állítva. Görgessen lefelé a dokumentum további ezeket egyéni házirendek beállításával. 

Miután javította a problémákat, amelyek a visszaállítás eredményezett, a frissítés ismét kezdeményezése a következő ugyanazokat a lépéseket előtt kell.

### <a name="get-list-of-all-available-version-for-all-environments-for-a-given-subscription"></a>Minden környezet egy adott előfizetéshez tartozó összes elérhető verzió listájának lekérése
Futtassa a következő parancsot, és a kimenet ehhez hasonló kapja.

"supportExpiryUtc" arra utasítja a akkor, ha egy adott kiadás lejár vagy lejárt. A legfrissebb kiadás nem rendelkezik érvényes dátum -, amelynek értéke "9999-12-31T23:59:59.9999999", amely csak azt jelenti, hogy a lejárat dátumát még nem állították.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }


```

## <a name="fabric-upgrade-behavior-when-the-cluster-upgrade-mode-is-automatic"></a>Fabric frissítési működés, ha a fürt üzemmód frissítése automatikus
A Microsoft fenntartja a fabric-kódra, és a egy Azure-fürtön futó configuration. A szoftver figyelt automatikus frissítései révén igény szerint elvégzése. Ezek a frissítések lehet kódot, konfigurációs vagy mindkettőt. Ahhoz, hogy az alkalmazás romlik a, hogy ezek a frissítések miatt csak minimális hatással van, vagy nem gyakorolt hatás, a következő szakaszában, a Microsoft végzi a frissítéseket:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>1. fázis: Frissítés történik az összes fürt házirendek használatával
Ebben a fázisban a frissítéseket a egyszerre több frissítési tartományt folytatja, és az alkalmazások, a fürtben futó tovább állásidő nélkül. A fürt állapotházirendeket (csomópont állapotát, az kombinációja a fürtben futó összes alkalmazás) a frissítés során betartását.

Ha a fürt állapotházirendeket nem teljesülnek, a frissítés vissza lesz állítva. Ezután e-mailt küldeni az előfizetés tulajdonosa van. Az e-mailt a következő információkat tartalmazza:

* Értesítés az, hogy a fürtfrissítések visszavonásához kellett.
* Helyreigazító műveletek végrehajtásához, akkor javasolt, ha van ilyen.
* (N), amíg nem tudjuk hajtsa végre a 2. fázis napok száma.

Próbálja meg hajtsa végre ugyanezt a frissítést néhány alkalommal abban az esetben a megjelenjenek infrastruktúra miatt nem sikerült. Az n nap elteltével a rendszer elküldte az e-mail dátumától számítva hogy folytassa a 2. fázis.

Ha a fürt állapotházirendeket teljesülnek, a frissítés sikeresnek minősül és befejezettként. Ez akkor fordulhat elő, a kezdeti frissítés, vagy a frissítési ismétlések során ebben a fázisban. Nincs nem sikeres Futtatás megerősítő e-mailt. Ez a ne küldjön túl sok e-mailek--fogad e-mailt, a szokásos kivételt kell tekinteni. Terveink szerint már a legtöbb a fürtfrissítések sikeres végrehajtásához az rendelkezésre állásának befolyásolása nélkül.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>2. fázis: Frissítés történik alapértelmezett egészségügyi házirendek segítségével
Ebben a fázisban a házirendek úgy, hogy voltak a frissítés kezdetén kifogástalan állapotú alkalmazások száma továbbra is ugyanazt a frissítési folyamat idejére vannak beállítva. 1. fázis, mint a 2. fázis verziófrissítések több frissítési tartományt folytatható egy időben, és az alkalmazások, a fürtben futó tovább állásidő nélkül. A fürt állapotházirendeket (csomópont állapotát, az kombinációja a fürtben futó összes alkalmazás) betartsák a frissítés idejére.

Ha a fürt állapotának házirendek érvényben nem teljesülnek, a frissítés vissza lesz állítva. Ezután e-mailt küldeni az előfizetés tulajdonosa van. Az e-mailt a következő információkat tartalmazza:

* Értesítés az, hogy a fürtfrissítések visszavonásához kellett.
* Helyreigazító műveletek végrehajtásához, akkor javasolt, ha van ilyen.
* (N), amíg nem tudjuk hajtsa végre a 3. fázis napok száma.

Próbálja meg hajtsa végre ugyanezt a frissítést néhány alkalommal abban az esetben a megjelenjenek infrastruktúra miatt nem sikerült. Emlékeztető e-mailt néhány nap alatt, mielőtt n napig mentése zajlik. Az n nap elteltével a rendszer elküldte az e-mail dátumától számítva hogy folytassa a 3. fázis. Az e-mailt küldünk, 2. fázis komolyan kell venni, és kell tenni a helyreigazító műveletek végrehajtásához.

Ha a fürt állapotházirendeket teljesülnek, a frissítés sikeresnek minősül és befejezettként. Ez akkor fordulhat elő, a kezdeti frissítés, vagy a frissítési ismétlések során ebben a fázisban. Nincs nem sikeres Futtatás megerősítő e-mailt.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>3. fázis: Frissítés történik agresszív házirendek használatával
Ebben a fázisban a health házirendek helyett az alkalmazások állapotát a frissítés felé egyszerűsítését szolgálja. Ebben a fázisban végül nagyon kevés fürtfrissítések. Ha a fürt lekérdezi az ebben a fázisban, van esély arra, hogy az alkalmazás akkor kerül sérült, és/vagy elveszíti a rendelkezésre állás.

A két fázisban hasonlóan, 3. fázis verziófrissítések folytatható, több frissítési tartományt egyszerre.

Ha a fürt állapotházirendeket nem teljesülnek, a frissítés vissza lesz állítva. Próbálja meg hajtsa végre ugyanezt a frissítést néhány alkalommal abban az esetben a megjelenjenek infrastruktúra miatt nem sikerült. Ezt követően a fürt ki van tűzve, így már nem kap támogatást és/vagy a frissítéseket.

Ezt az információt egy e-mailt az előfizetés tulajdonosa a helyreigazító műveletek együtt érkezik. Bármely olyan állapotba, ahol a 3. fázis nem sikerült beolvasni a fürtök várhatóan nem lesz.

Ha a fürt állapotházirendeket teljesülnek, a frissítés sikeresnek minősül és befejezettként. Ez akkor fordulhat elő, a kezdeti frissítés, vagy a frissítési ismétlések során ebben a fázisban. Nincs nem sikeres Futtatás megerősítő e-mailt.

## <a name="cluster-configurations-that-you-control"></a>Ön által megadott fürtkonfigurációk
Mellett megadhatja azokat a fürt frissítési módot, az alábbiakban a konfigurációk, amely egy élő fürthöz módosíthatja.

### <a name="certificates"></a>Tanúsítványok
Hozzáadhat új, vagy egyszerűen törölni a fürt és az ügyfél a portálon keresztül tanúsítványai. Tekintse meg [részletes útmutatót a jelen dokumentum](service-fabric-cluster-security-update-certs-azure.md)

![Képernyőkép a tanúsítvány-ujjlenyomatok az Azure Portalon.][CertificateUpgrade]

### <a name="application-ports"></a>Alkalmazásportok
Alkalmazásportok typ uzlu társított terheléselosztó erőforrás-tulajdonságok módosításával módosíthatja. A portálon, vagy közvetlenül Resource Manager PowerShell használatával.

A csomópont típusa pedig virtuális gépek új port megnyitásához tegye a következőket:

1. Adja hozzá a megfelelő-load balancer egy új mintavételt.
   
    Ha a fürt üzembe helyezte a portál használatával, a terheléselosztók nevesített (LB-name, az erőforrás-csoport-NodeTypename), egy az egyes csomóponttípusok. Mivel az a load balancer neveket csak egy erőforráscsoporton belül egyedi, emellett akkor ajánlott, ha egy adott erőforráscsoport alatt keresse meg őket.
   
    ![Képernyőkép a mintavétel hozzáadása a terheléselosztóhoz a portálon.][AddingProbes]
2. Új szabály hozzáadása a terheléselosztóhoz.
   
    Új szabály hozzáadása az azonos terheléselosztóhoz a mintavétel az előző lépésben létrehozott használatával.
   
    ![Új szabály hozzáadása a terheléselosztóhoz a portálon.][AddingLBRules]

### <a name="placement-properties"></a>Elhelyezési tulajdonságok
A csomóponttípusok mindegyikéhez az alkalmazások használni kívánt egyéni elhelyezési tulajdonságokat adhat hozzá. NodeType csomóponttípus egy alapértelmezett tulajdonság hozzáadása, explicit módon nélkül használható.

> [!NOTE]
> Elhelyezési korlátozások, a csomópont-tulajdonságok és hogyan adhat meg hozzájuk használatára vonatkozó részletekért lásd a következő szakaszra: "Elhelyezési korlátozások és a csomópont tulajdonságok" a Service Fabric-fürt Resource Manager-dokumentumban a [leíró a fürt](service-fabric-cluster-resource-manager-cluster-description.md).
> 
> 

### <a name="capacity-metrics"></a>A kapacitás-metrikák
Minden egyes csomóponttípus betöltésének jelentéséhez az alkalmazásban használni kívánt egyéni kapacitási mérőszámokat adhat hozzá. Betöltésének jelentéséhez a kapacitási mérőszámot használatának részletes ismertetéséért tekintse meg a Service Fabric-fürt Resource Manager-dokumentumok a [a fürt leíró](service-fabric-cluster-resource-manager-cluster-description.md) és [metrikák és a terhelés](service-fabric-cluster-resource-manager-metrics.md).

### <a name="fabric-upgrade-settings---health-polices"></a>Fabric-frissítési beállítások – irányelveinek
Megadhat egyéni irányelveinek fabric frissítése. Ha a fürt automatikus hálófrissítések értékre állította, ezek a szabályzatok a – 1. fázisa az automatikus hálófrissítések legyenek alkalmazva.
Ha a manuális fabric-fürtöt verziófrissítések állított be, ezeket a szabályzatokat alkalmazva minden alkalommal, amikor egy új verzió indítása a rendszer a fabric frissítése a fürt elindítson választja. Ha nem bírálja felül a szabályzatokat, az alapértelmezett értékeket használják.

Adja meg az egyéni állapotházirendeket, vagy tekintse át a jelenlegi beállítások szerint a "fabric frissítése" panel a speciális frissítési beállítások kiválasztásával. Tekintse át a következő képen látható, hogyan. 

![Egyéni házirendek kezelése][HealthPolices]

### <a name="customize-fabric-settings-for-your-cluster"></a>A fürt háló beállításainak testreszabása
Tekintse meg [service fabric-fürt hálóbeállítások](service-fabric-cluster-fabric-settings.md) mi, és hogyan szabhatja testre őket.

### <a name="os-patches-on-the-vms-that-make-up-the-cluster"></a>Operációsrendszer-javítások a fürtöt alkotó virtuális gépeken
Tekintse meg [Patch Orchestration Application](service-fabric-patch-orchestration-application.md) a fürt javítások telepítése a Windows Update gondoskodik az elérhető szolgáltatások folyamatosan összehangolt módon telepíthető. 

### <a name="os-upgrades-on-the-vms-that-make-up-the-cluster"></a>Operációs rendszer verziófrissítései a fürtöt alkotó virtuális gépeken
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
