---
title: Azure Application Gatewayjel kapcsolatos gyakori kérdések
description: Válaszok az Azure Application Gateway – gyakori kérdések.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 3/20/2019
ms.author: victorh
ms.openlocfilehash: 3c8a2fe9f4486fe4d33754b58f4e7ebec1b3252d
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682950"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Az Application Gateway kapcsolatos gyakori kérdések

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general"></a>Általános kérdések

### <a name="what-is-application-gateway"></a>Mi az Application Gateway?

Az Azure Application Gateway egy alkalmazáskézbesítési vezérlőt (ADC) biztosít szolgáltatásként. Az alkalmazások különböző 7. rétegbeli terheléselosztási funkciókat kínál. Ez a szolgáltatás a magas rendelkezésre állású, méretezhető és teljes körűen felügyelt, az Azure-ban.

### <a name="what-features-does-application-gateway-support"></a>Milyen funkciókat támogatja az Application Gateway?

Az Application Gateway támogatja az automatikus skálázás, SSL-kiürítés, és a végpontok közötti SSL, webalkalmazási tűzfal (WAF), cookie-alapú munkamenet-affinitást, URL-cím-alapú útválasztás, többhelyes üzemeltetés és egyéb funkciókat. Támogatott szolgáltatások teljes listáját lásd: [Application Gateway bemutatása](application-gateway-introduction.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Hogyan az Application Gateway és az Azure Load Balancer térnek el?

Az Application Gateway a 7. rétegbeli terheléselosztó, ami azt jelenti, hogy csak a webes forgalom (HTTP, HTTPS, WebSocket és HTTP/2) működik. Támogatja a képességek, például SSL lezárást, cookie-alapú munkamenet-affinitást és Ciklikus időszeleteléses terheléselosztás forgalmat. Töltse be a terheléselosztó elosztja-forgalmat, 4. réteg (TCP vagy UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Milyen protokollokat támogat az Application Gateway?

Az Application Gateway támogatja a HTTP, HTTPS, HTTP/2 és WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Hogyan támogatja az Application Gateway a HTTP/2?

Lásd: [HTTP/2-támogatás](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Milyen erőforrások háttérkészlet részeként támogatottak?

Lásd: [háttérerőforrásokhoz támogatott](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pool).

### <a name="in-what-regions-is-application-gateway-available"></a>Mely régiókban érhető el az Application Gateway?

Az Application Gateway az Azure globális minden régióban érhető el. Érhető el is [Azure China 21Vianet](https://www.azure.cn/) és [Azure Government](https://azure.microsoft.com/overview/clouds/government/).

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>A központi telepítés előfizetésemhez tartozó dedikált, vagy azt közösen használja ügyfelek?

Application Gateway-példány dedikált központi telepítés a virtuális hálózaton.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Az Application Gateway támogatja a HTTP – HTTPS átirányítás?

Átirányítás használata támogatott. Lásd: [Application Gateway átirányítás áttekintése](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>Milyen sorrendben dolgozzák fel figyelői?

Tekintse meg a [figyelő feldolgozási sorrendje](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Hol találom meg az Application Gateway IP- és DNS?

Ha egy nyilvános IP-címet használ végpontként, látni fogja az IP- és DNS-információkat a nyilvános IP-cím erőforrás. Vagy a Portalon, az application gateway számára – áttekintés oldalon. Ha belső IP-címeket használ, keresse meg az Áttekintés lapon található információkat.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Mik azok a Keep-Alive időkorlátja és a TCP üresjárati időkorlát beállításait?

 Az Application Gateway v1 termékváltozatban az életben tartási időtúllépési érték a 120 másodperc. A v2 szintű Termékváltozatot életben tartási időtúllépés: 75 másodperc. A TCP üresjárati időkorlát az előtér virtuális IP-(VIP) Application Gateway 4 perces alapértelmezett.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Az IP- vagy DNS-nevét az application gateway életciklusa alatt változik?

A VIP-címet módosíthatja, ha leállítja majd újraindítja az application gateway. Azonban a társított az application gateway DNS-név nem változik az átjáró élettartama során. Mivel a DNS-név nem módosítható, egy CNAME-alias használata, és az application gateway DNS-címére mutasson.

### <a name="does-application-gateway-support-static-ip"></a>Az Application Gateway támogatja a statikus IP-cím?

Igen, az Application Gateway v2 szintű Termékváltozatot támogatja a statikus nyilvános IP-címeket. A v1 Termékváltozatot támogatja a statikus belső IP-címek.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Az Application Gateway támogatja a több nyilvános IP-címek az átjárón?

Egy application gateway támogatja a csak egy nyilvános IP-címet.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Milyen méretű kell még saját alhálózatot az Application Gateway?

Lásd: [Application Gateway alhálózat mérete szempontok](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Üzembe helyezhetem egynél több Application Gateway-erőforrás egyetlen alhálózatra?

Igen. Felül egy adott Application Gateway telepítési több példányát telepíthet egy meglévő alhálózatot, amely tartalmazza a különböző Application Gateway erőforrás egy másik egyedi Application Gateway erőforrás.

Egy önálló alhálózati nem támogatja a Standard_v2 és a Standard Application Gateway együtt.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Az Application Gateway támogatja az x-továbbított – a fejlécek?

Igen. Lásd: [kérelem módosításai](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Mennyi ideig tart egy application gateway üzembe helyezéséhez? Az application gateway működni fog, frissítése közben?

Új Application Gateway v1 Termékváltozatot telepítések esetén is igénybe vehet akár 20 percig kiépítése. Példány mérete és száma módosítása nem zavaró, és ez idő alatt az átjáró aktív marad.

A v2 szintű Termékváltozatot használó központi telepítések percet is igénybe vehet legfeljebb 6 üzembe helyezni.

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Használható az Application Gatewayen háttérmoduljaként Exchange-kiszolgáló?

Nem. Az Application Gateway nem támogatja az e-mail-protokollok, mint például a SMTP, IMAP és POP3. 

## <a name="performance"></a>Teljesítmény

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Hogyan támogatja a Application Gateway a magas rendelkezésre állás és méretezhetőség?

Az Application Gateway v1 Termékváltozatot támogatja a magas rendelkezésre állású forgatókönyvek, helyezett üzembe két vagy több példány esetén. Azure ezek a példányok, győződjön meg arról, hogy a példányok nem egyszerre az összes sikertelen frissítési és a tartalék tartomány között osztja el. A v1 Termékváltozatot támogatja a méretezhetőség átjáróhoz megosztani a terhelés több példánya hozzáadásával.

A v2 szintű Termékváltozatot automatikusan biztosítja, hogy az új példányok vannak elosztva a tartalék tartományok és frissítési tartományok. Ha úgy dönt, hogy a zone redudancy, az a legújabb példányok is vannak elosztva a zónaszintű hiba rugalmasságot kínálnak a rendelkezésre állási zónák.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Hogyan válhatok egy Vészhelyreállítási forgatókönyvhöz adatközpontra elosztva több Application Gateway használatával?

A Traffic Manager használatával elosztani a forgalmat több application Gateway átjárók különböző adatközpontokban.

### <a name="does-application-gateway-support-autoscaling"></a>Az Application Gateway támogatja az automatikus skálázás?

Igen, az Application Gateway v2 szintű Termékváltozatot támogatja az automatikus skálázás. További információkért lásd: [automatikus skálázás és zónaredundáns Application Gateway (nyilvános előzetes verzió)](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant).

### <a name="does-manual-scale-up-or-scale-down-cause-downtime"></a>Manuális skálázás nem, és vertikális leskálázás OK állásidő?

Nem. Példányok frissítési és tartalék tartományok között oszlanak meg.

### <a name="does-application-gateway-support-connection-draining"></a>Az Application Gateway támogatja a kapcsolat kiürítése?

Igen. Beállíthat-kapcsolatának kiürítéséhez módosítása tagok találhatóak a háttérkészlet megszakítása nélkül. Ez a beállítás lehetővé, hogy továbbra is küldje a meglévő kapcsolatok a korábbi rendeltetési amíg újra nem indítják meg a kapcsolathoz bezárása vagy konfigurálható időkorlát lejár. -Kapcsolatának kiürítéséhez csak aktuális szükségszerű kapcsolatok befejezésére vár. Az Application Gateway nem ismeri az alkalmazás munkamenet-állapot.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Módosítható példány mérete a közepes és nagy megszakítása nélkül?

Igen. Az Azure elosztja a példányok, győződjön meg arról, hogy a példányok egyszerre az összes nem sikertelen a frissítés és a tartalék tartományok között. Az Application Gateway támogatja a méretezés több példányát megosztani a terhelés átjáróhoz hozzáadásával.

## <a name="configuration"></a>Konfiguráció

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Az Application Gateway mindig telepítve van a virtuális hálózaton?

Igen. Az Application Gateway mindig helyezünk üzembe a virtuális hálózat alhálózatán. Ez az alhálózat csak az application Gateway-átjárókon is tartalmazhat. További információkért lásd: [virtuális hálózatot és alhálózatot követelmények](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Az Application Gateway kommunikálhatnak a virtuális hálózaton kívüli vagy előfizetést kívül példányok?

Mindaddig, amíg az IP-kapcsolattal rendelkezik, az Application Gateway az, hogy a virtuális hálózaton kívüli példányok kommunikálhat. Az Application Gateway is kommunikálhatnak kívül az előfizetésre, azt az osztályt. Ha azt tervezi, használja a belső IP-címek háttérkészlet-tagként, [virtuális hálózatok közötti társviszony](../virtual-network/virtual-network-peering-overview.md) vagy [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Üzembe helyezhetem az application gateway alhálózatának megkezdéséhez?

Nem. Azonban telepíthet az alhálózat más alkalmazásátjárók.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Hálózati biztonsági csoportok az application gateway alhálózatának támogatottak?

Lásd: [hálózati biztonsági csoportok az Application Gateway-alhálózat](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Az application gateway alhálózatának támogatja a felhasználó által megadott útvonalakat?

Lásd: [felhasználó által megadott útvonalakat az Application Gateway-alhálózat támogatott](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Mik azok a korlátok Application Gateway-en? Növelheti ezeket a korlátokat?

Lásd: [korlátozza az Application Gateway](../azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Egyidejűleg használható az Application Gateway belső és külső forgalom?

Igen. Az Application Gateway egy belső IP-cím és a egy külső IP-Címek száma az application gateway támogatja.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Az Application Gateway támogatja a virtuális hálózatok közötti társviszony létesítése?

Igen. Terheléselosztás forgalom a másik virtuális hálózatba tartozó virtuális hálózatok közötti társviszony segítségével.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Tudhatok meg a helyszíni kiszolgálók, amikor azok már kapcsolódik, az ExpressRoute vagy VPN-alagúton?

Igen, mindaddig, amíg forgalom engedélyezve van.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Egy háttérkészlet kiszolgálni a számos alkalmazás különböző portokat?

Mikroszolgáltatási architektúra használata támogatott. Mintavételi különböző portokat, szüksége több HTTP-beállítások konfigurálása.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Az egyéni minták támogatják a helyettesítő karakterekkel vagy reguláris kifejezést válasz adatokon?

Nem. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Útválasztási szabályok feldolgozásának módja az Application Gateway?

Lásd: [szabályok feldolgozása sorrendben](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Az egyéni mintavételek menüpontban mi nem a gazdagép mezőt jelölésére?

A gazdagép mező határozza meg, hogy küldjön a mintavétel, ha konfigurálta a többhelyes az Application Gateway neve. Ellenkező esetben használja a "127.0.0.1". Ez az érték eltér a virtuális gép állomásneve. A formátum \<protokoll\>://\<gazdagép\>:\<port\>\<elérési út\>.

### <a name="can-i-whitelist-application-gateway-access-to-only-a-few-source-ips"></a>Az Application Gateway a hozzáférést csak néhány forrás IP-címek engedélyezési lehetőségeket?

Igen. Lásd: [korlátozza a hozzáférést a megadott forrás IP-címek](https://docs.microsoft.com/azure/application-gateway/configuration-overview#whitelist-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Használhatom ugyanazt a portot a nyilvános és privát néző is figyelői?

Nem.

## <a name="configuration---ssl"></a>Konfiguráció – SSL

### <a name="what-certificates-does-application-gateway-support"></a>Milyen tanúsítványokat támogatja az Application Gateway?

Az Application Gateway támogatja az önaláírt tanúsítványokat, a hitelesítésszolgáltató (CA) tanúsítvány, a bővített ellenőrzés (Bővített) tanúsítványok és a helyettesítő tanúsítványok.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Milyen titkosító csomagok does Application Gateway támogatja?

Az Application Gateway támogatja az alábbi titkosító csomagok. 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

Információk az SSL-beállítások testreszabása: [konfigurálása SSL-szabályzat verziók és az Application Gatewayen titkosító csomagok](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Az Application Gateway támogatja a forgalom, a háttérkiszolgáló újratitkosítási vizsgálat?

Igen. Az Application Gateway támogatja az SSL-alapú kiszervezéshez és végpontok közötti SSL-t, amely forgalmat a háttérbeli reencrypt.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Konfigurálhatja a vezérlő az SSL-protokollverziók, SSL-szabályzat?

Igen. Application Gateway számára megtagadja a TLS1.0 TLS1.1 és TLS1.2 konfigurálhatja. Alapértelmezés szerint az SSL 2.0 és 3.0 már le van tiltva, és nem konfigurálható.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Konfigurálhatok titkosító csomagok és a házirend sorrendjét?

Igen. Az Application Gatewayben, is [titkosító csomagok konfigurálása](application-gateway-ssl-policy-overview.md). Meghatározhat egy olyan egyéni szabályzatot, engedélyezni kell legalább egy, a következő titkosító csomag használatát. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Az Application Gateway háttérbeli felügyeleti az SHA256 titkosítást használ.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Hány SSL-tanúsítványokat támogatja az Application Gateway?

Az Application Gateway támogatja a legfeljebb 100 SSL-tanúsítványokat.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Hány hitelesítési tanúsítványokat a háttérrendszer újratitkosítási vizsgálat támogatja az Application Gateway?

Az Application Gateway támogatja a legfeljebb 10 hitelesítési tanúsítványokat. Az alapértelmezett érték 5.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Nem az Application Gateway natív módon integrálja az Azure Key Vault?

Nem.

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Hogyan konfigurálható a HTTPS-figyelőként .com-és .net? 

Több tartományalapú (gazdagép-alapú) útválasztás, a többhelyes figyelővel létrehozása, állítsa be a figyelők, amely a HTTPS protokoll használatára, és rendelje hozzá a figyelők az útválasztási szabályokat. További információkért lásd: [több hely üzemeltetése az Application Gateway használatával](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview). 

## <a name="configuration---web-application-firewall-waf"></a>Konfiguráció – a webalkalmazási tűzfal (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>A WAF Termékváltozat kínál az összes funkcióját a Standard Termékváltozat?

Igen. WAF minden funkció támogatja a Standard termékváltozat.

### <a name="which-crs-versions-does-application-gateway-support"></a>Mely CRS-verziókat támogatja az Application Gateway?

Az Application Gateway támogatja a CRS [2.2.9-es](application-gateway-crs-rulegroups-rules.md#owasp229) és CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

### <a name="how-do-i-monitor-waf"></a>Hogyan figyelhetem a WAF?

Diagnosztikai naplózás révén WAF figyelő. További információkért lásd: [diagnosztikai naplózás és mérőszámok az Application Gateway](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>Észlelés üzemmód blokkolja a forgalmat?

Nem. Észlelés üzemmód csak naplózza a forgalmat, amely elindítja a WAF-szabály.

### <a name="can-i-customize-waf-rules"></a>WAF-szabályok testreszabása

Igen. További információkért lásd: [testreszabása WAF-szabálycsoportjainak és szabályok](application-gateway-customize-waf-rules-portal.md).

### <a name="what-rules-are-currently-available-for-waf"></a>Milyen szabályok jelenleg elérhető WAF?

WAF jelenleg támogatja a CRS [2.2.9-es](application-gateway-crs-rulegroups-rules.md#owasp229) és [3.0](application-gateway-crs-rulegroups-rules.md#owasp30). Ezek a szabályok, amelyek az Open Web Application Security Project (OWASP) azonosítja a top-10 biztonsági résének többségével alapvető biztonsági adja meg: 

* SQL-injektálás elleni védelem
* Parancsfájl-kezelési webhelyközi védelme
* Például parancsinjektálás, HTTP-kéréscsempészet, HTTP-válaszfelosztás és távolifájl-beszúrásos támadás gyakori webes támadások elleni védelem
* HTTP protokoll megsértése elleni védelem
* HTTP protokollanomáliák (például hiányzó gazdagép-felhasználói ügynök és Accept (Elfogadás) fejlécek) elleni védelem
* Robotprogramok, webbejárók és képolvasók elleni védelem
* Gyakori alkalmazások konfigurációs hibáinak észlelése (vagyis Apache, IIS és így tovább)

További információkért lásd: [OWASP top-10 biztonsági rések](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

### <a name="does-waf-support-ddos-protection"></a>Támogatja a WAF DDoS elleni védelem?

Igen. Engedélyezheti a DDoS protection a virtuális hálózaton, ahol az application gateway üzemel. Ez a beállítás biztosítja, hogy az Azure DDoS Protection szolgáltatás is védelmet nyújt a application gateway virtuális IP-cím (VIP).

## <a name="diagnostics-and-logging"></a>Diagnosztika és naplózás

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Milyen típusú naplók nyújt az Application Gateway?

Az Application Gateway három naplókat biztosít: 

* **ApplicationGatewayAccessLog**: A hozzáférési napló tartalmazza minden egyes kérelem, az application gateway frontend elküldve. Szerepel a hívó IP, kért URL-cím, válasz késése, visszatérési kód és bájt be és ki. A hozzáférési napló 300 másodpercenként gyűjti. Az application gateway egy bejegyzés tartalmazza.
* **ApplicationGatewayPerformanceLog**: A teljesítménynaplóban Alkalmazásátjáró teljesítményének adatait rögzíti. Információk közé tartozik az átviteli sebesség (bájt), a kiszolgált kérések teljes, sikertelen kérések számát, és a megfelelő és nem megfelelő állapotú háttérrendszert példányok száma.
* **ApplicationGatewayFirewallLog**: Azoknál az alkalmazásátjáróknál, webalkalmazási Tűzfallal rendelkező konfigurált a tűzfal napló tartalmazza a kérelmeket, észlelési mód vagy a megelőzés üzemmód be van jelentkezve.

További információkért lásd: [háttérrendszer állapota, diagnosztikai naplók és mérőszámok az Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Honnan tudhatom meg, ha saját háttérkészlet-tagokra kifogástalan állapotban-e?

Állapot ellenőrzése a PowerShell-parancsmag használatával `Get-AzApplicationGatewayBackendHealth` vagy a portálon. További információkért lásd: [Application Gateway-diagnosztika](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Mi az a diagnosztikai naplók esetén a megtartási házirendben?

Diagnosztikai naplók folyamat az ügyfél tárfiókja. Ügyfelek állíthatja be a megtartási házirend alapján választaniuk. Diagnosztikai naplók is lehet küldeni egy eseményközpontba, vagy az Azure Monitor naplóira. További információkért lásd: [Application Gateway-diagnosztika](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Hogyan kaphatok auditnaplók az Application Gateway?

A portálon, az application gateway menü paneljén válassza ki a **tevékenységnapló** a napló eléréséhez. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Beállíthatom a riasztásokat az Application Gateway?

Igen. Az Application Gatewayben a riasztások a metrikák vannak konfigurálva. További információkért lásd: [Application Gateway-metrikák](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics) és [riasztási értesítések fogadása](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Hogyan elemezheti a forgalom statisztikák az Application Gateway?

Megtekintheti, és többféle módon hozzáférési naplók elemzése. Használja az Azure Monitor naplók, Excel, a Power BI és így tovább.

Emellett használhatja a Resource Manager-sablonnal, amely telepíti és futtatja a népszerű [GoAccess](https://goaccess.io/) jelentkezzen elemző eszköz az Application Gateway-naplók elérése. GoAccess értékes olyan HTTP-forgalom statisztikáit, például egyedi látogatóinak, a kért fájlok, a gazdagépek, az operációs rendszerek, a böngészők és a HTTP-állapotkódok biztosít. A Githubban, további információkért lásd: a [a Resource Manager-sablon mappája található információs fájlt](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Mi okozhatja a háttérkiszolgáló állapota ismeretlen állapottal végződött visszaadandó?

Általában jelenik meg egy ismeretlen állapot, a háttérkiszolgáló-hozzáférését blokkolja a hálózati biztonsági csoport (NSG), egyéni DNS- vagy felhasználó által meghatározott útválasztás (UDR) az application gateway alhálózatának. További információkért lásd: [háttérrendszer állapota, diagnosztikai naplózás és mérőszámok az Application Gateway](application-gateway-diagnostics.md).

## <a name="next-steps"></a>További lépések

Az Application Gateway kapcsolatos további információkért lásd: [Mi az Azure Application Gateway?](overview.md).
