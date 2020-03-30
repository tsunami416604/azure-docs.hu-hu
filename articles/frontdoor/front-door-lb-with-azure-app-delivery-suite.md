---
title: Azure Bejárati ajtó – Terheléselosztás az Azure alkalmazáskézbesítési csomagjával | Microsoft dokumentumok
description: Ez a cikk segítséget nyújt arról, hogy az Azure hogyan ajánlja a terheléselosztást az alkalmazáskézbesítési csomaggal
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 9f8d1959549eaddfb4a2c9ea271094db0073c788
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471710"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Terheléselosztás az Azure alkalmazáskézbesítési csomagjával

## <a name="introduction"></a>Bevezetés
A Microsoft Azure több globális és regionális szolgáltatást is kínál a hálózati forgalom elosztásának és terheléselosztásának kezeléséhez: Traffic Manager, Bejárati ajtó, Application Gateway és Terheléselosztó.  Az Azure számos régiójával és zónaszintű architektúrájával együtt ezek a szolgáltatások együttes használatával robusztus, méretezhető, nagy teljesítményű alkalmazásokat hozhat létre.

![Alkalmazáskézbesítési csomag ][1]
 
Ezek a szolgáltatások két kategóriába sorolhatók:
1. **A globális terheléselosztási szolgáltatások,** például a Traffic Manager és a Bejárati ajtó a végfelhasználóktól érkező forgalmat a regionális háttérrendszereken, felhőkközött vagy akár a hibrid helyszíni szolgáltatások között osztja el. A globális terheléselosztás a forgalmat a legközelebbi szolgáltatás-háttérrendszerhez irányítja, és reagál a szolgáltatás megbízhatóságának vagy teljesítményének változásaira, hogy mindig bekapcsolt, maximális teljesítményt nyújtson a felhasználók számára. 
2. **A regionális terheléselosztási szolgáltatások,** például a standard terheléselosztó vagy az Application Gateway lehetővé teszik a virtuális hálózatokon (VNE) belüli forgalom elosztását a virtuális gépek (VM-ek) vagy a régión belüli zónaszintű szolgáltatásvégpontok között.

A globális és regionális szolgáltatások kombinálása az alkalmazásban egy végponttól végpontig megbízható, nagy teljesítményt nyújt, és biztonságos módja annak, hogy a forgalmat a felhasználók és a felhasználók az IaaS, PaaS vagy helyszíni szolgáltatások. A következő szakaszban ismertetik ezeket a szolgáltatásokat.

## <a name="global-load-balancing"></a>Globális terheléselosztás
**A Traffic Manager** globális DNS-terheléselosztást biztosít. Megvizsgálja a bejövő DNS-kérelmeket, és kifogástalan háttérprogrammal válaszol, az ügyfél által kiválasztott útválasztási házirendnek megfelelően. Az útválasztási módszerek lehetőségei a következők:
- Teljesítmény-útválasztás a kérelmező nek a legközelebbi háttérrendszerre való elküldéséhez a késés szempontjából.
- Prioritás-útválasztás az összes forgalmat egy háttérrendszer, más háttérrendszerek biztonsági mentésként.
- Súlyozott ciklikus multiplexelés útválasztás, amely elosztja a forgalmat az egyes háttér-háttérszolgáltatásokhoz rendelt súlyozás alapján.
- Földrajzi útválasztás annak biztosítása érdekében, hogy az adott földrajzi régiókban található kérelmezők az ezekhez a régiókhoz leképezett háttérrendszerekre irányuljanak (például a Spanyolországból érkező összes kérelmet a Franciaország közép-azure-régiójába kell irányítani)
- Alhálózati útválasztás, amely lehetővé teszi az IP-címtartományok hozzárendelését a háttérrendszerekhez, így az ezekből érkező kérések a megadott háttérrendszernek lesznek elküldve (például a vállalati központ IP-címtartományából csatlakozó összes felhasználónak más webes tartalmat kell kapnia, mint az általános felhasználók)

Az ügyfél közvetlenül csatlakozik a háttérrendszerhez. Az Azure Traffic Manager észleli, ha egy háttérrendszer nem megfelelő állapotú, és majd átirányítja az ügyfeleket egy másik kifogástalan állapotú példányra. A szolgáltatásról az [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) dokumentációjában olvashat bővebben.

**Az Azure Front Door** dinamikus webhelygyorsítást (DSA) biztosít, beleértve a globális HTTP terheléselosztást is.  Megvizsgálja a bejövő HTTP-kérelmek útvonalakat a legközelebbi szolgáltatás háttérrendszer / régió a megadott állomásnév, URL elérési út és a konfigurált szabályok.  
A Front Door megszakítja a Microsoft hálózatának szélén lévő HTTP-kérelmeket, és aktívan megvizsgálja az alkalmazások vagy az infrastruktúra állapotának vagy késésének változásait.  Bejárati ajtó, majd mindig irányítja a forgalmat a leggyorsabb és a rendelkezésre álló (egészséges) háttér. A szolgáltatásról a Front Door [útválasztási architektúrájának](front-door-routing-architecture.md) részleteiés [forgalomútválasztási módszerei](front-door-routing-methods.md) című dokumentumban olvashat bővebben.

## <a name="regional-load-balancing"></a>Regionális terheléselosztás
Az Application Gateway szolgáltatásként biztosítja az alkalmazáskézbesítési vezérlőt (ADC), amely különböző Layer 7 terheléselosztási lehetőségeket kínál az alkalmazásszámára. Lehetővé teszi az ügyfelek számára, hogy optimalizálják a webfarm okait a CPU-igényes SSL-végződés alkalmazásátjáróra történő kiszervezésével. A Layer 7 egyéb útválasztási képességei közé tartozik a bejövő forgalom ciklikus multiplexelése, a cookie-alapú munkamenet-affinitás, az URL-útvonal-alapú útválasztás, valamint az egyetlen alkalmazásátjáró mögött több webhely üzemeltetésének lehetősége. Az Application Gateway konfigurálható internetfelé néző átjáróként, csak belső átjáróként vagy a kettő kombinációjaként. Az Application Gateway teljes mértékben felügyelt, méretezhető és magas rendelkezésre állású. Diagnosztikai és naplózási képességek széles skáláját biztosítja a jobb kezelhetőség érdekében.
A terheléselosztó az Azure SDN-verem szerves része, amely nagy teljesítményű, alacsony késleltetésű Layer 4 terheléselosztási szolgáltatásokat biztosít az összes UDP- és TCP-protokollhoz. Kezeli a bejövő és kimenő kapcsolatokat. Segítségével nyilvános és belső elosztott terhelésű végpontok konfigurálhatók, valamint szabályok definiálhatók, amelyek a bejövő kapcsolatokat a háttérbeli készletben található célokra irányítják TCP- és HTTP-állapotellenőrzési lehetőségek használatával, a szolgáltatás rendelkezésre állásának felügyeletéhez.


## <a name="choosing-a-global-load-balancer"></a>Globális terheléselosztó kiválasztása
Amikor globális terheléselosztót választ a Traffic Manager és az Azure Front Door között a globális útválasztáshoz, vegye figyelembe, hogy mi a hasonló, és mi a különbség a két szolgáltatásban.   Mindkét szolgáltatás
- **Többgeos redundancia:** Ha egy régió leáll, a forgalom zökkenőmentesen a legközelebbi régióba vezet az alkalmazás tulajdonosának beavatkozása nélkül.
- **Legközelebbi terület-útválasztás:** A forgalom automatikusan a legközelebbi régióba lesz irányítva

</br>Az alábbi táblázat a Traffic Manager és az Azure Bejárati ajtó közötti különbségeket ismerteti:</br>

| Traffic Manager | Azure Front Door |
| --------------- | ------------------------ |
|**Bármilyen protokoll:** Mivel a Traffic Manager a DNS-rétegen működik, bármilyen típusú hálózati forgalmat továbbíthat; HTTP, TCP, UDP stb. | **HTTP-gyorsítás:** A Bejárati ajtó forgalom proxied szélén a Microsoft hálózata.  Emiatt a HTTP(S) kérelmek látnak késés és átviteli fejlesztések csökkenti késés az SSL-egyeztetés és az AFD-ből az alkalmazáshoz való gyorskapcsolatot.|
|**Helyszíni útválasztás:** Ha a DNS-rétegen halad, a forgalom mindig pontról pontra halad.  Útválasztás a fiókirodából a helyszíni adatközpont is közvetlen elérési utat; még a saját hálózatán is a Traffic Manager használatával. | **Független méretezhetőség:** A Bejárati ajtó a HTTP-kérelemmel működik, a különböző URL-címekre irányuló kérelmek a szabályok és az egyes alkalmazásmikroszolgáltatások állapota alapján különböző háttér- és regionális szolgáltatáskészletekre (mikroszolgáltatások) irányíthatók.|
|**Számlázási formátum:** A DNS-alapú számlázás a felhasználókkal és a több felhasználóval rendelkező szolgáltatások esetében a költségek csökkentésére szolgáló fennsíkokon méreteket ölthet. |**Szövegközi biztonság:** Bejárati ajtajának lehetővé teszi a szabályok, például a sebességkorlátozás és az IP-ACL-ing, hogy lehetővé teszi a háttérrendszerek védelme előtt a forgalom eléri az alkalmazást. 

</br>A Bejárati ajtajával http-munkaterhelések teljesítmény, működőképesség és biztonsági előnyök miatt azt javasoljuk, hogy az ügyfelek a Bejárati ajtót használják a HTTP-munkaterhelésükhöz.    Traffic Manager és a Bejárati ajtó lehet használni párhuzamosan kiszolgálni az alkalmazás összes forgalmat. 

## <a name="building-with-azures-application-delivery-suite"></a>Építés az Azure alkalmazáskézbesítési csomagjával 
Azt javasoljuk, hogy minden webhely, API-k, szolgáltatások földrajzilag redundáns, és forgalmat a felhasználók a legközelebbi (legalacsonyabb késés) helyen őket, amikor csak lehetséges.  A Traffic Manager, a Bejárati ajtó, az Application Gateway és a Terheléselosztó szolgáltatásainak kombinálása lehetővé teszi, hogy földrajzilag és zónaszinten redundáns állapotú szolgáltatásokat hozzon létre a megbízhatóság, a méretezés és a teljesítmény maximalizálása érdekében.

Az alábbi ábrán egy példaszolgáltatást mutatunk be, amely az összes szolgáltatás kombinációját használja egy globális webszolgáltatás létrehozásához.   Ebben az esetben az építész a Traffic Manager használatával irányította a globális háttérrendszereket a fájl- és objektumkézbesítéshez, miközben a Bejárati ajtót használva globálisan irányítja az URL-útvonalakat, amelyek megfelelnek a /store/* mintának az App Service-be áttelepített szolgáltatáshoz, miközben az összes többi kérelmet a regionális alkalmazásátjárókhoz továbbítják.

A régióban, az IaaS-szolgáltatás, az alkalmazás fejlesztője úgy döntött, hogy minden URL-t, amely megfelel a minta /images/* szolgálnak ki egy dedikált készlet virtuális gépek, amelyek eltérnek a többi webfarm.

Emellett a dinamikus tartalmat kiszolgáló alapértelmezett virtuálisgép-készletnek egy magas rendelkezésre állású fürtön tárolt háttér-adatbázissal kell beszélnie. A teljes üzembe helyezés az Azure Resource Manageren keresztül van beállítva.

Az alábbi ábra a forgatókönyv architektúráját mutatja be:

![Alkalmazáskézbesítési csomag részletes architektúrája][2] 

> [!NOTE]
> Ez a példa csak egy a sok lehetséges konfigurációk az Azure által nyújtott terheléselosztási szolgáltatások. A Traffic Manager, a Bejárati ajtó, az Application Gateway és a Terheléselosztó keverhető és a terheléselosztási igényeknek leginkább megfelelően illeszthető. Ha például az SSL-kiszervezés vagy a 7- es réteg feldolgozása nem szükséges, a terheléselosztó használható az Application Gateway helyett.


## <a name="next-steps"></a>Következő lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png
