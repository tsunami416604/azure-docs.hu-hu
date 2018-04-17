---
title: Az Azure Service Fabric-fürt frissítése |} Microsoft Docs
description: A Service Fabric-kódot és/vagy a Service Fabric-fürt, beleértve a fürt frissítési mód, tanúsítványok hozzáadása az alkalmazás-porttal rendelkezik, ennek során az operációsrendszer-javítások, frissítés beállítása futtató konfiguráció frissítése, és így tovább. Mi a számíthat, ha a frissítés történik?
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/10/2017
ms.author: aljo
ms.openlocfilehash: 22dae85c4125c54003f65aba3ef112ffcec9b76d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="upgrade-an-azure-service-fabric-cluster"></a>Az Azure Service Fabric-fürt frissítése
> [!div class="op_single_selector"]
> * [Azure-fürttel](service-fabric-cluster-upgrade.md)
> * [Önálló fürthöz](service-fabric-cluster-upgrade-windows-server.md)
> 
> 

Bármely modern rendszerben megtervezése a bővíthetőség a termék hosszú távú sikert eléréséhez kulcs. Az Azure Service Fabric-fürt, amely a saját, de részben Microsoft által felügyelt erőforrás. A cikkből megtudhatja, mi automatikusan kezeli, és mi beállíthatja saját maga.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>A fabric-verzió a fürtön futó vezérlése
Beállíthatja, hogy a fürt automatikus háló frissítések fogadására, azokat a Microsoft által kiadott, vagy választhat egy támogatott fabric-verzió azt szeretné, hogy a fürt által állnia.

Ehhez a "upgrademode érték" fürt konfigurálása a portál beállítása vagy létrehozásakor, vagy később egy élő fürthöz időpontjában erőforrás-kezelő használatával 

> [!NOTE]
> Ügyeljen arra, hogy az mindig a háló támogatott verzióját futtató fürtre tartsa. Amennyiben, és amikor azt egy új verzióját a service fabric szóló értesítés, az előző verzió legalább attól az időponttól 60 nap után végéhez van megjelölve. az új kiadásokat történik bejelentés [a service fabric csapat blogja a](https://blogs.msdn.microsoft.com/azureservicefabric/). Kiválasztásához, majd az új kiadásban érhető el. 
> 
> 

a fürt fut, a kiadás lejárta előtt 14 nappal olyan állapotesemény jön létre, amely a fürt elhelyezi a figyelmeztetési állapot. A fürt figyelmeztetési állapotban marad, amíg nem támogatott háló verzióra frissíti.

### <a name="setting-the-upgrade-mode-via-portal"></a>A portálon keresztül frissítési mód beállítása
Beállíthatja a fürt automatikus vagy kézi, amikor a fürt létrehozásakor.

![Create_Manualmode][Create_Manualmode]

Állíthat be a fürt automatikus vagy manuális egy élő fürthöz, a kezelés tapasztalatok használatával. 

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>A portálon keresztül manuális módra van állítva a fürthöz egy új verziójára frissít.
Egy új verzióra frissítéséhez ehhez szüksége a rendelkezésre álló verzió válassza a legördülő listából, és mentse. A Fabric-frissítésnek lekérdezi kezdődött el automatikusan. A fürt állapotházirendeket (kombinációjából csomópont állapotát, a fürtben futó összes alkalmazás) betartsák a frissítés során.

Ha a fürt állapotházirendeket nem teljesülnek, a frissítés vissza lesz állítva. Ez a dokumentum további görgetve ezen egyéni házirendek beállításával. 

Miután a problémákat, amelyek a visszaállítási kijavítása, kell kezdeményezése a frissítés újbóli ugyanazokat a lépéseket, mielőtt a következő.

![Manage_Automaticmode][Manage_Automaticmode]

### <a name="setting-the-upgrade-mode-via-a-resource-manager-template"></a>A Resource Manager-sablon használatával a frissítési mód beállítása
A "upgrademode érték" konfiguráció hozzáadása a Microsoft.ServiceFabric/clusters erőforrás-definícióban, és a "clusterCodeVersion" értékre a lent látható módon támogatott fabric-verziók valamelyikét, és majd a sablon telepítéséhez. Az érvényes értékek a "upgrademode érték": "Manual" vagy "Automatikus"

![ARMUpgradeMode][ARMUpgradeMode]

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>A Resource Manager-sablon használatával manuális módra van állítva a fürthöz egy új verziójára frissít.
Manuális üzemmódban a fürt esetén egy új verziójára, módosítsa a "clusterCodeVersion" valamelyik támogatott verzióra, majd telepítse azt. A központi telepítési sablon, a háló frissítési megrúgni lekérdezi kezdődött el automatikusan. A fürt állapotházirendeket (kombinációjából csomópont állapotát, a fürtben futó összes alkalmazás) betartsák a frissítés során.

Ha a fürt állapotházirendeket nem teljesülnek, a frissítés vissza lesz állítva. Ez a dokumentum további görgetve ezen egyéni házirendek beállításával. 

Miután a problémákat, amelyek a visszaállítási kijavítása, kell kezdeményezése a frissítés újbóli ugyanazokat a lépéseket, mielőtt a következő.

### <a name="get-list-of-all-available-version-for-all-environments-for-a-given-subscription"></a>Az összes elérhető verzió listáját beszerzése az adott előfizetéshez tartozó minden környezetben
A következő parancsot, és egy kimeneti hasonló szerezheti.

"supportExpiryUtc" közli a Ha egy adott kiadás lejárati ideje, vagy lejárt. A legújabb kiadás nem rendelkezik érvényes dátum - értéke a "9999-12-31T23:59:59.9999999", amely csak azt jelenti, hogy a lejárati még nem állították.

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

## <a name="fabric-upgrade-behavior-when-the-cluster-upgrade-mode-is-automatic"></a>Háló frissítési működés, ha a fürt frissítési mód automatikus
A Microsoft fenntartja a háló kód és a futó Azure-fürtben lévő konfigurációs. A szoftverfrissítési automatikus figyelt frissítéseket igény szerint alapon végezzük. Ezek a frissítések lehet kódot, konfigurációs vagy mindkettőt. Győződjön meg arról, hogy az alkalmazás szenved, nincs hatása vagy miatt ezek a frissítések minimális hatással kezeli, végezzük a frissítések a következő lépésből áll:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>1. fázis: Frissítés történik az összes fürt házirendek használatával
Ebben a fázisban a frissítéseket egy frissítési tartományt folytatható egyszerre, és a fürtben futó alkalmazások tovább állásidő nélkül. A fürt állapotházirendeket (kombinációjából csomópont állapotát, a fürtben futó összes alkalmazás) betartsák a frissítés során.

Ha a fürt állapotházirendeket nem teljesülnek, a frissítés vissza lesz állítva. Ezután egy e-mailt küld az előfizetés tulajdonosa. Az e-mailt a következő információkat tartalmazza:

* Értesítés, hogy le kellett állítsa vissza a fürtök frissítése.
* Javasolt javító műveleteket, ha van ilyen.
* (N), amíg azt hajtható végre a 2. szakasza napok száma.

Próbálja meg végrehajtani ugyanezt a frissítést néhány alkalommal abban az esetben, ha a megjelenjenek infrastruktúra okok miatt nem sikerült. Az e-mailben küldött napjától n nap elteltével a Folytatás a 2. szakasza.

Ha a fürt állapotházirendeket teljesülnek, a frissítés sikeresnek minősül és befejezettként. Ez akkor fordulhat elő, a kezdeti frissítés, vagy a frissítési ismétlések során ebben a fázisban. Nincs e-mailes megerősítés sikeres futtató van. Ez a ne a szokásos módon folytatódik kivételt kell tekinteni, túl sok e-mailek--küld egy e-maileket küldjön. Várhatóan legtöbb a a fürt frissítési sikeres végrehajtásához az alkalmazások rendelkezésre állásáról befolyásolása nélkül.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>2. fázis: Egy frissítés végrehajtása csak alapértelmezett állapotházirendeket használatával
Ebben a fázisban a házirendek úgy, hogy a száma, melyeket a frissítés elején megfelelő alkalmazások változatlan marad, a frissítési folyamat alatt vannak állítva. 1. fázis, mint a 2. szakasza frissítéseket egy frissítési tartományt folytatható egyszerre, és a fürtben futó alkalmazások tovább állásidő nélkül. A fürt állapotházirendeket (kombinációjából csomópont állapotát, a fürtben futó összes alkalmazás) betartsák a frissítés ideje alatt.

Ha a fürt házirendek érvényben nem teljesülnek, a frissítés vissza lesz állítva. Ezután egy e-mailt küld az előfizetés tulajdonosa. Az e-mailt a következő információkat tartalmazza:

* Értesítés, hogy le kellett állítsa vissza a fürtök frissítése.
* Javasolt javító műveleteket, ha van ilyen.
* (N) csak akkor hajtható végre a 3 nap száma.

Próbálja meg végrehajtani ugyanezt a frissítést néhány alkalommal abban az esetben, ha a megjelenjenek infrastruktúra okok miatt nem sikerült. Egy emlékeztető e-mailt küld néhány napos n naponta mentése előtt. Az e-mailben küldött napjától számított n nap után azt folytassa a 3. Az e-maileket, a 2. szakasza kapni súlyosan kell venni, és a javító műveleteket kell tenni.

Ha a fürt állapotházirendeket teljesülnek, a frissítés sikeresnek minősül és befejezettként. Ez akkor fordulhat elő, a kezdeti frissítés, vagy a frissítési ismétlések során ebben a fázisban. Nincs e-mailes megerősítés sikeres futtató van.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>3. fázis: A frissítés végrehajtása túl szigorú házirendek használatával
Ebben a fázisban a házirendek körétől vannak a frissítés helyett a futó alkalmazások állapotát. Ebben a fázisban végül fürt nagyon kevés frissítés. Ha a fürt lekérdezi az ebben a fázisban, van esély arra, hogy az alkalmazás akkor kerül sérült állapotba, és/vagy rendelkezésre állási elvesznek.

A két fázisban hasonló, a 3 frissítések folytatható, több frissítési tartományt egyszerre.

Ha a fürt állapotházirendeket nem teljesülnek, a frissítés vissza lesz állítva. Próbálja meg végrehajtani ugyanezt a frissítést néhány alkalommal abban az esetben, ha a megjelenjenek infrastruktúra okok miatt nem sikerült. Ezután a fürt rögzítve van, így már nem kap támogatási és/vagy a frissítéseket.

Ezeket az adatokat e-mailt küld az előfizetés tulajdonosa a javító műveleteket együtt. Nem minden fürt olyan állapotba, ha a 3 nem beolvasni várhatóan.

Ha a fürt állapotházirendeket teljesülnek, a frissítés sikeresnek minősül és befejezettként. Ez akkor fordulhat elő, a kezdeti frissítés, vagy a frissítési ismétlések során ebben a fázisban. Nincs e-mailes megerősítés sikeres futtató van.

## <a name="cluster-configurations-that-you-control"></a>Vezérelt fürtkonfigurációk
Mellett meg a fürt frissítési mód, az alábbiakban megtalálja azokat a konfigurációkat, amelyek egy élő fürthöz módosíthatja.

### <a name="certificates"></a>Tanúsítványok
Hozzáadhat új, vagy a fürt és az ügyfél a portálon tanúsítványainak egyszerűen törölje. Tekintse meg [Ez a dokumentum részletes információkra van szüksége](service-fabric-cluster-security-update-certs-azure.md)

![Képernyőkép a tanúsítvány-ujjlenyomatok az Azure portálon.][CertificateUpgrade]

### <a name="application-ports"></a>Alkalmazás-portok
A Load Balancer erőforrás-tulajdonságok a csomóponttípus társított alkalmazás portok módosíthatja. Is használhatja a portált, vagy az erőforrás-kezelő a PowerShell segítségével közvetlenül.

A csomóponttípus összes virtuális gépet az új port megnyitásához tegye a következőket:

1. Adja hozzá egy új mintavételt a megfelelő terheléselosztót.
   
    Ha a fürt a portál használatával telepítette, a terheléselosztók elnevezése "LB-neve az erőforrás csoport NodeTypename", az egyes csomóponttípusok egyet. Mivel a load balancer nevek egyediek-csak egy erőforráscsoporton belül, célszerű, ha egy adott erőforrás csoportban keresse meg őket.
   
    ![Képernyőkép a vizsgálatok ad hozzá egy terhelés-kiegyenlítő a portálon.][AddingProbes]
2. Új szabály hozzáadása a terheléselosztóhoz.
   
    Új szabály az vizsgálatot, amely az előző lépésben létrehozott használatával adja hozzá az azonos terheléselosztóhoz.
   
    ![Új szabály hozzáadása egy terhelés-kiegyenlítő a portálon.][AddingLBRules]

### <a name="placement-properties"></a>Elhelyezési tulajdonságok
Az egyes a csomóponttípusok az alkalmazások használni kívánt egyéni elhelyezési tulajdonságokat adhat hozzá. A NodeType egy alapértelmezett tulajdonság, amely explicit módon hozzáadná nélkül is használható.

> [!NOTE]
> A placement Constraints korlátozásokat, csomópont tulajdonságait, és hogyan adhat meg hozzájuk a részletekért tekintse át a részt "Elhelyezési megkötések és csomópont tulajdonságok" a Service Fabric fürt erőforrás-kezelő dokumentumban a [leíró a fürt](service-fabric-cluster-resource-manager-cluster-description.md).
> 
> 

### <a name="capacity-metrics"></a>A kapacitás metrikák
Az egyes csomóponttípus betöltésének jelentéséhez az alkalmazásban használni kívánt egyéni kapacitási mérőszámokat adhat hozzá. A teljesítmény-mérőszámait betöltésének jelentéséhez használatára vonatkozó részleteket tekintse meg a Service Fabric fürt erőforrás-kezelő dokumentumok a [a fürt leíró](service-fabric-cluster-resource-manager-cluster-description.md) és [metrikák és a betöltés](service-fabric-cluster-resource-manager-metrics.md).

### <a name="fabric-upgrade-settings---health-polices"></a>Frissítési beállítások fabric - irányelveinek
Megadhat egyéni irányelveinek fabric frissítéséhez. Ha a fürt automatikus fabric frissítéskezelésének adott meg, ezek a házirendek a-1. szakasza az automatikus fabric frissítéskezelésének beolvasása alkalmazza.
Ha meg van a fürt manuális háló frissítéseket, majd ezek a házirendek érvényben minden alkalommal, amikor egy új verziót indítására, a rendszer a fabric frissítés a fürt indítsa választja. Ha nem bírálja felül a házirendek, az alapértelmezett beállításokat használja.

Adja meg az egyéni házirendek, vagy a "fabric-frissítésnek" panel alatt a jelenlegi beállítások áttekintéséhez válassza ki a Speciális beállítások frissítése. Tekintse át az alábbi képen módjáról. 

![Egyéni házirendek kezelése][HealthPolices]

### <a name="customize-fabric-settings-for-your-cluster"></a>A fürt háló beállításainak testreszabása
Tekintse meg [service fabric-fürt hálóbeállításokat](service-fabric-cluster-fabric-settings.md) mi, és hogyan szabhatja azokat.

### <a name="os-patches-on-the-vms-that-make-up-the-cluster"></a>A virtuális gépeken a fürtöt alkotó operációsrendszer-javítások
Tekintse meg [javítás Vezénylési alkalmazás](service-fabric-patch-orchestration-application.md) javítások telepítését a Windows Update orchestrated módon, az elérhető szolgáltatások mindig megőrzi a fürtön is telepíthető. 

### <a name="os-upgrades-on-the-vms-that-make-up-the-cluster"></a>Az operációs rendszer frissítései a a fürtöt alkotó virtuális gépeken
Ha frissítenie kell a virtuális gépek a fürt operációsrendszer-lemezképet, tegye azt egy virtuális gép egyszerre. Az Ön felelőssége ehhez a frissítéshez--jelenleg nincs automatizálva ehhez.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan szabhatja testre az egyes a [szolgáltatás fabric fürt háló beállításai](service-fabric-cluster-fabric-settings.md)
* Megtudhatja, hogyan [a fürt vagy horizontális](service-fabric-cluster-scale-up-down.md)
* További tudnivalók [alkalmazásfrissítések](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
