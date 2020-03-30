---
title: Az Azure Application Gateway szolgáltatásai
description: További információ az Azure Application Gateway funkcióiról
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 550d9f4f5396b2165260e39cd28222b083dd6756
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279987"
---
# <a name="azure-application-gateway-features"></a>Az Azure Application Gateway szolgáltatásai

Az [Azure Application Gateway](overview.md) egy webes forgalomra vonatkozó terheléselosztó, amellyel kezelheti a webalkalmazásai forgalmát.

![Az Alkalmazásátjáró fogalmi](media/overview/figure1-720.png)

Az Application Gateway a következő szolgáltatásokat tartalmazza:

- [Secure Sockets Layer (SSL/TLS) végződés](#secure-sockets-layer-ssltls-termination)
- [Automatikus skálázás](#autoscaling)
- [Zóna redundanciája](#zone-redundancy)
- [Statikus VIP](#static-vip)
- [Web Application Firewall (Webalkalmazási tűzfal)](#web-application-firewall)
- [Bejövőforgalom-vezérlő az AKS-hez](#ingress-controller-for-aks)
- [URL-alapú útválasztás](#url-based-routing)
- [Több hely üzemeltetése](#multiple-site-hosting)
- [Átirányítás](#redirection)
- [Munkamenet-affinitás](#session-affinity)
- [Websocket- és HTTP/2-forgalom](#websocket-and-http2-traffic)
- [Kapcsolatkiürítés](#connection-draining)
- [Egyéni hibalapok](#custom-error-pages)
- [HTTP-fejlécek átírása](#rewrite-http-headers)
- [Méretezés](#sizing)

## <a name="secure-sockets-layer-ssltls-termination"></a>Secure Sockets Layer (SSL/TLS) végződés

Az alkalmazásátjáró támogatja az SSL/TLS-végződtetést az átjárón, amely után a forgalom általában titkosítatlanul áramlik a háttérkiszolgálókra. Ez a funkció lehetővé teszi, hogy a webkiszolgálók megszabaduljanak a magas titkosítási és visszafejtési üzemeltetési költségektől. De néha a kiszolgálókkal való titkosítatlan kommunikáció nem elfogadható megoldás. Ennek oka lehet a biztonsági követelmények, megfelelőségi követelmények, vagy az alkalmazás csak biztonságos kapcsolatot fogadhat el. Ezeknél az alkalmazásoknál az alkalmazásátjáró támogatja a végpontok közötti SSL/TLS titkosítást.

További információ: [Az SSL-végződés áttekintése és az SSL végpontok között és az Alkalmazásátjáró](ssl-overview.md)

## <a name="autoscaling"></a>Automatikus skálázás

Az Application Gateway Standard_v2 támogatja az automatikus skálázást, és a forgalmi terhelési minták módosítása alapján fel- vagy leskálázható. Az automatikus skálázással elkerülhető, hogy már a kiépítés során meg kelljen határozni az üzemelő példány méretét vagy a példányszámot. 

Az Application Gateway Standard_v2 szolgáltatásairól az [Automatikus skálázás v2 Termékváltozat](application-gateway-autoscaling-zone-redundant.md)című témakörben talál további információt.

## <a name="zone-redundancy"></a>Zóna redundanciája

A Standard_v2 Application Gateway több rendelkezésre állási zónára is kiterjedhet, így jobb hibarugalmasságot biztosít, és minden zónában nem kell külön alkalmazásátjárókat létesítenie.

## <a name="static-vip"></a>Statikus VIP

Az alkalmazásátjáró Standard_v2 termékváltozat támogatja a statikus VIP-típus kizárólag. Ez biztosítja, hogy az alkalmazásátjáróhoz társított VIP nem változik még az Alkalmazásátjáró élettartama alatt sem.

## <a name="web-application-firewall"></a>Web Application Firewall (Webalkalmazási tűzfal)

A Web Application Firewall (WAF) egy olyan szolgáltatás, amely központosított védelmet nyújt a webalkalmazásoknak a gyakori biztonsági rések és biztonsági rések elleni védelemben. A WAF az [OWASP (Open Web Application Security Project) 3.1](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) (csak WAF_v2), a 3.0 és a 2.2.9 alapvető szabálykészletein alapul. 

A webalkalmazások egyre inkább ki vannak téve rosszindulatú támadásoknak, amelyek az ismert biztonsági réseket használják ki. Az ilyen jellegű támadások között például gyakoriak az SQL-injektálásos és a webhelyek közötti, parancsprogramot alkalmazó támadások. Az ilyen támadások megakadályozása az alkalmazás kódjában kihívást jelenthet, és szigorú felügyeletet, javítást és megfigyelést igényelhet az alkalmazás topológiájának számos rétegén. A központosított webalkalmazási tűzfal egyszerűbbé teszi a biztonságfelügyeletet, és segít az alkalmazás-rendszergazdáknak a fenyegetések vagy a behatolások elleni védekezésben. Emellett a WAF-megoldás gyorsabban képes kezelni a biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett hogy az egyes webalkalmazások védelmét biztosítaná. A meglévő alkalmazásátjárók könnyen konvertálhatók webalkalmazás-tűzfallal rendelkező alkalmazásátjáróvá.

További információ: [Mi az Azure webalkalmazás-tűzfal?](../web-application-firewall/overview.md).

## <a name="ingress-controller-for-aks"></a>Bejövőforgalom-vezérlő az AKS-hez
Az Application Gateway ingress controller (AGIC) lehetővé teszi, hogy az Application Gateway az [Azure Kubernetes-szolgáltatás (AKS)](https://azure.microsoft.com/services/kubernetes-service/) fürtjének be- és azon fürtjeként használható. 

A be- ésres-vezérlő podként fut az AKS-fürtön belül, és felhasználja a [Kubernetes ingress resources-t,](https://kubernetes.io/docs/concepts/services-networking/ingress/) és átalakítja őket egy Application Gateway-konfigurációvá, amely lehetővé teszi az átjáró számára a Kubernetes-podok terheléselosztási forgalomát. A be- ésakozó vezérlő csak az Application Gateway Standard_v2 és WAF_v2 suk-ot támogatja. 

További információ: [Application Gateway Ingress Controller (AGIC)](ingress-controller-overview.md).

## <a name="url-based-routing"></a>URL-alapú útválasztás

Az URL-alapú útválasztás lehetővé teszi, hogy a kérésben szereplő URL-cím alapján irányítsa a forgalmat a háttér-kiszolgálókészlethez. Az egyik lehetőség az, hogy a különböző típusú tartalmakra vonatkozó kéréseket különböző készletekhez irányítja.

Például `http://contoso.com/video/*` iránti kérelmek VideoServerPoolba, míg a `http://contoso.com/images/*` felé irányuló kérelmek az ImageServerPoolba vannak továbbítva. Ha a kérés egyik elérésiút-kategóriába sem sorolható, a DefaultServerPool az alapértelmezett kiszolgáló.

További információt az [URL-útvonal-köröztetés – áttekintés című témakörben talál.](url-route-overview.md)

## <a name="multiple-site-hosting"></a>Több hely üzemeltetése

A többhelyes üzemeltetéssel egynél több webhelyet konfigurálhat ugyanazon az Application Gateway-példányon. Ez a szolgáltatás lehetővé teszi, hogy hatékonyabb topológiát konfiguráljon a központi telepítésekhez, ha legfeljebb 100 webhelyet ad hozzá egy Alkalmazásátjáróhoz (az optimális teljesítmény érdekében). Mindegyik webhelyet a saját készletéhez lehet irányítani. Az Application Gateway például a `contoso.com` és a `fabrikam.com` forgalmát is kiszolgálhatja a ContosoServerPool és a FabrikamServerPool kiszolgálókészletekből.

A `http://contoso.com` iránti kérelmek a ContosoServerPoolba, míg a `http://fabrikam.com` felé irányuló kérelmek a FabrikamServerPoolba vannak továbbítva.

Hasonlóképpen, ugyanazon szülőtartomány két altartományát ugyanazon Application Gateway-telepítésről üzemeltetheti. Az altartományok használatának példái között lehet az egyetlen Application Gateway-telepítésen üzemeltetett `http://blog.contoso.com` és `http://app.contoso.com`.

További információ: [Application Gateway multiple site hosting](multiple-site-overview.md).

## <a name="redirection"></a>Átirányítás

Számos webalkalmazás esetében gyakori eset az automatikus HTTP–HTTPS átirányítás annak érdekében, hogy az alkalmazás és a felhasználói közötti kommunikáció titkosított útvonalon történjen.

A múltban előfordulhat, hogy olyan technikákat használt, mint például a dedikált készlet létrehozása, amelynek egyetlen célja, hogy átirányítsa a HTTP-n kapott kérelmeket HTTPS-re. Az Application Gateway támogatja a forgalom az Application Gateway alapján való átirányításának lehetőségét. Ez leegyszerűsíti az alkalmazáskonfigurációt, optimalizálja az erőforrás-használatot, és új átirányítási forgatókönyveket támogat, például a globális és útvonalalapú átirányítást. Az Application Gateway átirányítási támogatása nem korlátozódik a HTTP-ről a HTTPS-átirányításra. Ez egy általános átirányítási mechanizmus, így a szabályokkal bármilyen megadott portról és portra átirányíthat. A szolgáltatás a külső webhelyre való átirányítást is támogatja.

Az Application Gateway átirányítási támogatása a következő funkciókat nyújtja:

- Globális átirányítás portok között a Gatewayen. Ez lehetővé teszi a HTTP–HTTPS átirányítást egy webhelyen.
- Útvonalalapú átirányítás. Ez a fajta átirányítás csak a megadott webhelyrészen engedélyezi a HTTP–HTTPS átirányítást, például egy `/cart/*` kifejezéssel jelzett bevásárlókosár részen.
- Átirányítás külső helyre.

További információt az [Application Gateway átirányítás – áttekintés című témakörben talál.](redirect-overview.md)

## <a name="session-affinity"></a>Munkamenet-affinitás

A Cookie-alapú munkamenet-affinitás akkor hasznos, ha egy felhasználói munkamenetet egy adott kiszolgálón szeretne tartani. Az átjáróval kezelt cookie-k használatával az Application Gateway képes egy felhasználói munkamenet minden újabb forgalmát ugyanarra a kiszolgálóra irányítani feldolgozásra. Ez a funkció olyan esetekben lehet fontos, amelyekben egy felhasználói munkamenethez tartozó munkamenet-állapotot helyileg ment a rendszer a kiszolgálón.

További információ: [Hogyan működik az alkalmazásátjáró.](how-application-gateway-works.md#modifications-to-the-request)

## <a name="websocket-and-http2-traffic"></a>Websocket- és HTTP/2-forgalom

Az Application Gateway natív támogatást nyújt a Websocket- és HTTP/2-protokollok számára. Kizárólag WebSocket-támogatásra vonatkozó felhasználói beállítás nem létezik.

A WebSocket és a HTTP/2 protokollok teljes körű duplex kommunikációt tesznek lehetővé egy kiszolgáló és egy ügyfél között egy hosszú ideig futó TCP-kapcsolaton. Ez interaktívabb kommunikációt eredményez a webkiszolgáló és az ügyél között, amely anélkül marad kétirányú, hogy a HTTP-alapú implementációkban kötelező lekérdezésekre lenne szükség. Ezek a protokollok a HTTP-vel ellentétben alacsony terheléssel rendelkeznek, és ugyanazt a TCP-kapcsolatot több kérelemhez/válaszhoz is felhasználhatják, ami hatékonyabb erőforrás-kihasználtságot eredményez. Ezek a protokollok a hagyományos, 80-as és 443-as HTTP-portokon működnek.

További információt a [WebSocket-támogatás](application-gateway-websocket.md) és [a HTTP/2 támogatás](configuration-overview.md#http2-support)című témakörben talál.

## <a name="connection-draining"></a>Kapcsolatkiürítés

A kapcsolatkiürítéssel zökkenőmentesen végrehajtható a háttérkészlettagok eltávolítása a tervezett szolgáltatásfrissítések során. E beállítás engedélyezése háttérbeli HTTP-beállítással történik, és a szabálylétrehozás keretében az adott háttérkészlet összes tagjára alkalmazható. Ha engedélyezve van, az Application Gateway biztosítja, hogy a háttérkészlet összes deregistering példánya ne kapjon új kérelmet, miközben lehetővé teszi a meglévő kérelmek teljesítését egy beállított időkorláton belül. Ez mind a háttérpéldányokra vonatkozik, amelyeket a felhasználói konfiguráció módosítása kifejezetten eltávolít a háttérkészletből, és az okat, amelyek et az állapotmintak által meghatározott állapotmentesállapotúként jelentenek. Ez alól az egyetlen kivétel a példányok regisztrációjának törléséhez kötött kérelmek, amelyeket az átjáró által kezelt munkamenet-affinitás miatt explicit módon töröltek a regisztrációból, és amelyek továbbra is a regisztráció megszüntetése korhatárosak.

További információt az [Alkalmazásátjáró konfigurációjának – áttekintés című témakörben talál.](configuration-overview.md#connection-draining)

## <a name="custom-error-pages"></a>Egyéni hibalapok

Az Application Gatewayjel testreszabhatók a hibaoldalak. Az egyéni hibaoldalakon feltüntetheti saját védjegyeit, és egyéni elrendezést használhat.

További információt az Egyéni hibák című [témakörben talál.](custom-error.md)

## <a name="rewrite-http-headers"></a>HTTP-fejlécek átírása

A HTTP-fejlécek lehetővé teszik, hogy az ügyfél és a kiszolgáló további információkat adjon át a kéréssel vagy a válaszsal. A HTTP-fejlécek újraírásával számos fontos forgatókönyvet érhet el, például:

- Biztonsági fejlécmezők, például HSTS/ X-XSS-Protection hozzáadása.
- Válaszfejléc-mezők eltávolítása, amelyek bizalmas információkat fedhetnek fel.
- Portadatok levonása az X-Forwarded-For fejlécekből.

Az Application Gateway támogatja a HTTP-kérelem- és válaszfejlécek hozzáadását, eltávolítását vagy frissítését, miközben a kérés- és válaszcsomagok az ügyfél- és a háttérkészletek között mozognak. Azt is lehetővé teszi, hogy feltételeket adjon hozzá annak érdekében, hogy a megadott fejlécek csak bizonyos feltételek teljesülése esetén legyenek újraírva.

További információt a [HTTP-fejlécek újraírása című témakörben talál.](rewrite-http-headers.md)

## <a name="sizing"></a>Méretezés

Az Application Gateway Standard_v2 konfigurálható automatikus skálázáshoz vagy rögzített méretű központi telepítésekhez. Ez a termékváltozat nem kínál különböző példányméreteket. A v2 teljesítményéről és díjszabásáról az [Automatikus skálázás v2 termékváltozatban](application-gateway-autoscaling-zone-redundant.md#pricing)talál további információt.

Az Application Gateway standard három méretben érhető el: **Kicsi,** **Közepes**és **Nagy**. A Kicsi méret ideális fejlesztési és tesztelési célokra.

Az Application Gateway korlátainak teljes listáját lásd: [Az Application Gateway szolgáltatási korlátozásai](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

Az alábbi táblázat az ssl-kiszervezést engedélyező alkalmazásátjáró v1-példányok átlagos teljesítmény-átviteli teljesítményét mutatja be:

| Átlagos háttéroldal-válaszméret | Kicsi | Közepes | Nagy |
| --- | --- | --- | --- |
| 6 KB |7,5 Mbps |13 Mbps |50 Mbps |
| 100 KB |35 Mbps |100 Mbps |200 Mbps |

> [!NOTE]
> Ezek az értékek az alkalmazásátjáró hozzávetőleges átviteli sebességét jelzik. A tényleges átvitel számos környezeti tényezőtől függ, például az átlagos lapmérettől, a háttérpéldányok helyétől és a lapkiszolgálás feldolgozási időtartamától. A pontos teljesítményszámokhoz saját teszteket kell futtatnia. Ezek az értékek csupán útmutatóul szolgálnak a kapacitástervezéshez.

## <a name="next-steps"></a>További lépések

- Az Application Gateway működésének megismerése – [Az alkalmazásátjáró működése](how-application-gateway-works.md)