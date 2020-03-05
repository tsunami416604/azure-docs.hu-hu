---
title: Mi az Azure Application Gateway?
description: Megtudhatja, hogyan kezelheti az alkalmazása webes forgalmát az Azure Application Gateway segítségével.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 03/03/2020
ms.author: victorh
ms.openlocfilehash: f3621feb688b3b257cd4f685a9be306d75700f4a
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273611"
---
# <a name="what-is-azure-application-gateway"></a>Mi az Azure Application Gateway?

Az Azure Application Gateway egy webes forgalomra vonatkozó terheléselosztó, amellyel kezelheti a webalkalmazásai forgalmát. A hagyományos terheléselosztók az átviteli rétegen működnek (OSI 4. réteg – TCP és UDP), és a forrás IP-címe és portja alapján irányítják a forgalmat a célállomás IP-címére és portjára.

![Application Gateway fogalma](media/overview/figure1-720.png)

A Application Gateway használatával a HTTP-kérések további attribútumain alapuló útválasztási döntéseket hozhat létre, például az URI elérési útvonalát vagy a gazdagép fejléceit. A forgalmat például a bejövő URL-cím alapján irányíthatja. Tehát ha a bejövő URL-cím a `/images` kifejezést tartalmazza, a forgalmat adott, képekre konfigurált kiszolgálókra irányíthatja (azaz egy készletbe). Ha `/video` szerepel az URL-címben, a rendszer átirányítja a forgalmat egy másik, videókra optimalizált készletbe.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Ezt a fajta útválasztást alkalmazásrétegbeli (OSI 7. réteg) terheléselosztásnak nevezzük. Az Azure Application Gateway URL-alapú és egyéb útválasztásra is képes.

>[!NOTE]
> Az Azure teljeskörűen felügyelt terheléselosztási megoldások együttesét biztosítja a különböző forgatókönyvekre. Ha nagy teljesítményű, kis késleltetésű, 4. rétegbeli terheléselosztásra van szüksége, tekintse meg [a mi az Azure Load Balancer?](../load-balancer/load-balancer-overview.md) Ha globális DNS-terheléselosztást keres, tekintse meg a [Mi az Traffic Manager?](../traffic-manager/traffic-manager-overview.md) A végpontok közötti forgatókönyvek a megoldások kombinálásával is hasznosak lehetnek.
>
> Az Azure terheléselosztási lehetőségeinek összehasonlítását lásd: [Az Azure terheléselosztási lehetőségeinek áttekintése](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

Az Azure Application Gateway a következő funkciókkal rendelkezik:

## <a name="secure-sockets-layer-ssltls-termination"></a>SSL (SSL/TLS) megszakítása

Az Application Gateway támogatja az SSL/TLS-lezárást az átjárón, amely után a forgalom általában titkosítatlan folyamatokat továbbít a háttér-kiszolgálókra. Ez a funkció lehetővé teszi, hogy a webkiszolgálók megszabaduljanak a magas titkosítási és visszafejtési üzemeltetési költségektől. A kiszolgálókon előforduló titkosítatlan kommunikáció azonban nem elfogadható megoldás. Ennek oka lehet a biztonsági követelmények, a megfelelőségi követelmények, vagy az alkalmazás csak biztonságos kapcsolatokat fogad el. Ezekben az alkalmazásokban az Application Gateway támogatja a végpontok közötti SSL/TLS titkosítást.

## <a name="autoscaling"></a>Automatikus skálázás

A Application Gateway Standard_v2 támogatja az automatikus skálázást, és a forgalmi terhelési minták módosításán alapuló vertikális fel-vagy leskálázást végez. Az automatikus skálázással elkerülhető, hogy már a kiépítés során meg kelljen határozni az üzemelő példány méretét vagy a példányszámot. További információ a Application Gateway Standard_v2 funkcióival kapcsolatban: [autoskálázás v2 SKU](application-gateway-autoscaling-zone-redundant.md).

## <a name="zone-redundancy"></a>Zóna redundancia

Egy Application Gateway Standard_v2 több Availability Zonesra terjedhet ki, így jobb hibatűrést biztosít, és nem kell külön Application Gateway-átjárókat kiépíteni minden zónában.

## <a name="static-vip"></a>Statikus VIP

Az Application Gateway Standard_v2 SKU kizárólag a statikus VIP-típust támogatja. Ez biztosítja, hogy az Application gatewayhez társított virtuális IP-cím még a Application Gateway élettartama alatt sem változik.

## <a name="web-application-firewall"></a>Web application firewall (Webalkalmazási tűzfal)

A webalkalmazási tűzfal (WAF) egy olyan szolgáltatás, amely központosított védelmet biztosít a webalkalmazások számára a gyakori biztonsági rések és sebezhetőségek ellen. A WAF a [OWASP (webalkalmazás-biztonsági projekt megnyitása) alapszabálya](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) , a 3,1 (csak WAF_v2), a 3,0 és a 2.2.9. 

A webalkalmazások egyre inkább ki vannak téve rosszindulatú támadásoknak, amelyek az ismert biztonsági réseket használják ki. Az ilyen jellegű támadások között például gyakoriak az SQL-injektálásos és a webhelyek közötti, parancsprogramot alkalmazó támadások. Az ilyen támadások megakadályozása az alkalmazás kódjában kihívást jelenthet, és szigorú felügyeletet, javítást és megfigyelést igényelhet az alkalmazás topológiájának számos rétegén. A központosított webalkalmazási tűzfal egyszerűbbé teszi a biztonságfelügyeletet, és segít az alkalmazás-rendszergazdáknak a fenyegetések vagy a behatolások elleni védekezésben. Emellett a WAF-megoldás gyorsabban képes kezelni a biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett hogy az egyes webalkalmazások védelmét biztosítaná. A meglévő alkalmazásátjárókat egyszerűen át lehet alakítani webalkalmazási tűzfallal rendelkező alkalmazásátjárókká.

További információ: [Mi az az Azure webalkalmazási tűzfal?](../web-application-firewall/overview.md).

## <a name="ingress-controller-for-aks"></a>Bejövőforgalom-vezérlő az AKS-hez
A Application Gateway beáramlási vezérlő (AGIC) lehetővé teszi, hogy a Application Gateway használja az [Azure Kubernetes-szolgáltatás (ak)](https://azure.microsoft.com/services/kubernetes-service/) fürtjének bemenő példánya számára. 

A bejövő vezérlő egy Pod-ként fut az AK-fürtön belül, és a [Kubernetes bejövő erőforrásait](https://kubernetes.io/docs/concepts/services-networking/ingress/) használja, és átalakítja azokat egy Application Gateway konfigurációba, amely lehetővé teszi az átjáró számára a forgalom terheléselosztását a Kubernetes-hüvelybe. A bejövő vezérlő csak Application Gateway Standard_v2 és WAF_v2 SKU-t támogat. 

További információ: [Application Gateway Inbehatolási vezérlő (AGIC)](ingress-controller-overview.md).

## <a name="url-based-routing"></a>URL-alapú útválasztás

Az URL-alapú útválasztás lehetővé teszi, hogy a kérésben szereplő URL-cím alapján irányítsa a forgalmat a háttér-kiszolgálókészlethez. Az egyik lehetőség az, hogy a különböző típusú tartalmakra vonatkozó kéréseket különböző készletekhez irányítja.

Például `http://contoso.com/video/*` iránti kérelmek VideoServerPoolba, míg a `http://contoso.com/images/*` felé irányuló kérelmek az ImageServerPoolba vannak továbbítva. Ha a kérés egyik elérésiút-kategóriába sem sorolható, a DefaultServerPool az alapértelmezett kiszolgáló.

További információ: [URL-alapú útválasztás Application Gatewaysal](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

## <a name="multiple-site-hosting"></a>Több hely üzemeltetése

A többhelyes üzemeltetéssel egynél több webhelyet konfigurálhat ugyanazon az Application Gateway-példányon. Ez a funkció lehetővé teszi, hogy hatékonyabb topológiát konfiguráljon az üzemelő példányokhoz, ha akár 100 webhelyet ad hozzá egy Application Gatewayhoz (az optimális teljesítmény érdekében). Mindegyik webhelyet a saját készletéhez lehet irányítani. Az Application Gateway például a `contoso.com` és a `fabrikam.com` forgalmát is kiszolgálhatja a ContosoServerPool és a FabrikamServerPool kiszolgálókészletekből.

A `http://contoso.com` iránti kérelmek a ContosoServerPoolba, míg a `http://fabrikam.com` felé irányuló kérelmek a FabrikamServerPoolba vannak továbbítva.

Hasonlóképpen, ugyanazon szülőtartomány két altartományát ugyanazon Application Gateway-telepítésről üzemeltetheti. Az altartományok használatának példái között lehet az egyetlen Application Gateway-telepítésen üzemeltetett `http://blog.contoso.com` és `http://app.contoso.com`.

További információ: [többhelyes üzemeltetés Application Gatewaysal](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

## <a name="redirection"></a>Átirányítás

Számos webalkalmazás esetében gyakori eset az automatikus HTTP–HTTPS átirányítás annak érdekében, hogy az alkalmazás és a felhasználói közötti kommunikáció titkosított útvonalon történjen.

A múltban olyan technikákat is használhat, mint például a dedikált készlet létrehozása, amelynek a célja, hogy átirányítsa a HTTP-n keresztül fogadott kérelmeket HTTPS-re. Az Application Gateway támogatja a forgalom az Application Gateway alapján való átirányításának lehetőségét. Ez leegyszerűsíti az alkalmazáskonfigurációt, optimalizálja az erőforrás-használatot, és új átirányítási forgatókönyveket támogat, például a globális és útvonalalapú átirányítást. Application Gateway átirányítás támogatása nem korlátozódik a HTTP-re a HTTPS-átirányításra. Ez egy általános átirányítási mechanizmus, így a szabályokkal bármilyen megadott portról és portra átirányíthat. A szolgáltatás a külső webhelyre való átirányítást is támogatja.

Az Application Gateway átirányítási támogatása a következő funkciókat nyújtja:

- Globális átirányítás portok között a Gatewayen. Ez lehetővé teszi a HTTP–HTTPS átirányítást egy webhelyen.
- Útvonalalapú átirányítás. Ez a fajta átirányítás csak a megadott webhelyrészen engedélyezi a HTTP–HTTPS átirányítást, például egy `/cart/*` kifejezéssel jelzett bevásárlókosár részen.
- Átirányítás külső helyre.

További információ: a [forgalom átirányítása](https://docs.microsoft.com/azure/application-gateway/redirect-overview) a Application Gatewaysal.

## <a name="session-affinity"></a>Munkamenet-affinitás

A Cookie-alapú munkamenet-affinitás akkor hasznos, ha egy felhasználói munkamenetet egy adott kiszolgálón szeretne tartani. Az átjáróval kezelt cookie-k használatával az Application Gateway képes egy felhasználói munkamenet minden újabb forgalmát ugyanarra a kiszolgálóra irányítani feldolgozásra. Ez a funkció olyan esetekben lehet fontos, amelyekben egy felhasználói munkamenethez tartozó munkamenet-állapotot helyileg ment a rendszer a kiszolgálón.

## <a name="websocket-and-http2-traffic"></a>Websocket- és HTTP/2-forgalom

Az Application Gateway natív támogatást nyújt a Websocket- és HTTP/2-protokollok számára. Kizárólag WebSocket-támogatásra vonatkozó felhasználói beállítás nem létezik.

A WebSocket és a HTTP/2 protokollok teljes körű duplex kommunikációt tesznek lehetővé egy kiszolgáló és egy ügyfél között egy hosszú ideig futó TCP-kapcsolaton. Ez interaktívabb kommunikációt eredményez a webkiszolgáló és az ügyél között, amely anélkül marad kétirányú, hogy a HTTP-alapú implementációkban kötelező lekérdezésekre lenne szükség. Ezek a protokollok alacsony terheléssel rendelkeznek, ellentétben a HTTP-vel, és több kérelem/válasz esetében is felhasználhatják ugyanazt a TCP-kapcsolatokat, ami hatékonyabb erőforrás-kihasználtságot eredményez. Ezek a protokollok a hagyományos, 80-as és 443-as HTTP-portokon működnek.

További információ: [WebSocket-támogatás](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) és [http/2-támogatás](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

## <a name="connection-draining"></a>Kapcsolatkiürítés

A kapcsolatkiürítéssel zökkenőmentesen végrehajtható a háttérkészlettagok eltávolítása a tervezett szolgáltatásfrissítések során. E beállítás engedélyezése háttérbeli HTTP-beállítással történik, és a szabálylétrehozás keretében az adott háttérkészlet összes tagjára alkalmazható. Ha engedélyezve van, a Application Gateway biztosítja, hogy a háttér-készletek összes regisztrációja ne kapjon új kérést, miközben lehetővé teszi a meglévő kérelmek befejezését egy beállított időkorláton belül. Ez mindkét háttérbeli példányra vonatkozik, amelyeket a rendszer kifejezetten eltávolít a háttérbeli készletből egy felhasználói konfigurációs módosítással, és a háttérbeli példányokat, amelyeket a rendszer nem kifogástalanként jelentett a Health-Szondák által meghatározott módon. Ez alól egyetlen kivétel a példányok deregisztrációja, amelyek explicit módon lettek törölve, mert az átjáró által felügyelt munkamenet affinitása miatt a rendszer továbbra is a deregistering instances-ben marad.

További információkért tekintse meg a [Application Gateway konfiguráció áttekintése](https://docs.microsoft.com/azure/application-gateway/configuration-overview#connection-draining)című szakaszt.

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

Application Gateway Standard_v2 konfigurálható automatikus skálázáshoz vagy rögzített méretű központi telepítésekhez. Ez az SKU nem biztosít különböző méretű példányokat. A v2 teljesítményével és díjszabásával kapcsolatos további információkért lásd: automatikus [skálázás v2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#pricing).

A standard szintű Application Gateway három méretben érhető el: **kicsi**, **közepes**és **nagy**. A Kicsi méret ideális fejlesztési és tesztelési célokra.

Az Application Gateway korlátainak teljes listáját lásd: [Az Application Gateway szolgáltatási korlátozásai](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

Az alábbi táblázat az egyes Application Gateway v1-példányok átlagos teljesítmény-átviteli sebességét mutatja be, amelyeken engedélyezve van az SSL-kiszervezés:

| Az átlagos háttér-oldal válaszának mérete | Kicsi | Közepes | Nagy |
| --- | --- | --- | --- |
| 6 KB |7,5 Mbps |13 Mbps |50 Mbps |
| 100 KB |35 Mbps |100 Mbps |200 Mbps |

> [!NOTE]
> Ezek az értékek az alkalmazásátjáró hozzávetőleges átviteli sebességét jelzik. A tényleges átvitel számos környezeti tényezőtől függ, például az átlagos lapmérettől, a háttérpéldányok helyétől és a lapkiszolgálás feldolgozási időtartamától. A pontos teljesítményszámokhoz saját teszteket kell futtatnia. Ezek az értékek csupán útmutatóul szolgálnak a kapacitástervezéshez.

## <a name="next-steps"></a>Következő lépések

Az igényeitől és a környezetétől függően az Azure Portallal, az Azure PowerShellel vagy az Azure CLI-vel hozhat létre egy Application Gateway-tesztet:

- [Gyors útmutató: webes forgalom közvetlen továbbítása az Azure Application Gateway-Azure Portal](quick-create-portal.md)
- [Első lépések – A webes forgalom irányítása az Azure Application Gateway szolgáltatással – Azure PowerShell](quick-create-powershell.md)
- [Első lépések – A webes forgalom irányítása az Azure Application Gateway szolgáltatással – Azure CLI](quick-create-cli.md)
