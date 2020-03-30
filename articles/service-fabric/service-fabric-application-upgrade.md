---
title: A Service Fabric alkalmazásfrissítése
description: Ez a cikk egy bevezetést tartalmaz a Service Fabric-alkalmazások frissítésének, beleértve a frissítési módok kiválasztását és az állapot-ellenőrzések elvégzését.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 2dc484b49c5250510e5f018cbbc2da107573d452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259044"
---
# <a name="service-fabric-application-upgrade"></a>A Service Fabric alkalmazásfrissítése
Az Azure Service Fabric-alkalmazások szolgáltatások gyűjteménye. A frissítés során a Service Fabric összehasonlítja az új [alkalmazás jegyzékfájlját](service-fabric-application-and-service-manifests.md) az előző verzióval, és meghatározza, hogy az alkalmazás mely szolgáltatások igényelnek frissítéseket. A Service Fabric összehasonlítja a szolgáltatásjegyzékekben lévő verziószámokat az előző verzió verziószámaival. Ha egy szolgáltatás nem változott, a szolgáltatás nem frissül.

## <a name="rolling-upgrades-overview"></a>Működés közbeni frissítések – áttekintés
Működés közbeni alkalmazásfrissítés, a frissítés végrehajtása szakaszokban történik. A frissítés minden egyes fázisban a fürt csomópontjainak egy részhalmazára, az úgynevezett frissítési tartományra vonatkozik. Ennek eredményeképpen az alkalmazás továbbra is elérhető a frissítés során. A frissítés során a fürt a régi és az új verziók keverékét is tartalmazhatja.

Ezért a két verziónak előre és hátra kompatibilisnek kell lennie. Ha nem kompatibilisek, az alkalmazás rendszergazdája felelős a többfázisú frissítés megrendezéséért a rendelkezésre állás fenntartása érdekében. Többfázisú frissítés során az első lépés az alkalmazás egy köztes verziójára való frissítés, amely kompatibilis az előző verzióval. A második lépés a végleges verzió frissítése, amely megszakítja a kompatibilitást a frissítés előtti verzióval, de kompatibilis a köztes verzióval.

A fürt konfigurálásakor a fürtjegyzékben a frissítési tartományok vannak megadva. A frissítési tartományok nem kapnak frissítéseket adott sorrendben. A frissítési tartomány egy alkalmazás központi telepítésének logikai egysége. A frissítési tartományok lehetővé teszik, hogy a szolgáltatások a frissítés során is magas rendelkezésre állásban maradjanak.

Nem folyamatos frissítések akkor lehetségesek, ha a frissítés a fürt összes csomópontjára vonatkozik, ami akkor áll fenn, ha az alkalmazás csak egy frissítési tartománnyal rendelkezik. Ez a megközelítés nem ajánlott, mivel a szolgáltatás leáll, és nem érhető el a frissítés időpontjában. Emellett az Azure nem nyújt semmilyen garanciát, ha egy fürt csak egy frissítési tartománnyal van beállítva.

A frissítés befejezése után az összes szolgáltatás és replikák(példányok) maradnak ugyanabban a verzióban, azaz, ha a frissítés sikeres, akkor frissül az új verzióra; ha a frissítés sikertelen, és visszalesz állítva, azok visszalesznek állítva a régi verzióra.

## <a name="health-checks-during-upgrades"></a>Állapot-ellenőrzések a frissítések során
A frissítéshez állapotházirendeket kell beállítani (vagy alapértelmezett értékeket lehet használni). A frissítés akkor sikeresnek minősül, ha az összes frissítési tartomány a megadott időtúltartományokon belül frissül, és ha az összes frissítési tartomány kifogástalanállapotúnak minősül.  A kifogástalan állapotú frissítési tartomány azt jelenti, hogy a frissítési tartomány megfelelt az állapotházirendben megadott összes állapotellenőrzésnek. Például egy állapotházirend megrendelheti, hogy egy alkalmazáspéldányon belüli összes szolgáltatás *kifogástalan állapotú,* a Service Fabric által meghatározott állapot.

Állapotházirendek és ellenőrzések a Service Fabric frissítése során a szolgáltatás és az alkalmazás független. Ez azt, hogy nem végeznek szolgáltatásspecifikus teszteket.  Például a szolgáltatás átviteli követelmény, de a Service Fabric nem rendelkezik az átviteli adatok ellenőrzéséhez. Az [elvégzett ellenőrzéseket az állapotcikkekben](service-fabric-health-introduction.md) található. A frissítés során bekövetkező ellenőrzések közé tartoznak az alkalmazáscsomag megfelelő másolásának, a példány indítása és így tovább.

Az alkalmazás állapota az alkalmazás gyermekentitásainak összesítése. Röviden, service fabric kiértékeli az alkalmazás állapotát az alkalmazáson keresztül jelentett állapoton keresztül. Azt is értékeli az alkalmazás összes szolgáltatásának állapotát így. A Service Fabric tovább értékeli az alkalmazásszolgáltatások állapotát a gyermekek állapotának összesítésével, például a szolgáltatás replika. Miután az alkalmazás állapotházirendje teljesült, a frissítés folytatódhat. Ha az állapotházirend sérül, az alkalmazás frissítése sikertelen lesz.

## <a name="upgrade-modes"></a>Frissítési módok
Az alkalmazásfrissítéshez ajánlott mód a figyelt mód, amely a gyakran használt mód. A Figyelt mód egy frissítési tartományon hajtja végre a frissítést, és ha az összes állapotellenőrzés megfelel (a megadott házirend szerint), automatikusan továbblép a következő frissítési tartományra.  Ha az állapotellenőrzések sikertelenek, és/vagy időtúloldalba kerül, a frissítés vagy visszaáll a frissítési tartományra, vagy a mód nem figyelt manuálisra változik. A frissítés konfigurálásával a sikertelen frissítések két üzemmódjának egyikét választhatja ki. 

A nem figyelt manuális üzemmód manuális beavatkozást igényel a frissítési tartomány minden frissítése után, hogy a következő frissítési tartományban elinduljon a frissítés. Nem Service Fabric állapot-ellenőrzéseket végeznek. A rendszergazda a frissítés megkezdése előtt elvégzi az állapot- vagy állapotellenőrzéseket a következő frissítési tartományban.

## <a name="upgrade-default-services"></a>Alapértelmezett szolgáltatások frissítése
Az [alkalmazásjegyzékben](service-fabric-application-and-service-manifests.md) definiált egyes alapértelmezett szolgáltatásparaméterek is frissíthetők az alkalmazásfrissítés részeként. Csak az [Update-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) szolgáltatáson keresztül módosítandó szolgáltatásparaméterek módosíthatók a frissítés részeként. Az alapértelmezett szolgáltatások módosításának viselkedése az alkalmazás frissítése során a következő:

1. Az új alkalmazásjegyzékben olyan alapértelmezett szolgáltatások jönnek létre, amelyek még nem léteznek a fürtben.
2. Az előző és az új alkalmazásjegyzékekben is létező alapértelmezett szolgáltatások frissülnek. Az új alkalmazásjegyzék alapértelmezett szolgáltatásának paraméterei felülírják a meglévő szolgáltatás paramétereit. Az alkalmazás frissítése automatikusan visszaáll, ha egy alapértelmezett szolgáltatás frissítése sikertelen.
3. Az új alkalmazásjegyzékben nem létező alapértelmezett szolgáltatások törlődnek, ha léteznek a fürtben. **Vegye figyelembe, hogy egy alapértelmezett szolgáltatás törlése a szolgáltatás összes állapotának törlését eredményezi, és nem vonható vissza.**

Az alkalmazásfrissítés visszaállításakor az alapértelmezett szolgáltatásparaméterek a frissítés megkezdése előtt visszaállnak a régi értékekre, de a törölt szolgáltatások nem hozhatók létre újra a régi állapotukkal.

> [!TIP]
> Az [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) fürt konfigurációs beállításának *igaznak* kell lennie a fenti 2. és 3. szabály engedélyezéséhez (alapértelmezett szolgáltatásfrissítés és -törlés). Ez a szolgáltatás a Service Fabric 5.5-ös verziójától kezdve támogatott.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Több alkalmazás frissítése HTTPS-végponttal
A HTTP**S**használatakor ügyeljen arra, hogy ne használja ugyanazt a **portot** ugyanazon alkalmazás különböző példányaihoz. Ennek az az oka, hogy a Service Fabric nem lesz képes frissíteni a tanúsítványt az egyik alkalmazáspéldányok. Ha például az 1-es vagy a 2-es alkalmazás is frissíteni szeretné az 1-es tanúsítványt a 2-es tanúsítványra. Amikor a frissítés történik, a Service Fabric előfordulhat, hogy megtisztította a cert 1 regisztráció http.sys annak ellenére, hogy a másik alkalmazás továbbra is használja. Ennek megakadályozása érdekében a Service Fabric észleli, hogy már van egy másik alkalmazáspéldány regisztrálva a porton a tanúsítvánnyal (a http.sys miatt), és nem felel meg a művelet.

Ezért a Service Fabric nem támogatja két különböző szolgáltatás frissítését **ugyanazon port** használatával különböző alkalmazáspéldányokban. Más szóval nem használhatja ugyanazt a tanúsítványt ugyanazon a porton lévő különböző szolgáltatásokon. Ha ugyanazon a porton megosztott tanúsítvánnyal kell rendelkeznie, meg kell győződnie arról, hogy a szolgáltatások elhelyezési korlátozásokkal rendelkező különböző gépeken vannak elhelyezve. Vagy fontolja meg a Service Fabric dinamikus portok, ha lehetséges, minden egyes alkalmazáspéldányok használata. 

Ha a https szolgáltatással sikertelen frissítési hibát lát, a következő hibaüzenet jelenik meg: "A Windows HTTP Server API nem támogat több tanúsítványt a porttal rendelkező alkalmazásokhoz."

## <a name="application-upgrade-flowchart"></a>Alkalmazásfrissítési folyamatábra
Az ezt a bekezdést követő folyamatábra segíthet a Service Fabric-alkalmazások frissítési folyamatának megértésében. A folyamat különösen azt írja le, hogy az idő-out, beleértve *a HealthCheckStableDuration*, HealthCheckRetryTimeout és *UpgradeHealthCheckInterval,* segítségével szabályozhatja, hogy az egyik frissítési tartományban a frissítés sikeresnek vagy sikertelennek minősüljön.In the flow describes how the time-outs, including HealthCheckStableDuration , *HealthCheckRetryTimeout*, and UpgradeHealthCheckInterval , help control when the upgrade in one update domain is considered a success or a failure.

![A Service Fabric-alkalmazás frissítési folyamata][image]

## <a name="next-steps"></a>További lépések
[Az alkalmazás Visual Studio használatával történő frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti az alkalmazás frissítésén a Visual Studió használatával.

[Az alkalmazás PowerShell használatával történő frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti a PowerShell használatával az alkalmazásfrissítésen.

A frissítési paraméterek használatával szabályozhatja, hogy az alkalmazás hogyan [frissítsen.](service-fabric-application-upgrade-parameters.md)

Tegye kompatibilissé az alkalmazásfrissítéseket az [adatszerializálás](service-fabric-application-upgrade-data-serialization.md)használatának elsajátításával.

A speciális funkciók használata az alkalmazás frissítése során a [Speciális témakörökre](service-fabric-application-upgrade-advanced.md)hivatkozva.

Az alkalmazásfrissítések gyakori problémáinak megoldása az [Alkalmazásfrissítések hibaelhárítása](service-fabric-application-upgrade-troubleshooting.md)című témakör lépéseire hivatkozva.

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
