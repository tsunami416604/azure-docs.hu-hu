---
title: Gyakori kérdések az Azure Application Gateway-ről
description: Válaszok az Azure Application Gateway alkalmazásátjáróval kapcsolatos gyakori kérdésekre.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 04/01/2020
ms.author: victorh
ms.openlocfilehash: 0e5a683c95e213a0fc37ec4f559e89abfecf034a
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758906"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Gyakori kérdések az Alkalmazásátjáróval kapcsolatban

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az alábbiakban az Azure Application Gateway alkalmazásátjáróval kapcsolatos gyakori kérdéseket tetszetős kérdéseket tesznek fel.

## <a name="general"></a>Általános kérdések

### <a name="what-is-application-gateway"></a>Mi az Application Gateway?

Az Azure Application Gateway szolgáltatásként biztosít egy alkalmazáskézbesítési vezérlőt (ADC). Különböző 7-es réteges terheléselosztási lehetőségeket kínál az alkalmazásokhoz. Ez a szolgáltatás magas rendelkezésre állású, méretezhető és teljes mértékben az Azure által felügyelt.

### <a name="what-features-does-application-gateway-support"></a>Milyen szolgáltatásokat támogat az Application Gateway?

Az Application Gateway támogatja az automatikus skálázást, a TLS-kiszervezést és a végpontok közötti TLS-t, a webalkalmazás tűzfalát (WAF), a cookie-alapú munkamenet-affinitást, az URL-elérési út-alapú útválasztást, a többhelyes üzemeltetést és egyéb funkciókat. A támogatott szolgáltatások teljes listáját az Alkalmazásátjáró – bevezetés című témakörben [tetszését tartalmazza.](application-gateway-introduction.md)

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Miben különbözik az Application Gateway és az Azure Load Balancer?

Az Application Gateway egy 7-es réteges terheléselosztó, ami azt jelenti, hogy csak webes forgalommal működik (HTTP, HTTPS, WebSocket és HTTP/2). Támogatja a képességek, mint például a TLS-végződés, a cookie-alapú munkamenet-affinitás, és ciklikus multiplexelés a terheléselosztási forgalom. A terheléselosztó terheléselosztása a 4.

### <a name="what-protocols-does-application-gateway-support"></a>Milyen protokollokat támogat az Application Gateway?

Az Application Gateway támogatja a HTTP, HTTPS, HTTP/2 és WebSocket protokollt.

### <a name="how-does-application-gateway-support-http2"></a>Hogyan támogatja az Application Gateway a HTTP/2 protokollt?

Lásd [HTTP/2 támogatás](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Milyen erőforrások támogatottak egy háttérkészlet részeként?

Lásd a [támogatott háttér-erőforrásokat.](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools)

### <a name="in-what-regions-is-application-gateway-available"></a>Milyen régiókban érhető el az Application Gateway?

Az Application Gateway a globális Azure minden régiójában elérhető. Az Azure China [21Vianet](https://www.azure.cn/) és az [Azure Government](https://azure.microsoft.com/overview/clouds/government/)szolgáltatásban is elérhető.

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Ez a központi telepítés dedikált az előfizetésemhez, vagy az ügyfelek között van megosztva?

Az Application Gateway egy dedikált központi telepítés a virtuális hálózatban.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Támogatja az Application Gateway a HTTP-HTTPS átirányítást?

Az átirányítás támogatott. Lásd: [Alkalmazásátjáró átirányítás – áttekintés.](application-gateway-redirect-overview.md)

### <a name="in-what-order-are-listeners-processed"></a>Milyen sorrendben dolgozzák fel a hallgatókat?

Tekintse meg a [figyelőfeldolgozás sorrendjét.](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners)

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Hol találom az Application Gateway IP-címét és DNS-ét?

Ha egy nyilvános IP-címet használ végpontként, megtalálja az IP-és DNS-adatokat a nyilvános IP-cím erőforrás. Vagy keresse meg a portálon, az áttekintő lapon az alkalmazásátjáró. Ha belső IP-címeket használ, keresse meg az információkat az áttekintő lapon.

A v2 termékváltozat esetében nyissa meg a nyilvános IP-erőforrást, és válassza a **Konfiguráció**lehetőséget. A **DNS-névcímke (nem kötelező)** mező a DNS-név konfigurálásához érhető el.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Milyen beállításokat vezetnek be a Keep-Alive időtúl- és TCP-tétlen időtúláravonatkozó beállítások?

*A Keep-Alive időtúllépés* határozza meg, hogy az Application Gateway mennyi ideig várjon arra, hogy az ügyfél egy állandó kapcsolaton újabb HTTP-kérelmet küldjön, mielőtt újra felhasználná vagy bezárná. *A TCP tétlen időtúllépéshatározza* meg, hogy a TCP-kapcsolat mennyi ideig maradjon nyitva, ha nincs tevékenység. 

Az Application Gateway v1 Termékváltozatban a *Keep-Alive időtúllépést* 120 másodperc, a v2-sku-ban pedig 75 másodperc. A *TCP-adatle-időkitöltés* 4 perces alapértelmezett a v1 és az Application Gateway 2 Termékváltozatának előtér-virtuális IP-címén (VIP). Ezek az értékek nem módosíthatók.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Megváltozik az IP- vagy DNS-név az alkalmazásátjáró élettartama alatt?

Az Application Gateway V1 Termékváltozatban a VIP módosíthatja, ha leállítja és elindítja az alkalmazásátjárót. De az alkalmazásátjáróhoz társított DNS-név nem változik az átjáró élettartama alatt. Mivel a DNS-név nem változik, használjon CNAME aliast, és mutasson az alkalmazásátjáró DNS-címére. Az Application Gateway V2 Termékváltozatban az IP-címet statikusként állíthatja be, így az IP- és DNS-név nem változik az alkalmazásátjáró élettartama alatt. 

### <a name="does-application-gateway-support-static-ip"></a>Támogatja az Application Gateway a statikus IP-címet?

Igen, az Application Gateway v2 termékváltozat támogatja a statikus nyilvános IP-címeket. A v1 termékváltozat támogatja a statikus belső IP-k.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Az Application Gateway több nyilvános IP-t is támogat az átjárón?

Az alkalmazásátjáró csak egy nyilvános IP-címet támogat.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Mekkora kell tenni az alhálózat az Application Gateway?

Lásd: [Az Alkalmazásátjáró alhálózatának méretével kapcsolatos szempontok.](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet)

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Telepíthetek egynél több Application Gateway-erőforrást egyetlen alhálózatra?

Igen. Egy adott Application Gateway központi telepítésének több példánya mellett egy másik Application Gateway-erőforrást is kiépíthet egy másik Application Gateway-erőforrást tartalmazó meglévő alhálózatba.

Egyetlen alhálózat nem támogatja a Standard_v2 és a Standard Application Gateway együtt.

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>Támogatja az Application Gateway v2 a felhasználó által definiált útvonalakat (UDR)?

Igen, de csak konkrét forgatókönyveket. További információt az [Application Gateway konfigurációjának áttekintése című témakörben talál.](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet)

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Támogatja az Application Gateway az x-forwarded-for fejléceket?

Igen. Lásd: [Módosítások a kérelemhez](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Mennyi ideig tart egy alkalmazásátjáró üzembe helyezése? Működni fog az alkalmazásátjáróm a frissítés ideje alatt?

Új Application Gateway v1 termékváltozatok üzembe helyezések akár 20 percet is igénybe vehet. A példányméretének vagy a számlálónak a módosításai nem zavaróak, és az átjáró ez idő alatt aktív marad.

A v2 termékváltozatot használó legtöbb központi telepítés körülbelül 6 percet vesz igénybe. A központi telepítés típusától függően azonban hosszabb időt is igénybe vehet. Például több rendelkezésre állási zónában több példányt tartalmazó központi telepítések több mint 6 percet is igénybe vehet. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Használhatom az Exchange Server-t háttérrendszerként az Application Gateway-rel?

Nem. Az Application Gateway nem támogatja az olyan e-mail protokollokat, mint az SMTP, az IMAP és a POP3.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Van-e útmutatást a v1 termékváltozatról a v2 termékváltozatra való áttelepítéshez?

Igen. További információt az [Azure Application Gateway és a Web Application Firewall áttelepítése a v1-ről a 2-es v2-re.](migrate-v1-v2.md)

### <a name="will-the-application-gateway-v1-sku-continue-to-be-supported"></a>Az Application Gateway v1 termékváltozat továbbra is támogatott lesz?

Igen. Az Application Gateway v1 termékváltozat továbbra is támogatott lesz. Azonban erősen ajánlott, hogy a v2-re való áttéréssel kihasználja az adott termékváltozat szolgáltatásfrissítéseit. További információ: [Autoscaling and Zone-redundáns Application Gateway v2](application-gateway-autoscaling-zone-redundant.md).

## <a name="performance"></a>Teljesítmény

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Hogyan támogatja az Application Gateway a magas rendelkezésre állást és a méretezhetőséget?

Az Application Gateway v1 termékváltozat támogatja a magas rendelkezésre állású forgatókönyvek, ha két vagy több példány telepítése. Az Azure osztja ezeket a példányokat a frissítési és tartalék tartományok között annak érdekében, hogy a példányok nem minden sikertelen egyszerre. A v1 termékváltozat támogatja a méretezhetőséget azáltal, hogy ugyanazon átjáró több példányát adja hozzá a terhelés megosztásához.

A v2 termékváltozat automatikusan biztosítja, hogy az új példányok a tartalék tartományok között legyenek elosztva, és frissítse a tartományokat. Ha zónaredundancia lehetőséget választja, a legújabb példányok is elvannak osztva a rendelkezésre állási zónák között, hogy zónaszintű hibák rugalmasságát kínálják.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Hogyan valósíthatok meg egy vész-forgatókönyvet az adatközpontok között az Application Gateway használatával?

A Traffic Manager használatával forgalmat oszthat el több alkalmazásátjáró között különböző adatközpontokban.

### <a name="does-application-gateway-support-autoscaling"></a>Támogatja az Application Gateway az automatikus skálázást?

Igen, az Application Gateway v2 termékváltozat támogatja az automatikus skálázást. További információ: [Autoscaling and Zone-redundáns Application Gateway](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>A manuális vagy automatikus felskálázás vagy leskálázás okozza az állásidőt?

Nem. A példányok a frissítési tartományok és a tartalék tartományok között vannak elosztva.

### <a name="does-application-gateway-support-connection-draining"></a>Támogatja az Application Gateway a kapcsolat kiürítését?

Igen. Beállíthatja a kapcsolat kiürítését a háttérkészlet tagjainak megszakítás nélküli módosításához. További információt az [Application Gateway kapcsolatkiürítési szakaszában](features.md#connection-draining)talál.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Módosíthatom a példány méretét közepesről nagyra megszakítás nélkül?

Igen.

## <a name="configuration"></a>Konfiguráció

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Az Application Gateway mindig virtuális hálózatban van telepítve?

Igen. Az Application Gateway mindig egy virtuális hálózati alhálózatban van telepítve. Ez az alhálózat csak alkalmazásátjárókat tartalmazhat. További információt a [virtuális hálózatokra és az alhálózatokra vonatkozó követelmények című témakörben talál.](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet)

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Az Application Gateway kommunikálhat a virtuális hálózaton kívüli vagy az előfizetésén kívüli példányokkal?

Mindaddig, amíg IP-kapcsolat, Application Gateway képes kommunikálni példányok kívül a virtuális hálózaton, hogy az ő in. Az Application Gateway az on kívül is kommunikálhat a példányokkal. Ha belső IP-tagokként szeretné használni a belső IP-állomásokat, használja a [virtuális hálózati társviszony-létesítést](../virtual-network/virtual-network-peering-overview.md) vagy az [Azure VPN-átjárót.](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Telepíthetek bármi mást az alkalmazásátjáró alhálózatában?

Nem. De az alhálózatban más alkalmazásátjárók is üzembe helyezhetők.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>A hálózati biztonsági csoportok támogatottak az alkalmazásátjáró alhálózatán?

Lásd: [Hálózati biztonsági csoportok az Application Gateway alhálózatban.](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet)

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Az alkalmazásátjáró alhálózata támogatja a felhasználó által definiált útvonalakat?

Lásd: [Az Application Gateway alhálózat által támogatott felhasználó által definiált útvonalak.](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet)

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Milyen korlátják az Application Gateway-t? Növelhetem ezeket a korlátokat?

Lásd: [Alkalmazásátjáró-korlátozások](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Használhatom egyszerre az Application Gateway-t külső és belső forgalomban is?

Igen. Az Application Gateway alkalmazásátjárónként egy belső IP-t és egy külső IP-címet támogat.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Támogatja az Application Gateway a virtuális hálózati társviszony-létesítést?

Igen. A virtuális hálózati társviszony-létesítés segít a terheléselosztásban más virtuális hálózatok forgalmának.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Beszélhetek helyszíni kiszolgálókkal, ha ExpressRoute- vagy VPN-alagutakkal vannak összekötve?

Igen, amíg a forgalom engedélyezett.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Egy háttérkészlet számos alkalmazást kiszolgálhat különböző portokon?

Mikroszolgáltatás architektúra támogatott. A különböző portok mintavételéhez több HTTP-beállítást kell konfigurálnia.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Az egyéni mintavételek támogatják a helyettesítő karaktereket vagy a regex-et a válaszadatokon?

Nem. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Hogyan történik az útválasztási szabályok feldolgozása az Application Gateway-ben?

Lásd: [A feldolgozási szabályok sorrendje](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Az egyéni mintavételek esetében mit jelent a Gazdamező?

Az Állomás mező megadja a mintavételt küldendő nevet, ha többhelyet konfigurált az Application Gateway webhelyen. Ellenkező esetben használja a "127.0.0.1" . Ez az érték eltér a virtuálisgép-állomás nevétől. Formátuma \<protokoll\>\<://\>\<host\>\<\>: port elérési útja .

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Engedélyezhetem az Application Gateway csak néhány forrás IP-címhez való hozzáférését?

Igen. Lásd: [Hozzáférés korlátozása adott forrás IP-khez](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Használhatom ugyanazt a portot mind a nyilvános, mind a privát figyelők számára?

Nem.

### <a name="does-application-gateway-support-ipv6"></a>Támogatja az Application Gateway az IPv6 protokollt?

Az Application Gateway v2 jelenleg nem támogatja az IPv6 protokollt. Csak iPv4-et használó kétveremű virtuális hálózatban működhet, de az átjáró alhálózatának csak IPv4-nek kell lennie. Az Application Gateway v1 nem támogatja a kétverédes virtuális hálózatokat. 

## <a name="configuration---tls"></a>Konfiguráció - TLS

### <a name="what-certificates-does-application-gateway-support"></a>Milyen tanúsítványokat támogat az Application Gateway?

Az Application Gateway támogatja az önaláírt tanúsítványokat, a hitelesítésszolgáltatói tanúsítványokat, a kiterjesztett érvényesítési (EV) tanúsítványokat és a helyettesítő karakteres tanúsítványokat.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Milyen titkosítási csomagokat támogat az Application Gateway?

Az Application Gateway a következő titkosítási csomagokat támogatja. 

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

A TLS-beállítások testreszabásáról a [TLS-házirendverziók és titkosítási csomagok konfigurálása az Application Gateway alkalmazásban](application-gateway-configure-ssl-policy-powershell.md)című témakörben talál további információt.

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Támogatja az Application Gateway a háttérrendszer forgalmának újratitkosítását?

Igen. Az Application Gateway támogatja a TLS-kiszervezést és a végpontok között lévő TLS-t, amely újratitkosítja a forgalmat a háttérrendszerhez.

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Konfigurálhatom a TLS-házirendet a TLS protokollverziók vezérlésére?

Igen. Az Application Gateway konfigurálható a TLS1.0, a TLS1.1 és a TLS1.2 megtagadására. Alapértelmezés szerint az SSL 2.0 és a 3.0 már le van tiltva, és nem konfigurálható.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Konfigurálhatom a titkosítási csomagokat és a házirend-sorrendet?

Igen. Az Application Gateway alkalmazásban beállíthatja a [titkosítási csomagokat.](application-gateway-ssl-policy-overview.md) Egyéni házirend definiálásához engedélyezze a következő titkosítási csomagok legalább egyikét. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Az Application Gateway az SHA256-ot használja a háttérrendszer-kezeléshez.

### <a name="how-many-tlsssl-certificates-does-application-gateway-support"></a>Hány TLS/SSL-tanúsítványt támogat az Application Gateway?

Az Application Gateway legfeljebb 100 TLS/SSL tanúsítványt támogat.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Hány hitelesítési tanúsítványt támogat az Application Gateway a háttérrendszer-újratitkosításhoz?

Az Application Gateway legfeljebb 100 hitelesítési tanúsítványt támogat.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Az Application Gateway natív módon integrálható az Azure Key Vaultszolgáltatással?

Igen, az Application Gateway v2 termékváltozat támogatja a Key Vault. További információt a [TLS-végződtetés key vault-tanúsítványokkal című témakörben talál.](key-vault-certs.md)

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Hogyan konfigurálhatom a HTTPS-figyelők konfigurálását .com és .net webhelyekhez? 

Több tartományalapú (állomásalapú) útválasztás esetén többhelyes figyelők hozhat létre, beállíthatja a HTTPS protokollt használó figyelők beállítását, és társíthatja a figyelőket az útválasztási szabályokhoz. További információt a [Több webhely üzemeltetése az Application Gateway használatával című témakörben talál.](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview)

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Használhatok speciális karaktereket a .pfx fájljelben?

Nem, csak alfanumerikus karaktereket használjon a .pfx fájljelszavában.

## <a name="configuration---web-application-firewall-waf"></a>Konfiguráció - webalkalmazás tűzfala (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>A WAF termékváltozat a standard termékváltozatban elérhető összes funkciót kínálja?

Igen. A WAF támogatja a standard termékváltozat összes szolgáltatását.

### <a name="how-do-i-monitor-waf"></a>Hogyan figyelhetem a WAF-ot?

A WAF figyelése diagnosztikai naplózáson keresztül. További információ: [Diagnosztikai naplózás és az Application Gateway metrikái.](application-gateway-diagnostics.md)

### <a name="does-detection-mode-block-traffic"></a>Az észlelési mód blokkolja a forgalmat?

Nem. Az észlelési mód csak a WAF-szabályt kiváltó forgalmat naplózza.

### <a name="can-i-customize-waf-rules"></a>Testre szabhatom a WAF-szabályokat?

Igen. További információt a [WAF-szabálycsoportok és -szabályok testreszabása](application-gateway-customize-waf-rules-portal.md)című témakörben talál.

### <a name="what-rules-are-currently-available-for-waf"></a>Milyen szabályok állnak jelenleg rendelkezésre a WAF számára?

A WAF jelenleg támogatja a CRS [2.2.9](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp229), [3.0](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp30)és [3.1 .WAF](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp31). Ezek a szabályok biztosítják az alapszintű biztonságot az Open Web Application Security Project (OWASP) által azonosított 10 legfontosabb biztonsági rés nagy részével szemben: 

* SQL-injektálás elleni védelem
* Helyek közötti parancsfájlok védelme
* Védelem a gyakori webes támadásokkal, például a parancsbefecskendezéssel, a HTTP-kérelemcsempészettel, a HTTP-válaszok megosztásával és a távoli fájlfelvételsel kapcsolatos támadásokkal szemben
* HTTP protokoll megsértése elleni védelem
* HTTP protokollanomáliák (például hiányzó gazdagép-felhasználói ügynök és Accept (Elfogadás) fejlécek) elleni védelem
* Robotprogramok, webbejárók és képolvasók elleni védelem
* Gyakori alkalmazáshelytelen konfigurációk észlelése (azaz Apache, IIS és így tovább)

További információt az [OWASP top 10-es számú biztonsági résecímű](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)témakörben talál.

### <a name="does-waf-support-ddos-protection"></a>Támogatja a WAF a DDoS védelmet?

Igen. Engedélyezheti a DDoS-védelmet azon a virtuális hálózaton, ahol az alkalmazásátjáró telepítve van. Ez a beállítás biztosítja, hogy az Azure DDoS Protection szolgáltatás is védi az alkalmazásátjáró virtuális IP (VIP).

## <a name="configuration---ingress-controller-for-aks"></a>Konfiguráció - ingress vezérlő az AKS-hez

### <a name="what-is-an-ingress-controller"></a>Mi az a ingress vezérlő?

Kubernetes lehetővé `deployment` `service` teszi létrehozása és erőforrás-készlet a fürtön belüli podok egy csoportját. Ugyanazon szolgáltatás külső elérhetővé [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) tétele érdekében egy erőforrás van definiálva, amely terheléselosztást, TLS-végződtetést és névalapú virtuális üzemeltetést biztosít.
Az `Ingress` erőforrás kielégítéséhez egy be- éselőtér-vezérlőre van `Ingress` szükség, amely figyeli az erőforrások változásait, és konfigurálja a terheléselosztó házirendeket.

Az Application Gateway ingress controller lehetővé teszi, hogy az [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) egy Azure [Kubernetes-szolgáltatás,](https://azure.microsoft.com/services/kubernetes-service/) más néven AKS-fürt be- és be- és be- és be- és be- és be- és be- és be- és áttéréseként használható.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Egyetlen be- éselődési vezérlőpéldány több alkalmazásátjárót is kezelhet?

Jelenleg a be- éselőjáték-vezérlő egy példánya csak egy application átjáróhoz társítható.

## <a name="diagnostics-and-logging"></a>Diagnosztika és naplózás

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Milyen típusú naplókat biztosít az Application Gateway?

Az Application Gateway három naplót biztosít: 

* **ApplicationGatewayAccessLog**: A hozzáférési napló tartalmazza az alkalmazásátjáró előtér-előtérbe küldött minden kérelmet. Az adatok közé tartozik a hívó IP-címét, a kért URL-címet, a válaszkésést, a visszatérési kódot, valamint a be- és ki- és be- és ki- és be- és bájtokat. Alkalmazásátjárónként egy rekordot tartalmaz.
* **ApplicationGatewayPerformanceLog**: A teljesítménynapló rögzíti az egyes alkalmazásátjárók teljesítményadatait. Az információ tartalmazza az átviteli átviteli bájtban, az összes kiszolgált kérelmek száma, sikertelen kérelmek száma, valamint a kifogástalan és nem megfelelő háttérpéldányok száma.
* **ApplicationGatewayFirewallLog**: A WAF-fel konfigurált alkalmazásátjárók esetében a tűzfalnapló olyan kéréseket tartalmaz, amelyeket észlelési vagy megelőzési módban naplóznak.

Minden naplót 60 másodpercenként gyűjtünk. További információ: [Háttérrendszer állapota, diagnosztikai naplók és metrikák az Application Gateway.](application-gateway-diagnostics.md)

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Honnan tudhatom, hogy a háttérkészlet-tagok kifogástalan állapotúak-e?

Állapot ellenőrzése a PowerShell-parancsmag `Get-AzApplicationGatewayBackendHealth` vagy a portál használatával. További információt az [Application Gateway diagnosztikája című témakörben talál.](application-gateway-diagnostics.md)

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Mi a diagnosztikai naplók adatmegőrzési szabálya?

A diagnosztikai naplók az ügyfél tárfiókjába áramlanak. Az ügyfelek a preferenciájuk alapján állíthatják be az adatmegőrzési szabályt. Diagnosztikai naplók is elküldhető egy eseményközpont vagy az Azure Monitor naplók. További információt az [Application Gateway diagnosztikája című témakörben talál.](application-gateway-diagnostics.md)

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Hogyan szerezhető be az Application Gateway naplói?

A portálon az alkalmazásátjáró menüpaneljén válassza a **Tevékenységnapló** lehetőséget a napló eléréséhez. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Beállíthatok riasztásokat az Application Gateway segítségével?

Igen. Az Application Gateway, riasztások vannak konfigurálva a metrikákon. További információt az [Application Gateway metrikái](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics) és [riasztási értesítések fogadása című témakörben talál.](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Hogyan elemezhetem az Application Gateway forgalmi statisztikáit?

A hozzáférési naplókat többféleképpen tekintheti meg és elemezheti. Használja az Azure Monitor naplóit, excelt, Power BI-t és így tovább.

Olyan Erőforrás-kezelő sablont is használhat, amely [GoAccess](https://goaccess.io/) telepíti és futtatja az Application Gateway hozzáférési naplóinak népszerű GoAccess-naplóelemzőjét. A GoAccess értékes HTTP-forgalmi statisztikákat biztosít, például egyedi látogatókat, kért fájlokat, állomásokat, operációs rendszereket, böngészőket és HTTP-állapotkódokat. További információt a GitHubon az [Erőforráskezelő sablonmappájában található Fontos fájl ban](https://aka.ms/appgwgoaccessreadme)talál.

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Mi okozhatja a háttérállapot ismeretlen állapotának visszatérését?

Általában ismeretlen állapot jelenik meg, ha a háttérrendszerhez való hozzáférést egy hálózati biztonsági csoport (NSG), egyéni DNS vagy felhasználó által definiált útválasztás (UDR) blokkolja az alkalmazásátjáró alhálózatán. További információ: [Háttérrendszer állapota, diagnosztikai naplózása és metrikák az Application Gateway.](application-gateway-diagnostics.md)

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>Van-e olyan eset, amikor az NSG folyamatnaplói nem jelenítik meg az engedélyezett forgalmat?

Igen. Ha a konfiguráció megfelel a következő forgatókönyvnek, az NSG-folyamatnaplókban nem jelenik meg az engedélyezett forgalom:
- Telepítette az Application Gateway 2-es hálózatát
- NSG-vel rendelkezik az alkalmazásátjáró alhálózatán
- Engedélyezte az NSG-folyamatnaplókat az NSG-n

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Hogyan használhatom az Application Gateway V2-t csak privát előtér-IP-címmel?

Az Application Gateway V2 jelenleg nem csak a privát IP-módot támogatja. A következő kombinációkat támogatja:
* Privát IP és nyilvános IP
* Csak nyilvános IP-cím

De ha szeretné használni Application Gateway V2 csak privát IP, akkor kövesse az alábbi eljárást:
1. Alkalmazásátjáró létrehozása nyilvános és privát előtér-IP-címmel
2. Ne hozzon létre figyelők a nyilvános előtér IP-cím. Az Application Gateway nem hallgatja meg a nyilvános IP-cím forgalmát, ha nem hoz létre figyelők.
3. [Hozzon](https://docs.microsoft.com/azure/virtual-network/security-overview) létre és csatoljon egy hálózati biztonsági csoportot az Application Gateway alhálózathoz a következő konfigurációval, prioritási sorrendben:
    
    a. A Forrásból **GatewayManager** szolgáltatáscímkeként és a Cél as **Any** vagy a Destination portból származó forgalom **engedélyezése 65200-65535**néven. Ez a porttartomány az Azure-infrastruktúra kommunikációjához szükséges. Ezek a portok tanúsítványhitelesítéssel védettek (zárolva vannak). A külső entitások, beleértve az Átjáró felhasználói rendszergazdáit is, nem kezdeményezhetnek módosításokat ezeken a végpontokon megfelelő tanúsítványok nélkül.
    
    b. A Forrásból származó forgalom engedélyezése **AzureLoadBalancer** szolgáltatáscímkeként és **célportként**
    
    c. Az összes bejövő forgalom megtagadása a Forrásból **internetes** szolgáltatáscímkeként és célportként **bármely**ként. Adja meg ennek a szabálynak a *legkevesebb prioritást* a bejövő szabályokban
    
    d. Tartsa meg az alapértelmezett szabályokat, például a VirtualNetwork bejövő beengedését, hogy a privát IP-cím hozzáférése ne legyen letiltva
    
    e. A kimenő internetkapcsolat nem tiltható le. Ellenkező esetben a naplózással, a mérőszámokkal és így tovább problémákkal kell szembenéznie.

Minta NSG-konfiguráció csak ![a privát IP-hozzáféréshez: Application Gateway V2 NSG konfiguráció csak magánjellegű IP-hozzáféréshez](./media/application-gateway-faq/appgw-privip-nsg.png)

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>Támogatja az Application Gateway affinitáscookie-k a SameSite attribútumot?
Igen, a [Chromium böngésző](https://www.chromium.org/Home) [v80 frissítése](https://chromiumdash.appspot.com/schedule) megbízást adott a SameSite attribútum nélküli HTTP-cookie-kra, amelyeket SameSite=Lax néven kell kezelni. Ez azt jelenti, hogy az Application Gateway affinitás cookie-t a böngésző nem küldi el harmadik fél környezetében. A forgatókönyv támogatásához az Application Gateway a meglévő *ApplicationGatewayAffinity* cookie mellett egy *másik, ApplicationGatewayAffinityCORS* nevű cookie-t is befecskendez.  Ezek a cookie-k hasonlóak, de az *ApplicationGatewayAffinityCORS* cookie-hoz két további attribútum tartozik hozzá: *SameSite=None; Biztonságos*. Ezek az attribútumok még a kereszteredetű kérelmek esetében is ragadós munkameneteket tartanak fenn. További információt a [cookie-alapú affinitás című részben](configuration-overview.md#cookie-based-affinity) talál.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Application Gateway-ről, olvassa el [a Mi az Azure Application Gateway?](overview.md).
