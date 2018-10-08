---
title: Azure Front Door Service | Microsoft Docs
description: Ez a cikk az Azure Front Doorról nyújt áttekintést. Ismerje meg, hogy ez-e a megfelelő választás az alkalmazáshoz érkező felhasználói forgalom terheléselosztásához.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/29/2018
ms.author: sharadag
ms.openlocfilehash: 346fa5721df6c9ce0cf355adea21f59c93a394a9
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47040482"
---
# <a name="what-is-azure-front-door-service"></a>Mi az az Azure Front Door Service?
Az Azure Front Door Service segítségével meghatározhatja, felügyelheti és monitorozhatja a webes forgalmának globális forgalomirányítását, optimalizálhatja annak teljesítményét, és azonnali globális feladatátvétellel magas rendelkezésre állást biztosíthat. A Front Door használatával a globális (több régióban található) fogyasztói és nagyvállalati alkalmazásait olyan robusztus, nagy teljesítményű, személyre szabott és modern alkalmazásokká, API-kká és tartalmakká alakíthatja, amelyek az Azure-on keresztül elérhetik a globális közönségüket.

A Front Door a 7. rétegben, a HTTP/HTTPS rétegben működik, és szétválasztott TCP-alapú anycast protokollal és a Microsoft globális hálózatával javítja a globális összekapcsolhatóságot. Így minden kiválasztott útválasztási metódus esetén biztosíthatja, hogy a Front Door a lehető leggyorsabb és a legnagyobb rendelkezésre állású alkalmazás-háttérrendszerre irányítsa ügyfélkérelmeit. Az alkalmazás-háttérrendszer egy, az Azure-on kívül vagy belül üzemeltetett, internetkapcsolattal rendelkező szolgáltatás. A Front Door számos különböző [forgalom-útválasztási módszert](front-door-routing-methods.md) és [háttérrendszer-állapotfigyelési lehetőséget](front-door-health-probes.md) biztosít, hogy megfeleljen a különböző alkalmazások igényeinek és az automatikus feladatátvételi modelleknek. A [Traffic Managerhez](../traffic-manager/traffic-manager-overview.md) hasonlóan, a Front Door is ellenáll a meghibásodásoknak, beleértve akár egy egész Azure-régió meghibásodását is.

>[!NOTE]
> Az Azure teljeskörűen felügyelt terheléselosztási megoldások együttesét biztosítja a különböző forgatókönyvekre. Ha DNS-alapú globális útvonalválasztásra van szüksége, de **nem** teljesíti a Transport Layer Security (TLS) protokoll-lezárás („SSL-kiszervezés”) követelményeit vagy per-HTTP/HTTPS kérelmeket, illetve alkalmazásréteg-feldolgozást keres, tekintse át az [Traffic Managert](../traffic-manager/traffic-manager-overview.md) ismertető cikket. Ha egy azonos régióban található kiszolgálói között szeretne terheléselosztást megvalósítani, akkor az alkalmazás rétegű terheléselosztás esetén tekintse át az [Application Gatewayt](../application-gateway/application-gateway-introduction.md), hálózati rétegű esetén pedig a [Load Balancert](../load-balancer/load-balancer-overview.md) ismertető cikket. A végpontok közötti forgatókönyvek esetében előnyt jelenthet ezen megoldások igény szerinti kombinációja.

A Front Door a következő funkciókat tartalmazza:

## <a name="accelerate-application-performance"></a>Az alkalmazásteljesítmény gyorsítása
A Front Door szétválasztott TCP-alapú anycast protokollal biztosítja, hogy a végfelhasználók azonnal kapcsolódhassanak a legközelebbi Front Door POP-hoz (Elérési ponthoz). A Front Door POP-okból az alkalmazás-háttérrendszerekre történő csatlakozások a Microsoft globális hálózatán keresztül történnek, így a teljesítmény csökkenése nélkül valósíthat meg magasabb rendelkezésre állást és nagyobb megbízhatóságot. A háttérrendszerrel történő összekapcsolás is a legalacsonyabb hálózati késleltetésen alapul. A Front Door útvonalválasztásával kapcsolatos további tudnivalókat a [Szétválasztott TCP](front-door-routing-architecture.md#splittcp) és az [Anycast protokoll](front-door-routing-architecture.md#anycast) cikkekben talál.

## <a name="increase-application-availability-with-smart-health-probes"></a>Alkalmazás rendelkezésre állásának növelése intelligens állapotmintákkal

A Front Door lehetővé teszi a kritikus fontosságú alkalmazások magas rendelkezésre állását azáltal, hogy intelligens állapotmintákat használ, hogy a háttérrendszereket egyszerre figyeli késleltetés és rendelkezésre állás szempontjából, és hogy azonnali automatikus feladatátvételt biztosít, ha egy háttérrendszer leáll. Így állásidő nélkül futtathat tervezett karbantartási műveleteket az alkalmazásokon. A Front Door alternatív háttérrendszerekre irányítja a forgalmat, amíg a karbantartás folyamatban van.

## <a name="url-based-routing"></a>URL-alapú útválasztás
Az URL-alapú útválasztás lehetővé teszi, hogy a kérésben szereplő URL-cím alapján irányítsa a forgalmat a háttérrendszer-készletekhez. Az egyik forgatókönyv az, hogy a különböző típusú tartalmakra vonatkozó kéréseket különböző háttérrendszer-készlethez irányítja.

Például a `http://www.contoso.com/users/*` iránti kérelmek a UserProfilePoolba, míg a `http://www.contoso.com/products/*` felé irányuló kérelmek az ProductInventoryPoolba vannak továbbítva.  A Front Door a legjobb algoritmusokat felhasználva még összetettebb útvonalillesztési forgatókönyveket is lehetővé tesz, így ha egyik útvonalminta sem illeszkedik, akkor a `http://www.contoso.com/*` esetén az alapértelmezett útválasztási szabály lesz kiválasztva, és a forgalom az alapértelmezett útválasztási gyűjtőszabályhoz kerül. További tudnivalókért tekintse meg az [útvonalillesztést](front-door-route-matching.md) ismertető cikket.

## <a name="multiple-site-hosting"></a>Több hely üzemeltetése
Több hely üzemeltetése funkcióval azonos Front Door-konfiguráción egynél több webhelyet is konfigurálhat. Ezzel a funkcióval hatékonyabb topológiát konfigurálhat a telepítéseihez, mivel egyetlen Front Door-konfigurációhoz különböző webhelyeket is hozzáadhat. Az alkalmazásai architektúrájának megfelelően az Azure Front Door Service-t konfigurálhatja úgy, hogy minden egyes webhelyet a saját háttérrendszer-készletéhez irányítson, vagy akár úgy is, hogy különböző webhelyeket ugyanahhoz a háttérrendszer-készlethez irányítson. A Front Door például az `images.contoso.com` és a `videos.contoso.com` forgalmát is kiszolgálhatja az ImagePool és a VideoPool nevű háttérrendszer-készletekből. De konfigurálható mindkét előtérbeli állomás úgy is, hogy a forgalma ugyanabba a MediaPool nevű háttérrendszer-készletbe legyen irányítva.

Hasonlóképpen konfigurálhat két különböző tartományt, a `www.contoso.com` és a `www.fabrikam.com` tartományokat ugyanazon a Front Dooron.

## <a name="session-affinity"></a>Munkamenet-affinitás
A Cookie-alapú munkamenet-affinitás akkor hasznos, ha egy felhasználói munkamenetet egy adott alkalmazás-háttérrendszeren szeretne tartani. A Front Doorral kezelt cookie-k használatával egy felhasználói munkamenet minden újabb forgalma ugyanarra az alkalmazás-háttérrendszerre lesz irányítva feldolgozásra. Ez a funkció olyan esetekben lehet fontos, amelyekben egy felhasználói munkamenethez tartozó munkamenet-állapotot helyileg ment a rendszer az alkalmazás-háttérrendszeren.

## <a name="secure-sockets-layer-ssl-termination"></a>Secure Sockets Layer- (SSL-) lezárás
A Front Door támogatja a peremhálózaton az SSL-lezárást, így a felhasználók beállíthatnak SSL-csatlakozásokat a Front Door-környezetekhez, ahelyett, hogy hosszan tartó kapcsolatokat építenének ki az alkalmazás-háttérrendszerrel. Ezenkívül a Front Door egyaránt támogatja a HTTP- és a HTTPS-csatlakozásokat is a Front Door-környezetek és a háttérrendszerek között. Így végpontok közötti SSL-titkosítás is beállítható. Ha például a Front Doorhoz egy alkalmazás számítási feladataként egy perc alatt 5000 kérelem érkezik, akkor aktív szolgáltatások esetén a kapcsolatok meleg újrafelhasználásának köszönhetően körülbelül mindössze 500 csatlakozás fog kiépülni az alkalmazás-háttérrendszerrel, így a háttérrendszereken jelentős terheléscsökkentés érhető el.

## <a name="custom-domains-and-certificate-management"></a>Egyéni tartományok és tanúsítványok kezelése
Amikor a Front Doort használja a tartalom továbbítására, és azt szeretné, hogy a saját tartományneve jelenjen meg a Front Door URL-címében, egyéni tartományt kell létrehoznia. A látható tartománynév hasznos lehet az ügyfelei számára, és a vállalati arculat szempontjából is.
A Front Door a HTTPS-t is támogatja az egyéni tartománynevekben. Ezzel a funkcióval kiválaszthatja a forgalmához a Front Door által kezelt tanúsítványait, vagy feltöltheti a saját SSL-tanúsítványát.

## <a name="application-layer-security"></a>Alkalmazásrétegbeli biztonság
Az Azure Front Doorral egyéni webalkalmazási tűzfalszabályok (WAF-szabályok) készítésével hozzáférés-vezérlést valósíthat meg, amellyel megvédheti a HTTP/HTTPS-számítási feladatait attól, hogy feltörjék őket az ügyféloldali IP-cím, országkód és HTTP-paraméterek alapján. Ezenkívül a Front Doorral létrehozhat olyan sebességkorlátozó szabályokat is, amelyekkel a robotok rosszindulatú forgalma ellen is hatékonyan védekezhet. 

Maga a Front Door platform alapszintű [Azure DDoS Protectionnel](../virtual-network/ddos-protection-overview.md) rendelkezik. További védelemként engedélyezheti a virtuális hálózatain a Standard szintű Azure DDoS Protectiont, így automatikus finomhangolással és kárenyhítéssel megvédheti erőforrásait a hálózati rétegből érkező (TCP/UDP) támadásokkal szemben. A Front Door egy 7. rétegű fordított proxy, amely alapértelmezés szerint csak a háttérrendszerek felé irányuló forgalmat engedi át, minden más forgalmat blokkol.

## <a name="url-rewrite"></a>URL-átírás
A Front Door azáltal támogatja az [URL-átírást](front-door-url-rewrite.md), hogy lehetővé teszi egy olyan egyéni továbbítási útvonal konfigurálását, amely a háttérrendszer felé továbbítandó kérelem megalkotásakor használható. A Front Door továbbá lehetővé teszi, hogy konfiguráljon egy olyan állomásfejlécet, amelyet akkor küld el rendszer, amikor továbbítja a kérelmet a háttérrendszer felé.

## <a name="protocol-support---ipv6-and-http2-traffic"></a>Protokolltámogatás: IPv6- és HTTP/2-forgalom
Az Azure Front Door natívan támogatja a végpontok közötti IPv6-kapcsolatokat, valamint a HTTP/2 protokollt. 

A HTTP/2 protokoll teljes körű duplex kommunikációt tesz lehetővé egy alkalmazás-háttérrendszer és egy ügyfél között egy hosszú ideig futó TCP-kapcsolaton. A HTTP/2 interaktívabb kommunikációt eredményez a háttérrendszer és az ügyfél között, amely anélkül marad kétirányú, hogy a HTTP-alapú implementációkban kötelező lekérdezésekre lenne szükség. A HTTP-vel ellentétben a HTTP/2 protokoll alacsony többletterheléssel bír, és több kérelem/válasz típusú forgalomhoz is használhatja ugyanazt a TCP-kapcsolatot, ami az erőforrások hatékonyabb kihasználását eredményezi. További tudnivalók az [Azure Front Door HTTP/2-támogatásáról](front-door-http2.md).

## <a name="pricing"></a>Díjszabás

Díjszabási információkért tekintse meg [A Front Door díjszabása](https://azure.microsoft.com/pricing/details/frontdoor/) című cikket.

## <a name="next-steps"></a>További lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) megismerése.
