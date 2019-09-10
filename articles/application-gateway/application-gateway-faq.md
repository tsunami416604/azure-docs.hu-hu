---
title: Gyakran ismételt kérdések az Azure Application Gateway
description: Válaszokat talál az Azure Application Gateway-val kapcsolatos gyakori kérdésekre.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/31/2019
ms.author: victorh
ms.openlocfilehash: fd4eef9771ae89e330c99b398ad6d473356213f5
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858531"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Gyakori kérdések a Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az alábbi gyakori kérdések az Azure Application Gateway.

## <a name="general"></a>Általános

### <a name="what-is-application-gateway"></a>Mi az Application Gateway?

Az Azure Application Gateway egy Application Delivery Controller (ADC) szolgáltatást biztosít szolgáltatásként. Különböző, 7. rétegbeli terheléselosztási funkciókat kínál az alkalmazásaihoz. Ez a szolgáltatás kiválóan elérhető, méretezhető és teljes körűen felügyelhető az Azure-ban.

### <a name="what-features-does-application-gateway-support"></a>Milyen funkciókat támogat a Application Gateway?

Application Gateway támogatja az automatikus skálázást, az SSL-kiszervezést és a végpontok közötti SSL-t, a webalkalmazási tűzfalat (WAF), a cookie-alapú munkamenet-affinitást, az URL-elérésiút-alapú útválasztást, a többhelyes üzemeltetést és más funkciókat. A támogatott szolgáltatások teljes listájáért lásd: [Bevezetés a Application Gatewayba](application-gateway-introduction.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Miben különböznek Application Gateway és Azure Load Balancer?

A Application Gateway egy 7. rétegbeli terheléselosztó, ami azt jelenti, hogy csak a webes forgalom (HTTP, HTTPS, WebSocket és HTTP/2) használatával működik. Olyan képességeket támogat, mint az SSL-lezárás, a cookie-alapú munkamenet-affinitás és a ciklikus multiplexelés a terheléselosztási forgalom számára. Load Balancer terheléselosztási forgalmat a 4-es rétegben (TCP vagy UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Milyen protokollokat támogat a Application Gateway?

A Application Gateway támogatja a HTTP, a HTTPS, a HTTP/2 és a WebSocket használatát.

### <a name="how-does-application-gateway-support-http2"></a>Hogyan támogatja a Application Gateway a HTTP/2-t?

Lásd: [http/2 támogatás](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Milyen erőforrások támogatottak a háttérbeli készlet részeként?

Lásd: [támogatott háttérbeli erőforrások](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools).

### <a name="in-what-regions-is-application-gateway-available"></a>Milyen régiókban Application Gateway elérhető?

Application Gateway a globális Azure minden régiójában elérhető. Az [Azure China 21Vianet](https://www.azure.cn/) és [Azure Government](https://azure.microsoft.com/overview/clouds/government/)is elérhető.

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Ez az üzembe helyezés a saját előfizetéshez van hozzárendelve, vagy megosztották az ügyfelek között?

Application Gateway egy dedikált üzembe helyezés a virtuális hálózaton.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Támogatja a Application Gateway a HTTP és a HTTPS közötti átirányítást?

Az átirányítás támogatott. Lásd: [Application Gateway átirányítás áttekintése](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>Milyen sorrendben dolgozza fel a figyelőket?

Tekintse meg a [figyelő feldolgozásának sorrendjét](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Hol találom a Application Gateway IP-címet és a DNS-t?

Ha nyilvános IP-címet használ végpontként, a nyilvános IP-cím erőforráson megtalálhatja az IP-és DNS-információkat. Vagy keresse meg a portálon az Application Gateway áttekintés lapján. Ha belső IP-címeket használ, keresse meg az információkat az Áttekintés oldalon.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Mik a Keep-Alive időkorlát és a TCP Üresjárati időkorlát beállításai?

 A Application Gateway v1 SKU-ban az életben tartási időkorlát 120 másodperc. A v2 SKU életben tartási időtúllépése 75 másodperc. A TCP Üresjárati időkorlát 4 perces alapértelmezett érték a Application Gateway virtuális IP-címe (VIP) esetében.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Az IP-cím vagy a DNS-név módosul az Application Gateway élettartama során?

Ha leállítja és elindítja az Application Gatewayt, a VIP megváltozhat. Az Application gatewayhez társított DNS-név azonban nem változik az átjáró élettartama során. Mivel a DNS-név nem változik, CNAME aliast kell használnia, és az Application Gateway DNS-címeként kell mutatnia.

### <a name="does-application-gateway-support-static-ip"></a>Támogatja a Application Gateway a statikus IP-címet?

Igen, a Application Gateway v2 SKU támogatja a statikus nyilvános IP-címeket. A v1 SKU támogatja a statikus belső IP-címeket.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Application Gateway támogatja több nyilvános IP-címet az átjárón?

Az Application Gateway csak egy nyilvános IP-címet támogat.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Milyen nagy legyen az alhálózat a Application Gateway?

Lásd [Application Gateway alhálózat méretével kapcsolatos szempontokat](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Telepíthetek több Application Gateway erőforrást egyetlen alhálózatra?

Igen. Egy adott Application Gateway-telepítés több példányán kívül egy másik egyedi Application Gateway erőforrást is kiépíthet egy meglévő alhálózatba, amely egy másik Application Gateway erőforrást tartalmaz.

Egyetlen alhálózat nem támogatja a Standard_v2 és a standard Application Gateway együttes használatát.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Támogatja a Application Gateway az x által továbbított fejléceket?

Igen. Tekintse [meg a kérelem módosításait](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Mennyi ideig tart az Application Gateway üzembe helyezése? Működik az Application Gateway a frissítés közben?

Az új Application Gateway v1 SKU üzembe helyezése akár 20 percet is igénybe vehet. A példányok méretének vagy számának módosításai nem zavaróak, és az átjáró ebben az időszakban aktív marad.

A v2 SKU-t használó legtöbb üzemelő példány körülbelül 6 percet vesz igénybe. Azonban a telepítés típusától függően hosszabb időt is igénybe vehet. Ha például több Availability Zones több példányban üzemelő példányok több mint 6 percet vesznek igénybe. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Használhatom az Exchange Servert háttérként a Application Gateway?

Nem. A Application Gateway nem támogatja az e-mail-protokollok, például az SMTP, az IMAP és a POP3 használatát. 

## <a name="performance"></a>Teljesítmény

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Hogyan támogatja a Application Gateway a magas rendelkezésre állást és a méretezhetőséget?

A Application Gateway v1 SKU támogatja a magas rendelkezésre állási helyzeteket, ha két vagy több példányt telepített. Az Azure ezeket a példányokat a frissítési és a tartalék tartományok között osztja el, így biztosítva, hogy a példányok ne legyenek egyszerre a hibák. A v1 SKU támogatja a skálázhatóságot azáltal, hogy a terhelés megosztásához hozzáad egy átjáró több példányát.

A v2 SKU automatikusan biztosítja, hogy az új példányok a tartalék tartományok és a frissítési tartományok között legyenek elosztva. Ha a zóna-redundancia lehetőséget választja, a legújabb példányok a rendelkezésre állási zónák között is elterjednek, hogy a zónákhoz való sikertelen rugalmasságot nyújtsanak.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Hogyan egy DR-forgatókönyvet az adatközpontok között a Application Gateway használatával?

A Traffic Manager használatával terjesztheti a forgalmat több Application Gateway-átjáró között különböző adatközpontokban.

### <a name="does-application-gateway-support-autoscaling"></a>Támogatja Application Gateway az automatikus skálázást?

Igen, a Application Gateway v2 SKU támogatja az automatikus skálázást. További információ: automatikus [skálázás és zóna – redundáns Application Gateway](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-scale-up-or-scale-down-cause-downtime"></a>A manuális vertikális felskálázás vagy leskálázás miatt leáll a leállás?

Nem. A példányok a frissítési tartományok és a tartalék tartományok között oszlanak meg.

### <a name="does-application-gateway-support-connection-draining"></a>Támogatja a Application Gateway a kapcsolatok kiürítését?

Igen. A kapcsolatok kiürítését beállíthatja úgy, hogy megszakítsa a háttérbeli készlet tagjainak megváltoztatását. Ez a beállítás lehetővé teszi, hogy továbbra is a meglévő kapcsolatokat a korábbi célhelyre küldje, amíg a kapcsolat bezáródik vagy egy konfigurálható időtúllépés lejár. A kapcsolat kiürítése csak az aktuális, repülés közbeni kapcsolatok befejezésére vár. Application Gateway nem ismeri az alkalmazás munkamenet-állapotát.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Válthatok a példány mérete közepesről nagyra megszakítás nélkül?

Igen. Az Azure frissíti a példányokat a frissítési és a tartalék tartományok között, így biztosítva, hogy a példányok egyszerre ne legyenek sikertelenek. Application Gateway támogatja a skálázást azáltal, hogy a terhelés megosztásához hozzáad egy átjáró több példányát.

## <a name="configuration"></a>Konfiguráció

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Application Gateway mindig virtuális hálózatban van üzembe helyezve?

Igen. A Application Gateway mindig egy virtuális hálózati alhálózaton van üzembe helyezve. Ez az alhálózat csak Application Gateway-t tartalmazhat. További információ: [virtuális hálózati és alhálózati követelmények](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Képes Application Gateway kommunikálni a virtuális hálózatán kívüli példányokkal vagy az előfizetésén kívül?

Ha IP-kapcsolattal rendelkezik, Application Gateway tud kommunikálni a virtuális hálózatán kívüli példányokkal. A Application Gateway az előfizetésen kívüli példányokkal is tud kommunikálni. Ha belső IP-címeket kíván használni a háttérbeli készlet tagjaiként, használja a [Virtual Network-peering](../virtual-network/virtual-network-peering-overview.md) vagy az [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Telepíthetek az Application Gateway alhálózatban bármilyen más szolgáltatást?

Nem. Az alhálózaton azonban más Application Gateway-átjárókat is telepíthet.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Támogatottak-e a hálózati biztonsági csoportok az Application Gateway alhálózaton?

Lásd: [hálózati biztonsági csoportok a Application Gateway alhálózatban](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Támogatja az Application Gateway alhálózata a felhasználó által megadott útvonalakat?

Tekintse [meg a Application Gateway alhálózat által támogatott, felhasználó által megadott útvonalakat](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Milyen korlátozások vonatkoznak a Application Gatewayra? Növelhetim ezeket a korlátokat?

Lásd: [Application Gateway korlátok](../azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Használhatom-e egyidejűleg a külső és belső forgalomhoz Application Gateway is?

Igen. Application Gateway támogatja az Application Gateway egy belső IP-címét és egy külső IP-címét.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Támogatja a Application Gateway a virtuális hálózatok társítását?

Igen. A virtuális hálózatok egymással történő elosztása segít a forgalom terheléselosztásában más virtuális hálózatokban.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Tudok kommunikálni a helyszíni kiszolgálókkal, amikor ExpressRoute vagy VPN-alagutak csatlakoznak?

Igen, amíg a forgalom engedélyezett.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Az egyik háttérbeli készlet számos különböző porton található alkalmazást képes kiszolgálni?

A Service architektúrája támogatott. A különböző portokon történő mintavételhez több HTTP-beállítást is konfigurálnia kell.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Az egyéni mintavételek támogatják a helyettesítő karaktereket vagy a regexet a válaszokban?

Nem. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Hogyan történik az útválasztási szabályok feldolgozása Application Gateway?

Lásd [a feldolgozási szabályok sorrendjét](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Az egyéni mintavételek esetében Mit jelent a gazdagép mező?

A gazdagép mező azt a nevet adja meg, amelyet a mintavétel elküldéséhez a többhelyes Application Gatewayn konfigurált. Ellenkező esetben használja a "127.0.0.1" kulcsszót. Ez az érték eltér a virtuális gép gazdagépének nevétől. A formátuma \<az\>://\<Host\>:\<port\>elérési\>útja.\<

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Engedélyezhető Application Gateway csak néhány forrás IP-cím eléréséhez?

Igen. Lásd: [adott forrás IP-címekhez való hozzáférés korlátozása](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Használhatom ugyanazt a portot mind a nyilvános, mind a privát figyelőkhöz?

Nem.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Van elérhető útmutató a v1 SKU-ról a v2 SKU-ra való Migrálás céljából?

Igen. Részletekért lásd: az [Azure Application Gateway és a webalkalmazási tűzfal áttelepítésének v1-ről v2-re](migrate-v1-v2.md).


## <a name="configuration---ssl"></a>Konfiguráció – SSL

### <a name="what-certificates-does-application-gateway-support"></a>Milyen tanúsítványokat támogat a Application Gateway?

Application Gateway támogatja az önaláírt tanúsítványokat, a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat, a kiterjesztett ellenőrzési (EV) tanúsítványokat és a helyettesítő tanúsítványokat.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Milyen titkosítási csomagokat támogat a Application Gateway?

A Application Gateway a következő titkosítási csomagokat támogatja. 

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

Az SSL-beállítások testreszabásával kapcsolatos további információkért lásd: [az SSL-szabályzatok és a titkosítási csomagok konfigurálása Application Gatewayon](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Támogatja a Application Gateway a háttér felé irányuló forgalom újratitkosítását?

Igen. Application Gateway támogatja az SSL-alapú kiszervezést és a végpontok közötti SSL-t, amely a háttér felé irányuló forgalmat titkosítja.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Konfigurálható az SSL-szabályzat az SSL protokoll verzióinak szabályozására?

Igen. A Application Gateway a TLS 1.0, a TLS 1.1 és a TLS 1.2 megtagadására is beállítható. Alapértelmezés szerint az SSL 2,0 és a 3,0 már le van tiltva, és nem konfigurálható.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Konfigurálható a titkosítási csomagok és a házirendek sorrendje?

Igen. Application Gateway a [titkosító csomagok konfigurálhatók](application-gateway-ssl-policy-overview.md). Egyéni házirend definiálásához engedélyezze a következő titkosítási csomagok legalább egyikét. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Application Gateway a SHA256 használja a háttér-felügyelethez.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Hány SSL-tanúsítvány Application Gateway támogatást?

A Application Gateway legfeljebb 100 SSL-tanúsítványt támogat.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Hány hitelesítési tanúsítvány használható a háttérbeli újratitkosításhoz Application Gateway?

A Application Gateway legfeljebb 10 hitelesítési tanúsítványt támogat. Az alapértelmezett érték az 5.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Application Gateway natív módon integrálható a Azure Key Vaultrel?

Igen, a Application Gateway v2 SKU támogatja a Key Vault. További információ: SSL- [lezárás Key Vault tanúsítványokkal](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Hogyan konfigurálja a HTTPS-figyelőket a következőhöz:. com és .net-helyek? 

Több tartományalapú (gazdagép-alapú) útválasztáshoz létrehozhat többhelyes figyelőket, beállíthat HTTPS protokollt használó figyelőket, és társíthatja a figyelőket az útválasztási szabályokhoz. További információ: [több webhely üzemeltetése Application Gateway használatával](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Használhatok speciális karaktereket a saját. pfx fájl jelszavában?

Nem, csak alfanumerikus karaktereket használjon a. pfx-fájl jelszavában.

## <a name="configuration---web-application-firewall-waf"></a>Konfiguráció – webalkalmazási tűzfal (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>A WAF SKU a standard SKU-ban elérhető összes funkciót felkínálja?

Igen. A WAF a standard SKU összes funkcióját támogatja.

### <a name="which-crs-versions-does-application-gateway-support"></a>Mely CRS-verziókat támogatja a Application Gateway?

Application Gateway támogatja a CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) és a CRS [3,0](application-gateway-crs-rulegroups-rules.md#owasp30)-et.

### <a name="how-do-i-monitor-waf"></a>Hogyan monitor WAF?

WAF figyelése diagnosztikai naplózással. További információ: [diagnosztikai naplózás és mérőszámok Application Gatewayhoz](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>Blokkolja az észlelési mód a forgalmat?

Nem. Az észlelési mód csak a WAF-szabályt kiváltó forgalmat naplózza.

### <a name="can-i-customize-waf-rules"></a>Testreszabhatók a WAF-szabályok?

Igen. További információ: [WAF és-szabályok testreszabása](application-gateway-customize-waf-rules-portal.md).

### <a name="what-rules-are-currently-available-for-waf"></a>Jelenleg milyen szabályok érhetők el a WAF?

A WAF jelenleg a CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) és a [3,0](application-gateway-crs-rulegroups-rules.md#owasp30)-es verziója támogatja. Ezek a szabályok alapkonfigurációt biztosítanak a Web Application Security Project (OWASP) által azonosított legtöbb 10 legfontosabb biztonsági rés ellen: 

* SQL-injektálás elleni védelem
* Helyek közötti parancsfájlok elleni védelem
* Védelem a gyakori webes támadásokkal szemben, például a parancsok befecskendezése, a HTTP-kérések csempészete, a HTTP-válaszok felosztása és a távoli fájl-integrációs támadás ellen
* HTTP protokoll megsértése elleni védelem
* HTTP protokollanomáliák (például hiányzó gazdagép-felhasználói ügynök és Accept (Elfogadás) fejlécek) elleni védelem
* Robotprogramok, webbejárók és képolvasók elleni védelem
* Az alkalmazások gyakori konfigurációs beállításainak észlelése (azaz Apache, IIS stb.)

További információ: [OWASP Top-10 biztonsági rések](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

### <a name="does-waf-support-ddos-protection"></a>Támogatja a WAF a DDoS Protectiont?

Igen. Engedélyezheti a DDoS Protection szolgáltatást azon a virtuális hálózaton, amelyen az Application Gateway telepítve van. Ezzel a beállítással biztosíthatja, hogy a Azure DDoS Protection szolgáltatás az Application Gateway virtuális IP-címét (VIP) is védi.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Van elérhető útmutató a v1 SKU-ról a v2 SKU-ra való Migrálás céljából?

Igen. Részletekért lásd: az [Azure Application Gateway és a webalkalmazási tűzfal áttelepítésének v1-ről v2-re](migrate-v1-v2.md).

## <a name="diagnostics-and-logging"></a>Diagnosztika és naplózás

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Milyen típusú naplókat Application Gateway nyújtani?

A Application Gateway három naplót biztosít: 

* **ApplicationGatewayAccessLog**: A hozzáférési napló tartalmazza az Application Gateway előtér-felületére küldött összes kérelmet. Az adat magában foglalja a hívó IP-címét, a kért URL-címet, a válasz késését, a visszatérési kódot, valamint a bejövő és kimenő bájtokat. A hozzáférési napló gyűjtése 300 másodpercenként történik. Egy-egy rekordot tartalmaz az Application Gateway-ben.
* **ApplicationGatewayPerformanceLog**: A teljesítmény napló rögzíti az egyes Application Gateway-átjárók teljesítményadatait. Az adatok közé tartozik az átviteli sebesség, a kiszolgált kérelmek száma, a sikertelen kérelmek száma, valamint az állapot és a nem kifogástalan háttérbeli példányok száma.
* **ApplicationGatewayFirewallLog**: A WAF-vel konfigurált Application Gateway-átjárók esetén a tűzfal naplója az észlelési mód vagy a megelőzési mód használatával naplózott kérelmeket tartalmaz.

További információ: [a háttér állapota, a diagnosztikai naplók és a metrikák a Application Gatewayhoz](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Hogyan tudni, hogy a háttér-készlet tagjainak állapota Kifogástalan-e?

Ellenőrizze az állapotot a PowerShell- `Get-AzApplicationGatewayBackendHealth` parancsmag vagy a portál használatával. További információ: [Application Gateway diagnosztika](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Mi a diagnosztikai naplók adatmegőrzési szabályzata?

A diagnosztikai naplók az ügyfél Storage-fiókjába áramlanak. Az ügyfelek megadhatják az adatmegőrzési szabályzatot a preferencia alapján. A diagnosztikai naplókat egy Event hub-vagy Azure Monitor-naplóba is lehet elküldeni. További információ: [Application Gateway diagnosztika](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Hogyan beolvasni a naplókat a Application Gatewayhoz?

A portálon, az Application Gateway menü paneljén válassza a **műveletnapló** lehetőséget a napló eléréséhez. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Megadhatok riasztásokat a Application Gateway?

Igen. Application Gateway a riasztások a metrikák használatára vannak konfigurálva. További információ: [Application Gateway mérőszámok](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics) és [Riasztási értesítések fogadása](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Hogyan elemezni Application Gateway forgalmi statisztikáit?

A hozzáférési naplókat többféleképpen is megtekintheti és elemezheti. Azure Monitor a naplók, az Excel, a Power BI és így tovább.

Olyan Resource Manager-sablont is használhat, amely a népszerű [GoAccess](https://goaccess.io/) log Analyzert telepíti és futtatja Application Gateway hozzáférési naplókhoz. A GoAccess olyan értékes HTTP-forgalmi statisztikát biztosít, mint például az egyedi látogatók, a kért fájlok, gazdagépek, operációs rendszerek, böngészők és HTTP-állapotkódok. További információ a GitHubon: a [Resource Manager-sablon mappájában található információs fájl](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Mi okozhatja, hogy a háttér állapota ismeretlen állapotot ad vissza?

Általában ismeretlen állapot jelenik meg, ha a háttérhez való hozzáférést egy hálózati biztonsági csoport (NSG), egyéni DNS vagy felhasználó által definiált útválasztás (UDR) blokkolja az Application Gateway alhálózaton. További információ: [a háttér állapota, a diagnosztika naplózása és a Application Gateway mérőszámai](application-gateway-diagnostics.md).

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>Van olyan eset, amikor a NSG-naplók nem jelenítik meg az engedélyezett forgalmat?

Igen. Ha a konfiguráció megfelel a következő forgatókönyvnek, nem jelenik meg az engedélyezett forgalom a NSG flow naplóiban:
- Telepítette Application Gateway v2-t
- Rendelkezik egy NSG az Application Gateway alhálózaton
- Engedélyezte a NSG folyamat naplóit az adott NSG

## <a name="next-steps"></a>További lépések

További információ a Application Gatewayről: [Mi az az Azure Application Gateway?](overview.md).
