---
title: Az Azure bejárati ajtajának Service - forgalom-útválasztási módszerei |} A Microsoft Docs
description: Ez a cikk segít megérteni a különböző forgalom-útválasztási módszer bejárati ajtajának által használt
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
ms.openlocfilehash: bd1278db43ba31ed78f13a826a330e16c3bc8d57
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820844"
---
# <a name="front-door-routing-methods"></a>Bejárati ajtajának útválasztási módszerek

Azure bejárati ajtajának Service többféle forgalom-útválasztási módszerrel határozza meg, hogyan irányíthatja a HTTP/HTTPS-forgalmat a különböző Szolgáltatásvégpontok támogat. Az egyes bejárati ajtajának elérése ügyfél kérelmet a konfigurált útválasztási módszert alkalmazva lesz annak érdekében, hogy a rendszer továbbítja a kérelmeket a legjobb háttér-példányhoz. 

Nincsenek elérhető bejárati ajtó a négy fő fogalmat kell forgalom-útválasztás:

* **[Késés](#latency):** A késés-alapú útválasztás biztosítja, hogy kérések érkeznek a legkisebb késés háttérkomponenseinek elfogadható érzékenységi tartományon belül. Alapvetően a felhasználói kérések érkeznek a hálózati késés megállapodást háttérrendszerek "legközelebbi" készletét.
* **[Prioritás](#priority):** A különböző háttérrendszerekre is prioritásokat lehet kiosztani, ha szeretne egy elsődleges service háttérrendszer használata minden forgalmat, és adja meg a biztonsági mentések, abban az esetben, ha az elsődleges vagy a biztonsági mentési háttérrendszerek nem érhető el.
* **[Súlyozott](#weighted):** Ha meg szeretné elosztani a forgalmat egy készletét-háttérrendszerek esetében egyenletesen vagy súly együttható megfelelően, súlyok rendelhet a különböző háttérrendszerekre.
* **Munkamenet-affinitás:** Munkamenet-affinitás az előtér-gazdagépek vagy tartományokban, ha azt szeretné, hogy egy felhasználó érkező későbbi kérelmeket érkeznek ugyanarra a háttérrendszerre mindaddig, amíg a felhasználó munkamenetét még mindig aktív és a háttér-példány továbbra is jelentések kifogástalan állapotadat-mintavételek alapján konfigurálhatja. 

A Front Door összes konfigurációja tartalmazza a háttérrendszer állapotának monitorozását és az automatizált azonnali globális feladatátvételt. További információkért lásd: [bejárati ajtajának háttérrendszer figyelési](front-door-health-probes.md). A bejárati ajtajának beállítható úgy, hogy egyetlen útválasztási módszer alapján vagy munkahelyi és az alkalmazástól függően kell segítségével több vagy minden útválasztási módszer kombinálva hozhat létre egy optimális útválasztási topológiáját.

## <a name = "latency"></a>Legalacsonyabb késések alapú forgalom-útválasztási

Két vagy több hely háttérrendszereket telepítése szerte a világon számos alkalmazás válaszkészségét növelheti irányítaná a forgalmat, hogy a végfelhasználók számára "legközelebb" helyre. Számára a bejárati ajtajának konfigurációt az alapértelmezett forgalom-útválasztási módszer kéréseket a végfelhasználók számára a bejárati ajtajának környezetből, amely a kérés érkezett a legközelebbi háttérrendszere továbbítja. A csomópontválasztásos architektúra az Azure bejárati ajtajának Service kombinálva, ez a megközelítés biztosítja, hogy, hogy a végfelhasználók get maximális teljesítmény személyre szabott mindegyike saját helye alapján.

A "legközelebbi" háttér nem feltétlenül legközelebbi mért földrajzi távolságra. Ehelyett bejárati ajtajának hálózati késés megvizsgálja, hogy a legközelebbi háttérrendszerek határozza meg. Tudjon meg többet [bejárati ajtajának útválasztási architektúra](front-door-routing-architecture.md). 

Az alábbi, az általános döntési folyamatot:

| Rendelkezésre álló háttérkomponensei | Prioritás | Késés jel (állapotadat-mintavétel alapján) | Súlyozások |
|-------------| ----------- | ----------- | ----------- |
| Először is, válassza ki az engedélyezett és a visszaadott kifogástalan háttérkomponenseinek (200 OK) az állapotmintához. Tegyük fel, nincsenek hat háttérrendszerek A, B, C, D, E, és az F, és a közöttük lévő C nem kifogástalan, és E le van tiltva. Így elérhető háttérrendszerek listája a következő A, B, D és F.  | Ezután a rendelkezésre állók közül a legmagasabb prioritást háttérrendszerek ki van jelölve. Tegyük fel háttérrendszer A, B és D prioritása 1 és a háttérrendszer F 2-es prioritást. Így a kiválasztott háttérrendszerek lesz A, B és d| Válassza ki a késési tartományon (legkisebb késés & megadott ms késést és nagybetűk megkülönböztetése)-alapú háttérkomponensek. Tegyük fel, ha egy 15 ms, a B kiszolgáló lesz az ms 30 és D, erről a bejárati ajtajának környezetben, ahol a kérés érkezett, és késés érzékenységi 30 ms, akkor legkisebb késés készlet magában foglalja a háttérrendszer A és B 60 ms, mert D túli 30 ms erről a legközelebbi háttérrendszerhez, amely rögzíti. | Végül bejárati ajtajának a rendszer Ciklikus időszeleteléses a forgalmat, többek között a megadott súlyok aránya háttérrendszereket végső kiválasztott készletét. Tegyük fel, A háttér-e legalább 5 és a háttérkiszolgáló B súlyú, 8, majd a forgalom szétosztani 5:8, többek között a és b háttérrendszerek aránya |

>[!NOTE]
> Alapértelmezés szerint a késés érzékenységi tulajdonság értéke 0 ms, vagyis, mindig továbbítja a kérést a leggyorsabb háttérrendszert.


## <a name = "priority"></a>Prioritásalapú forgalom-útválasztási

Gyakran egy szervezet szeretné a szolgáltatások megbízhatóságot biztosítanak egy vagy több biztonsági mentési szolgáltatás telepítésével, abban az esetben, ha az elsődleges szolgáltatás leáll. A teljes Ez a topológia is nevezzük aktív/készenléti vagy az aktív/passzív telepítési topológia. A "Priority" forgalom-útválasztási módszer lehetővé teszi, hogy az Azure-ügyfelek egyszerűen implementálni a feladatátvételi minta.

Az alapértelmezett bejárati ajtajának háttérrendszerek az azonos prioritású listáját tartalmazza. Alapértelmezés szerint bejárati ajtajának küld forgalmat csak a legmagasabb prioritást háttérrendszerek (prioritás a legalacsonyabb érték), a háttérrendszerek elsődleges készletét. Az elsődleges háttérrendszerek nem érhetők el, ha az bejárati ajtajának irányítja a forgalmat a másodlagos készletét háttérrendszerek (prioritás a második legkisebb metrikájút érték). Ha az elsődleges és másodlagos háttérrendszerek nem érhetők el, áramlik a forgalom, a harmadik, és így tovább. A háttérrendszer rendelkezésre állását a konfigurált állapota (engedélyezve vagy letiltva) alapul, és állapota határozza meg, a folyamatban lévő háttérrendszer állapotának mintavételei.

### <a name="configuring-priority-for-backends"></a>A háttérrendszerek prioritás konfigurálása

A háttérkészlet, a bejárati ajtajának konfigurációs belül a háttérrendszereket mindegyike rendelkezik tulajdonságot, "Priority", amely 1 és 5 közötti szám lehet. A Azure bejárati ajtajának szolgáltatás explicit módon Ez a tulajdonság használatával az egyes háttérrendszerek háttérrendszer prioritás konfigurálását. Ez a tulajdonság 1 és 5 közötti értéket. Alacsonyabb érték egy nagyobb prioritást jelölnek. Háttérrendszerek megoszthatja a prioritási értékek.

## <a name = "weighted"></a>Súlyozott forgalom-útválasztási módszer
"Súlyozott" forgalom-útválasztási módszer lehetővé teszi, hogy egyenletesen osztja el a forgalmat, vagy egy előre meghatározott súlyozási használandó.

Súlyozott forgalom-útválasztási módszer esetében a súlyt rendel az egyes háttérrendszerek a háttérkészlet bejárati ajtajának konfigurációjában. A súlyok 1 és 1000 közötti egész számok. Ez a paraméter '50' alapértelmezett súlyozást használ.

Az elfogadott késés érzékenységi (meghatározottak szerint) belül elérhető háttérrendszerek listájában, többek között a forgalmat egy Ciklikus időszeleteléses mechanizmust megadott súlyok aránya lekérdezi terjesztése. Ha a késés érzékenységi értéke 0 ezredmásodperc, majd ezt a tulajdonságot nem lépnek életbe, kivéve ha az azonos hálózati késéssel rendelkező két háttérrendszerek. 

A súlyozott mód néhány hasznos forgatókönyveket teszi lehetővé:

* **Az alkalmazásfrissítés fokozatos**: Forgalom átirányítása az új háttérrendszer százalékát, és fokozatosan növelje a forgalom a többi háttérrendszerek névértéken életre idővel.
* **Alkalmazás migrálását az Azure**: Hozzon létre egy háttérkészlet Azure és a külső háttérrendszerek. Állítsa be az új háttérrendszerek előnye a háttérrendszerek súlyát. Fokozatosan beállíthat ez kezdve az új háttérrendszerek le van tiltva, amelyek majd hozzá kell rendelni őket a legalacsonyabb súlyok lassan növelje, szintre, ahol a legnagyobb forgalmú tartanak. Majd végül a kevésbé előnyben részesített háttérrendszerek letiltása és eltávolításával, majd a készletből.  
* **Felhőbeli tartalékkapacitás további kapacitás**: Gyorsan bontsa ki a-felhőbe egy helyi központi mögött bejárati ajtajának helyezésével. A felhőben extra kapacitásra van szüksége, amikor hozzáadása vagy további háttérrendszerek engedélyezze, és adja meg, milyen forgalom részét az egyes háttérrendszerek irányul.

## <a name = "affinity"></a>Munkamenet-affinitás
Munkamenet-affinitás nélküli alapértelmezés szerint bejárati ajtajának továbbítja a terheléselosztás konfigurációs, különösen az késleltetések a különböző háttérrendszereket módosítása esetén, vagy ha a terhelés alapján különböző háttérrendszereket az ugyanazon ügyféltől érkező kérések ugyanaz a felhasználó a különböző kérésekhez egy másik bejárati ajtajának környezetben Sarkvidék. Egyes állapotalapú alkalmazások vagy forgatókönyvek esetében viszont előnyösebb, ha az ugyanazon felhasználótól érkező későbbi kérelmek ugyanarra a háttérrendszerre kerülnek, amely az első kérelmet feldolgozta. A cookie-alapú munkamenet-affinitás akkor hasznos, ha ugyanazon a háttérrendszeren szeretne tartani egy felhasználói munkamenetet. Felügyelt bejárati ajtajának cookie-k használatával Azure bejárati ajtajának Service irányíthatók forgalmát háttérkiszolgálóra egy felhasználói munkamenetet ugyanarra a háttérrendszerre feldolgozás céljából, ha a háttérrendszer állapota kifogástalan, és a felhasználói munkamenet még nem járt le. 

A munkamenet-affinitást az előtérbeli gazdagép szintjén engedélyezheti minden konfigurált tartomány (vagy altartomány) számára. Az engedélyezés után a Front Door hozzáad egy cookie-t a felhasználói munkamenethez. A cookie-alapú munkamenet-affinitás lehetővé teszi a Front Door számára, hogy azonos IP-cím esetén is azonosítsa a különböző felhasználókat, amely egyenletesebb forgalomelosztást tesz lehetővé a különböző háttérrendszerek között.

A cookie élettartama megegyezik a felhasználói munkamenet élettartamával, mivel a Front Door jelenleg csak a munkameneti cookie-kat támogatja. 

> [!NOTE]
> Nyilvános proxyk zavarhatják munkamenet-affinitás. Ez azért van így, mert egy munkamenet-affinitási cookie-k hozzáadása a választ, amely nem hajtható végre, ha a válasz gyorsítótárazható, azt lenne zavarja a cookie-kat, ugyanazt az erőforrást igénylő ügyfelek számára a bejárati ajtajának munkamenetet megköveteli. Ennek megelőzése érdekében a munkamenet-affinitás fog **nem** létesíthető, ha a háttérrendszer gyorsítótárazható választ küld, amikor kísérlet történik ez. Ha a munkamenet már létrejött, nem számít, ha a válasz a háttérbeli gyorsítótárazható.
> Munkamenet-affinitás jön létre a következő körülmények között **, kivéve, ha** a választ egy HTTP-304 állapotkódja:
> - A válasz beállítása adott értékkel rendelkezik a ```Cache-Control``` fejlécet, amely megakadályozza a gyorsítótárazás, például a "privát" vagy a no-store ".
> - A válasz tartalmaz egy ```Authorization``` fejlécet, amely nem járt le.
> - A válasz állapotkódja egy HTTP 302.

## <a name="next-steps"></a>További lépések

- [Frontdoor létrehozására](quickstart-create-front-door.md) vonatkozó információk.
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
