---
title: Mi az Azure Application Gateway?
description: Megtudhatja, hogyan kezelheti az alkalmazása webes forgalmát az Azure Application Gateway segítségével.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 1/11/2019
ms.author: victorh
ms.openlocfilehash: 21aac318542f9d30cb44d940392d05367f1f7b9f
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246466"
---
# <a name="what-is-azure-application-gateway"></a>Mi az Azure Application Gateway?

Az Azure Application Gateway egy webes forgalomra vonatkozó terheléselosztó, amellyel kezelheti a webalkalmazásai forgalmát. A hagyományos terheléselosztók az átviteli rétegen működnek (OSI 4. réteg – TCP és UDP), és a forrás IP-címe és portja alapján irányítják a forgalmat a célállomás IP-címére és portjára.

![Az Application Gateway fogalmi](media/overview/figure1-720.png)

Az Application Gateway segítségével azonban még részletesebben kezelheti mindezt. A forgalmat például a bejövő URL-cím alapján irányíthatja. Tehát ha a bejövő URL-cím a `/images` kifejezést tartalmazza, a forgalmat adott, képekre konfigurált kiszolgálókra irányíthatja (azaz egy készletbe). Ha az URL-cím a `/video` kifejezést tartalmazza, a forgalom egy másik, videók számára optimalizált készlethez kerül.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Ezt a fajta útválasztást alkalmazásrétegbeli (OSI 7. réteg) terheléselosztásnak nevezzük. Az Azure Application Gateway URL-alapú és egyéb útválasztásra is képes. 

Az Azure Application Gateway a következő funkciókkal rendelkezik:

## <a name="autoscaling-public-preview"></a>Az automatikus skálázás nyilvános előzetes verziója

A jelen cikkben ismertetett szolgáltatások mellett az Application Gateway nyilvános előzetes verziója egy új Termékváltozatban [Standard_V2], így az automatikus skálázás és egyéb fontos teljesítményjavítás is kínál.

- **Automatikus skálázás** – Az automatikus skálázású termékváltozat lehetővé teszi az üzemelő Application Gateway- vagy WAF-példányok esetében a változó adatforgalmi terhelési mintázatok alapján történő vertikális fel- és leskálázást. Az automatikus skálázással elkerülhető, hogy már a kiépítés során meg kelljen határozni az üzemelő példány méretét vagy a példányszámot. 

- **Zónaredundancia**  – Az üzemelő Application Gateway- vagy WAF-példányok átfoghatnak több rendelkezésre állási zónát is, így nem kell minden egyes zónában külön Application Gateway-példányokat kiépíteni és átléptetni a Traffic Managerrel.

- **Statikus virtuális IP-cím** – Az Application Gateway virtuális IP-címe mostantól kizárólag statikus virtuális IP-cím lehet. Így biztosítható, hogy az Application Gatewayhez társított virtuális IP-cím újraindítást követően se módosuljon.

- **Gyorsabb üzembe helyezés és frissítés** az általánosan elérhető termékváltozathoz képest. 

- **Ötször hatékonyabb SSL-kiszervezés** az általánosan elérhető termékváltozathoz képest.

Az Application Gateway nyilvános előzetes verziójú funkcióival kapcsolatban további információt az [automatikus skálázású és zónaredundáns Application Gatewayt (nyilvános előzetes verzió)](application-gateway-autoscaling-zone-redundant.md) bemutató cikk tartalmaz.

## <a name="secure-sockets-layer-ssl-termination"></a>Secure Sockets Layer- (SSL-) lezárás

Az Application Gateway támogatja az SSL-lezárást az átjárónál, ami után a forgalom rendszerint titkosítatlanul áramlik a háttérkiszolgálókhoz. Ez a funkció lehetővé teszi, hogy a webkiszolgálók megszabaduljanak a magas titkosítási és visszafejtési üzemeltetési költségektől. Azonban a kiszolgálókhoz néha titkosítatlan kommunikáció nem elfogadható megoldás. Ezt okozhatja a biztonsági követelmények, megfelelőségi előírások, vagy az alkalmazás csak biztonságos kapcsolatot fogad el. Ezekhez az alkalmazásokhoz az application gateway támogatja a teljes körű SSL-titkosítást.

## <a name="azure-kubernetes-service-aks-ingress-controller-preview"></a>Az Azure Kubernetes Service (AKS) bejövőforgalom-vezérlője (előzetes verzió) 

Az Application Gateway bejövőforgalom-vezérlője podként fut az AKS-fürtön belül, és lehetővé teszi az Application Gateway egy AKS-fürt bemeneti pontjaként való üzemeltetését. 

További információt az [Azure Application Gateway bejövőforgalom-vezérlőjét](https://azure.github.io/application-gateway-kubernetes-ingress/) ismertető részben talál.

## <a name="connection-draining"></a>Kapcsolatkiürítés

A kapcsolatkiürítéssel zökkenőmentesen végrehajtható a háttérkészlettagok eltávolítása a tervezett szolgáltatásfrissítések során. E beállítás engedélyezése háttérbeli HTTP-beállítással történik, és a szabálylétrehozás keretében az adott háttérkészlet összes tagjára alkalmazható. Az engedélyezést követően az Application Gateway gondoskodik arról, hogy azon háttérkészletbeli példányok, amelyek regisztrációja megszüntetés alatt áll, ne fogadjanak új kéréseket, a meglévő kérések viszont végre legyenek hajtva a megadott időkorláton belül. Ez egyaránt vonatkozik azokra a háttérpéldányokra, amelyek API-hívással, explicit módon lettek eltávolítva a háttérkészletből, és azokra is, amelyeket az állapot-mintavételek „Nem kifogástalan” állapotúnak minősítenek.

## <a name="custom-error-pages"></a>Egyéni hibalapok
Az Application Gatewayjel testreszabhatók a hibaoldalak. Az egyéni hibaoldalakon feltüntetheti saját védjegyeit, és egyéni elrendezést használhat.

További információt az [egyéni Application Gateway-hibaoldalak létrehozását ismertető részben](custom-error.md) talál.

## <a name="web-application-firewall"></a>Web application firewall (Webalkalmazási tűzfal)

A webalkalmazási tűzfal (WAF) az Application Gateway egyik szolgáltatása, amely központi védelmet nyújt a webalkalmazásoknak a gyakori biztonsági rések ellen. A WAF az [alapvető OWASP- (Open Web Application Security Project-) szabálykészletek](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0-s vagy 2.2.9-es verzióinak szabályai alapján működik. 

A webalkalmazások egyre inkább ki vannak téve rosszindulatú támadásoknak, amelyek az ismert biztonsági réseket használják ki. Az ilyen jellegű támadások között például gyakoriak az SQL-injektálásos és a webhelyek közötti, parancsprogramot alkalmazó támadások. Az ilyen támadások megakadályozása az alkalmazás kódjában kihívást jelenthet, és szigorú felügyeletet, javítást és megfigyelést igényelhet az alkalmazás topológiájának számos rétegén. A központosított webalkalmazási tűzfal egyszerűbbé teszi a biztonságfelügyeletet, és segít az alkalmazás-rendszergazdáknak a fenyegetések vagy a behatolások elleni védekezésben. Emellett a WAF-megoldás gyorsabban képes kezelni a biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett hogy az egyes webalkalmazások védelmét biztosítaná. A meglévő alkalmazásátjárókat egyszerűen át lehet alakítani webalkalmazási tűzfallal rendelkező alkalmazásátjárókká.

## <a name="url-based-routing"></a>URL-alapú útválasztás

Az URL-alapú útválasztás lehetővé teszi, hogy a kérésben szereplő URL-cím alapján irányítsa a forgalmat a háttér-kiszolgálókészlethez. Az egyik lehetőség az, hogy a különböző típusú tartalmakra vonatkozó kéréseket különböző készletekhez irányítja.

Például `http://contoso.com/video/*` iránti kérelmek VideoServerPoolba, míg a `http://contoso.com/images/*` felé irányuló kérelmek az ImageServerPoolba vannak továbbítva. Ha a kérés egyik elérésiút-kategóriába sem sorolható, a DefaultServerPool az alapértelmezett kiszolgáló.

## <a name="multiple-site-hosting"></a>Több hely üzemeltetése

A többhelyes üzemeltetéssel egynél több webhelyet konfigurálhat ugyanazon az Application Gateway-példányon. Ez a funkció lehetővé teszi, hogy egy application gateway akár 100 webhelyek hozzáadása a hatékonyabb topológiát beállításához. Mindegyik webhelyet a saját készletéhez lehet irányítani. Az Application Gateway például a `contoso.com` és a `fabrikam.com` forgalmát is kiszolgálhatja a ContosoServerPool és a FabrikamServerPool kiszolgálókészletekből.

A `http://contoso.com` iránti kérelmek a ContosoServerPoolba, míg a `http://fabrikam.com` felé irányuló kérelmek a FabrikamServerPoolba vannak továbbítva.

Hasonlóképpen, ugyanazon szülőtartomány két altartományát ugyanazon Application Gateway-telepítésről üzemeltetheti. Az altartományok használatának példái között lehet az egyetlen Application Gateway-telepítésen üzemeltetett `http://blog.contoso.com` és `http://app.contoso.com`.

## <a name="redirection"></a>Átirányítás

Számos webalkalmazás esetében gyakori eset az automatikus HTTP–HTTPS átirányítás annak érdekében, hogy az alkalmazás és a felhasználói közötti kommunikáció titkosított útvonalon történjen. 

Régebben olyan technikákat alkalmaztak ehhez, mint egy dedikált készlet létrehozása, amelynek egyetlen célja a bejövő HTTP-kérelmek átirányítása HTTPS-re. Az Application Gateway támogatja a forgalom az Application Gateway alapján való átirányításának lehetőségét. Ez leegyszerűsíti az alkalmazáskonfigurációt, optimalizálja az erőforrás-használatot, és új átirányítási forgatókönyveket támogat, például a globális és útvonalalapú átirányítást. Az Application Gateway átirányítási támogatása nem csak a HTTP–HTTPS átirányításra vonatkozik. Ez egy általános átirányítási mechanizmus, így a szabályokkal bármilyen megadott portról és portra átirányíthat. A szolgáltatás a külső webhelyre való átirányítást is támogatja.

Az Application Gateway átirányítási támogatása a következő funkciókat nyújtja:

- Globális átirányítás portok között a Gatewayen. Ez lehetővé teszi a HTTP–HTTPS átirányítást egy webhelyen.
- Útvonalalapú átirányítás. Ez a fajta átirányítás csak a megadott webhelyrészen engedélyezi a HTTP–HTTPS átirányítást, például egy `/cart/*` kifejezéssel jelzett bevásárlókosár részen.
- Átirányítás külső helyre.

## <a name="session-affinity"></a>Munkamenet-affinitás

A Cookie-alapú munkamenet-affinitás akkor hasznos, ha egy felhasználói munkamenetet egy adott kiszolgálón szeretne tartani. Az átjáróval kezelt cookie-k használatával az Application Gateway képes egy felhasználói munkamenet minden újabb forgalmát ugyanarra a kiszolgálóra irányítani feldolgozásra. Ez a funkció olyan esetekben lehet fontos, amelyekben egy felhasználói munkamenethez tartozó munkamenet-állapotot helyileg ment a rendszer a kiszolgálón.

## <a name="websocket-and-http2-traffic"></a>Websocket- és HTTP/2-forgalom

Az Application Gateway natív támogatást nyújt a Websocket- és HTTP/2-protokollok számára. Kizárólag WebSocket-támogatásra vonatkozó felhasználói beállítás nem létezik. A HTTP/2-támogatás az Azure PowerShell használatával engedélyezhető.

A WebSocket és a HTTP/2 protokollok teljes körű duplex kommunikációt tesznek lehetővé egy kiszolgáló és egy ügyfél között egy hosszú ideig futó TCP-kapcsolaton. Ez interaktívabb kommunikációt eredményez a webkiszolgáló és az ügyél között, amely anélkül marad kétirányú, hogy a HTTP-alapú implementációkban kötelező lekérdezésekre lenne szükség. A HTTP-vel ellentétben ezek a protokollok alacsony többletterheléssel bírnak, és több kérelem/válasz típusú forgalomhoz is használhatják ugyanazt a TCP-kapcsolatot, ami az erőforrások hatékonyabb kihasználását eredményezi. Ezek a protokollok a hagyományos, 80-as és 443-as HTTP-portokon működnek.

## <a name="rewrite-http-headers-public-preview"></a>Újraírási HTTP-fejléceket (nyilvános előzetes verzió)

HTTP-fejlécek engedélyezése az ügyfél és a kiszolgáló át a kérelem vagy válasz további információkat. Ezek HTTP fejlécek segít, például a biztonsági fejléc számos fontos forgatókönyv újraírását mezők, például HSTS / X-XSS-védelmet, vagy válaszfejléc eltávolítása mezők, amelyek felfedhet bizalmas adatokat, például a háttér-kiszolgáló nevét. 

Az Application Gateway mostantól támogatja a bejövő HTTP-kéréseket, valamint a kimenő HTTP-válaszok fejlécek újraírási képessége. Lesz hozzá, távolíthatja el vagy frissítse a HTTP-kérelmek és válaszfejlécek, amíg a kérelem/válasz-csomagok áthelyezése az ügyfél és a háttérkiszolgáló készletek között. Módosíthatja is a standard (meghatározott [RFC 2616](https://www.ietf.org/rfc/rfc2616.txt)) valamint a nem szabványos üzenetfejlécének mezői.  

Az nyilvános előzetes funkcióval kapcsolatos további információkért lásd: [Újraírási HTTP-fejlécek](rewrite-http-headers.md).

## <a name="next-steps"></a>További lépések

Az igényeitől és a környezetétől függően az Azure Portallal, az Azure PowerShellel vagy az Azure CLI-vel hozhat létre egy Application Gateway-tesztet:

- [Rövid útmutató: Az Azure Application Gatewayjel – Azure portal webes forgalom irányítása](quick-create-portal.md).
- [Rövid útmutató: A közvetlen webes forgalom az Azure Application Gatewayjel – Azure PowerShell-lel](quick-create-powershell.md)
- [Rövid útmutató: A közvetlen webes forgalom az Azure Application Gatewayjel – Azure CLI-vel](quick-create-cli.md)
