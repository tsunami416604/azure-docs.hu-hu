---
title: Az Azure Networking Services áttekintése
description: Ismerje meg az Azure-beli hálózatkezelési szolgáltatásokat, valamint azok képességeit – a kapcsolódási szolgáltatásokat, az alkalmazás-védelmi szolgáltatásokat, az Application Delivery Servicest és a hálózat figyelését.
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: kumud
ms.openlocfilehash: 097d2b4dffd1dda02d8e342a11b3a907bad4e90a
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85851654"
---
# <a name="azure-networking-services-overview"></a>Az Azure Networking Services áttekintése

Az Azure hálózati szolgáltatásai különféle hálózati funkciókat biztosítanak, amelyek együtt vagy külön is használhatók. A következő főbb képességek bármelyikére kattintva további információkat tudhat meg róluk:
- [**Csatlakozási szolgáltatások**](#connect): az Azure-erőforrások és a helyszíni erőforrások összekapcsolása az azure-Virtual Network (VNet), a Virtual WAN, a ExpressRoute, a VPN Gateway, a Virtual Network NAT Gateway, a Azure DNS, a peering Service és az Azure Bastion használatával.
- [**Alkalmazás-védelmi szolgáltatások**](#protect): az alkalmazásokat az Azure-Private link, a DDoS Protection, a tűzfal, a hálózati biztonsági csoportok, a webalkalmazási tűzfal és a Virtual Network végpontok bármely vagy több kombinációjának használatával biztosíthatja az alkalmazások védelmére.
- [**Application Delivery Services**](#deliver): az Azure-hálózatban lévő alkalmazásokat az azure-Content DELIVERY Network (CDN), az Azure bejárati szolgáltatás, a Traffic Manager, a Application Gateway, az Internet Analyzer és a Load Balancer használatával biztosíthatja.
- [**Hálózatfigyelő**](#monitor): az Azure-Network Watcher, a ExpressRoute monitor, a Azure monitor vagy a VNet terminál-hozzáférési pont (TAP) használatával figyelheti a hálózati erőforrásokat.

## <a name="connectivity-services"></a><a name="connect"></a>Kapcsolati szolgáltatások
 
Ez a szakasz azokat a szolgáltatásokat ismerteti, amelyek kapcsolatot biztosítanak az Azure-erőforrások, a helyszíni hálózat és az Azure-erőforrások közötti kapcsolat, valamint az Azure-Virtual Network (VNet), a Virtual WAN, a ExpressRoute, a VPN Gateway, a Virtual Network NAT Gateway, a Azure DNS, az Azure peering Service és az Azure Bastion szolgáltatásokkal.

|Szolgáltatás|Miért érdemes használni?|Forgatókönyvek|
|---|---|---|
|[Virtuális hálózat](#vnet)|Lehetővé teszi az Azure-erőforrások számára, hogy biztonságosan kommunikáljanak egymással, az internettel és a helyszíni hálózatokkal.| <p>[Hálózati forgalom szűrése](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[Hálózati forgalom továbbítása](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[Erőforrások hálózati hozzáférésének korlátozása](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[Virtuális hálózatok csatlakoztatása](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[ExpressRoute](#expressroute)|Kiterjesztheti helyszíni hálózatait a Microsoft-felhőbe egy olyan privát kapcsolaton keresztül, amely egy kapcsolati szolgáltató által könnyíti meg.|<p>[Az ExpressRoute-kapcsolatcsoport létrehozása és módosítása](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[ExpressRoute-kapcsolatcsoport társviszonyának létrehozása és módosítása](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[ExpressRoute-áramkörök útválasztási szűrőinek konfigurálása és kezelése](../expressroute/how-to-routefilter-portal.md)</p>|
|[VPN Gateway](#vpngateway)|Titkosított forgalmat küld egy Azure-beli virtuális hálózat és egy helyszíni hely között a nyilvános interneten keresztül.|<p>[Helyek közötti kapcsolatok](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[Virtuális hálózatok közötti kapcsolatok](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[Pont – hely kapcsolatok](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[Virtual WAN](#virtualwan)|Optimalizálja és automatizálja az Azure-ra és az-ra irányuló ág-kapcsolatot. Az Azure-régiók olyan hubok, amelyekhez az ágakat összekapcsolhatjuk.|<p>[Helyek közötti kapcsolatok](../virtual-wan/virtual-wan-site-to-site-portal.md), [ExpressRoute kapcsolatok](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[Azure DNS](#dns)|Olyan DNS-tartományokat üzemeltet, amelyek Microsoft Azure infrastruktúra használatával biztosítják a névfeloldást.|<p>[Üzemeltessen saját tartományt az Azure DNS-ben](../dns/dns-delegate-domain-azure-dns.md)</p><p>[DNS-rekordok létrehozása egy webalkalmazáshoz](../dns/dns-web-sites-custom-domain.md)</p> <p>[Alias-rekord létrehozása a Traffic Managerhoz](../dns/tutorial-alias-tm.md)</p> <p>[Alias-rekord létrehozása nyilvános IP-címhez](../dns/tutorial-alias-pip.md)</p> <p>[Alias-rekord létrehozása a zóna erőforrásrekord számára](../dns/tutorial-alias-rr.md)</p>|
|[Azure Bastion](#bastion)|Biztonságos és zökkenőmentes RDP/SSH-kapcsolatot konfigurálhat a virtuális gépekhez közvetlenül a TLS-Azure Portal. Amikor az Azure Bastion-n keresztül kapcsolódik, a virtuális gépeknek nincs szükségük nyilvános IP-címekre|<p>[Azure Bastion-gazdagép létrehozása](../bastion/bastion-create-host-portal.md)</p><p>[Kapcsolódás az SSH-val Linux rendszerű virtuális géphez](../bastion/bastion-connect-vm-ssh.md)</p><p>[Kapcsolódás RDP használatával Windows rendszerű virtuális géphez](../bastion/bastion-connect-vm-rdp.md)</p>|
|[Virtuális hálózati NAT-átjáró](#nat)|Hozzon létre egy NAT-átjárót a virtuális gép kimenő kapcsolatának biztosításához.|<p>[NAT-átjáró létrehozása](../virtual-network/quickstart-create-nat-gateway-portal.md)</p>|
|[Azure-partneri szolgáltatás (előzetes verzió)](#azurepeeringservice)|Együttműködés a szolgáltatókkal a Microsoft Cloud számára a nyilvános hálózaton keresztül történő optimális és megbízható útválasztáshoz.|<p>[Azure-partneri szolgáltatás regisztrálása](../peering-service/azure-portal.md)</p>|
||||


### <a name="virtual-network"></a><a name="vnet"></a>Virtuális hálózat

Az Azure Virtual Network (VNet) az Azure-beli magánhálózat alapvető építőeleme. A virtuális hálózatok a következőket végezheti el:
- **Kommunikáció az Azure-erőforrások között**: telepíthet virtuális gépeket és számos más típusú Azure-erőforrást egy virtuális hálózatra, például Azure app Service környezetekre, az Azure Kubernetes szolgáltatásra (ak) és az Azure Virtual Machine Scale Setsre. A virtuális hálózatokon üzembe helyezhető Azure-erőforrások teljes listájáért lásd: [Virtuális hálózati szolgáltatás integrálása](../virtual-network/virtual-network-for-azure-services.md).
- **Kommunikáció egymás között**: a virtuális hálózatok összekapcsolhatók egymással, így a virtuális hálózatban lévő erőforrások egymással is kommunikálhatnak, a virtuális hálózatok használatával. Az összekacsolt virtuális hálózatok lehetnek azonos vagy eltérő Azure-régiókban. További információ: [Virtual Network peering](../virtual-network/virtual-network-peering-overview.md).
- **Kommunikáció az internettel**: a VNet összes erőforrása alapértelmezés szerint képes kommunikálni a kimenő internettel. Bejövő kommunikációt létesíthet egy erőforrással egy nyilvános IP-cím vagy Load Balancer hozzárendelésével. A kimenő kapcsolatok kezeléséhez [nyilvános IP-címeket](../virtual-network/virtual-network-public-ip-address.md) vagy nyilvános [Load Balancer](../load-balancer/load-balancer-overview.md) is használhat.
- **Kommunikáció helyszíni hálózatokkal**: a helyszíni számítógépeit és hálózatait [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy [ExpressRoute](../expressroute/expressroute-introduction.md)használatával összekapcsolhatja egy virtuális hálózattal.

További információ: [Mi az az Azure Virtual Network?](../virtual-network/virtual-networks-overview.md)

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
A ExpressRoute lehetővé teszi, hogy a helyszíni hálózatait a Microsoft-felhőben a kapcsolati szolgáltató által biztosított privát kapcsolaton keresztül bővítse. Ez a kapcsolat nem nyilvános. A forgalom nem az interneten keresztül halad át. Az ExpressRoute használatával kapcsolatokat létesíthet olyan Microsoft-felhőszolgáltatásokkal, mint például a Microsoft Azure, az Office 365 és a Dynamics 365.  További információ: [What is ExpressRoute?](../expressroute/expressroute-introduction.md).

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN Gateway
A VPN Gateway segítségével titkosított létesítmények közötti kapcsolatokat hozhat létre a virtuális hálózattal a helyszíni helyekről, illetve titkosított kapcsolatokat hozhat létre a virtuális hálózatok között. Különböző konfigurációk érhetők el VPN Gateway kapcsolatokhoz, például helyek közötti, pont – hely vagy VNet – VNet.
A következő ábra több helyek közötti VPN-kapcsolatot mutat be ugyanahhoz a virtuális hálózathoz.

![Helyek közötti Azure VPN Gateway kapcsolatok](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

További információ a VPN-kapcsolat különböző típusairól: [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="virtual-wan"></a><a name="virtualwan"></a>Virtual WAN
Az Azure Virtual WAN egy hálózati szolgáltatás, amely optimalizált és automatizált ág-kapcsolatot biztosít az Azure-hoz és a-n keresztül. Az Azure-régiók olyan hubok, amelyekhez az ágakat összekapcsolhatjuk. Az Azure-gerinc kihasználható az ágak összekapcsolásához és a VNet közötti kapcsolathoz is. Az Azure Virtual WAN számos Azure Cloud connectivity-szolgáltatást kínál, többek között a helyek közötti VPN-t, a ExpressRoute, a pont – hely típusú felhasználói VPN-t egyetlen operatív felületre. Az Azure virtuális hálózatok-hez való kapcsolódás virtuális hálózati kapcsolatok használatával történik. További információ: [Mi az az Azure Virtual WAN?](../virtual-wan/virtual-wan-about.md).

![Virtuális WAN ábrája](./media/networking-overview/virtualwan1.png)

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Az Azure DNS egy üzemeltetési szolgáltatás, amely a Microsoft Azure infrastruktúráját használja a DNS-tartományok névfeloldásához. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti. További információ: [Mi az Azure DNS?](../dns/dns-overview.md)

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
Az Azure Bastion szolgáltatás egy új, teljes körűen felügyelt, a virtuális hálózaton belül kiépített, teljesen platform által felügyelt Pásti szolgáltatás. Biztonságos és zökkenőmentes RDP/SSH-kapcsolatot biztosít a virtuális gépekhez közvetlenül a TLS-en keresztül Azure Portal. Ha az Azure Bastionon keresztül csatlakozik, a virtuális gépeinek nincs szüksége nyilvános IP-címre. További információ: [Mi az az Azure Bastion?](../bastion/bastion-overview.md).

![Azure Bastion-architektúra](./media/networking-overview/architecture.png)

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>Virtuális hálózati NAT-átjáró
Virtual Network NAT (hálózati címfordítás) egyszerűbbé teszi a csak kimenő internetkapcsolatot a virtuális hálózatokhoz. Ha egy alhálózaton van konfigurálva, minden kimenő kapcsolat a megadott statikus nyilvános IP-címeket használja. A kimenő kapcsolat a virtuális gépekhez közvetlenül csatlakoztatott terheléselosztó vagy nyilvános IP-címek nélkül is lehetséges. További információ: [Mi a Virtual Network NAT Gateway?](../virtual-network/nat-overview.md) 

![Virtuális hálózati NAT-átjáró](./media/networking-overview/flow-map.png)

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a>Azure-partneri szolgáltatás
Az Azure-alapú szolgáltatások a Microsoft Cloud Services, például az Office 365, a Dynamics 365, a szolgáltatott szoftver (SaaS) szolgáltatásai, az Azure vagy bármely, a nyilvános interneten keresztül elérhető Microsoft-szolgáltatás számára növelik az ügyfelek kapcsolódását. További információ: [Mi az Azure-partneri szolgáltatás?](../peering-service/about.md).

## <a name="application-protection-services"></a><a name="protect"></a>Alkalmazás-védelmi szolgáltatások

Ez a szakasz az Azure-beli hálózati szolgáltatásokat ismerteti, amelyek segítenek a hálózati erőforrások védelmében – az alkalmazások védelmét az Azure-Private link, a DDoS Protection, a tűzfal, a hálózati biztonsági csoportok, a webalkalmazási tűzfal és a Virtual Network végpontok használatával.

|Szolgáltatás|Miért érdemes használni?|Forgatókönyv|
|---|---|---|
|[DDoS-védelem](#ddosprotection) |Magas rendelkezésre állás az alkalmazások számára a felesleges IP-forgalom elleni védelem ellenében|[Azure DDoS Protection kezelése](../virtual-network/manage-ddos-protection.md)|
|[Webalkalmazási tűzfal](#waf)|<p>Az [Azure WAF és a Application Gateway](../web-application-firewall/ag/ag-overview.md) helyi védelmet biztosít a nyilvános és privát címtartománybeli entitásoknak</p><p>A [bejárati ajtóval rendelkező Azure-WAF](../web-application-firewall/afds/afds-overview.md) a hálózat peremén a nyilvános végpontok számára biztosít védelmet.</p>|<p>[A robot védelmi szabályainak konfigurálása](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[Egyéni válaszkód konfigurálása](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[IP-korlátozási szabályok konfigurálása](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[A díjszabási korlát konfigurálása szabály](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Azure Firewall](#firewall)|Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi. Egy teljes mértékben állapotalapú tűzfalszolgáltatás, beépített magas rendelkezésre állással és korlátlan felhőméretezhetőséggel.|<p>[Azure Firewall üzembe helyezése vnet](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[-Azure Firewall üzembe helyezése hibrid hálózaton](../firewall/tutorial-hybrid-ps.md)</p> <p>[Bejövő forgalom szűrése Azure Firewall DNAT](../firewall/tutorial-firewall-dnat.md)</p>|
|[Network security groups (Hálózati biztonsági csoportok)](#nsg)|Teljes részletességű elosztott végpont-vezérlés a virtuális gépen/alhálózatban az összes hálózati forgalom forgalmához|[Hálózati forgalom szűrése hálózati biztonsági csoportokkal](../virtual-network/tutorial-filter-network-traffic.md)|
|[Virtuális hálózati szolgáltatásvégpontok](#serviceendpoints)|Lehetővé teszi egyes Azure-szolgáltatási erőforrások hálózati hozzáférésének korlátozását egy virtuális hálózati alhálózatra.|[PaaS-erőforrásokhoz való hálózati hozzáférés korlátozása](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
[Privát kapcsolat](#privatelink)|Lehetővé teszi az Azure Pásti-szolgáltatások (például az Azure Storage és a SQL Database) és az Azure által üzemeltetett felhasználói/partneri szolgáltatások elérését a virtuális hálózat privát végpontján keresztül.|<p>[Privát végpont létrehozása](../private-link/create-private-endpoint-portal.md)</p><p>[Privát kapcsolati szolgáltatás létrehozása](../private-link/create-private-link-service-portal.md)</p>|
|||
### <a name="ddos-protection"></a><a name="ddosprotection"></a>DDoS Protection 
A [Azure DDoS Protection](../virtual-network/manage-ddos-protection.md) a legkifinomultabb DDoS-fenyegetések elleni védelmet nyújt. A szolgáltatás továbbfejlesztett DDoS-elhárítási képességeket biztosít az alkalmazáshoz és a virtuális hálózatokban üzembe helyezett erőforrásokhoz. Emellett a Azure DDoS Protectiont használó ügyfeleink hozzáférhetnek a DDoS gyors reagálás támogatásához, hogy aktív támadás közben is bekapcsolódjanak a DDoS-szakértőkbe.

![DDoS Protection](./media/networking-overview/ddos-protection.png)

### <a name="web-application-firewall"></a><a name="waf"></a>Webalkalmazási tűzfal

Az [Azure webalkalmazási tűzfal](../web-application-firewall/overview.md) (WAF) védelmet nyújt a webalkalmazásoknak a gyakori webes biztonsági rések és sebezhetőségek, például az SQL-injektálás és a helyek közötti parancsfájlok használatával. Az Azure WAF a felügyelt szabályok segítségével a OWASP 10 legfontosabb biztonsági résen kívülről is biztosít védelmet a box-ban. Emellett az ügyfelek egyéni szabályokat is megadhatnak, amelyek az ügyfél által felügyelt szabályok a forrás IP-címtartomány alapján további védelmet biztosítanak, és olyan attribútumokat igényelnek, mint a fejlécek, a cookie-k, az űrlap adatmezői vagy a lekérdezési karakterlánc

Az ügyfelek dönthetnek úgy, hogy üzembe helyezik az [Azure WAF-t Application Gateway,](../application-gateway/waf-overview.md) amely regionális védelmet biztosít a nyilvános és privát címtartomány entitásai számára. Az ügyfelek dönthetnek úgy is, hogy az [Azure WAF](../frontdoor/waf-overview.md) üzembe helyezését is lehetővé teszi, amely a hálózati peremhálózat védelmét biztosítja a nyilvános végpontok számára.

![Webalkalmazási tűzfal](./media/networking-overview/waf-overview.png)


### <a name="azure-firewall"></a><a name="firewall"></a>Azure Firewall
Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi. A Azure Firewall használatával központilag hozhat létre, kényszerítheti és naplózhatja az alkalmazás-és hálózati kapcsolati szabályzatokat az előfizetések és a virtuális hálózatok között. Az Azure Firewall statikus nyilvános IP-címet használ a virtuális hálózat erőforrásaihoz, így a külső tűzfalak azonosíthatják a virtuális hálózatból érkező forgalmat. 

A Azure Firewallról további információt a [Azure Firewall dokumentációjában](../firewall/overview.md)talál.

![Tűzfal áttekintése](./media/networking-overview/firewall-threat.png)

### <a name="network-security-groups"></a><a name="nsg"></a>Network security groups (Hálózati biztonsági csoportok)
Az Azure-beli virtuális hálózatokban az Azure-erőforrások bejövő és kimenő hálózati forgalmát hálózati biztonsági csoportokkal szűrheti. További információ: [biztonsági áttekintés](../virtual-network/security-overview.md).

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Szolgáltatásvégpontok
A virtuális hálózatok (VNet) szolgáltatásvégpontjai egy közvetlen kapcsolaton keresztül kiterjesztik a virtuális hálózat magáncímterét és a VNet identitását az Azure-szolgáltatásokra. A végpontok segítségével biztosíthatja, hogy kritikus fontosságú Azure-szolgáltatási erőforrásai csak a virtuális hálózatain legyenek elérhetőek. A VNet felől az Azure-szolgáltatás felé irányuló forgalom mindig a Microsoft Azure gerinchálózatán halad át. További információ: [Virtual Network szolgáltatás-végpontok](../virtual-network/virtual-network-service-endpoints-overview.md).

![Virtuális hálózati szolgáltatásvégpontok](./media/networking-overview/vnet-service-endpoints-overview.png)

### <a name="azure-private-link"></a><a name="privatelink"></a>Azure Private Link
Az [Azure Private link](../private-link/private-link-overview.md) lehetővé teszi az Azure Pásti-szolgáltatások (például az Azure Storage és a SQL Database) és az Azure által üzemeltetett felhasználói/partneri szolgáltatások elérését a virtuális hálózat privát végpontján keresztül.
A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinc hálózatán halad át. A szolgáltatás nyilvános internetre való kimutatása már nem szükséges. Létrehozhatja saját privát kapcsolati szolgáltatását a virtuális hálózatban, és továbbíthatja az ügyfeleknek.

![Privát végpont áttekintése](./media/networking-overview/private-endpoint.png)


## <a name="application-delivery-services"></a><a name="deliver"></a>Application Delivery Services

Ez a szakasz az Azure-ban elérhető hálózati szolgáltatásokat ismerteti, amelyek segítenek az alkalmazások – Content Delivery Network, az Azure bejárati szolgáltatás, a Traffic Manager, a Load Balancer és a Application Gateway szolgáltatás nyújtásában.

|Szolgáltatás|Miért érdemes használni?|Forgatókönyv|
|---|---|---|
|[Content Delivery Network](#cdn)|Nagy sávszélességű tartalmat biztosít a felhasználóknak. A CDNs tárolja a gyorsítótárazott tartalmakat a végfelhasználók számára közel álló (POP) helyen található peremhálózati kiszolgálókon a késés csökkentése érdekében|<p>[CDN hozzáadása egy webalkalmazáshoz](../cdn/cdn-add-to-web-app.md)</p> <p>[-A Storage-Blobok elérése egy Azure CDN egyéni tartománnyal HTTPS-kapcsolaton keresztül](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[Egyéni tartomány hozzáadása az Azure CDN-végponthoz](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[HTTPS konfigurálása egyéni Azure CDN-tartományon](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate)</p>|
|[Azure Front Door Service](#frontdoor)|Lehetővé teszi a webes forgalom globális útválasztásának meghatározását, kezelését és figyelését azáltal, hogy optimalizálja a legjobb teljesítményt és azonnali globális feladatátvételt a magas rendelkezésre állás érdekében.|<p>[Egyéni tartomány hozzáadása az Azure Front Door Service-hez](../frontdoor/front-door-custom-domain.md)</p> <p>[HTTPS konfigurálása egyéni Front Door-tartományon](../frontdoor/front-door-custom-domain-https.md)</p><p>[A Geo-szűrés webalkalmazási tűzfal házirendjének beállítása](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[Traffic Manager](#trafficmanager)|A DNS-alapú forgalmat a globális Azure-régiókban lévő szolgáltatásokra osztja, miközben magas rendelkezésre állást és válaszadást biztosít.|<p> [Forgalom irányítása az alacsony késés érdekében](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[Forgalom irányítása magas prioritású végpontra](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [Forgalom szabályozása súlyozott végpontokkal](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[Forgalom irányítása a végpont földrajzi helye alapján](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [Forgalom irányítása a felhasználó alhálózata alapján](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[Load Balancer](#loadbalancer)|Regionális terheléselosztást biztosít a rendelkezésre állási zónák és a virtuális hálózatok közötti útválasztási forgalom alapján. Belső terheléselosztást biztosít az erőforrások közötti, valamint a regionális alkalmazás létrehozásához szükséges adatforgalom között.|<p> [Virtuális gépek internetes forgalmának terheléselosztása](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[Terheléselosztási forgalom elosztása virtuális hálózaton belüli virtuális gépek között](../load-balancer/tutorial-load-balancer-standard-internal-portal.md)<p>[A forgalom továbbítása adott virtuális gépek megadott portjára](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [Terheléselosztás és kimenő szabályok konfigurálása](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[Application Gateway](#applicationgateway)|Az Azure Application Gateway egy webes forgalomra vonatkozó terheléselosztó, amellyel kezelheti a webalkalmazásai forgalmát.|<p>[A webes forgalom irányítása az Azure Application Gatewayjel](../application-gateway/quick-create-portal.md)</p><p>[Oktatóanyag: Application Gateway konfigurálása TLS-megszakítással a Azure Portal használatával](../application-gateway/create-ssl-portal.md)</p><p>[Alkalmazásátjáró létrehozása URL-alapú átirányítással](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="content-delivery-network"></a><a name="cdn"></a>Content Delivery Network
Az Azure tartalomkézbesítési hálózat CDN a tartalmakat világszerte, stratégiai alapon elhelyezett fizikai csomópontokon gyorsítótárazva globális megoldást kínál a fejlesztők számára a tartalmak nagy sávszélességű gyors kézbesítéséhez a felhasználók felé. További információ a Azure CDNről: [Azure Content Delivery Network](../cdn/cdn-overview.md)

![Azure CDN](./media/networking-overview/cdn-overview.png)

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Azure bejárati ajtó szolgáltatás
Az Azure Front Door Service segítségével meghatározhatja, felügyelheti és monitorozhatja a webes forgalmának globális forgalomirányítását, optimalizálhatja annak teljesítményét, és azonnali globális feladatátvétellel magas rendelkezésre állást biztosíthat. A Front Door használatával a globális (több régióban található) fogyasztói és nagyvállalati alkalmazásait olyan robusztus, nagy teljesítményű, személyre szabott és modern alkalmazásokká, API-kká és tartalmakká alakíthatja, amelyek az Azure-on keresztül elérhetik a globális közönségüket. További információ: [Azure bejárati ajtó](../frontdoor/front-door-overview.md).


### <a name="traffic-manager"></a><a name="trafficmanager"></a>Traffic Manager

Az Azure Traffic Manager egy DNS-alapú forgalom-terheléselosztó, amely lehetővé teszi a szolgáltatásokhoz érkező forgalom optimális elosztását a globális Azure-régiókban, miközben magas rendelkezésre állást és válaszkészséget biztosít. Traffic Manager számos forgalom-útválasztási módszert biztosít a forgalom, például a prioritás, a súlyozott, a teljesítmény, a földrajzi, a többértékes vagy az alhálózat elosztására. További információ a forgalom-útválasztási módszerekről: [Traffic Manager útválasztási módszerek](../traffic-manager/traffic-manager-routing-methods.md).

A következő ábra a végpontok prioritáson alapuló útválasztását mutatja Traffic Manager:

![Azure Traffic Manager "prioritás" forgalom – útválasztási módszer](./media/networking-overview/priority.png)

További információ a Traffic Managerról: [Mi az az Azure Traffic Manager?](../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>Load Balancer
Az Azure Load Balancer nagy teljesítményű, kis késleltetésű 4. rétegbeli terheléselosztást biztosít az összes UDP-és TCP-protokollhoz. Felügyeli a bejövő és kimenő kapcsolatokat. A nyilvános és a belső terheléselosztású végpontokat is konfigurálhatja. Meghatározhatja azokat a szabályokat, amelyekkel leképezheti a bejövő kapcsolatokat a háttér-készletek célhelyei számára a TCP és a HTTP Health-Probing beállításainak használatával a szolgáltatás rendelkezésre állásának kezeléséhez. Ha többet szeretne megtudni a Load Balancerről, olvassa el a [Load Balancer áttekintő](../load-balancer/load-balancer-overview.md) cikket.

Az alábbi képen egy internetre irányuló többrétegű alkalmazás jelenik meg, amely külső és belső terheléselosztót is használ:

![Azure Load Balancer példa](./media/networking-overview/load-balancer.png)


### <a name="application-gateway"></a><a name="applicationgateway"></a>Application Gateway
Az Azure Application Gateway egy webes forgalomra vonatkozó terheléselosztó, amellyel kezelheti a webalkalmazásai forgalmát. Ez egy Application Delivery Controller (ADC) szolgáltatás, amely különböző 7. rétegbeli terheléselosztási funkciókat kínál alkalmazásai számára. További információ: [Mi az az Azure Application Gateway?](../application-gateway/overview.md)

Az alábbi ábra az URL-alapú útválasztást Application Gatewaysal mutatja.

![Application Gateway példa](./media/networking-overview/figure1-720.png)

## <a name="network-monitoring-services"></a><a name="monitor"></a>Hálózati figyelési szolgáltatások
Ez a szakasz az Azure hálózati szolgáltatásait ismerteti, amelyek segítenek a hálózati erőforrások figyelésében – Network Watcher, ExpressRoute-figyelő, Azure Monitor és Virtual Network KOPPINTanak.

|Szolgáltatás|Miért érdemes használni?|Forgatókönyv|
|---|---|---|
|[Network Watcher](#networkwatcher)|Segíti a kapcsolódási problémák figyelését és hibaelhárítását, segít a VPN-, NSG-és útválasztási problémák diagnosztizálásában, a csomagok rögzítésében a virtuális gépen, automatizálja a diagnosztikai eszközök aktiválását Azure Functions és Logic Apps használatával|<p>[A virtuális gép forgalomszűrési problémáinak diagnosztizálása](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[A virtuális gép útválasztási problémáinak diagnosztizálása](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[Virtuális gépek közötti kommunikáció figyelése](../network-watcher/connection-monitor.md)</p><p>[Hálózatok közötti kommunikációs problémák diagnosztizálása](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[Virtuális gép be- és kimenő forgalmának naplózása](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[ExpressRoute-figyelő](#expressroutemonitor)|Valós idejű monitorozást biztosít a hálózati teljesítmény, a rendelkezésre állás és a kihasználtság terén, segíti a hálózati topológia automatikus felderítését, gyorsabbá teszi a hibák elkülönítését, észleli az átmeneti hálózati problémákat, segíti a korábbi hálózati teljesítménnyel kapcsolatos jellemzők elemzését, támogatja a többszörös előfizetést.|<p>[Network Performance Monitor for ExpressRoute konfigurálása](../expressroute/how-to-npm.md)</p><p>[Az ExpressRoute monitorozása, mérőszámai és riasztásai](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure Monitor](#azuremonitor)|Segít megérteni, hogy az alkalmazások hogyan működnek, és proaktív módon azonosítják azokat a problémákat, amelyek hatással vannak a rájuk és az azoktól függő erőforrásokra.|<p>[Metrikák és riasztások Traffic Manager](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[Azure monitor diagnosztika a standard Load Balancer](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[Az Azure Firewall naplóinak és metrikáinak monitorozása](../firewall/tutorial-diagnostics.md)</p><p>[Az Azure webalkalmazási tűzfalának monitorozása és naplózása](../frontdoor/waf-front-door-monitor.md)</p>|
|[Virtual Network KOPPINTson](#vnettap)|Folyamatos adatfolyamot biztosít a virtuális gépek hálózati forgalmáról a csomagok gyűjtője számára, lehetővé teszi a hálózati és az alkalmazások teljesítmény-felügyeleti megoldásait és biztonsági elemzési eszközeit|[VNet-KOPPINTó erőforrás létrehozása](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="network-watcher"></a><a name="networkwatcher"></a>Network Watcher
Az Azure Network Watcher eszközeivel monitorozhatja és diagnosztizálhatja az erőforrásokat egy Azure virtuális hálózaton belül, illetve megtekintheti azok metrikáit, és engedélyezheti vagy letiltja azok naplóit. További információ: [Mi az Network Watcher?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>ExpressRoute-figyelő
A ExpressRoute-áramköri metrikák, az erőforrás-naplók és a riasztások megtekintésével kapcsolatban lásd: [ExpressRoute-figyelés,-metrikák és-riasztások](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
Az Azure Monitor az alkalmazások rendelkezésre állását és teljesítményét növeli a lehető legnagyobbra egy olyan átfogó megoldás biztosításával, amely a felhőből és a helyszíni környezetből származó telemetriaadatokat gyűjt, elemez, valamint műveleteket hajt végre az adatok alapján. Ez a szolgáltatás segít megérteni azt, hogy az alkalmazásai hogyan teljesítenek, valamint proaktív módon azonosítja a működésüket befolyásoló problémákat és azokat az erőforrásokat, amelyektől függenek. További információ: [Azure monitor Overview (áttekintés](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json)).
### <a name="virtual-network-tap"></a><a name="vnettap"></a>Virtual Network KOPPINTson
Az Azure Virtual Network (terminál-hozzáférési pont) funkció lehetővé teszi a virtuális gép hálózati forgalmának folyamatos továbbítását egy hálózati csomag gyűjtője vagy analitikai eszköze számára. A gyűjtő vagy az elemzési eszközt egy [hálózati virtuális berendezési](https://azure.microsoft.com/solutions/network-appliances/) partner kapja meg. 

Az alábbi képen látható, hogyan működik a virtuális hálózati KOPPINTÁS. 

![A virtuális hálózat KOPPINTÁSának működése](./media/networking-overview/virtual-network-tap-architecture.png)

További információ: [Mi az Virtual Network koppint](../virtual-network/virtual-network-tap-overview.md).

## <a name="next-steps"></a>További lépések

- Hozza létre az első VNet, és kapcsolódjon néhány virtuális géphez a [létrehozásához az első virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) című cikkben ismertetett lépések végrehajtásával.
- A számítógép csatlakoztatása egy VNet a [pont – hely kapcsolat konfigurálása című cikkben](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)ismertetett lépések végrehajtásával.
- Az internetre irányuló [terheléselosztó létrehozása](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) című cikkben ismertetett lépések végrehajtásával terheléselosztást végezhet a nyilvános kiszolgálók internetes forgalmával.
 
 
   
