---
title: Azure Application Gateway-funkciók
description: További tudnivalók az Azure Application Gateway szolgáltatásairól
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: victorh
ms.openlocfilehash: f021eed959ef88a1ef3671e1d0ace8080710c92a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80810232"
---
# <a name="azure-application-gateway-features"></a>Azure Application Gateway-funkciók

Az [Azure Application Gateway](overview.md) egy webes forgalomra vonatkozó terheléselosztó, amellyel kezelheti a webalkalmazásai forgalmát.

![Application Gateway fogalma](media/overview/figure1-720.png)

Application Gateway a következő funkciókat tartalmazza:

- [SSL (SSL/TLS) megszakítása](#secure-sockets-layer-ssltls-termination)
- [Automatikus skálázás](#autoscaling)
- [Zóna redundancia](#zone-redundancy)
- [Statikus VIP](#static-vip)
- [Webalkalmazási tűzfal](#web-application-firewall)
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

## <a name="secure-sockets-layer-ssltls-termination"></a>SSL (SSL/TLS) megszakítása

Az Application Gateway támogatja az SSL/TLS-lezárást az átjárón, amely után a forgalom általában titkosítatlan folyamatokat továbbít a háttér-kiszolgálókra. Ez a funkció lehetővé teszi, hogy a webkiszolgálók megszabaduljanak a magas titkosítási és visszafejtési üzemeltetési költségektől. Előfordulhat azonban, hogy a kiszolgálókkal való titkosítatlan kommunikáció nem elfogadható lehetőség. Ennek oka lehet a biztonsági követelmények, a megfelelőségi követelmények, vagy az alkalmazás csak biztonságos kapcsolatokat fogad el. Ezekben az alkalmazásokban az Application Gateway támogatja a végpontok közötti SSL/TLS titkosítást.

További információ: [az SSL-lezárás és a végpontok közötti SSL áttekintése Application Gateway](ssl-overview.md)

## <a name="autoscaling"></a>Automatikus skálázás

A Application Gateway Standard_v2 támogatja az automatikus skálázást, és a forgalmi terhelési minták módosításán alapuló vertikális fel-vagy leskálázást végez. Az automatikus skálázással elkerülhető, hogy már a kiépítés során meg kelljen határozni az üzemelő példány méretét vagy a példányszámot. 

További információ a Application Gateway Standard_v2 funkcióival kapcsolatban: [autoskálázás v2 SKU](application-gateway-autoscaling-zone-redundant.md).

## <a name="zone-redundancy"></a>Zóna redundancia

A Standard_v2 Application Gateway több Availability Zonesra terjedhet ki, így jobb hibatűrést biztosít, és nem kell külön Application Gateway-átjárókat kiépíteni minden zónában.

## <a name="static-vip"></a>Statikus VIP

Az Application Gateway Standard_v2 SKU kizárólag a statikus VIP-típust támogatja. Ez biztosítja, hogy az Application gatewayhez társított virtuális IP-cím még a Application Gateway élettartama alatt sem változik.

## <a name="web-application-firewall"></a>Webalkalmazási tűzfal

A webalkalmazási tűzfal (WAF) egy olyan szolgáltatás, amely központosított védelmet biztosít a webalkalmazások számára a gyakori biztonsági rések és sebezhetőségek ellen. A WAF a [OWASP (webalkalmazás-biztonsági projekt megnyitása) alapszabálya](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) , a 3,1 (csak WAF_v2), a 3,0 és a 2.2.9. 

A webalkalmazások egyre inkább ki vannak téve rosszindulatú támadásoknak, amelyek az ismert biztonsági réseket használják ki. Az ilyen jellegű támadások között például gyakoriak az SQL-injektálásos és a webhelyek közötti, parancsprogramot alkalmazó támadások. Az ilyen támadások megakadályozása az alkalmazás kódjában kihívást jelenthet, és szigorú felügyeletet, javítást és megfigyelést igényelhet az alkalmazás topológiájának számos rétegén. A központosított webalkalmazási tűzfal egyszerűbbé teszi a biztonságfelügyeletet, és segít az alkalmazás-rendszergazdáknak a fenyegetések vagy a behatolások elleni védekezésben. Emellett a WAF-megoldás gyorsabban képes kezelni a biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett hogy az egyes webalkalmazások védelmét biztosítaná. A meglévő Application Gateway-átjárók egyszerűen alakíthatók át egy webalkalmazási tűzfallal kompatibilis Application Gateway-átjáróra.

További információ: [Mi az az Azure webalkalmazási tűzfal?](../web-application-firewall/overview.md).

## <a name="ingress-controller-for-aks"></a>Bejövőforgalom-vezérlő az AKS-hez
A Application Gateway beáramlási vezérlő (AGIC) lehetővé teszi, hogy a Application Gateway használja az [Azure Kubernetes-szolgáltatás (ak)](https://azure.microsoft.com/services/kubernetes-service/) fürtjének bemenő példánya számára. 

A bejövő vezérlő az AK-fürtön belül fut, és a Kubernetes beáramlási [erőforrásait](https://kubernetes.io/docs/concepts/services-networking/ingress/) használja, és átalakítja őket egy Application Gateway konfigurációra, amely lehetővé teszi az átjáró számára a forgalom terheléselosztását a Kubernetes-hüvelybe. A bejövő vezérlő csak Application Gateway Standard_v2 és WAF_v2 SKU-t támogat. 

További információ: [Application Gateway Inbehatolási vezérlő (AGIC)](ingress-controller-overview.md).

## <a name="url-based-routing"></a>URL-alapú útválasztás

Az URL-alapú útválasztás lehetővé teszi, hogy a kérésben szereplő URL-cím alapján irányítsa a forgalmat a háttér-kiszolgálókészlethez. Az egyik lehetőség az, hogy a különböző típusú tartalmakra vonatkozó kéréseket különböző készletekhez irányítja.

Például `http://contoso.com/video/*` iránti kérelmek VideoServerPoolba, míg a `http://contoso.com/images/*` felé irányuló kérelmek az ImageServerPoolba vannak továbbítva. Ha a kérés egyik elérésiút-kategóriába sem sorolható, a DefaultServerPool az alapértelmezett kiszolgáló.

További információ: az [URL-alapú útválasztás áttekintése](url-route-overview.md).

## <a name="multiple-site-hosting"></a>Több hely üzemeltetése

A többhelyes üzemeltetéssel egynél több webhelyet konfigurálhat ugyanazon az Application Gateway-példányon. Ez a funkció lehetővé teszi, hogy hatékonyabb topológiát konfiguráljon az üzemelő példányokhoz, ha akár 100 webhelyet ad hozzá egy Application Gatewayhoz (az optimális teljesítmény érdekében). Mindegyik webhelyet a saját készletéhez lehet irányítani. Az Application Gateway például a `contoso.com` és a `fabrikam.com` forgalmát is kiszolgálhatja a ContosoServerPool és a FabrikamServerPool kiszolgálókészletekből.

A `http://contoso.com` iránti kérelmek a ContosoServerPoolba, míg a `http://fabrikam.com` felé irányuló kérelmek a FabrikamServerPoolba vannak továbbítva.

Hasonlóképpen, ugyanazon szülőtartomány két altartományát ugyanazon Application Gateway-telepítésről üzemeltetheti. Az altartományok használatának példái között lehet az egyetlen Application Gateway-telepítésen üzemeltetett `http://blog.contoso.com` és `http://app.contoso.com`.

További információ: [Application Gateway több hely üzemeltetése](multiple-site-overview.md).

## <a name="redirection"></a>Átirányítás

Számos webalkalmazás esetében gyakori eset az automatikus HTTP–HTTPS átirányítás annak érdekében, hogy az alkalmazás és a felhasználói közötti kommunikáció titkosított útvonalon történjen.

A múltban olyan technikákat is használhat, mint például a dedikált készlet létrehozása, amelynek a célja, hogy átirányítsa a HTTP-n keresztül fogadott kérelmeket HTTPS-re. Az Application Gateway támogatja a forgalom az Application Gateway alapján való átirányításának lehetőségét. Ez leegyszerűsíti az alkalmazáskonfigurációt, optimalizálja az erőforrás-használatot, és új átirányítási forgatókönyveket támogat, például a globális és útvonalalapú átirányítást. Application Gateway átirányítás támogatása nem korlátozódik a HTTP-re a HTTPS-átirányításra. Ez egy általános átirányítási mechanizmus, így a szabályokkal bármilyen megadott portról és portra átirányíthat. A szolgáltatás a külső webhelyre való átirányítást is támogatja.

Az Application Gateway átirányítási támogatása a következő funkciókat nyújtja:

- Globális átirányítás portok között a Gatewayen. Ez lehetővé teszi a HTTP–HTTPS átirányítást egy webhelyen.
- Útvonalalapú átirányítás. Ez a fajta átirányítás csak a megadott webhelyrészen engedélyezi a HTTP–HTTPS átirányítást, például egy `/cart/*` kifejezéssel jelzett bevásárlókosár részen.
- Átirányítás külső helyre.

További információ: [Application Gateway átirányítások áttekintése](redirect-overview.md).

## <a name="session-affinity"></a>Munkamenet-affinitás

A Cookie-alapú munkamenet-affinitás akkor hasznos, ha egy felhasználói munkamenetet egy adott kiszolgálón szeretne tartani. Az átjáróval kezelt cookie-k használatával az Application Gateway képes egy felhasználói munkamenet minden újabb forgalmát ugyanarra a kiszolgálóra irányítani feldolgozásra. Ez a funkció olyan esetekben lehet fontos, amelyekben egy felhasználói munkamenethez tartozó munkamenet-állapotot helyileg ment a rendszer a kiszolgálón.

További információt az [Application Gateway működéséről](how-application-gateway-works.md#modifications-to-the-request)szóló témakörben talál.

## <a name="websocket-and-http2-traffic"></a>Websocket- és HTTP/2-forgalom

Az Application Gateway natív támogatást nyújt a Websocket- és HTTP/2-protokollok számára. Kizárólag WebSocket-támogatásra vonatkozó felhasználói beállítás nem létezik.

A WebSocket és a HTTP/2 protokollok teljes körű duplex kommunikációt tesznek lehetővé egy kiszolgáló és egy ügyfél között egy hosszú ideig futó TCP-kapcsolaton. Ez interaktívabb kommunikációt eredményez a webkiszolgáló és az ügyél között, amely anélkül marad kétirányú, hogy a HTTP-alapú implementációkban kötelező lekérdezésekre lenne szükség. Ezek a protokollok alacsony terheléssel rendelkeznek, ellentétben a HTTP-vel, és több kérelem/válasz esetében is felhasználhatják ugyanazt a TCP-kapcsolatokat, ami hatékonyabb erőforrás-kihasználtságot eredményez. Ezek a protokollok a hagyományos, 80-as és 443-as HTTP-portokon működnek.

További információ: [WebSocket-támogatás](application-gateway-websocket.md) és [http/2-támogatás](configuration-overview.md#http2-support).

## <a name="connection-draining"></a>Kapcsolatkiürítés

A kapcsolatkiürítéssel zökkenőmentesen végrehajtható a háttérkészlettagok eltávolítása a tervezett szolgáltatásfrissítések során. E beállítás engedélyezése háttérbeli HTTP-beállítással történik, és a szabálylétrehozás keretében az adott háttérkészlet összes tagjára alkalmazható. Ha engedélyezve van, a Application Gateway biztosítja, hogy a háttérbeli készletek összes regisztrációja ne kapjon új kérést, miközben lehetővé teszi a meglévő kérelmek befejezését egy beállított időkorláton belül. Ez mindkét háttérbeli példányra vonatkozik, amelyeket a rendszer kifejezetten eltávolít a háttérbeli készletből egy felhasználói konfigurációs módosítással, és a háttérbeli példányokat, amelyeket a rendszer nem kifogástalanként jelentett a Health-Szondák által meghatározott módon. Ez alól kivételt képeznek a példányok deregisztrációja, amelyek explicit módon lettek elvégezve, mert az átjáró által felügyelt munkamenet-affinitása miatt a rendszer továbbra is a deregistering instances-ben folytatja a regisztrációt.

További információ: [Application Gateway konfiguráció áttekintése](configuration-overview.md#connection-draining).

## <a name="custom-error-pages"></a>Egyéni hibalapok

Az Application Gatewayjel testreszabhatók a hibaoldalak. Az egyéni hibaoldalakon feltüntetheti saját védjegyeit, és egyéni elrendezést használhat.

További információ: [Egyéni hibák](custom-error.md).

## <a name="rewrite-http-headers"></a>HTTP-fejlécek átírása

A HTTP-fejlécek lehetővé teszik az ügyfél és a kiszolgáló számára, hogy további információkat adjon át a kérésnek vagy a válasznak. A HTTP-fejlécek újraírásával több fontos forgatókönyvet is elvégezheti, például:

- Biztonsággal kapcsolatos fejléc-mezők hozzáadása, például HSTS/X-XSS-Protection.
- A bizalmas adatokat felderítő válasz fejléc mezőinek eltávolítása.
- A port adatainak kibontása az X által továbbított fejlécből.

Application Gateway támogatja a HTTP-kérelem és-válasz fejlécek hozzáadását, eltávolítását vagy frissítését, míg a kérelmek és válaszok csomagjai az ügyfél és a háttérbeli készletek között mozognak. Emellett lehetőséget biztosít olyan feltételek hozzáadására, amelyekkel biztosítható, hogy a megadott fejlécek csak akkor legyenek újraírva, ha bizonyos feltételek teljesülnek.

További információt a HTTP- [fejlécek újraírása](rewrite-http-headers.md)című témakörben talál.

## <a name="sizing"></a>Méretezés

Application Gateway Standard_v2 konfigurálható automatikus skálázáshoz vagy rögzített méretű központi telepítésekhez. Ez az SKU nem biztosít különböző méretű példányokat. A v2 teljesítményével és díjszabásával kapcsolatos további információkért lásd: automatikus [skálázás v2 SKU](application-gateway-autoscaling-zone-redundant.md#pricing).

A standard szintű Application Gateway három méretben érhető el: **kicsi**, **közepes**és **nagy**. A Kicsi méret ideális fejlesztési és tesztelési célokra.

Az Application Gateway korlátainak teljes listáját lásd: [Az Application Gateway szolgáltatási korlátozásai](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

Az alábbi táblázat az egyes Application Gateway v1-példányok átlagos teljesítmény-átviteli sebességét mutatja be, amelyeken engedélyezve van az SSL-kiszervezés:

| Az átlagos háttér-oldal válaszának mérete | Kicsi | Közepes | Nagy |
| --- | --- | --- | --- |
| 6 KB |7,5 Mbps |13 Mbps |50 Mbps |
| 100 KB |35 Mbps |100 Mbps |200 Mbps |

> [!NOTE]
> Ezek az értékek az alkalmazásátjáró hozzávetőleges átviteli sebességét jelzik. A tényleges átvitel számos környezeti tényezőtől függ, például az átlagos lapmérettől, a háttérpéldányok helyétől és a lapkiszolgálás feldolgozási időtartamától. A pontos teljesítményszámokhoz saját teszteket kell futtatnia. Ezek az értékek csupán útmutatóul szolgálnak a kapacitástervezéshez.

## <a name="version-feature-comparison"></a>Verzió funkcióinak összehasonlítása

Application Gateway v1-v2 funkciók összehasonlításához lásd: automatikus [skálázás és zóna – redundáns Application Gateway v2](application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku)

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan működik az Application Gateway működése – [az Application Gateway működése](how-application-gateway-works.md)