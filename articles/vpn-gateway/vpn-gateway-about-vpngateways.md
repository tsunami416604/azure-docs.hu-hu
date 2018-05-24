---
title: Azure VPN Gateway | Microsoft Docs
description: Ebben a cikkben megismerkedhet a VPN-átjárókkal, és megtudhatja, hogyan csatlakozhat VPN-átjáró segítségével az Azure virtuális hálózatokhoz. Beleértve az IPsec/IKE helyek közötti, létesítmények közötti és VNet–VNet megoldásokat, valamint a pont–hely típusú VPN-t.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.assetid: 2358dd5a-cd76-42c3-baf3-2f35aadc64c8
ms.service: vpn-gateway
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2018
ms.author: cherylmc
ms.openlocfilehash: 01052d8058d1b1e3a03f584ad66da2ad79f4c425
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-vpn-gateway"></a>Mi az a VPN-átjáró?

A VPN-átjáró a virtuális hálózati átjárók egy olyan típusa, amely titkosított adatforgalmat továbbít a nyilvános interneten keresztül egy Azure virtuális hálózat és egy helyszíni hely között. A VPN-átjáróval továbbá titkosított adatforgalmat továbbíthat az Azure-alapú virtuális hálózatok között a Microsoft hálózatán keresztül. Minden egyes virtuális hálózat kizárólag egy VPN-átjáróval rendelkezhet. Egy VPN-átjáróhoz azonban létrehozhat több kapcsolatot is. Amikor többhelyes csatlakozást hoz létre egyetlen VPN-átjáróhoz, az összes VPN-alagút az elérhető sávszélességen osztozkodik.

## <a name="whatis"></a>Mi az a virtuális hálózati átjáró?

A virtuális hálózati átjáró két vagy több virtuális gépből áll, amelyek egy *átjáróalhálózat* nevű, Ön által létrehozott speciális alhálózatra vannak telepítve. Az átjáróalhálózatban található virtuális gépek a virtuális hálózati átjáró létrehozásakor jönnek létre. A virtuális hálózati átjáró virtuális gépei úgy vannak konfigurálva, hogy az átjáróra jellemző táblákat és átjárószolgáltatásokat tartalmazzanak. A virtuális hálózati átjáró virtuális gépeit nem konfigurálhatja közvetlenül, és nem szabad további erőforrásokat telepíteni az átjáróalhálózatra.

Egy virtuális hálózati átjáró létrehozása akár 45 percet is igénybe vehet. Virtuális hálózati átjáró létrehozásakor a rendszer telepíti az átjáró virtuális gépeit az átjáróalhálózatra, és konfigurálja őket az Ön által megadott beállításokkal. Az egyik konfigurált beállítás az átjáró típusa. A vpn átjárótípus megadja, hogy a létrehozott virtuális hálózati átjáró típusa VPN-átjáró. Miután létrehozott egy VPN-átjárót, létrehozhat egy IPsec/IKE VPN-alagútkapcsolatot az adott VPN-átjáró és egy másik VPN-átjáró (VNet–VNet) között, vagy létrehozhat egy létesítmények közötti IPsec/IKE VPN-alagútkapcsolatot a VPN-átjáró és egy helyszíni VPN-eszköz között (helyek között). Létrehozhat egy pont–hely típusú VPN-kapcsolatot is (IKEv2- vagy SSTP-alapú VPN), amellyel távoli helyről csatlakozhat virtuális hálózatához, például egy konferenciáról vagy otthonról.

## <a name="configuring"></a>VPN Gateway-átjáró konfigurálása

A VPN-átjárós kapcsolatok több erőforrást használnak, amelyek speciális beállításokkal konfigurálhatók. Az erőforrások többsége külön konfigurálható, néhány erőforrást azonban egy bizonyos sorrendben kell konfigurálni.

### <a name="settings"></a>Beállítások

Az egyes erőforrások megfelelő beállítása kritikus fontosságú a sikeres kapcsolat létrehozásához. További információ a VPN Gateway önálló erőforrásairól és beállításairól: [About VPN Gateway settings](vpn-gateway-about-vpn-gateway-settings.md) (Információk a VPN Gateway beállításairól). A cikk az átjáró-, a VPN- és a kapcsolattípusokkal, az átjáró-alhálózatokkal, a helyi hálózati átjárókkal, az átjáró-termékváltozatokkal, valamint a különböző erőforrások beállításaival kapcsolatos további hasznos információkat tartalmaz.

### <a name="tools"></a>Üzembehelyezési eszközök

Az erőforrások létrehozásának és konfigurálásának megkezdéséhez használjon egy konfigurációs eszközt, például az Azure Portalt. A további erőforrások konfigurálásához, vagy adott esetekben a létező erőforrások módosításához később átválthat egy másik eszközre, például a PowerShellre. Jelenleg nem lehet a minden erőforrást és erőforrás-beállítást az Azure Portalon konfigurálni. Az egyes kapcsolati topológiákhoz tartozó cikkekben lévő utasítások egyértelműsítik, hogy mikor van szükség egy speciális konfigurációs eszközre. 

### <a name="models"></a>Üzemi modell

Jelenleg két üzemi modell van az Azure-ban. A VPN Gateway konfigurálásakor a követendő lépések a virtuális hálózat létrehozásához használt üzemi modelltől függenek. Ha például a virtuális hálózatot a klasszikus üzembe helyezési modellel hozta létre, a VPN-átjáró beállításinak létrehozására és konfigurálására a klasszikus üzembe helyezési modell irányelvei és utasításai vonatkoznak. További információk az üzembe helyezési modellekről: [Az erőforrás-kezelői és a klasszikus üzembe helyezési modellek ismertetése](../azure-resource-manager/resource-manager-deployment-model.md).

### <a name="planningtable"></a>Tervezési táblázat

Az alábbi táblázat segíthet eldönteni, melyik az Ön megoldásához legmegfelelőbb kapcsolat.

[!INCLUDE [cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

## <a name="gwsku"></a>Átjáró-termékváltozatok

Egy virtuális hálózati átjáró létrehozásakor meg kell adnia a használni kívánt termékváltozatot. Válassza ki a számítási feladatok, az átviteli sebesség, a funkciók és a szolgáltatói szerződés igényeinek megfelelő termékváltozatot. További információ az átjáró-termékváltozatokról: [Átjáró-termékváltozatok](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

### <a name="benchmark"></a>Átjáró-termékváltozatok alagút, kapcsolat és átviteli sebesség szerint

[!INCLUDE [Aggregated throughput by SKU](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="diagrams"></a>Kapcsolati topológia-diagramok

Fontos szem előtt tartani, hogy a VPN-átjárós kapcsolatokhoz különböző konfigurációk érhetők el. Önnek kell eldöntenie, melyik konfiguráció felel meg a legjobban az igényeinek. Az alábbi szakaszokban a következő VPN-átjárós kapcsolatokról találhat információt és topológia-diagramokat: Az alábbi szakaszokban található táblázatok a következőket tartalmazzák:

* Elérhető üzemi modell
* Elérhető konfigurációs eszközök
* Hivatkozások, amelyek közvetlenül egy cikkre mutatnak, ha van ilyen

Az ábrák és a leírások segítségével kiválaszthatja az igényeinek megfelelő kapcsolati topológiát. Az ábrák bemutatják a fő alapvető topológiákat, az ábrákat útmutatásként használva azonban lehetséges összetettebb konfigurációk létrehozása is.

## <a name="s2smulti"></a>Helyek közötti és többhelyes (IPsec/IKE VPN-alagút)

### <a name="S2S"></a>Helyek közötti kapcsolat

A helyek közötti (Site-to-Site, S2S) VPN Gateway-kapcsolat egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-alagúton keresztüli kapcsolat. A helyek közötti kapcsolatok létesítmények közötti és hibrid konfigurációk esetében is alkalmazhatók. A helyek közötti kapcsolatokhoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy nyilvános IP-cím, és nem NAT mögött helyezkedik el. A VPN-eszköz kiválasztásával kapcsolatos információkért lásd [a VPN Gateway gyakori kérdései közül a VPN-eszközökkel foglalkozókat](vpn-gateway-vpn-faq.md#s2s).

![Azure VPN Gateway helyek közti kapcsolat – példa](./media/vpn-gateway-about-vpngateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="Multi"></a>Többhelyes kapcsolat

Ez a típusú kapcsolat a helyek közötti kapcsolat egy változata. A virtuális hálózati átjáróról több VPN-kapcsolatot hoz létre, amelyek általában több helyszíni helyhez csatlakoznak. Ha több kapcsolatot használ, RouteBased (útvonalalapú) VPN-típust kell alkalmaznia (klasszikus virtuális hálózatok használatakor ezt dinamikus átjárónak nevezik). Mivel minden virtuális hálózat csak egy VPN-átjáróval rendelkezhet, az átjárón keresztüli összes kapcsolat osztozik a rendelkezésre álló sávszélességen. Ezt a kapcsolattípust gyakran „többhelyes” kapcsolatnak nevezik.

![Azure VPN Gateway többhelyes kapcsolat – példa](./media/vpn-gateway-about-vpngateways/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Üzembe helyezési modellek és módszerek a helyek közötti és többhelyes kapcsolatokhoz

[!INCLUDE [site-to-site and multi-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="P2S"></a>Pont–hely kapcsolat (IKEv2- vagy SSTP-alapú VPN)

A pont–hely (P2S) VPN-átjátókapcsolat lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat létesítéséhez a kapcsolatot az ügyfélszámítógépről kell elindítani. Ez a megoldás főleg távmunkások számára hasznos, akik egy távoli helyről szeretnének csatlakozni egy Azure virtuális hálózatokhoz, például otthonról vagy konferenciáról. A pont–hely VPN emellett akkor is hasznos megoldás lehet a helyek közötti VPN helyett, ha csak néhány ügyfelet szeretne egy virtuális hálózathoz csatlakoztatni.

A helyek közötti kapcsolatoktól eltérően a pont–hely kapcsolatok nem igényelnek helyszíni nyilvános IP-címet vagy VPN-eszközt a működéshez. A pont–hely (P2S) kapcsolatok helyek közötti (S2S) kapcsolatokkal együtt is használhatók ugyanazon a VPN-átjárón keresztül, ha a kapcsolatok minden konfigurációs követelménye kompatibilis egymással. További információk a pont–hely kapcsolatokról: [Információk a pont–hely VPN-ről](point-to-site-about.md).


![Azure VPN Gateway pont–hely kapcsolat – példa](./media/vpn-gateway-about-vpngateways/point-to-site.png)

### <a name="deployment-models-and-methods-for-p2s"></a>Üzemi modellek és módszerek a pont–hely (P2S) kapcsolatokhoz

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="V2V"></a>Virtuális hálózatok közötti kapcsolatok (IPsec/IKE VPN-alagút)

Egy virtuális hálózat egy másikkal való összekapcsolása (a virtuális hálózatok közötti kapcsolat) nagyon hasonlít egy virtuális hálózat egy helyszíni helyhez való csatlakoztatásához. Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE használatával való kialakításához. A virtuális hálózatok közötti kommunikációt kombinálhatja többhelyes kapcsolati konfigurációkkal is. Így létrehozhat olyan hálózati topológiákat, amelyek a létesítmények közötti kapcsolatokat a virtuális hálózatok közötti kapcsolatokkal kombinálják.

Az összekapcsolt virtuális hálózatok lehetnek:

* azonos vagy eltérő régiókban
* azonos vagy eltérő előfizetésekben 
* azonos vagy eltérő üzemi modellekben

![Azure VPN Gateway virtuális hálózatok közötti kapcsolat – példa](./media/vpn-gateway-about-vpngateways/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>Üzemi modellek közötti kapcsolat

Az Azure jelenleg kétféle üzemi modellt kínál: a klasszikust és a Resource Managert. Ha már egy ideje használja az Azure-t, valószínűleg futtat Azure virtuális gépeket és példányszerepköröket egy klasszikus virtuális hálózaton. Lehetséges, hogy az újabb virtuális gépek és szerepkörpéldányok egy Resource Managerben létrehozott virtuális hálózatban futnak. Létrehozhat egy kapcsolatot a virtuális hálózatok között, így lehetővé teheti, hogy az egyik virtuális hálózatban lévő erőforrások közvetlenül kommunikáljanak a másikban lévő erőforrásokkal.

### <a name="vnet-peering"></a>Társviszony létesítése virtuális hálózatok között

A kapcsolat létrehozására használhat virtuális hálózatok közötti társviszonyt, ha a virtuális hálózat megfelel bizonyos követelményeknek. A virtuális hálózatok közötti társviszony nem használ virtuális hálózati átjárót. További információ: [Társviszony létesítése virtuális hálózatok között](../virtual-network/virtual-network-peering-overview.md).

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Üzembe helyezési modellek és módszerek virtuális hálózatok közötti kapcsolatokhoz

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="ExpressRoute"></a>ExpressRoute (privát kapcsolat)

Az ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft Cloudba egy privát kapcsolaton keresztül, amelyet egy kapcsolatszolgáltató biztosít. Az ExpressRoute használatával kapcsolatokat létesíthet a Microsoft-felhőszolgáltatásokkal, például a Microsoft Azure-ral, az Office 365-tel és a CRM Online-nal. A kapcsolatok lehetnek: bármely elemek közötti (IP VPN) hálózat, pontok közötti Ethernet-hálózat vagy egy virtuális keresztkapcsolat egy kapcsolatszolgáltatón keresztül egy közös elhelyezési létesítményben.

Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. Így az ExpressRoute-kapcsolatok a tipikus internetes kapcsolatoknál megbízhatóbbak, gyorsabbak, gyorsabb a válaszidejük, és biztonságosabbak.

Az ExpressRoute-kapcsolatok virtuális hálózati átjárót használnak a szükséges konfiguráció részeként. Az ExpressRoute-kapcsolatokban a virtuális hálózati átjáró a „VPN” helyett az „ExpressRoute” átjárótípussal van konfigurálva. Bár az ExpressRoute-kapcsolatcsoporton keresztül haladó forgalom alapértelmezés szerint nincs titkosítva, lehetséges olyan megoldást létrehozni, amely lehetővé teszi a titkosított forgalom küldését ExpressRoute-kapcsolatcsoporton. További információ az ExpressRoute-tal kapcsolatban: [Az ExpressRoute technikai áttekintése](../expressroute/expressroute-introduction.md).

## <a name="coexisting"></a>Egyidejű helyek közötti és ExpressRoute-kapcsolatok

Az ExpressRoute egy, a Microsoft-szolgáltatásokkal (köztük az Azure-ral) a WAN-on, és nem a nyilvános interneten keresztül kialakított közvetlen, privát kapcsolat. A helyek közötti VPN-adatforgalom titkosítva, a nyilvános interneten halad át. A helyek közötti VPN- és az ExpressRoute-kapcsolatok azonos virtuális hálózathoz való konfigurálásának lehetősége több előnnyel jár.

A helyek közötti VPN-t konfigurálhatja biztonságos feladatátvételi útvonalként az ExpressRoute számára, vagy használhat helyek közötti VPN-eket is a hálózat részét nem képező, az ExpressRoute-on keresztül kapcsolódó helyekhez való csatlakozáshoz. Ebben a konfigurációban két virtuális hálózati átjáróra van szükség ugyanahhoz a virtuális hálózathoz, amelyek közül az egyik a „Vpn” átjárótípust, a másik pedig az „ExpressRoute” átjárótípust használja.

![ExpressRoute és VPN egyidejű kapcsolatok – példa](./media/vpn-gateway-about-vpngateways/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute-coexist"></a>Üzemi modellek és módszerek egyidejű helyek közötti (S2S) és ExpressRoute-kapcsolatokhoz

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="pricing"></a>Díjszabás

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

További információ a VPN Gateway átjáró-termékváltozatokról: [Gateway SKUs](vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Átjáró-termékváltozatok).

## <a name="faq"></a>GYIK

A VPN Gateway-re vonatkozó gyakori kérdésekért lásd a [VPN Gateway gyakori kérdéseit](vpn-gateway-vpn-faq.md).

## <a name="next-steps"></a>További lépések

- A VPN-átjáró konfigurációjának megtervezése Lásd: [A VPN-átjáró tervezése és kialakítása](vpn-gateway-plan-design.md).
- További információkért tekintse meg [A VPN Gatewayjel kapcsolatos gyakori kérdések](vpn-gateway-vpn-faq.md) című szakaszt.
- Tekintse meg az [Előfizetés- és szolgáltatáskorlátok](../azure-subscription-service-limits.md#networking-limits) című szakaszt.
- Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg.
