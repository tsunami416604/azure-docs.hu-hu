---
title: Azure Bejárati ajtó - forgalomútválasztási módszerek | Microsoft dokumentumok
description: Ez a cikk segít megérteni a Bejárati ajtó által használt különböző forgalom-útválasztási módszereket
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 69ef68dafc2385eb5614179c3d04265250383104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471540"
---
# <a name="front-door-routing-methods"></a>Bejárati ajtó útválasztási módszerei

Az Azure Bejárati ajtó támogatja a különböző forgalom-útválasztási módszerek meghatározásához, hogyan irányíthatja a HTTP/HTTPS-forgalmat a különböző szolgáltatás végpontok. Ha az ügyfélkérelmek mindegyike eléri a bejárati ajtót, a konfigurált útválasztási módszer lesz alkalmazva annak biztosítására, hogy a kérelmek a legjobb háttérpéldányra továbbítódnak. 

A bejárati ajtóban négy fő fogalma van a forgalomútvonalnak:

* ** [Késés:](#latency)** A késés-alapú útválasztás biztosítja, hogy a kérelmek küldése a legalacsonyabb késésháttér-támogatott a érzékenységi tartományon belül elfogadható. Alapvetően a felhasználói kérelmek küldése a "legközelebbi" háttérrendszerek hálózati késés tekintetében.
* ** [Prioritás](#priority):** A prioritásokat a különböző háttérrendszerekhez rendelheti, ha elsődleges szolgáltatás-háttérszolgáltatást szeretne használni az összes forgalomhoz, és biztonsági mentéseket biztosít, ha az elsődleges vagy a biztonsági mentési háttérrendszerek nem érhetők el.
* ** [Súlyozott:](#weighted)** Súlyokat rendelhet a különböző háttérrendszerekhez, ha a forgalmat a háttérrendszerek egy készletében szeretné elosztani, akár egyenletesen, akár súlyegyütthatók szerint.
* **Munkamenet-affinitás:** Beállíthatja a munkamenet-affinitást az előtér-állomások vagy tartományok számára, ha azt szeretné, hogy a felhasználó további kérései ugyanabba a háttérrendszerbe legyenek elküldve, amíg a felhasználói munkamenet aktív, és a háttérpéldány továbbra is kifogástalan állapotú állapotmintavételek alapján jelenti az állapotát. 

A Front Door összes konfigurációja tartalmazza a háttérrendszer állapotának monitorozását és az automatizált azonnali globális feladatátvételt. További információ: [Front Door Backend Monitoring](front-door-health-probes.md). A bejárati ajtó beállítható úgy, hogy egyetlen útválasztási módszer alapján dolgozzon, és az alkalmazás igényeitől függően több vagy az összes ilyen útválasztási módszert használhat az optimális útválasztási topológia létrehozásához.

## <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Legkisebb késések alapú forgalom-útválasztás

A háttérrendszerek üzembe helyezése a világ két vagy több helyén javíthatja számos alkalmazás válaszképességét azáltal, hogy a forgalmat a végfelhasználókhoz "legközelebb" lévő helyre irányítja. A bejárati ajtajának konfigurációjához az alapértelmezett forgalom-útválasztási módszer továbbítja a végfelhasználók tól érkező kérelmeket a legközelebbi háttérrendszerre a bejárati ajtaját tartalmazó környezetből, amely megkapta a kérést. Az Azure Front Door Anycast architektúrájával kombinálva ez a megközelítés biztosítja, hogy minden végfelhasználó a tartózkodási helyük alapján személyre szabott maximális teljesítményt kapjon.

A "legközelebbi" háttérprogram nem feltétlenül legközelebb a földrajzi távolság alapján mért. Ehelyett a Bejárati ajtó a hálózati késés mérésével határozza meg a legközelebbi háttérrendszereket. További információ a [Front Door útválasztási architektúrájáról.](front-door-routing-architecture.md) 

Az alábbiakban az általános döntési folyamat:

| Elérhető háttérrendszerek | Prioritás | Késésjel (egészségügyi vizsgálat alapján) | Súlyok |
|-------------| ----------- | ----------- | ----------- |
| Először válassza ki az összes támogatott és kifogástalan állapotú (200 OK) az állapotminta összes háttérrendszerét. Hat a, B, B, C, D, E és F háttérközpont van, és köztük c nem kifogástalan, az E pedig le van tiltva. Így a rendelkezésre álló háttérrendszerek listája A, B, D és F.  | Ezután a rendelkezésre álló kontrórendszerek közül a legfontosabb fontosságú háttérrendszerek kerülnek kiválasztásra. Mondjuk, az A, B és D háttér-háttér-1-es, az F háttéralátámasztás pedig 2-es prioritással rendelkezik. Így a kiválasztott háttérrendszerek A, B és D lesznek.| Válassza ki a háttérrendszerek etkéséstartomány (a legkisebb késés & késés érzékenysége ms megadott). Tegyük, hogy ha A 15 ms, b 30 ms, D pedig 60 ms-ra van a bejárati ajtó környezettől, ahol a kérelem landolt, és a késés érzékenysége 30 ms, akkor a legalacsonyabb késleltetésű készlet az A és B háttérprogramból áll, mivel D 30 ms-nál távolabb van a legközelebbi háttérprogramtól, amely A. | Végül, bejárati ajtó lesz ciklikus robin a forgalom között a végső kiválasztott medence háttérrendszerek a megadott súlyok aránya. Tegyük, hogy ha az A háttér-háttér-irányító súlya 5, a B háttéroldal súlya pedig 8, akkor a forgalom 5:8 arányban lesz elosztva az A és B háttérrendszerek között. |

>[!NOTE]
> Alapértelmezés szerint a késésérzékenységi tulajdonság 0 ms-ra van állítva, azaz mindig továbbítsa a kérést a leggyorsabban elérhető háttérrendszernek.


## <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Prioritásalapú forgalom-útválasztás

A szervezet gyakran szeretne megbízhatóságot biztosítani a szolgáltatásaiszámára egy vagy több biztonsági mentési szolgáltatás telepítésével arra az esetre, ha elsődleges szolgáltatásuk megszakadna. Az egész iparágban ezt a topológiát aktív/készenléti vagy aktív/passzív üzembe helyezési topológiának is nevezik. A "Prioritás" forgalom-útválasztási módszer lehetővé teszi az Azure-ügyfelek számára, hogy könnyen megvalósítsák ezt a feladatátvételi mintát.

Az alapértelmezett bejárati ajtó a háttérrendszerek azonos prioritású listáját tartalmazza. Alapértelmezés szerint a Bejárati ajtó csak a legfelső prioritású háttérrendszereknek (a prioritás legalacsonyabb értékének) küld forgalmat, azaz a háttérrendszerek elsődleges készletének. Ha az elsődleges háttérrendszerek nem érhetők el, a bejárati ajtó a forgalmat a másodlagos háttérrendszerek másodlagos készletéhez irányítja (a második legalacsonyabb prioritási érték). Ha az elsődleges és a másodlagos háttérrendszerek nem érhetők el, a forgalom megy a harmadik, és így tovább. A háttérprogram rendelkezésre állása a konfigurált állapot (engedélyezve vagy letiltva) és a folyamatos háttér-állapot állapot az állapotminták határozza meg.

### <a name="configuring-priority-for-backends"></a>Prioritás konfigurálása háttérrendszerekhez

A bejárati ajtó konfigurációján belül a háttérrendszerek mindegyike rendelkezik egy "Priority" nevű tulajdonsággal, amely 1 és 5 közötti szám lehet. Az Azure Bejárati ajtajával konfigurálja a háttér-prioritást explicit módon használja ezt a tulajdonságot minden háttérszolgáltatáshoz. Ez a tulajdonság 1 és 5 közötti érték. Az alacsonyabb értékek magasabb prioritást jelentenek. A háttérrendszerek megoszthatják a prioritási értékeket.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Súlyozott forgalom-útválasztási módszer
A "Súlyozott" forgalom-útválasztási módszer lehetővé teszi a forgalom egyenletes elosztását vagy egy előre meghatározott súlyozás használatát.

A Súlyozott forgalom-útválasztási módszerben súlyt rendelhet az egyes háttérrendszerhez a háttérkészlet bejárati ajtajának konfigurációjában. A súlyok 1 és 1000 közötti egész számok. Ez a paraméter '50' alapértelmezett súlyt használ.

Az elfogadott késésérzékenységen belül rendelkezésre álló háttérrendszerek listája között (a megadott módon) a forgalom ciklikus multiplexelési mechanizmusban kerül elosztásra a megadott súlyok arányában. Ha a késés érzékenysége 0 ezredmásodpercre van állítva, akkor ez a tulajdonság csak akkor lép érvénybe, ha két háttérkapcsolat azonos hálózati késésű. 

A súlyozott módszer lehetővé tesz néhány hasznos forgatókönyvet:

* **Fokozatos alkalmazásfrissítés:** A forgalom bizonyos százalékát rendelje n ki egy új háttérrendszerhez való útvonalhoz, és fokozatosan növelje a forgalmat az idő múlásával, hogy más háttérrendszerekkel egyenrangúvá tegye azt.
* **Alkalmazások áttelepítése az Azure-ba:** Hozzon létre egy háttérkészletet az Azure-ral és a külső háttérrendszerekkel. Állítsa be a háttérrendszerek súlyát az új háttérrendszerek előnyben részesítéséhez. Ezt fokozatosan beállíthatja, kezdve azzal, hogy az új háttérrendszerek le vannak tiltva, majd a legalacsonyabb súlyokat rendeli hozzájuk, és lassan olyan szintre növeli, ahol a legnagyobb forgalmat bonyolítják. Ezután végül letiltja a kevésbé preferált háttérrendszereket, és eltávolítja őket a készletből.  
* **Felhő-bursting további kapacitás:** Gyorsan bővítheti a helyszíni üzembe helyezésa a felhőbe azáltal, hogy a Bejárati ajtó mögé helyezi. Ha további kapacitásra van szüksége a felhőben, további háttérrendszereket adhat hozzá vagy engedélyezhet, és megadhatja, hogy a forgalom melyik része kerül az egyes háttérrendszerekhez.

## <a name="session-affinity"></a><a name = "affinity"></a>Munkamenet-affinitás
Alapértelmezés szerint munkamenet-affinitás nélkül a Bejárati ajtó továbbítja az ugyanabból az ügyféltől származó kérelmeket különböző háttérrendszerekre a terheléselosztási konfiguráció alapján, különösen azért, mert a különböző háttérrendszerek késése idobe változik, vagy ha ugyanabból a különböző kérésből származó különböző kérelmek felhasználó egy másik bejárati ajtókörnyezetben landol. Egyes állapotalapú alkalmazások vagy forgatókönyvek esetében viszont előnyösebb, ha az ugyanazon felhasználótól érkező későbbi kérelmek ugyanarra a háttérrendszerre kerülnek, amely az első kérelmet feldolgozta. A cookie-alapú munkamenet-affinitás akkor hasznos, ha ugyanazon a háttérrendszeren szeretne tartani egy felhasználói munkamenetet. A Bejárati ajtó által kezelt cookie-k használatával az Azure Bejárati ajtaját irányíthatja a felhasználói munkamenetből származó további forgalmat ugyanarra a háttérrendszerre feldolgozásra, amíg a háttérrendszer kifogástalan állapotú, és a felhasználói munkamenet még nem járt le. 

A munkamenet-affinitást az előtérbeli gazdagép szintjén engedélyezheti minden konfigurált tartomány (vagy altartomány) számára. Az engedélyezés után a Front Door hozzáad egy cookie-t a felhasználói munkamenethez. A cookie-alapú munkamenet-affinitás lehetővé teszi a Front Door számára, hogy azonos IP-cím esetén is azonosítsa a különböző felhasználókat, amely egyenletesebb forgalomelosztást tesz lehetővé a különböző háttérrendszerek között.

A cookie élettartama megegyezik a felhasználói munkamenet élettartamával, mivel a Front Door jelenleg csak a munkameneti cookie-kat támogatja. 

> [!NOTE]
> A nyilvános proxyk zavarhatják a munkamenet-affinitást. Ennek az az oka, hogy a munkamenet létrehozásához a Bejárati ajtónak munkamenet-affinitáscookie-t kell hozzáadnia a válaszhoz, amely nem végezhető el, ha a válasz gyorsítótárazható, mivel megzavarná az ugyanazt az erőforrást kérő többi ügyfél cookie-jait. Ennek érdekében a munkamenet-affinitás **nem** jön létre, ha a háttérrendszer gyorsítótárazható választ küld, amikor ezt megkísérli. Ha a munkamenet már létrejött, nem számít, ha a válasz a háttérrendszer gyorsítótárazható.
> A munkamenet-affinitás a következő körülmények között jön létre, **kivéve, ha** a válasz HTTP 304-es állapotkóddal rendelkezik:
> - A válasz meghatározott értékeket ```Cache-Control``` állított be a fejléchez, amely megakadályozza a gyorsítótárazást, például "privát" vagy nem-üzlet.The response has specific values set for the header that prevents caching, such as "private" or no-store".
> - A válasz ```Authorization``` olyan fejlécet tartalmaz, amely még nem járt le.
> - A válasz http 302 állapotkóddal rendelkezik.

## <a name="next-steps"></a>További lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
