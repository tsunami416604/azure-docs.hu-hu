---
title: Mi az Azure Application Gateway?
description: Megtudhatja, hogyan kezelheti az alkalmazása webes forgalmát az Azure Application Gateway segítségével.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 5/31/2019
ms.author: victorh
ms.openlocfilehash: 5f7fd47a096ddd57150a466f85fabcfc2f7045d9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564871"
---
# <a name="what-is-azure-application-gateway"></a>Mi az Azure Application Gateway?

Az Azure Application Gateway egy webes forgalomra vonatkozó terheléselosztó, amellyel kezelheti a webalkalmazásai forgalmát. A hagyományos terheléselosztók az átviteli rétegen működnek (OSI 4. réteg – TCP és UDP), és a forrás IP-címe és portja alapján irányítják a forgalmat a célállomás IP-címére és portjára.

![Application Gateway fogalma](media/overview/figure1-720.png)

A Application Gateway használatával a HTTP-kérések további attribútumain alapuló útválasztási döntéseket hozhat létre, például az URI elérési útvonalát vagy a gazdagép fejléceit. A forgalmat például a bejövő URL-cím alapján irányíthatja. Tehát ha a bejövő URL-cím a `/images` kifejezést tartalmazza, a forgalmat adott, képekre konfigurált kiszolgálókra irányíthatja (azaz egy készletbe). Ha `/video` a értéke az URL-cím, akkor a rendszer átirányítja a forgalmat egy másik, videókra optimalizált készletbe.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Ezt a fajta útválasztást alkalmazásrétegbeli (OSI 7. réteg) terheléselosztásnak nevezzük. Az Azure Application Gateway URL-alapú és egyéb útválasztásra is képes.

Az Azure Application Gateway a következő funkciókkal rendelkezik:

## <a name="secure-sockets-layer-ssltls-termination"></a>SSL (SSL/TLS) megszakítása

Az Application Gateway támogatja az SSL/TLS-lezárást az átjárón, amely után a forgalom általában titkosítatlan folyamatokat továbbít a háttér-kiszolgálókra. Ez a funkció lehetővé teszi, hogy a webkiszolgálók megszabaduljanak a magas titkosítási és visszafejtési üzemeltetési költségektől. A kiszolgálókon előforduló titkosítatlan kommunikáció azonban nem elfogadható megoldás. Ennek oka lehet a biztonsági követelmények, a megfelelőségi követelmények, vagy az alkalmazás csak biztonságos kapcsolatokat fogad el. Ezekben az alkalmazásokban az Application Gateway támogatja a végpontok közötti SSL/TLS titkosítást.

## <a name="autoscaling"></a>Automatikus skálázás

A Standard_v2 vagy a WAF_v2 SKU alatt üzemelő vagy WAF üzemelő példányok támogatják az automatikus skálázást, és vertikális fel-és leskálázást végeznek a forgalmi terhelési minták módosítása alapján. Application Gateway Az automatikus skálázással elkerülhető, hogy már a kiépítés során meg kelljen határozni az üzemelő példány méretét vagy a példányszámot. További információ a Application Gateway standard_v2 és WAF_v2 funkcióiról: automatikus [skálázás v2 SKU](application-gateway-autoscaling-zone-redundant.md).

## <a name="zone-redundancy"></a>Zóna redundancia

A Standard_v2 vagy a WAF_v2 SKU alá tartozó Application Gateway vagy WAF-alapú telepítések több Availability Zonesra is kiterjedhetnek, így jobb hibatűrést biztosítanak, és nem kell külön Application Gateway-átjárót kiépíteni minden zónában.

## <a name="static-vip"></a>Statikus VIP

Az Application Gateway VIP on Standard_v2 vagy WAF_v2 SKU kizárólag a statikus VIP-típusokat támogatja. Ez biztosítja, hogy az Application gatewayhez társított virtuális IP-cím még a Application Gateway élettartama alatt sem változik.

## <a name="web-application-firewall"></a>Webalkalmazási tűzfal

A webalkalmazási tűzfal (WAF) az Application Gateway egyik szolgáltatása, amely központi védelmet nyújt a webalkalmazásoknak a gyakori biztonsági rések ellen. A WAF az [alapvető OWASP- (Open Web Application Security Project-) szabálykészletek](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0-s vagy 2.2.9-es verzióinak szabályai alapján működik. 

A webalkalmazások egyre inkább ki vannak téve rosszindulatú támadásoknak, amelyek az ismert biztonsági réseket használják ki. Az ilyen jellegű támadások között például gyakoriak az SQL-injektálásos és a webhelyek közötti, parancsprogramot alkalmazó támadások. Az ilyen támadások megakadályozása az alkalmazás kódjában kihívást jelenthet, és szigorú felügyeletet, javítást és megfigyelést igényelhet az alkalmazás topológiájának számos rétegén. A központosított webalkalmazási tűzfal egyszerűbbé teszi a biztonságfelügyeletet, és segít az alkalmazás-rendszergazdáknak a fenyegetések vagy a behatolások elleni védekezésben. Emellett a WAF-megoldás gyorsabban képes kezelni a biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett hogy az egyes webalkalmazások védelmét biztosítaná. A meglévő alkalmazásátjárókat egyszerűen át lehet alakítani webalkalmazási tűzfallal rendelkező alkalmazásátjárókká.

További információ: webalkalmazási [tűzfal (WAF) Application Gateway](https://docs.microsoft.com/azure/application-gateway/waf-overview)).

## <a name="url-based-routing"></a>URL-alapú útválasztás

Az URL-alapú útválasztás lehetővé teszi, hogy a kérésben szereplő URL-cím alapján irányítsa a forgalmat a háttér-kiszolgálókészlethez. Az egyik lehetőség az, hogy a különböző típusú tartalmakra vonatkozó kéréseket különböző készletekhez irányítja.

Például `http://contoso.com/video/*` iránti kérelmek VideoServerPoolba, míg a `http://contoso.com/images/*` felé irányuló kérelmek az ImageServerPoolba vannak továbbítva. Ha a kérés egyik elérésiút-kategóriába sem sorolható, a DefaultServerPool az alapértelmezett kiszolgáló.

További információ: [URL-alapú útválasztás Application Gatewaysal](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

## <a name="multiple-site-hosting"></a>Több hely üzemeltetése

A többhelyes üzemeltetéssel egynél több webhelyet konfigurálhat ugyanazon az Application Gateway-példányon. Ez a funkció lehetővé teszi, hogy hatékonyabb topológiát konfiguráljon az üzemelő példányokhoz, ha akár 100 webhelyet ad hozzá egy Application gatewayhez. Mindegyik webhelyet a saját készletéhez lehet irányítani. Az Application Gateway például a `contoso.com` és a `fabrikam.com` forgalmát is kiszolgálhatja a ContosoServerPool és a FabrikamServerPool kiszolgálókészletekből.

A `http://contoso.com` iránti kérelmek a ContosoServerPoolba, míg a `http://fabrikam.com` felé irányuló kérelmek a FabrikamServerPoolba vannak továbbítva.

Hasonlóképpen, ugyanazon szülőtartomány két altartományát ugyanazon Application Gateway-telepítésről üzemeltetheti. Az altartományok használatának példái között lehet az egyetlen Application Gateway-telepítésen üzemeltetett `http://blog.contoso.com` és `http://app.contoso.com`.

További információ: többhelyes [üzemeltetés Application Gatewaysal](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

## <a name="redirection"></a>Átirányítás

Számos webalkalmazás esetében gyakori eset az automatikus HTTP–HTTPS átirányítás annak érdekében, hogy az alkalmazás és a felhasználói közötti kommunikáció titkosított útvonalon történjen.

A múltban olyan technikákat is használhat, mint például a dedikált készlet létrehozása, amelynek a célja, hogy átirányítsa a HTTP-n keresztül fogadott kérelmeket HTTPS-re. Az Application Gateway támogatja a forgalom az Application Gateway alapján való átirányításának lehetőségét. Ez leegyszerűsíti az alkalmazáskonfigurációt, optimalizálja az erőforrás-használatot, és új átirányítási forgatókönyveket támogat, például a globális és útvonalalapú átirányítást. Application Gateway átirányítás támogatása nem korlátozódik a HTTP-re a HTTPS-átirányításra. Ez egy általános átirányítási mechanizmus, így a szabályokkal bármilyen megadott portról és portra átirányíthat. A szolgáltatás a külső webhelyre való átirányítást is támogatja.

Az Application Gateway átirányítási támogatása a következő funkciókat nyújtja:

- Globális átirányítás portok között a Gatewayen. Ez lehetővé teszi a HTTP–HTTPS átirányítást egy webhelyen.
- Útvonalalapú átirányítás. Ez a fajta átirányítás csak a megadott webhelyrészen engedélyezi a HTTP–HTTPS átirányítást, például egy `/cart/*` kifejezéssel jelzett bevásárlókosár részen.
- Átirányítás külső helyre.

További információ: a [forgalom](https://docs.microsoft.com/azure/application-gateway/redirect-overview) átirányítása a Application Gatewaysal.

## <a name="session-affinity"></a>Munkamenet-affinitás

A Cookie-alapú munkamenet-affinitás akkor hasznos, ha egy felhasználói munkamenetet egy adott kiszolgálón szeretne tartani. Az átjáróval kezelt cookie-k használatával az Application Gateway képes egy felhasználói munkamenet minden újabb forgalmát ugyanarra a kiszolgálóra irányítani feldolgozásra. Ez a funkció olyan esetekben lehet fontos, amelyekben egy felhasználói munkamenethez tartozó munkamenet-állapotot helyileg ment a rendszer a kiszolgálón.

## <a name="websocket-and-http2-traffic"></a>Websocket- és HTTP/2-forgalom

Az Application Gateway natív támogatást nyújt a Websocket- és HTTP/2-protokollok számára. Kizárólag WebSocket-támogatásra vonatkozó felhasználói beállítás nem létezik.

A WebSocket és a HTTP/2 protokollok teljes körű duplex kommunikációt tesznek lehetővé egy kiszolgáló és egy ügyfél között egy hosszú ideig futó TCP-kapcsolaton. Ez interaktívabb kommunikációt eredményez a webkiszolgáló és az ügyél között, amely anélkül marad kétirányú, hogy a HTTP-alapú implementációkban kötelező lekérdezésekre lenne szükség. Ezek a protokollok alacsony terheléssel rendelkeznek, ellentétben a HTTP-vel, és több kérelem/válasz esetében is felhasználhatják ugyanazt a TCP-kapcsolatokat, ami hatékonyabb erőforrás-kihasználtságot eredményez. Ezek a protokollok a hagyományos, 80-as és 443-as HTTP-portokon működnek.

További információ: WebSocket- [támogatás](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) és [http/2-támogatás](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

## <a name="azure-kubernetes-service-aks-ingress-controller-preview"></a>Az Azure Kubernetes Service (AKS) bejövőforgalom-vezérlője (előzetes verzió) 

Az Application Gateway bejövőforgalom-vezérlője podként fut az AKS-fürtön belül, és lehetővé teszi az Application Gateway egy AKS-fürt bemeneti pontjaként való üzemeltetését. Ez csak Application Gateway v2 esetén támogatott.

További információt az [Azure Application Gateway bejövőforgalom-vezérlőjét](https://azure.github.io/application-gateway-kubernetes-ingress/) ismertető részben talál.

## <a name="connection-draining"></a>Kapcsolatkiürítés

A kapcsolatkiürítéssel zökkenőmentesen végrehajtható a háttérkészlettagok eltávolítása a tervezett szolgáltatásfrissítések során. E beállítás engedélyezése háttérbeli HTTP-beállítással történik, és a szabálylétrehozás keretében az adott háttérkészlet összes tagjára alkalmazható. Ha engedélyezve van, Application Gateway biztosítja, hogy a háttér-készletek összes példánya ne kapjon új kérést, miközben a meglévő kérések a beállított időkorláton belül befejeződik. Ez mind a háttérbeli példányokra vonatkozik, amelyeket a rendszer egy API-hívással kifejezetten eltávolít a háttérbeli készletből, és a háttérbeli példányokat, amelyeket az állapot-mintavételek határoznak meg.

## <a name="custom-error-pages"></a>Egyéni hibalapok

Az Application Gatewayjel testreszabhatók a hibaoldalak. Az egyéni hibaoldalakon feltüntetheti saját védjegyeit, és egyéni elrendezést használhat.

További információ: [Egyéni hibák](custom-error.md).

## <a name="rewrite-http-headers"></a>HTTP-fejlécek átírása

A HTTP-fejlécek lehetővé teszik az ügyfél és a kiszolgáló számára, hogy további információkat adjon át a kérésnek vagy a válasznak. A HTTP-fejlécek újraírásával több fontos forgatókönyvet is elvégezheti, például:

- Biztonsággal kapcsolatos fejléc-mezők hozzáadása, például HSTS/X-XSS-Protection.
- A bizalmas adatokat felderítő válasz fejléc mezőinek eltávolítása.
- A port adatainak kibontása az X által továbbított fejlécből.

Application Gateway támogatja a HTTP-kérelem és-válasz fejlécek hozzáadását, eltávolítását vagy frissítését, míg a kérelmek és válaszok csomagjai az ügyfél és a háttérbeli készletek között mozognak. Emellett lehetőséget biztosít olyan feltételek hozzáadására, amelyekkel biztosítható, hogy a megadott fejlécek csak akkor legyenek újraírva, ha bizonyos feltételek teljesülnek.

További információt a HTTP- [fejlécek](rewrite-http-headers.md)újraírása című témakörben talál.

## <a name="sizing"></a>Méretezés

Application Gateway Standard_v2 és a WAF_v2 SKU konfigurálható automatikus skálázáshoz vagy rögzített méretű központi telepítésekhez. Ezek a SKU-ket nem biztosítanak különböző méretű példányok.

A Application Gateway standard és WAF SKU jelenleg három méretben érhető el: **Kis**, **közepes**és **nagy**. A Kicsi méret ideális fejlesztési és tesztelési célokra.

Az Application Gateway korlátainak teljes listáját lásd: [Az Application Gateway szolgáltatási korlátozásai](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

Az alábbi táblázatban az egyes SSL-alapú kiszervezéshez engedélyezett alkalmazásátjárókhoz tartozó átlagos átviteli sebességek szerepelnek:

| Az átlagos háttér-oldal válaszának mérete | Kicsi | Közepes | Nagy |
| --- | --- | --- | --- |
| 6 KB |7,5 Mbps |13 Mbps |50 Mbps |
| 100 KB |35 Mbps |100 Mbps |200 Mbps |

> [!NOTE]
> Ezek az értékek az alkalmazásátjáró hozzávetőleges átviteli sebességét jelzik. A tényleges átvitel számos környezeti tényezőtől függ, például az átlagos lapmérettől, a háttérpéldányok helyétől és a lapkiszolgálás feldolgozási időtartamától. A pontos teljesítményszámokhoz saját teszteket kell futtatnia. Ezek az értékek csupán útmutatóul szolgálnak a kapacitástervezéshez.

## <a name="next-steps"></a>További lépések

Az igényeitől és a környezetétől függően az Azure Portallal, az Azure PowerShellel vagy az Azure CLI-vel hozhat létre egy Application Gateway-tesztet:

- [Rövid útmutató: Webes forgalom közvetlen továbbítása az Azure Application Gateway-Azure Portal](quick-create-portal.md)
- [Rövid útmutató: Webes forgalom közvetlen továbbítása az Azure Application Gateway-Azure PowerShell](quick-create-powershell.md)
- [Rövid útmutató: Webes forgalom közvetlen továbbítása az Azure Application Gateway – Azure CLI](quick-create-cli.md)
