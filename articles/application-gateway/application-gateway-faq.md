---
title: Gyakori kérdések az Azure Application Gateway
description: Ez az oldal nyújt az Azure Application Gatewayjel kapcsolatos gyakori kérdésekre adott válaszok
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 3/20/2019
ms.author: victorh
ms.openlocfilehash: c40f372d3574f940e475a6626f998adae37a6d61
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58851150"
---
# <a name="frequently-asked-questions-for-application-gateway"></a>Az Application Gateway gyakori kérdések

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general"></a>Általános kérdések

### <a name="what-is-application-gateway"></a>Mi az Application Gateway?

Az Azure Application Gateway-példány egy alkalmazás Vezérlőszolgáltatást (ADC) szolgáltatás, az alkalmazások különböző 7. rétegbeli terheléselosztási funkciókat kínál. Magas rendelkezésre állású és skálázható szolgáltatás, amely az Azure teljes körűen felügyelt kínál.

### <a name="what-features-does-application-gateway-support"></a>Milyen funkciókat támogatja az Application Gateway?

Az Application Gateway támogatja az automatikus skálázás, SSL-kiürítés és teljes körű SSL, webalkalmazási tűzfal, cookie-alapú munkamenet-affinitást, URL-cím-alapú útválasztást, többhelyes üzemeltetési és mások. Támogatott szolgáltatások teljes listáját lásd: [Application Gateway bemutatása](application-gateway-introduction.md).

### <a name="what-is-the-difference-between-application-gateway-and-azure-load-balancer"></a>Mi a különbség az Application Gateway és az Azure Load Balancer?

Az Application Gateway egy 7. rétegbeli terheléselosztó, ami azt jelenti, és együttműködik a csak internetes forgalmat (HTTP/HTTPS/WebSocket/HTTP/2). Támogatja a képességek, például SSL lezárást, cookie-alapú munkamenet-affinitást és Ciklikus időszeleteléses terheléselosztás a adatforgalom. Töltse be a terheléselosztó terhelés jóváírásokat összefoglaló balances forgalom rétegben 4 (TCP/UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Milyen protokollokat támogat az Application Gateway?

Az Application Gateway támogatja a HTTP, HTTPS, HTTP/2 és WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Hogyan támogatja az Application Gateway a HTTP/2?

Lásd: [HTTP/2-támogatás](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support) megtudhatja, hogyan támogatja az Application gateway a HTTP/2 protokoll.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Milyen erőforrások jelenleg háttérkészlet részeként támogatottak?

Lásd: [háttérerőforrásokhoz támogatott](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pool) további erőforrások az Application gateway által támogatott.

### <a name="what-regions-is-the-service-available-in"></a>Mely régiókban érhető el a szolgáltatást?

Az Application Gateway az Azure globális minden régióban érhető el. Érhető el is [Azure China 21Vianet](https://www.azure.cn/) és [Azure Government](https://azure.microsoft.com/overview/clouds/government/)

### <a name="is-this-a-dedicated-deployment-for-my-subscription-or-is-it-shared-across-customers"></a>Ez egy dedikált üzemelő példánya az előfizetésem vagy azt közösen használja ügyfelek?

Application Gateway-példány dedikált központi telepítés a virtuális hálózaton.

### <a name="is-http-https-redirection-supported"></a>Is HTTP->HTTPS redirection supported?

Átirányítás használata támogatott. Lásd: [Application Gateway átirányítás áttekintése](application-gateway-redirect-overview.md) további.

### <a name="in-what-order-are-listeners-processed"></a>Milyen sorrendben dolgozzák fel figyelői?

Lásd: [figyelői feldolgozási sorrendje](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-application-gateways-ip-and-dns"></a>Hol találom meg az Application Gateway IP- és DNS?

Nyilvános IP-cím-végpontként használatakor ezt az információt találja a nyilvános IP-cím erőforrás, illetve az Áttekintés oldal az application gatewayhez a portálon. A belső IP-címek ez található az Áttekintés oldalon.

### <a name="what-is-keep-alive-timeout-and-tcp-idle-timeout-setting-on-application-gateway"></a>Mi az időköz és TCP üresjárati időkorlát beállítás Application Gateway-en?

A v1 Termékváltozatot életben tartási időtúllépési érték, 120 is lehet v2 szintű Termékváltozatot Keep-Alive időkorlát 75 is lehet TCP üresjárati időkorlát az előtér virtuális IP-CÍMEK az Application Gateway a 4 perces alapértelmezett.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Az IP- vagy DNS-nevét az Application Gateway életciklusa alatt változik?

A VIP-címet módosíthatja, ha az application gateway leállítása és elindítása. Az application gateway társított DNS-név nem változik az átjáró életciklusa. Ezért ajánlott egy CNAME-aliast használni, és az application gateway DNS-címére mutasson.

### <a name="does-application-gateway-support-static-ip"></a>Az Application Gateway támogatja a statikus IP-cím?

Igen, az Application Gateway v2 szintű Termékváltozatot támogatja a statikus nyilvános IP-címeket. A v1 Termékváltozatot támogatja a statikus belső IP-címek.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Az Application Gateway támogatja a több nyilvános IP-címek az átjárón?

Csak egy nyilvános IP-cím egy application gateway esetében támogatott.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Milyen méretű kell még saját alhálózatot az Application Gateway?

Lásd: [Application Gateway alhálózat mérete szempontok](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet) tudni, hogy az alhálózat méretét, a telepítéshez szükséges.

### <a name="q-can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>K. Üzembe helyezhetem egynél több Application Gateway-erőforrás egyetlen alhálózatra?

Igen, egy adott Application Gateway telepítési több példánya mellett, telepíthet egy meglévő alhálózatot, amely tartalmazza a különböző Application Gateway erőforrás egy másik egyedi Application Gateway erőforrás.

Standard_v2 és a Standard Application Gateway keverve ugyanazon az alhálózaton nem támogatott.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Az Application Gateway támogatja az x-továbbított – a fejlécek?

Igen. Lásd: [kérelem módosításai](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request) további információ az Application Gateway által támogatott x – továbbított – a fejlécek.

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-does-my-application-gateway-still-work-when-being-updated"></a>Mennyi ideig tart egy Application Gateway üzembe helyezéséhez? Az Application Gateway továbbra is működik, ha a frissítés alatt?

Új Application Gateway v1 Termékváltozatot telepítések esetén is igénybe vehet akár 20 percig kiépítése. Példány mérete és száma módosításai nem zavaró, és ez idő alatt az átjáró aktív marad.

V2 szintű Termékváltozatot központi telepítések kiépítése körülbelül 5-6 percig is eltarthat.

### <a name="can-exchange-server-be-used-as-backend-with-application-gateway"></a>Az Alkalmazásátjáró háttérbeli használhatók az Exchange server?

Nem, az Application Gateway nem támogatja e-mail-protokollok, mint például az SMTP-, IMAP- és POP3. 

## <a name="performance"></a>Teljesítmény

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Hogyan támogatja a Application Gateway a magas rendelkezésre állás és méretezhetőség?

Az Application Gateway v1 Termékváltozatot támogatja a magas rendelkezésre állás elérésére, ha két vagy több példánya van. Azure ezek a példányok győződjön meg arról, hogy minden példány nem egy időben, frissítési és a tartalék tartomány között osztja el. A v1 Termékváltozatot támogatja a méretezhetőség átjáróhoz megosztani a terhelés több példánya hozzáadásával.

A v2 szintű Termékváltozatot automatikusan biztosítja, hogy az új példányok vannak elosztva a tartalék tartományok és frissítési tartományok. Ha ki van választva a zone redudancy, az a legújabb példányok is vannak elosztva a zónaszintű hiba rugalmasságot kínálnak a rendelkezésre állási zónák.

### <a name="how-do-i-achieve-dr-scenario-across-data-centers-with-application-gateway"></a>Hogyan válhatok Vészhelyreállítási forgatókönyvhöz rendelkező Application Gateway adatközpontokon?

Ügyfeleink a Traffic Manager használatával elosztani a forgalmat több Application Gateway átjárók különböző adatközpontokban.

### <a name="is-autoscaling-supported"></a>Az automatikus skálázás támogatott?

Igen, az Application Gateway v2 szintű Termékváltozatot támogatja az automatikus skálázás. További információkért lásd: [automatikus skálázás és zónaredundáns Application Gateway (nyilvános előzetes verzió)](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-scale-updown-cause-downtime"></a>Manuális skálázás történik, felfelé és lefelé OK állásidő?

Nem jár. Példányok frissítési és tartalék tartományok között oszlanak meg.

### <a name="does-application-gateway-support-connection-draining"></a>Az Application Gateway támogatja a kapcsolat kiürítése?

Igen. -Kapcsolatának kiürítéséhez módosítása tagok találhatóak a háttérkészlet megszakítása nélkül is beállíthatja. Ez lehetővé teszi a meglévő kapcsolatok továbbra is az előző cél kell küldeni, amíg a kapcsolat le van zárva, vagy egy konfigurálható időkorlát lejár. Aktuális szükségszerű kapcsolatok végrehajtásához megvárja, amíg a kapcsolat csak kiürítése. Az Application Gateway még nem ismeri az alkalmazás munkamenet-állapot.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Módosítható példány mérete a közepes és nagy megszakítása nélkül?

Igen, az Azure elosztja példányok, győződjön meg arról, hogy minden példány nem egy időben a frissítés és a tartalék tartományok között. Az Application Gateway támogatja a méretezés több példányát megosztani a terhelés átjáróhoz hozzáadásával.

## <a name="configuration"></a>Konfiguráció

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Az Application Gateway mindig telepítve van a virtuális hálózaton?

Igen, az Application Gateway mindig helyezünk üzembe a virtuális hálózat alhálózatán. Ez az alhálózat csak az Application Gateway átjárók tartalmazhat. Lásd: [virtuális hálózatot és alhálózatot követelmények](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet) alhálózatot az Application Gateway szempontok megismerése.

### <a name="can-application-gateway-communicate-with-instances-outside-of-the-virtual-network-it-is-in-or-outside-of-the-subscription-it-is-in"></a>Az Application Gateway kommunikálhatnak példányok van a virtuális hálózaton kívül, vagy kívül található az előfizetés?

Az Application Gateway az, hogy a virtuális hálózaton kívüli vagy kívül van, az előfizetés példányok kommunikálhatnak, mindaddig, amíg nincs IP-kapcsolat. Háttérkészlet-tagként belső IP-címek használatát tervezi, akkor a szükséges [virtuális hálózatok közötti társviszony-létesítés](../virtual-network/virtual-network-peering-overview.md) vagy [VPN-átjáró](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Üzembe helyezhetem az application gateway alhálózatának megkezdéséhez?

Nem, de telepítheti az alhálózat más alkalmazásátjárók.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Hálózati biztonsági csoportok az application gateway alhálózatának támogatottak?

Lásd: [az Application Gateway-alhálózatot a hálózati biztonsági csoportok korlátozásai](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet) ismerje meg a hálózati biztonsági csoportok az application gateway alhálózatának támogatott.

### <a name="are-user-defined-routes-supported-on-the-application-gateway-subnet"></a>Felhasználó által megadott útvonalakat az application gateway alhálózatának támogatottak?

Lásd: [felhasználó által megadott útvonalak korlátozások](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet) további információ a felhasználó által megadott útvonalakat az application gateway alhálózatának támogatott.

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Mik azok a korlátok Application Gateway-en? Növelheti ezeket a korlátokat?

Lásd: [Application Gateway korlátainak](../azure-subscription-service-limits.md#application-gateway-limits) korlátait megtekintéséhez.

### <a name="can-i-use-application-gateway-for-both-external-and-internal-traffic-simultaneously"></a>Használható az Application Gateway belső és külső forgalom egyszerre?

Igen, az Application Gateway támogatja az egy belső IP-cím és a egy külső IP-Címek száma az application gateway kellene.

### <a name="is-vnet-peering-supported"></a>Támogatott virtuális hálózatok közötti társviszony?

Igen, virtuális hálózatok közötti társviszony támogatja, és más virtuális hálózatokhoz a forgalom terheléselosztásához előnyös.

### <a name="can-i-talk-to-on-premises-servers-when-they-are-connected-by-expressroute-or-vpn-tunnels"></a>Tudhatok a helyszíni kiszolgálók ExpressRoute vagy VPN-alagúton csatlakozáskor?

Igen, mindaddig, amíg forgalom engedélyezve van.

### <a name="can-i-have-one-backend-pool-serving-many-applications-on-different-ports"></a>Számos alkalmazás különböző portokat kiszolgálása egy háttérkészlet is van?

Micro service-architektúra használata támogatott. Több http-beállítások különböző portokat mintavétel konfigurálva kell lennie.

### <a name="do-custom-probes-support-wildcardsregex-on-response-data"></a>Az egyéni minták támogatják a helyettesítő karakterekkel vagy reguláris kifejezést válasz adatokon?

Az egyéni minták támogatja helyettesítő vagy reguláris kifejezést érkezett válasz adatait.

### <a name="how-are-rules-processed"></a>Szabályok feldolgozásának módja?

Lásd: [szabályok feldolgozása sorrendben](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules) tudni, hogyan útválasztási szabályok-folyamat az Application Gateway alkalmazásban.

### <a name="what-does-the-host-field-for-custom-probes-signify"></a>Mi a gazdagép mezőt az egyéni mintavételek jelölésére?

A gazdagép mező a neve a mintavétel való küldéséhez. Alkalmazható csak akkor, ha a többhelyes konfigurálva van az Application Gatewayen, ellenkező esetben használja a "127.0.0.1". Ez az érték eltér a virtuális gép állomásnevét, és formátumú \<protokoll\>://\<gazdagép\>:\<port\>\<elérési út\>.

### <a name="can-i-whitelist-application-gateway-access-to-a-few-source-ips"></a>Application Gateway hozzáférés néhány forrás IP-címek engedélyezési lehetőségeket?

Igen. Lásd: [korlátozza a hozzáférést a megadott forrás IP-címek](https://docs.microsoft.com/azure/application-gateway/configuration-overview#whitelist-application-gateway-access-to-a-few-source-ips) való győződjön meg arról, hogy csak engedélyezett forrásoldali IP-címek az Application Gateway hozzáférhet.

### <a name="can-the-same-port-be-used-for-both-public-and-private-facing-listeners"></a>Használhatja ugyanazt a portot a nyilvános és privát irányuló figyelői?

Nem, ez nem lehetséges.

## <a name="configuration---ssl"></a>Konfiguráció – SSL

### <a name="what-certificates-are-supported-on-application-gateway"></a>Milyen tanúsítványok az Application Gatewayen támogatottak?

Önaláírt tanúsítványok, a CA-tanúsítványok, a Bővített tanúsítványok és a helyettesítő tanúsítványok használata támogatott.

### <a name="what-are-the-current-cipher-suites-supported-by-application-gateway"></a>Mik azok az aktuális titkosító csomagok, az Application Gateway által támogatott?

Az aktuális, az Application Gateway által támogatott titkosító csomagok a következők. Lásd: [konfigurálása SSL-szabályzat verziók és az Application Gatewayen titkosító csomagok](application-gateway-configure-ssl-policy-powershell.md) megtudhatja, hogyan szabhatja testre az SSL-beállításokat.

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

### <a name="does-application-gateway-also-support-re-encryption-of-traffic-to-the-backend"></a>Az Application Gateway is támogatja a forgalmat a háttérbeli újbóli titkosítása?

Igen, az Application Gateway támogatja az SSL-alapú kiszervezéshez és teljes körű SSL-t, amely újra titkosítja a forgalmat a háttérbeli.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Konfigurálhatja az SSL-szabályzat az SSL-protokollverziók vezérlésére?

Igen, az Application Gateway számára megtagadja a TLS1.0 TLS1.1 és TLS1.2 is beállíthatja. Az SSL 2.0 és 3.0 alapértelmezés szerint már letiltott és nem konfigurálhatók.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Konfigurálhatok titkosító csomagok és a házirend sorrendjét?

Igen, [titkosító csomag használatát konfigurációs](application-gateway-ssl-policy-overview.md) használata támogatott. Egyéni házirend meghatározása, legalább egy, a következő titkosító csomag használatát engedélyezni kell. Az Application gateway az SHA256 titkosítást használ a háttérbeli felügyeleti.

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

### <a name="how-many-ssl-certificates-are-supported"></a>Hány SSL-tanúsítványok támogatottak?

Legfeljebb 100 SSL tanúsítványok használata támogatott.

### <a name="how-many-authentication-certificates-for-backend-re-encryption-are-supported"></a>Hány hitelesítési tanúsítványokat a háttérrendszer újbóli titkosítására támogatottak?

Legfeljebb 10 hitelesítési tanúsítványokat a rendszer az alapértelmezett 5 használata támogatott.

### <a name="does-application-gateway-integrate-with-azure-key-vault-natively"></a>Nem az Application Gateway integrálása az Azure Key Vault natív módon?

Nem, nincs integrálva van az Azure Key Vault.

### <a name="how-to-configure-https-listeners-for-com-and-net-sites"></a>Hogyan konfigurálható a HTTPS-figyelőként .com-és .net? 

Több tartományi (gazdagép-alapú) útválasztás, létrehozhat többhelyes figyelőket, válassza ki a HTTPS protokoll figyelő konfigurációban és a figyelők társítása az útválasztási szabályokat. További részletekért lásd: [az Application Gateway több hely üzemeltetése](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview). 

## <a name="configuration---web-application-firewall-waf"></a>Konfiguráció – a webalkalmazási tűzfal (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-with-the-standard-sku"></a>A WAF Termékváltozat kínál a standard szintű Termékváltozat érhető el minden funkció?

WAF Igen, a Standard termékváltozat támogatja az összes funkciót.

### <a name="what-is-the-crs-version-application-gateway-supports"></a>Mi az Application Gateway CRS-verzió támogatja?

Az Application Gateway támogatja a CRS [2.2.9-es](application-gateway-crs-rulegroups-rules.md#owasp229) és CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

### <a name="how-do-i-monitor-waf"></a>Hogyan figyelhetem a WAF?

Diagnosztikai naplózás keresztül figyelt WAF, további információ a diagnosztikai naplózást található [diagnosztikai naplózás és mérőszámok az Application Gateway számára](application-gateway-diagnostics.md)

### <a name="does-detection-mode-block-traffic"></a>Észlelés üzemmód blokkolja a forgalmat?

Nem, a észlelési mód csak naplózza a forgalmat, amely a WAF-szabály aktiválva.

### <a name="can-i-customize-waf-rules"></a>WAF-szabályok testreszabása

Igen, a WAF-szabályok is testre szabható. További információkért lásd: [testreszabása WAF-szabálycsoportjainak és szabályok](application-gateway-customize-waf-rules-portal.md)

### <a name="what-rules-are-currently-available"></a>Milyen szabályok jelenleg elérhetők

WAF jelenleg támogatja a CRS [2.2.9-es](application-gateway-crs-rulegroups-rules.md#owasp229) és [3.0](application-gateway-crs-rulegroups-rules.md#owasp30), itt alapvetőbiztonságirésénektöbbségévelazelső10biztonságiréseitáltalanyitottWebApplicationSecurityProject(OWASP)biztosítótalálható[ OWASP top 10 biztonsági rések](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)

* SQL-injektálás elleni védelem

* Webhelyek közötti, parancsprogramot alkalmazó támadások elleni védelem

* Gyakori webes támadások (például parancsinjektálás, HTTP-kéréscsempészet, HTTP-válaszfelosztás és távolifájl-beszúrásos támadás) elleni védelem

* HTTP protokoll megsértése elleni védelem

* HTTP protokollanomáliák (például hiányzó gazdagép-felhasználói ügynök és Accept (Elfogadás) fejlécek) elleni védelem

* Robotprogramok, webbejárók és képolvasók elleni védelem

* Gyakori alkalmazások konfigurációs hibáinak észlelése (vagyis Apache, IIS stb.)

### <a name="does-waf-also-support-ddos-prevention"></a>WAF is támogatja a DDoS-megelőzési?

Igen. Engedélyezheti a DDos protection a vneten, ahol az application gateway üzemel. Ez biztosítja, hogy az application gateway a virtuális IP-CÍMEK is védi az Azure DDos Protection szolgáltatás használatával.

## <a name="diagnostics-and-logging"></a>Diagnosztikai és naplózási

### <a name="what-types-of-logs-are-available-with-application-gateway"></a>Milyen típusú naplók érhetők el az Application Gateway szolgáltatással?

Nincsenek elérhető, az Application Gateway három naplókat. Ezek a naplók és egyéb diagnosztikai képességek a további információkért lásd: [háttérrendszer állapota, diagnosztikai naplók és mérőszámok az Application Gateway](application-gateway-diagnostics.md).

* **ApplicationGatewayAccessLog** – a hozzáférési napló tartalmazza minden egyes kérelem, az application gateway frontend elküldve. A hívó IP-Címek használatához, kért URL-cím válasz késése szerepel, és visszatérési kód, bájt. Hozzáférési napló 300 másodpercenként gyűjti. Ez a napló egy application gateway-példányonként több rekordot tartalmaz.
* **ApplicationGatewayPerformanceLog** – a teljesítménynaplóban rögzíti a teljesítményadatok alapon példány kérelmek száma összesen szolgálja ki, beleértve a átviteli sebesség (bájt), a kérelmek teljes száma szolgálja ki, a sikertelen kérések száma, kifogástalan állapotú, és nem megfelelő állapotú háttér- példányok száma.
* **ApplicationGatewayFirewallLog** – a tűzfal naplója tartalmaz, amelynek része a webalkalmazási tűzfallal rendelkező application gateway az észlelésük vagy a megelőzési módban naplózott kérések.

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Honnan tudhatom meg, ha saját háttérkészlet-tagokra kifogástalan állapotban-e?

A PowerShell-parancsmagot is használhatja `Get-AzApplicationGatewayBackendHealth` vagy állapota a portálon keresztül ellenőrizni funkcionáló [Application Gateway-diagnosztika](application-gateway-diagnostics.md)

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Mi a megőrzési házirend a diagnosztikai naplók?

Diagnosztikai naplók folyamat ügyfelek storage-fiókba, és ügyfelek állíthatja be a megtartási házirend alapján választaniuk. Diagnosztikai naplók is küldhetők Event Hub vagy az Azure Monitor-naplókat. Lásd: [Application Gateway-diagnosztika](application-gateway-diagnostics.md) további részletekért.

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Hogyan kaphatok auditnaplók az Application Gateway?

Auditnaplók az Application Gateway számára érhetők el. Kattintson a portál **tevékenységnapló** az application gateway a napló eléréséhez menü panel. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Beállíthatom a riasztásokat az Application Gateway?

Igen, az Application Gateway támogatja a riasztásokat. Riasztások a metrikák vannak konfigurálva. Lásd: [Application Gateway metrikák](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics) további információ az Application Gateway metrikákat. Riasztásokkal kapcsolatos további tudnivalókért lásd: [riasztási értesítések fogadása](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Hogyan elemezheti a forgalom statisztikák az Application Gateway?

Megtekintheti és elemezheti hozzáférési naplók segítségével több mechanizmust, például az Azure Monitor naplóira, Excel, a Power BI stb.

A Resource Manager-sablon, amely telepíti és futtatja a népszerű Microsoft közzétette is [GoAccess](https://goaccess.io/) analyzer keresse meg a Application Gateway-hozzáférési naplók. GoAccess értékes olyan HTTP-forgalom statisztikáit, például egyedi látogatóinak, a kért fájlokat, a gazdagépek, a operációs rendszerek, a böngészők, HTTP-állapotkódok és egyéb biztosít. További részletekért tekintse meg a [a Resource Manager-sablon mappája a Githubon található információs fájlt](https://aka.ms/appgwgoaccessreadme).

### <a name="backend-health-returns-unknown-status-what-could-be-causing-this-status"></a>Háttérkiszolgáló állapotadatainak adja vissza a állapota ismeretlen, Mi ez az állapot okozza?

Leggyakoribb oka a hozzáférést a háttérbeli NSG-t, egyéni DNS blokkolja, vagy egy UDR az application gateway alhálózatának rendelkezik. Lásd: [háttérrendszer állapota, diagnosztikai naplózás és mérőszámok az Application Gateway](application-gateway-diagnostics.md) további.

## <a name="next-steps"></a>További lépések

További információ az Application Gateway lásd [Mi az Azure Application Gateway?](overview.md)
