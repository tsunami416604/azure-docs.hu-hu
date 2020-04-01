---
title: Az Azure hálózati szolgáltatásainak áttekintése
description: Ismerje meg az Azure hálózati szolgáltatásait és azok képességeit – kapcsolódási szolgáltatásokat, alkalmazásvédelmi szolgáltatásokat, alkalmazáskézbesítési szolgáltatásokat és hálózatfigyelést.
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: kumud
ms.openlocfilehash: c6b845eda1df39ccf5e4b2b1d6a615f3bc932b66
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474937"
---
# <a name="azure-networking-services-overview"></a>Az Azure hálózati szolgáltatásainak áttekintése

Az Azure hálózati szolgáltatásai számos olyan hálózati képességet biztosítanak, amelyek együtt vagy külön-külön használhatók. Ha többet szeretne megtudni róluk, kattintson az alábbi kulcsfontosságú funkciók valamelyikére:
- [**Kapcsolódási szolgáltatások:**](#connect)Azure-erőforrások és helyszíni erőforrások csatlakoztatása az Azure - Virtual Network (VNet), virtual WAN, ExpressRoute, VPN Gateway, Virtual Network NAT Gateway, Azure DNS, Társviszony-létesítési szolgáltatás és az Azure Bastion bármely vagy ezek kombinációjával.
- [**Alkalmazásvédelmi szolgáltatások**](#protect) Az Azure – Private Link, DDoS-védelem, tűzfal, hálózati biztonsági csoportok, webalkalmazás-tűzfal és virtuális hálózati végpontok bármely vagy többes hálózati szolgáltatásával védheti alkalmazásait.
- [**Alkalmazáskézbesítési szolgáltatások**](#deliver) Alkalmazásokat biztosíthat az Azure-hálózatban az Azure – Content Delivery Network (CDN), az Azure Front Door Service, a Traffic Manager, az Application Gateway, az Internet Analyzer és a Load Balancer bármely vagy többkombinációjával.
- [**Hálózatfigyelés**](#monitor) – A hálózati erőforrások figyelése az Azure-ban – Network Watcher, ExpressRoute-figyelő, Azure Monitor vagy VNet Terminálhozzáférési pont (TAP) bármely vagy ezek kombinációjával.

## <a name="connectivity-services"></a><a name="connect"></a>Kapcsolati szolgáltatások
 
Ez a szakasz azokat a szolgáltatásokat ismerteti, amelyek kapcsolatot biztosítanak az Azure-erőforrások között, kapcsolatot biztosítanak egy helyszíni hálózatról az Azure-erőforrásokhoz, valamint az Azure - Virtual Network (VNet), a Virtual WAN, az ExpressRoute, a VPN-átjáró, a virtuális hálózati NAT-átjáró, az Azure DNS, az Azure-társviszony-létesítési szolgáltatás és az Azure Bastion ágát.

|Szolgáltatás|Miért érdemes használni?|Forgatókönyvek|
|---|---|---|
|[Virtuális hálózat](#vnet)|Lehetővé teszi, hogy az Azure-erőforrások biztonságosan kommunikáljanak egymással, az interneten és a helyszíni hálózatokon.| <p>[Hálózati forgalom szűrése](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[Hálózati forgalom továbbítása](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[Erőforrások hálózati hozzáférésének korlátozása](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[Virtuális hálózatok csatlakoztatása](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[ExpressRoute](#expressroute)|Kiterjeszti a helyszíni hálózatokat a Microsoft-felhőre egy kapcsolatszolgáltató által támogatott magánkapcsolaton keresztül.|<p>[Az ExpressRoute-kapcsolatcsoport létrehozása és módosítása](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[ExpressRoute-kapcsolatcsoport társviszonyának létrehozása és módosítása](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[Útvonalszűrők konfigurálása és kezelése ExpressRoute-áramkörökhöz](../expressroute/how-to-routefilter-portal.md)</p>|
|[VPN Gateway](#vpngateway)|Titkosított forgalmat küld egy Azure virtuális hálózat és egy helyszíni hely között a nyilvános interneten keresztül.|<p>[Helyek közötti kapcsolatok](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[Virtuális hálózatok közötti kapcsolatok](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[Pont–hely kapcsolatok](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[Virtuális WAN](#virtualwan)|Optimalizálja és automatizálja az azure-beli és azon keresztüli ágkapcsolatot. Az Azure-régiók hubokként szolgálnak, amelyekhez összekapcsolhatja az ágakat.|<p>[Helyek közötti kapcsolatok](../virtual-wan/virtual-wan-site-to-site-portal.md), [ExpressRoute-kapcsolatok](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[Azure DNS](#dns)|Olyan DNS-tartományokat üzemeltet, amelyek a Microsoft Azure-infrastruktúra használatával névfeloldást biztosítanak.|<p>[Saját tartomány üzemeltetése az Azure DNS-ben](../dns/dns-delegate-domain-azure-dns.md)</p><p>[DNS-rekordok létrehozása webalkalmazáshoz](../dns/dns-web-sites-custom-domain.md)</p> <p>[Aliasrekord létrehozása a Traffic Manager számára](../dns/tutorial-alias-tm.md)</p> <p>[Aliasrekord létrehozása nyilvános IP-címhez](../dns/tutorial-alias-pip.md)</p> <p>[Aliasrekord létrehozása a zónaerőforrás-rekordhoz](../dns/tutorial-alias-rr.md)</p>|
|[Azure Bastion](#bastion)|Konfigurálja a biztonságos és zökkenőmentes RDP/SSH-kapcsolatot a virtuális gépekhez közvetlenül az Azure Portalon a TLS-en keresztül. Amikor az Azure Bastionon keresztül csatlakozik, a virtuális gépeknek nincs szükségük nyilvános IP-címre|<p>[Azure Bastion-gazdagép létrehozása](../bastion/bastion-create-host-portal.md)</p><p>[Csatlakozás SSH használatával Linux os virtuális géphez](../bastion/bastion-connect-vm-ssh.md)</p><p>[Csatlakozás RDP használatával Windows virtuális géphez](../bastion/bastion-connect-vm-rdp.md)</p>|
|[Virtuális hálózati NAT átjáró](#nat)|NAT-átjáró létrehozása a kimenő kapcsolat biztosításához egy virtuális gép számára.|<p>[NAT-átjáró létrehozása](../virtual-network/quickstart-create-nat-gateway-portal.md)</p>|
|[Azure-társviszony-létesítési szolgáltatás (előzetes verzió)](#azurepeeringservice)|Működjön együtt a szolgáltatókkal az optimális és megbízható útválasztás érdekében a Microsoft felhőbe a nyilvános hálózaton keresztül.|<p>[Regisztrálja az Azure-társviszony-létesítési szolgáltatást](../peering-service/azure-portal.md)</p>|
||||


### <a name="virtual-network"></a><a name="vnet"></a>Virtuális hálózat

Az Azure Virtual Network (VNet) az Azure-beli magánhálózat alapvető építőköve. A virtuális hálózatok segítségével:
- **Azure-erőforrások közötti kommunikáció:** Virtuális gépeket és számos más Azure-erőforrást telepíthet egy virtuális hálózatra, például az Azure App Service-környezetek, az Azure Kubernetes-szolgáltatás (AKS) és az Azure virtuálisgép-méretezési készletek. A virtuális hálózatokon üzembe helyezhető Azure-erőforrások teljes listájáért lásd: [Virtuális hálózati szolgáltatás integrálása](../virtual-network/virtual-network-for-azure-services.md).
- **Kommunikáció egymással**: Virtuális hálózatokat csatlakoztathat egymáshoz, lehetővé téve, hogy a virtuális hálózat erőforrásai kommunikáljanak egymással, virtuális hálózati társviszony-létesítés használatával. Az összekacsolt virtuális hálózatok lehetnek azonos vagy eltérő Azure-régiókban. További információt a [Virtuális hálózati társviszony-létesítés](../virtual-network/virtual-network-peering-overview.md)című témakörben talál.
- **Kommunikáció az internettel:** A virtuális hálózat minden erőforrása alapértelmezés szerint képes kommunikálni az internettel. Bejövő kommunikációt létesíthet egy erőforrással egy nyilvános IP-cím vagy Load Balancer hozzárendelésével. [Nyilvános IP-címek](../virtual-network/virtual-network-public-ip-address.md) vagy nyilvános [terheléselosztó](../load-balancer/load-balancer-overview.md) használatával is kezelheti a kimenő kapcsolatokat.
- **Kommunikáció a helyszíni hálózatokkal**: A [VPN-átjáró](../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy az [ExpressRoute](../expressroute/expressroute-introduction.md)segítségével csatlakoztathatja a helyszíni számítógépeket és hálózatokat egy virtuális hálózathoz.

További információ: [Mi az Azure virtuális hálózat?](../virtual-network/virtual-networks-overview.md).

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
Az ExpressRoute lehetővé teszi, hogy a helyszíni hálózatokat kiterjessze a Microsoft felhőbe egy kapcsolatszolgáltató által támogatott magánkapcsolaton keresztül. Ez a kapcsolat nem nyilvános. A forgalom nem az interneten keresztül halad át. Az ExpressRoute használatával kapcsolatokat létesíthet olyan Microsoft-felhőszolgáltatásokkal, mint például a Microsoft Azure, az Office 365 és a Dynamics 365.  További információ: [Mi az ExpressRoute?](../expressroute/expressroute-introduction.md)

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN Gateway
A VPN-átjáró segítségével hozhat létre titkosított, létesítmények közötti kapcsolatokat a virtuális hálózathoz a helyszíni helyekről, vagy titkosított kapcsolatokat hozhat létre a virtuális hálózatok között. A VPN-átjárókapcsolatokhoz különböző konfigurációk érhetők el, például a helyek közötti, a pont-hely vagy a virtuális hálózat és a virtuális hálózat között.
Az alábbi ábra több hely közötti VPN-kapcsolatot mutat be ugyanahhoz a virtuális hálózathoz.

![Helyek közötti Azure VPN-átjárókapcsolatok](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

A különböző típusú VPN-kapcsolatokról a [VPN-átjáró című](../vpn-gateway/vpn-gateway-about-vpngateways.md)témakörben talál további információt.

### <a name="virtual-wan"></a><a name="virtualwan"></a>Virtuális WAN
Az Azure Virtual WAN egy hálózati szolgáltatás, amely optimalizált és automatizált fiókkapcsolatot biztosít az Azure-hoz és azon keresztül. Az Azure-régiók hubokként szolgálnak, amelyekhez összekapcsolhatja az ágakat. Az Azure gerinchálózatának kihasználásával ágakat is csatlakoztathat, és élvezheti az ág-vnet-kapcsolatot. Az Azure Virtual WAN számos Azure-felhőbeli kapcsolódási szolgáltatást egyesít, például a helyek közötti VPN-t, az ExpressRoute-ot, a pont-hely felhasználói VPN-t egyetlen operatív felületen. Az Azure virtuális hálózatokkal való kapcsolat virtuális hálózati kapcsolatok használatával jön létre. További információ: [Mi az Azure virtuális WAN?](../virtual-wan/virtual-wan-about.md)

![Virtuális WAN ábrája](./media/networking-overview/virtualwan1.png)

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Az Azure DNS egy üzemeltetési szolgáltatás, amely a Microsoft Azure infrastruktúráját használja a DNS-tartományok névfeloldásához. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti. További információ: [Mi az Azure DNS?](../dns/dns-overview.md)

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
Az Azure Bastion szolgáltatás egy új, teljesen platform által felügyelt PaaS-szolgáltatás, amelyet a virtuális hálózaton belül biztosít. Biztonságos és zökkenőmentes RDP/SSH-kapcsolatot biztosít a virtuális gépekhez közvetlenül az Azure Portalon a TLS-en keresztül. Ha az Azure Bastionon keresztül csatlakozik, a virtuális gépeinek nincs szüksége nyilvános IP-címre. További információ: [Mi az Azure-bástya?](../bastion/bastion-overview.md)

![Azure Bástya architektúra](./media/networking-overview/architecture.png)

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>Virtuális hálózati NAT átjáró
A virtuális hálózati hálózati címfordítás (hálózati címfordítás) leegyszerűsíti a virtuális hálózatok csak kimenő internetkapcsolatát. Ha egy alhálózaton konfigurálva van, az összes kimenő kapcsolat a megadott statikus nyilvános IP-címeket használja. Kimenő kapcsolat lehetséges terheléselosztó vagy nyilvános IP-címek közvetlenül a virtuális gépekhez csatlakoztatott nélkül. További információ: [Mi a virtuális hálózati NAT átjáró?](../virtual-network/nat-overview.md) 

![Virtuális hálózati NAT átjáró](./media/networking-overview/flow-map.png)

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a>Azure-társviszony-létesítési szolgáltatás
Az Azure Társviszony-létesítési szolgáltatás javítja az ügyfélkapcsolatot a Microsoft felhőszolgáltatásaival, például az Office 365-tel, a Dynamics 365-tel, a szoftver szolgáltatásként (SaaS) szolgáltatásokkal, az Azure-ral vagy a nyilvános interneten keresztül elérhető Microsoft-szolgáltatásokkal. További információ: [Mi az Azure-társviszony-létesítési szolgáltatás?](../peering-service/about.md).

## <a name="application-protection-services"></a><a name="protect"></a>Alkalmazásvédelmi szolgáltatások

Ez a szakasz az Azure-beli hálózati szolgáltatásokat ismerteti, amelyek segítenek megvédeni a hálózati erőforrásokat – Az alkalmazások védelme az Azure - Private Link, A DDoS-védelem, a tűzfal, a hálózati biztonsági csoportok, a webalkalmazás-tűzfal és a virtuális hálózati végpontok bármely vagy ezek kombinációjával.

|Szolgáltatás|Miért érdemes használni?|Forgatókönyv|
|---|---|---|
|[DDoS védelem](#ddosprotection) |Magas rendelkezésre állás az alkalmazások számára a túlzott IP-forgalmi díjak elleni védelemmel|[Az Azure DDoS-védelem kezelése](../virtual-network/manage-ddos-protection.md)|
|[Webalkalmazás tűzfala](#waf)|<p>[Az Azure WAF az Application Gateway szolgáltatással](../web-application-firewall/ag/ag-overview.md) regionális védelmet nyújt a nyilvános és privát címtérben lévő entitások számára</p><p>[Az Azure WAF bejárati ajtóval](../web-application-firewall/afds/afds-overview.md) védelmet nyújt a hálózat szélén a nyilvános végpontok.</p>|<p>[Botvédelmi szabályok konfigurálása](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[Egyéni válaszkód konfigurálása](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[IP-korlátozási szabályok konfigurálása](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[Díjkorlát-szabály konfigurálása](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Azure Firewall](#firewall)|Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi. Egy teljes mértékben állapotalapú tűzfalszolgáltatás, beépített magas rendelkezésre állással és korlátlan felhőméretezhetőséggel.|<p>[Azure-tűzfal üzembe helyezése virtuális hálózatban](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[- Azure tűzfal telepítése hibrid hálózatban](../firewall/tutorial-hybrid-ps.md)</p> <p>[Bejövő forgalom szűrése az Azure Firewall DNST-vel](../firewall/tutorial-firewall-dnat.md)</p>|
|[Network security groups (Hálózati biztonsági csoportok)](#nsg)|Teljes részletes elosztott végpontvezérlő a virtuális gépnél/alhálózaton az összes hálózati forgalomhoz|[Hálózati forgalom szűrése hálózati biztonsági csoportokkal](../virtual-network/tutorial-filter-network-traffic.md)|
|[Virtuális hálózati szolgáltatásvégpontok](#serviceendpoints)|Lehetővé teszi, hogy korlátozza a hálózati hozzáférést bizonyos Azure-szolgáltatáserőforrásokhoz egy virtuális hálózati alhálózathoz|[PaaS-erőforrásokhoz való hálózati hozzáférés korlátozása](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
[Privát kapcsolat](#privatelink)|Lehetővé teszi az Azure PaaS-szolgáltatások (például az Azure Storage és az SQL Database) és az Azure által üzemeltetett ügyfél tulajdonú/partnerszolgáltatások elérését a virtuális hálózat privát végpontján keresztül.|<p>[Privát végpont létrehozása](../private-link/create-private-endpoint-portal.md)</p><p>[Privát kapcsolati szolgáltatás létrehozása](../private-link/create-private-link-service-portal.md)</p>|
|||
### <a name="ddos-protection"></a><a name="ddosprotection"></a>DDoS Protection 
[Az Azure DDoS Protection](../virtual-network/manage-ddos-protection.md) ellenintézkedéseket biztosít a legkifinomultabb DDoS-fenyegetések ellen. A szolgáltatás továbbfejlesztett DDoS-kockázatcsökkentési lehetőségeket biztosít az alkalmazás és a virtuális hálózatokban üzembe helyezett erőforrások számára. Emellett az Azure DDoS-védelmet használó ügyfelek hozzáférhetnek a DDoS gyorsreagálási támogatáshoz, hogy ddos-szakértőket vonjanak be egy aktív támadás során.

![DDoS Protection](./media/networking-overview/ddos-protection.png)

### <a name="web-application-firewall"></a><a name="waf"></a>Webalkalmazás tűzfala

[Az Azure Web Application Firewall](../web-application-firewall/overview.md) (WAF) védelmet nyújt a webalkalmazások a gyakori webes biztonsági rések és biztonsági rések, például az SQL-injektálás, és a webhelyek közötti parancsfájlok. Az Azure WAF a felügyelt szabályok on-t biztosít az OWASP 10 legfontosabb biztonsági rései elleni, dobozon kívüli védelemben. Ezenkívül az ügyfelek egyéni szabályokat is konfigurálhatnak, amelyek az ügyfél által felügyelt szabályok, hogy további védelmet biztosítsanak a forrás IP-tartománya alapján, és olyan attribútumokat kérhetnek, mint a fejlécek, a cookie-k, az űrlap adatmezők vagy a lekérdezési karakterlánc paraméterei.

Az ügyfelek dönthetnek úgy, hogy az [Azure WAF-ot az Application Gateway-rel](../application-gateway/waf-overview.md) telepítik, amely regionális védelmet biztosít a nyilvános és privát címtérben lévő entitások számára. Az ügyfelek is választhatnak, hogy üzembe helyezik [az Azure WAF bejárati ajtóval,](../frontdoor/waf-overview.md) amely védelmet nyújt a hálózat szélén a nyilvános végpontok.

![Web Application Firewall (Webalkalmazási tűzfal)](./media/networking-overview/waf-overview.png)


### <a name="azure-firewall"></a><a name="firewall"></a>Azure Firewall
Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi. Az Azure Firewall használatával központilag hozhat létre, kényszeríthetésre és naplózásra vonatkozó alkalmazás- és hálózati kapcsolati szabályzatokat az előfizetések és a virtuális hálózatok között. Az Azure Firewall statikus nyilvános IP-címet használ a virtuális hálózat erőforrásaihoz, így a külső tűzfalak azonosíthatják a virtuális hálózatból érkező forgalmat. 

Az Azure Firewall szolgáltatásról további információt az [Azure Firewall dokumentációjában](../firewall/overview.md)talál.

![Tűzfal áttekintése](./media/networking-overview/firewall-threat.png)

### <a name="network-security-groups"></a><a name="nsg"></a>Network security groups (Hálózati biztonsági csoportok)
Az Azure-beli virtuális hálózatokban az Azure-erőforrások bejövő és kimenő hálózati forgalmát hálózati biztonsági csoportokkal szűrheti. További információt a [Biztonság – áttekintés című témakörben talál.](../virtual-network/security-overview.md)

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Szolgáltatásvégpontok
A virtuális hálózatok (VNet) szolgáltatásvégpontjai egy közvetlen kapcsolaton keresztül kiterjesztik a virtuális hálózat magáncímterét és a VNet identitását az Azure-szolgáltatásokra. A végpontok segítségével biztosíthatja, hogy kritikus fontosságú Azure-szolgáltatási erőforrásai csak a virtuális hálózatain legyenek elérhetőek. A VNet felől az Azure-szolgáltatás felé irányuló forgalom mindig a Microsoft Azure gerinchálózatán halad át. További információ: [Virtual network service endpoints](../virtual-network/virtual-network-service-endpoints-overview.md).

![Virtuális hálózati szolgáltatásvégpontok](./media/networking-overview/vnet-service-endpoints-overview.png)

### <a name="azure-private-link"></a><a name="privatelink"></a>Azure Private Link
[Az Azure Private Link](../private-link/private-link-overview.md) lehetővé teszi az Azure PaaS-szolgáltatások (például az Azure Storage és az SQL Database) és az Azure által üzemeltetett ügyféltulajdonú/partnerszolgáltatások elérését a virtuális hálózat privát végpontján keresztül.
A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatát járja be. A szolgáltatás nyilvános internetnek való kitettsége már nem szükséges. Létrehozhatja saját privát kapcsolati szolgáltatását a virtuális hálózatban, és eljuttathatja az ügyfelekhez.

![Privát végpont – áttekintés](./media/networking-overview/private-endpoint.png)


## <a name="application-delivery-services"></a><a name="deliver"></a>Alkalmazáskézbesítési szolgáltatások

Ez a szakasz az Azure-beli hálózati szolgáltatásokat ismerteti, amelyek segítenek az alkalmazások – Network Watcher, ExpressRoute-figyelő, Azure Monitor vagy VNet terminálhozzáférési pont (TAP).

|Szolgáltatás|Miért érdemes használni?|Forgatókönyv|
|---|---|---|
|[Tartalomkézbesítési hálózat](#cdn)|Nagy sávszélességű tartalmat biztosít a felhasználóknak. A CDN-ek a gyorsítótárazott tartalmat a jelenléti pont (POP) olyan helyen tárolják, amely közel van a végfelhasználókhoz, hogy minimalizálja a késést|<p>[CDN hozzáadása webalkalmazáshoz](../cdn/cdn-add-to-web-app.md)</p> <p>[- Hozzáférés tárolási blobok egy Azure CDN egyéni tartomány HTTPS-en keresztül](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[Egyéni tartomány hozzáadása az Azure CDN-végponthoz](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[HTTPS konfigurálása Azure CDN egyéni tartományon](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate)</p>|
|[Azure Front Door Service](#frontdoor)|Lehetővé teszi a webes forgalom globális útválasztásának meghatározását, kezelését és figyelését a legjobb teljesítmény és a magas rendelkezésre állás érdekében azonnali globális feladatátvétel optimalizálásával.|<p>[Egyéni tartomány hozzáadása az Azure Front Door Service-hez](../frontdoor/front-door-custom-domain.md)</p> <p>[HTTPS konfigurálása bejárati ajtó egyéni tartományában](../frontdoor/front-door-custom-domain-https.md)</p><p>[Geoszűrő webalkalmazás-tűzfal házirend beállítása](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[Traffic Manager](#trafficmanager)|A DNS-alapú forgalmat a globális Azure-régiók között osztja el a szolgáltatások között, miközben magas rendelkezésre állást és válaszidőt biztosít|<p> [Forgalom irányítása az alacsony késés érdekében](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[Forgalom irányítása magas prioritású végpontra](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [Forgalom szabályozása súlyozott végpontokkal](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[Útvonalforgalom a végpont földrajzi elhelyezkedése alapján](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [Forgalom irányítása a felhasználó alhálózata alapján](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[Load Balancer](#loadbalancer)|Regionális terheléselosztást biztosít a rendelkezésre állási zónák közötti és a virtuális hálózatok közötti forgalom továbbításával. Belső terheléselosztást biztosít a forgalom nak az erőforrások közötti és az erőforrások közötti útválasztásával a regionális alkalmazás létrehozásához.|<p> [Virtuális gépek internetes forgalmának terheléselosztása](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[A virtuális hálózaton belüli virtuális gépek közötti terheléselosztási forgalom](../load-balancer/tutorial-load-balancer-basic-internal-portal.md)<p>[A forgalom továbbítása adott virtuális gépek egy adott portjára](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [Terheléselosztási és kimenő szabályok konfigurálása](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[Application Gateway](#applicationgateway)|Az Azure Application Gateway egy webes forgalomra vonatkozó terheléselosztó, amellyel kezelheti a webalkalmazásai forgalmát.|<p>[A webes forgalom irányítása az Azure Application Gatewayjel](../application-gateway/quick-create-portal.md)</p><p>[Oktatóanyag: Alkalmazásátjáró konfigurálása TLS-végződtetéssel az Azure Portal használatával](../application-gateway/create-ssl-portal.md)</p><p>[Alkalmazásátjáró létrehozása URL-alapú átirányítással](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="content-delivery-network"></a><a name="cdn"></a>Tartalomkézbesítési hálózat
Az Azure tartalomkézbesítési hálózat CDN a tartalmakat világszerte, stratégiai alapon elhelyezett fizikai csomópontokon gyorsítótárazva globális megoldást kínál a fejlesztők számára a tartalmak nagy sávszélességű gyors kézbesítéséhez a felhasználók felé. Az Azure CDN-ről az Azure Content Delivery Network című témakörben talál további [információt.](../cdn/cdn-overview.md)

![Azure CDN](./media/networking-overview/cdn-overview.png)

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Azure bejárati ajtó szolgáltatás
Az Azure Front Door Service segítségével meghatározhatja, felügyelheti és monitorozhatja a webes forgalmának globális forgalomirányítását, optimalizálhatja annak teljesítményét, és azonnali globális feladatátvétellel magas rendelkezésre állást biztosíthat. A Front Door használatával a globális (több régióban található) fogyasztói és nagyvállalati alkalmazásait olyan robusztus, nagy teljesítményű, személyre szabott és modern alkalmazásokká, API-kká és tartalmakká alakíthatja, amelyek az Azure-on keresztül elérhetik a globális közönségüket. További információ: [Azure Front Door](../frontdoor/front-door-overview.md).


### <a name="traffic-manager"></a><a name="trafficmanager"></a>Traffic Manager

Az Azure Traffic Manager egy DNS-alapú forgalom-terheléselosztó, amely lehetővé teszi a szolgáltatásokhoz érkező forgalom optimális elosztását a globális Azure-régiókban, miközben magas rendelkezésre állást és válaszkészséget biztosít. A Traffic Manager egy sor forgalom-útválasztási módszert biztosít a forgalom elosztásához, például a prioritást, a súlyozott, a teljesítményt, a földrajzi, a többértékű t vagy az alhálózatot. A forgalomútválasztási módszerekről a [Traffic Manager útválasztási módszerei című](../traffic-manager/traffic-manager-routing-methods.md)témakörben talál további információt.

Az alábbi ábra a Forgalomkezelővel való végpontprioritás-alapú útválasztást mutatja be:

![Azure Traffic Manager "Prioritás" forgalom-útválasztási módszer](./media/networking-overview/priority.png)

A Traffic Managerről a [Mi az Azure Traffic Manager?](../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>Load Balancer
Az Azure Load Balancer nagy teljesítményű, alacsony késleltetésű Layer 4 terheléselosztást biztosít az összes UDP- és TCP-protokollhoz. Kezeli a bejövő és kimenő kapcsolatokat. Nyilvános és belső terheléselosztásos végpontok konfigurálhatók. A tcp- és HTTP-állapotvizsgálati beállítások használatával meghatározhatja a bejövő kapcsolatok at a háttérkészlet-célokhoz való hozzárendeléséhez a szolgáltatás elérhetőségének kezeléséhez. Ha többet szeretne megtudni a terheléselosztóról, olvassa el a [Terheléselosztó áttekintéséről](../load-balancer/load-balancer-overview.md) szóló cikket.

Az alábbi képen egy internetre néző többrétegű alkalmazás látható, amely külső és belső terheléselosztókat is használ:

![Példa az Azure load balancer](./media/networking-overview/load-balancer.png)


### <a name="application-gateway"></a><a name="applicationgateway"></a>Application Gateway
Az Azure Application Gateway egy webes forgalomra vonatkozó terheléselosztó, amellyel kezelheti a webalkalmazásai forgalmát. Ez egy alkalmazáskézbesítési vezérlő (ADC) szolgáltatásként, amely különböző 7- es réteges terheléselosztási lehetőségeket kínál az alkalmazások számára. További információ: [Mi az Azure Application Gateway?](../application-gateway/overview.md).

Az alábbi ábrán url-elérési útválasztás látható az Application Gateway alkalmazásátjáróval.

![Példa alkalmazásátjáróra](./media/networking-overview/figure1-720.png)

## <a name="network-monitoring-services"></a><a name="monitor"></a>Hálózatfelügyeleti szolgáltatások
Ez a szakasz az Azure-beli hálózati szolgáltatásokat ismerteti, amelyek segítenek a hálózati erőforrások – Network Watcher, ExpressRoute Monitor, Azure Monitor és virtuális hálózati TAP – figyelésében.

|Szolgáltatás|Miért érdemes használni?|Forgatókönyv|
|---|---|---|
|[Network Watcher](#networkwatcher)|Segít a kapcsolódási problémák figyelésében és elhárításában, a VPN-, Az NSG-és útválasztási problémák diagnosztizálásában, a csomagok rögzítésében a virtuális számítógépen, automatizálja a diagnosztikai eszközök indítását az Azure Functions és a Logic Apps használatával|<p>[A virtuális gép forgalomszűrési problémáinak diagnosztizálása](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[A virtuális gép útválasztási problémáinak diagnosztizálása](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[Virtuális gépek közötti kommunikáció figyelése](../network-watcher/connection-monitor.md)</p><p>[Hálózatok közötti kommunikációs problémák diagnosztizálása](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[Virtuális gép be- és kimenő forgalmának naplózása](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[ExpressRoute-figyelő](#expressroutemonitor)|Valós idejű figyelése a hálózati teljesítmény, a rendelkezésre állás és a kihasználtság, segít az automatikus felderítése hálózati topológia, gyorsabb hibaelkülönítést, észleli az átmeneti hálózati problémák, segít elemezni a korábbi hálózati teljesítmény jellemzői, támogatja a több-előfizetés|<p>[Network Performance Monitor for ExpressRoute konfigurálása](../expressroute/how-to-npm.md)</p><p>[Az ExpressRoute monitorozása, mérőszámai és riasztásai](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure Monitor](#azuremonitor)|Segít megérteni, hogyan teljesítenek az alkalmazások, és proaktív módon azonosítja az őket érintő problémákat és az erőforrásokat, amelyektől függenek.|<p>[A Traffic Manager mutatói és riasztásai](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[Azure monitor diagnosztika standard terheléselosztóhoz](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[Az Azure Firewall naplóinak és metrikáinak monitorozása](../firewall/tutorial-diagnostics.md)</p><p>[Az Azure webalkalmazási tűzfalának monitorozása és naplózása](../frontdoor/waf-front-door-monitor.md)</p>|
|[Virtuális hálózati TAP](#vnettap)|A virtuális gép hálózati forgalmának folyamatos streamelése a csomaggyűjtőhöz, lehetővé teszi a hálózati és alkalmazásteljesítmény-kezelési megoldásokat és a biztonsági elemzési eszközöket|[VNet TAP-erőforrás létrehozása](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="network-watcher"></a><a name="networkwatcher"></a>Network Watcher
Az Azure Network Watcher eszközeivel monitorozhatja és diagnosztizálhatja az erőforrásokat egy Azure virtuális hálózaton belül, illetve megtekintheti azok metrikáit, és engedélyezheti vagy letiltja azok naplóit. További információ: [Mi a Hálózati figyelő?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>ExpressRoute-figyelő
Az ExpressRoute-körmetrikák, diagnosztikai naplók és riasztások megtekintéséről az [ExpressRoute figyelése, a metrikák és a riasztások](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json)című témakörben olvashat.
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
Az Azure Monitor az alkalmazások rendelkezésre állását és teljesítményét növeli a lehető legnagyobbra egy olyan átfogó megoldás biztosításával, amely a felhőből és a helyszíni környezetből származó telemetriaadatokat gyűjt, elemez, valamint műveleteket hajt végre az adatok alapján. Ez a szolgáltatás segít megérteni azt, hogy az alkalmazásai hogyan teljesítenek, valamint proaktív módon azonosítja a működésüket befolyásoló problémákat és azokat az erőforrásokat, amelyektől függenek. További információt az [Azure Monitor – áttekintés című témakörben talál.](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### <a name="virtual-network-tap"></a><a name="vnettap"></a>Virtuális hálózati TAP
Az Azure virtuális hálózati TAP (Terminal Access Point) lehetővé teszi a virtuális gép hálózati forgalmának folyamatos streamelését egy hálózati csomaggyűjtő vagy -elemző eszköz felé. A gyűjtő vagy elemző eszközt egy [hálózati virtuális berendezés](https://azure.microsoft.com/solutions/network-appliances/) partner biztosítja. 

Az alábbi képen látható, hogyan működik a virtuális hálózat TAP. 

![A virtuális hálózat TAP működése](./media/networking-overview/virtual-network-tap-architecture.png)

További információ: [Mi az a Virtuális hálózat TAP.](../virtual-network/virtual-network-tap-overview.md)

## <a name="next-steps"></a>További lépések

- Hozza létre az első virtuális hálózatot, és csatlakoztasson néhány virtuális gépet az [első virtuális hálózati](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk létrehozása című cikkben leírt lépések végrehajtásával.
- A számítógép virtuális hálózathoz való csatlakoztatásával a [Pont-hely kapcsolat konfigurálása című cikkben leírt](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)lépések végrehajtásával.
- Az internetes forgalom terheléselosztása a nyilvános kiszolgálók felé az [Internetfelé néző terheléselosztó](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) cikkben leírt lépések végrehajtásával.
 
 
   
