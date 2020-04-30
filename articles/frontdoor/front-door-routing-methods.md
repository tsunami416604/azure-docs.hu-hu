---
title: Azure bejárati ajtó – forgalom-útválasztási módszerek | Microsoft Docs
description: Ez a cikk segít megismerni a bejárati ajtó által használt különböző forgalom-útválasztási módszereket
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
ms.openlocfilehash: b7dd00d28ecfe844094677e0ae19f4fd359d97d0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687807"
---
# <a name="front-door-routing-methods"></a>A bejárati ajtó útválasztási módszerei

Az Azure bevezető ajtaja számos forgalom-útválasztási módszert támogat a HTTP/HTTPS-forgalom különböző szolgáltatási végpontokra való továbbításának meghatározásához. A bejárati ajtóhoz tartozó összes ügyfél-kérelem esetében a konfigurált útválasztási módszer lesz alkalmazva, hogy a kérések továbbítása a legjobb háttér-példányra történjen. 

Négy fő fogalmat kell megnyitnia a forgalom útválasztásához a bejárati ajtóban:

* ** [Késés](#latency):** A késleltetés-alapú útválasztás biztosítja, hogy a rendszer a kérelmeket az érzékenységi tartományon belül elfogadható legalacsonyabb késési szintekre küldje. A felhasználói kérelmeket alapvetően a "legközelebbi" háttérrendszer-csoportba küldik a hálózati késés tekintetében.
* ** [Prioritás](#priority):** A különböző háttérekhez is rendelhet prioritásokat, ha az elsődleges szolgáltatási hátteret szeretné használni az összes forgalomhoz, és biztonsági mentéseket biztosít, ha az elsődleges vagy a biztonsági mentési háttérrendszer nem érhető el.
* ** [Súlyozott](#weighted):** A súlyok kiosztása a különböző háttérrendszer esetében történik, ha a forgalmat háttérrendszer-készleten keresztül szeretné terjeszteni, akár egyenletesen, akár súlyozási együtthatók alapján.
* ** [Munkamenet-affinitás](#affinity):** Beállíthatja a munkamenet-affinitást a előtér-gazdagépekhez vagy-tartományokhoz, ha azt szeretné, hogy a felhasználótól érkező további kérések ugyanarra a háttérre legyenek küldve, amíg a felhasználói munkamenet továbbra is aktív, és a backend-példány továbbra is Kifogástalan állapotba állítja a Health-mintavételek alapján. 

A Front Door összes konfigurációja tartalmazza a háttérrendszer állapotának monitorozását és az automatizált azonnali globális feladatátvételt. További információ: az [első ajtó háttér-figyelése](front-door-health-probes.md). A bejárati ajtót úgy is konfigurálhatja, hogy egyetlen útválasztási módszer alapján működjön, és az alkalmazás igényeinek megfelelően több vagy mindegyik útválasztási módszert is használhat az optimális útválasztási topológia létrehozásához.

## <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Legalacsonyabb késések alapú forgalom – Útválasztás

A háttérrendszer két vagy több helyen történő üzembe helyezése a világ számos pontján javíthatja a különböző alkalmazások rugalmasságát azáltal, hogy az útválasztási forgalmat a végfelhasználók számára legközelebb eső helyre irányítja. Az alapértelmezett forgalom – a bejárati ajtó konfigurációjának útválasztási módszere továbbítja a végfelhasználók kéréseit a legközelebb háttérbe a kérést befogadó háztól. Ez a megközelítés biztosítja, hogy a végfelhasználók a helyük alapján személyre szabott maximális teljesítményt kapjanak.

A "legközelebb" háttér nem feltétlenül a legközelebbi földrajzi távolság alapján mérve. Ehelyett a bejárati ajtó határozza meg a legközelebbi háttereket a hálózati késés mérésével. További információ a [bejárati ajtó útválasztási architektúráján](front-door-routing-architecture.md). 

Alább látható a teljes döntési folyamat:

| Elérhető háttérrendszer | Prioritás | Késési jel (állapot-mintavétel alapján) | Súlyozással |
|-------------| ----------- | ----------- | ----------- |
| Először válassza ki az összes engedélyezett és kifogástalan állapotú (200 OK) háttérrendszer állapotát. Tegyük fel, hogy hat háttér van A, B, C, D, E és F, és ezek közül C nem kifogástalan, és az E le van tiltva. Így a rendelkezésre álló hátterek listája A, B, D és F.  | Ezután a legfontosabb prioritási háttérrendszer van kiválasztva a rendelkezésre állók közül. Tegyük fel, hogy az A, A B és A D háttér 1. prioritással rendelkezik, a háttérbeli F prioritása pedig 2. Így A kiválasztott háttérrendszer A, B és D lesz.| Válassza ki a késési tartománnyal rendelkező háttereket (a megadott ms-os késleltetési idő & késési érzékenysége). Tegyük fel, hogy ha a 15 MS, B 30 MS, és 60 D az a bejárati ajtót, ahol a kérelem kirakodása és a késés érzékenysége 30 MS, akkor a legalacsonyabb késési készlet az A és A B háttérből áll, mivel a D az a legközelebbi háttértől számított 30 MS távolságon kívül esik. | Végül a bejárati ajtó a kiválasztott súlyok arányában a háttérrendszer utolsó kijelölt készlete közötti adatforgalomra kerekíti a forgalmat. Tegyük fel, hogy ha az A backend 5 és a "B" háttérbeli súlyozása 8, akkor a forgalom a 5:8 arányban lesz elosztva az A és A B háttérrendszer között. |

>[!NOTE]
> Alapértelmezés szerint a késleltetési érzékenység tulajdonság értéke 0 MS, azaz mindig továbbítja a kérést a leggyorsabb rendelkezésre állási háttérnek.


## <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Prioritáson alapuló forgalom – Útválasztás

A szervezet gyakran szeretne megbízhatóságot biztosítani a szolgáltatásaihoz egy vagy több biztonsági mentési szolgáltatás üzembe helyezése esetén, ha az elsődleges szolgáltatás leáll. Az iparágban ezt a topológiát aktív/készenléti vagy aktív/passzív telepítési topológiának is nevezzük. A "prioritás" forgalom – az útválasztási módszer lehetővé teszi, hogy az Azure-ügyfelek könnyedén implementálják ezt a feladatátvételi mintát.

Az alapértelmezett bejárati ajtó a háttérrendszer azonos prioritású listáját tartalmazza. Alapértelmezés szerint a bevezető ajtó csak a legfontosabb prioritási háttérrendszer (a prioritás legalacsonyabb értéke) esetében küld forgalmat, azaz a háttérrendszer elsődleges készletét. Ha az elsődleges háttér nem érhető el, a bevezető ajtó a háttérrendszer másodlagos készletére irányítja át a forgalmat (a prioritás második legalacsonyabb értéke). Ha az elsődleges és a másodlagos háttérrendszer nem érhető el, a forgalom a harmadikra kerül, és így tovább. A háttérrendszer rendelkezésre állása a beállított állapoton (engedélyezve vagy letiltva), valamint az állapotú Szondák által meghatározott aktuális háttérbeli állapoton alapul.

### <a name="configuring-priority-for-backends"></a>A háttérrendszer prioritásának konfigurálása

Az első ajtó konfigurációjában a háttér-készletben található összes háttérrendszer egy "priority" nevű tulajdonsággal rendelkezik, amely 1 és 5 közötti szám lehet. Az Azure bejárati ajtót használva az egyes háttérrendszer esetében explicit módon konfigurálja a háttér prioritását. Ez a tulajdonság 1 és 5 közötti érték. Az alacsonyabb értékek magasabb prioritást jelentenek. A hátterek megoszthatják a prioritási értékeket.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Súlyozott forgalom – útválasztási módszer
A "súlyozott" forgalom-útválasztási módszer lehetővé teszi, hogy egyenletesen ossza el a forgalmat, vagy használjon előre definiált súlyozást.

A súlyozott forgalom – útválasztási módszernél a háttérbeli készlet előtérben lévő konfigurációjában minden egyes háttérhez hozzá kell rendelni a súlyozást. A súlyok 1 és 1000 közötti egész számok. Ez a paraméter az alapértelmezett "50" súlyozást használja.

Az elfogadott késési érzékenységen belüli elérhető háttérrendszer (ahogy meg van adva) többek között a forgalom a megadott súlyok arányában egy ciklikus időszeletelési mechanizmusban kerül kiosztásra. Ha a késési érzékenység 0 ezredmásodpercre van beállítva, akkor ez a tulajdonság nem lép érvénybe, kivéve, ha két háttérrel rendelkezik ugyanazzal a hálózati késéssel. 

A súlyozott módszer néhány hasznos forgatókönyvet is lehetővé tesz:

* **Fokozatos alkalmazás frissítése**: az új háttér felé irányuló forgalom százalékos arányának kiosztása, és az idő múlásával fokozatosan növelheti a forgalmat a többi háttérrel való egyenrangúság érdekében.
* **Alkalmazások áttelepítése az Azure**-ba: hozzon létre egy háttér-készletet az Azure-ban és a külső háttérrendszer használatával. Állítsa be a háttérrendszer súlyozását úgy, hogy az új háttérrendszer legyen előnyben részesíteni. Fokozatosan beállíthatja, hogy az új háttérrendszer le legyen tiltva, majd hozzárendeli a legalacsonyabb súlyozást, és lassan növelje azokat a szinteket, ahol a legtöbb forgalmat megkezdik. Végül tiltsa le a kevésbé preferált háttereket, és távolítsa el őket a készletből.  
* **Felhőbeli felskálázás a további kapacitáshoz**: gyorsan kiterjesztheti a helyszíni üzembe helyezést a felhőbe azáltal, hogy az első ajtót helyezi el. Ha további kapacitásra van szüksége a felhőben, több háttér hozzáadására és engedélyezésére is lehetősége van, és megadhatja, hogy a forgalom milyen hányada kerüljön az egyes háttérbe.

## <a name="session-affinity"></a><a name = "affinity"></a>Munkamenet-affinitás
Alapértelmezés szerint a munkamenet-affinitás nélkül a bejárati ajtó továbbítja az azonos ügyféltől származó kérelmeket a különböző háttérrendszer-beállításokhoz a terheléselosztási konfiguráció alapján, különösen a különböző háttérrendszer-környezetek késését, vagy ha az azonos felhasználótól érkező különböző kérések egy másik bejárati környezetben vannak. Egyes állapotalapú alkalmazások vagy forgatókönyvek esetében viszont előnyösebb, ha az ugyanazon felhasználótól érkező későbbi kérelmek ugyanarra a háttérrendszerre kerülnek, amely az első kérelmet feldolgozta. A cookie-alapú munkamenet-affinitás akkor hasznos, ha ugyanazon a háttérrendszeren szeretne tartani egy felhasználói munkamenetet. A bejárati ajtók által felügyelt cookie-k használatával az Azure bejárati ajtaja a felhasználói munkamenetből egy adott háttérbe irányíthatja a további forgalmat, ha a háttér állapota Kifogástalan, és a felhasználói munkamenet nem járt le. 

A munkamenet-affinitást az előtérbeli gazdagép szintjén engedélyezheti minden konfigurált tartomány (vagy altartomány) számára. Az engedélyezés után a Front Door hozzáad egy cookie-t a felhasználói munkamenethez. A cookie-alapú munkamenet-affinitás lehetővé teszi a Front Door számára, hogy azonos IP-cím esetén is azonosítsa a különböző felhasználókat, amely egyenletesebb forgalomelosztást tesz lehetővé a különböző háttérrendszerek között.

A cookie élettartama megegyezik a felhasználói munkamenet élettartamával, mivel a Front Door jelenleg csak a munkameneti cookie-kat támogatja. 

> [!NOTE]
> A nyilvános proxyk megakadályozhatják a munkamenet-affinitást. Ennek az az oka, hogy a munkamenet létrehozásához a bejárati ajtót kell hozzáadni egy munkamenet-affinitási cookie-nak a válaszhoz való hozzáadásához, ami nem végezhető el, ha a válasz gyorsítótárazható, mert az adott erőforrást kérő más ügyfelek cookie-jait megszakítja. Ennek elleni védelem érdekében a munkamenet-affinitás **nem** lesz létrehozva, ha a háttér-kezelő gyorsítótárazható választ küld a kísérlet során. Ha a munkamenet már létrejött, nem számít, hogy a háttérbeli válasz gyorsítótárazható-e.
> A munkamenet-affinitás a következő esetekben lesz létrehozva, **kivéve, ha** a válasz http 304 állapotkódot tartalmaz:
> - A válaszhoz megadott értékek vannak beállítva a ```Cache-Control``` fejléchez, amely megakadályozza a gyorsítótárazást, például "Private" vagy "Store".
> - A válasz olyan ```Authorization``` fejlécet tartalmaz, amely nem járt le.
> - A válasz HTTP 302 állapotkódot tartalmaz.

## <a name="next-steps"></a>További lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
