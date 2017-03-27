---
title: "A VPN Gateway áttekintése: Létesítmények közötti VPN-kapcsolatok létrehozása Azure-alapú virtuális hálózatokhoz | Microsoft Docs"
description: "A VPN Gateway áttekintése az Azure-alapú virtuális hálózatokhoz az interneten keresztül VPN-kapcsolattal történő csatlakozás módjait ismerteti. Tartalmazza az alapvető kapcsolatkonfigurációk ábráit is."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 2358dd5a-cd76-42c3-baf3-2f35aadc64c8
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 3be72ac862650d2381a56c4ab682e41a8aaa8a27
ms.lasthandoff: 03/17/2017


---
# <a name="about-vpn-gateway"></a>Információk a VPN Gateway-ről
Ha hálózati adatforgalmat szeretne továbbítani az Azure-alapú virtuális hálózatok és a helyszíni webhelyek között, létre kell hoznia egy virtuális hálózati átjárót a virtuális hálózathoz. A VPN-átjáró a virtuális hálózati átjárók egy olyan típusa, amely titkosított adatforgalmat továbbít nyilvános kapcsolaton keresztül. A VPN-átjárók használatával továbbá adatforgalmat továbbíthat az Azure-alapú virtuális hálózatok között a Microsoft hálózatán keresztül.

Két virtuális hálózati átjárótípus létezik: az „ExpressRoute” és a „VPN”. Virtuális hálózati átjáró létrehozásakor megadja a létrehozni kívánt átjárótípust. A VPN-átjáró olyan virtuális hálózati átjáró, amely a „VPN” típusú átjárótípust használja. 

Mindegyik virtuális hálózat kettő virtuális hálózati átjáróval rendelkezhet, de típusonként csak eggyel. A kiválasztott beállításoktól függően több kapcsolatot is létrehozhat egyetlen VPN-átjáróhoz. Erre egy példa a Többhelyes csatlakozás konfiguráció. Amikor többhelyes csatlakozást hoz létre egyetlen VPN-átjáróhoz, az összes VPN-alagút, így a pont–hely VPN-ek is az átjáró számára elérhető sávszélességen osztozkodnak.

## <a name="configuring-a-vpn-gateway"></a>VPN Gateway átjáró konfigurálása
A VPN-átjárós kapcsolatok több erőforrást használnak, amelyek speciális beállításokkal konfigurálhatók. Az erőforrások többsége külön konfigurálható, néhány esetben azonban egy bizonyos sorrendben kell őket konfigurálni.

###<a name="settings"></a>Beállítások
Az egyes erőforrások megfelelő beállítása kritikus fontosságú a sikeres kapcsolat létrehozásához. További információ a VPN Gateway önálló erőforrásairól és beállításairól: [About VPN Gateway settings](vpn-gateway-about-vpn-gateway-settings.md) (Információk a VPN Gateway beállításairól). Az átjáró-, a VPN- és a kapcsolattípusokról, az átjáró-alhálózatokról, a helyi hálózati átjárókról, valamint a különböző erőforrások beállításairól további hasznos információkat olvashat.

###<a name="deployment-tools"></a>Üzembe helyezési eszközök
Az erőforrások létrehozásának és konfigurálásának megkezdéséhez használjon egy konfigurációs eszközt, például az Azure Portalt. Később átválthat egy másik eszközre, például a PowerShellre, a további erőforrások konfigurálásához, vagy adott esetekben a létező erőforrások módosításához. Jelenleg nem lehet a minden erőforrást és erőforrás-beállítást az Azure Portalon konfigurálni. Az egyes kapcsolati topológiákhoz tartozó cikkekben lévő utasítások egyértelműsítik, hogy mikor van szükség egy speciális konfigurációs eszközre. 

###<a name="deployment-model"></a>Üzemi modell
A VPN Gateway konfigurálásakor a követendő lépések a virtuális hálózat létrehozásához használt üzemi modelltől függenek. Ha például a virtuális hálózatot a klasszikus üzembe helyezési modellel hozta létre, a VPN-átjáró beállításinak létrehozására és konfigurálására a klasszikus üzembe helyezési modell irányelvei és utasításai vonatkoznak. További információk az üzembe helyezési modellekről: [Az erőforrás-kezelői és a klasszikus üzembe helyezési modellek ismertetése](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="diagrams"></a>Kapcsolati topológia-diagramok
Fontos szem előtt tartani, hogy a VPN-átjárós kapcsolatokhoz különböző konfigurációk érhetők el. Önnek kell eldöntenie, melyik konfiguráció felel meg a legjobban az igényeinek. Az alábbi szakaszokban a következő VPN-átjárós kapcsolatokról találhat információt és topológia-diagramokat: Az alábbi szakaszokban található táblázatok a következőket tartalmazzák:

* Elérhető üzemi modell
* Elérhető konfigurációs eszközök
* Hivatkozások, amelyek közvetlenül egy cikkre mutatnak, ha van ilyen

Az ábrák és a leírások segítségével kiválaszthatja az igényeinek megfelelő kapcsolati topológiát. Az ábrák bemutatják a fő alapvető topológiákat, az ábrákat útmutatásként használva azonban lehetséges összetettebb konfigurációk létrehozása is.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Helyek közötti és többhelyes (IPsec/IKE VPN-alagút)
### <a name="S2S"></a>Helyek közötti kapcsolat
A helyek közötti (Site-to-Site, S2S) VPN Gateway-kapcsolat egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-alagúton keresztüli kapcsolat. Ehhez a típusú kapcsolathoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy nyilvános IP-cím, és nem NAT mögött helyezkedik el. A helyek közötti kapcsolatok létesítmények közötti és hibrid konfigurációk esetében is alkalmazhatók.   

![Azure VPN Gateway helyek közti kapcsolat – példa](./media/vpn-gateway-about-vpngateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="Multi"></a>Többhelyes kapcsolat
Ez a típusú kapcsolat a helyek közötti kapcsolat egy változata. A virtuális hálózati átjáróról több VPN-kapcsolatot hoz létre, amelyek általában több helyszíni helyhez csatlakoznak. Ha több kapcsolatot használ, RouteBased (útvonalalapú) VPN-típust kell alkalmaznia (klasszikus virtuális hálózatok használatakor ezt dinamikus átjárónak nevezik). Mivel minden virtuális hálózat csak egy VPN-átjáróval rendelkezhet, az átjárón keresztüli összes kapcsolat osztozik a rendelkezésre álló sávszélességen. Ezt gyakran „többhelyes” kapcsolatnak nevezik.

![Azure VPN Gateway többhelyes kapcsolat – példa](./media/vpn-gateway-about-vpngateways/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Üzembe helyezési modellek és módszerek a helyek közötti és többhelyes kapcsolatokhoz
[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="P2S"></a>Pont–hely típusú (SSTP-alapú VPN)
A pont–hely (P2S) VPN-átjátókonfiguráció lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat egy SSTP (Secure Socket Tunneling Protocol) használatával működő VPN-kapcsolat. A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet a működéshez. A VPN-kapcsolat létesítéséhez a kapcsolatot az ügyfélszámítógépről kell elindítani. Ez a megoldás akkor hasznos, ha távoli helyről szeretne csatlakozni a virtuális hálózatához, például otthonról vagy konferenciáról, és akkor is, ha csak néhány, a virtuális hálózathoz csatlakoztatandó ügyfele van. A pont–hely (P2S) kapcsolatok helyek közötti (S2S) kapcsolatokkal együtt is használhatók ugyanazon a VPN-átjárón keresztül, ha a kapcsolatok minden konfigurációs követelménye kompatibilis egymással.

![Azure VPN Gateway pont–hely kapcsolat – példa](./media/vpn-gateway-about-vpngateways/vpngateway-point-to-site-connection-diagram.png)

### <a name="deployment-models-and-methods-for-point-to-site"></a>Üzembe helyezési modellek és módszerek pont–hely kapcsolatokhoz
[!INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="V2V"></a>Virtuális hálózatok közötti kapcsolatok (IPsec/IKE VPN-alagút)
Egy virtuális hálózat egy másikkal való összekapcsolása (a virtuális hálózatok közötti kapcsolat) nagyon hasonlít egy virtuális hálózat egy helyszíni helyhez való csatlakoztatásához. Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE használatával való kialakításához. A virtuális hálózatok közötti kommunikációt kombinálhatja többhelyes kapcsolati konfigurációkkal is. Így létrehozhat olyan hálózati topológiákat, amelyek a létesítmények közötti kapcsolatokat a virtuális hálózatok közötti kapcsolatokkal kombinálják.

Az összekapcsolt virtuális hálózatok lehetnek:

* azonos vagy eltérő régiókban
* azonos vagy eltérő előfizetésekben 
* azonos vagy eltérő üzemi modellekben

![Azure VPN Gateway virtuális hálózatok közötti kapcsolat – példa](./media/vpn-gateway-about-vpngateways/vpngateway-vnet-to-vnet-connection-diagram.png)

###<a name="connections-between-deployment-models"></a>Üzemi modellek közötti kapcsolat
Az Azure jelenleg kétféle üzemi modellt kínál: a klasszikust és a Resource Managert. Ha már egy ideje használja az Azure-t, valószínűleg futtat Azure virtuális gépeket és példányszerepköröket egy klasszikus virtuális hálózaton. Lehetséges, hogy az újabb virtuális gépek és szerepkörpéldányok egy Resource Managerben létrehozott virtuális hálózatban futnak. Létrehozhat egy kapcsolatot a virtuális hálózatok között, így lehetővé teheti, hogy az egyik virtuális hálózatban lévő erőforrások közvetlenül kommunikáljanak a másikban lévő erőforrásokkal.

###<a name="vnet-peering"></a>Virtuális hálózatok közötti társviszony
A kapcsolat létrehozására használhat virtuális hálózatok közötti társviszonyt, ha a virtuális hálózat megfelel bizonyos követelményeknek. A virtuális hálózatok közötti társviszony nem használ virtuális hálózati átjárót. További információ: [Társviszony létesítése virtuális hálózatok között](../virtual-network/virtual-network-peering-overview.md).

###<a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Üzembe helyezési modellek és módszerek virtuális hálózatok közötti kapcsolatokhoz
[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="ExpressRoute"></a>ExpressRoute (dedikált privát kapcsolat)
A Microsoft Azure ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft-felhőbe egy dedikált privát kapcsolaton keresztül, amelyet egy kapcsolatszolgáltató biztosít. Az ExpressRoute használatával kapcsolatokat létesíthet a Microsoft-felhőszolgáltatásokkal, például a Microsoft Azure-ral, az Office 365-tel és a CRM Online-nal. A kapcsolatok lehetnek: bármely elemek közötti (IP VPN) hálózat, pontok közötti Ethernet-hálózat vagy egy virtuális keresztkapcsolat egy kapcsolatszolgáltatón keresztül egy közös elhelyezési létesítményben.

Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. Így az ExpressRoute-kapcsolatok a tipikus internetes kapcsolatoknál megbízhatóbbak, gyorsabbak, gyorsabb a válaszidejük, és biztonságosabbak.

Az ExpressRoute-kapcsolatok nem használnak VPN-átjárót, azonban használnak virtuális hálózati átjárót a szükséges konfiguráció részeként. Az ExpressRoute-kapcsolatokban a virtuális hálózati átjáró a „VPN” helyett az „ExpressRoute” átjárótípussal van konfigurálva. További információ az ExpressRoute-tal kapcsolatban: [Az ExpressRoute technikai áttekintése](../expressroute/expressroute-introduction.md).

## <a name="coexisting"></a>Egyidejű helyek közötti és ExpressRoute-kapcsolatok
Az ExpressRoute egy közvetlen, dedikált kapcsolat a Microsoft-szolgáltatásokkal (köztük az Azure-ral) WAN-on, és nem a nyilvános interneten keresztül. A helyek közötti VPN-adatforgalom titkosítva, a nyilvános interneten halad át. A helyek közötti VPN- és az ExpressRoute-kapcsolatok azonos virtuális hálózathoz való konfigurálásának lehetősége több előnnyel jár.

A helyek közötti VPN-t konfigurálhatja biztonságos feladatátvételi útvonalként az ExpressRoute számára, vagy használhat helyek közötti VPN-eket is a hálózat részét nem képező, az ExpressRoute-on keresztül kapcsolódó helyekhez való csatlakozáshoz. Ebben a konfigurációban két virtuális hálózati átjáróra van szükség ugyanahhoz a virtuális hálózathoz, amelyek közül az egyik a „Vpn” átjárótípust, a másik pedig az „ExpressRoute” átjárótípust használja.

![ExpressRoute és VPN egyidejű kapcsolatok – példa](./media/vpn-gateway-about-vpngateways/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute"></a>Üzembe helyezési modellek és módszerek a helyek közti (S2S) és ExpressRoute-kapcsolatokhoz
[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="pricing"></a>Díjszabás
[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

## <a name="gateway-skus"></a>Gateway termékváltozatok
[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

További információ a VPN Gateway átjáró-termékváltozatokról: [Gateway SKUs](vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Átjáró-termékváltozatok).

### <a name="estimated-aggregate-throughput-by-sku"></a>A termékváltozat becsült összesített átviteli sebessége
[!INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="next-steps"></a>Következő lépések
- A VPN-átjáró konfigurációjának megtervezése Lásd: [A VPN-átjáró tervezése és kialakítása](vpn-gateway-plan-design.md).
- További információkért tekintse meg [A VPN Gatewayjel kapcsolatos gyakori kérdések](vpn-gateway-vpn-faq.md) című szakaszt.
- Tekintse meg az [Előfizetés- és szolgáltatáskorlátok](../azure-subscription-service-limits.md#networking-limits) című szakaszt.


