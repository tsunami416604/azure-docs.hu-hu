---
title: "Információk a VPN Gateway-ről | Microsoft Docs"
description: "Ismerje meg az Azure Virtual Networks számára készült VPN Gateway-kapcsolatokat."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 2358dd5a-cd76-42c3-baf3-2f35aadc64c8
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: c934f78e514230958fad8b2aa9be4d2e56a3a835
ms.openlocfilehash: f74d531006a2c2cc4b12aac846c20c30317cc013


---
# <a name="about-vpn-gateway"></a>Információk a VPN Gateway-ről
A virtuális hálózati átjárók az Azure virtuális hálózatok és a helyszínek közötti, valamint az Azure-alapú virtuális hálózatok közötti hálózati adatforgalom elküldésére használatosak. VPN-átjáró konfigurálásakor létre kell hoznia és konfigurálnia kell egy virtuális hálózati átjárót és egy virtuális hálózati átjárókapcsolatot.

A Resource Manager-alapú üzemi modellben a virtuális hálózati átjárók létrehozásakor több beállítást is meg kell adnia. Az egyik szükséges beállítás a „-GatewayType”. Kétféle virtuális hálózati átjárótípus létezik: VPN és ExpressRoute. 

Amikor a hálózati adatforgalom átvitele egy dedikált privát kapcsolaton keresztül történik, az „ExpressRoute” átjárótípus használatos. Ezt ExpressRoute-átjárónak is hívják. Amikor a hálózati adatforgalom továbbítása titkosítva, egy nyilvános kapcsolaton keresztül történik, a „Vpn” átjárótípus használatos. Ez VPN-átjáróként ismert. A hely–hely, pont–hely és a virtuális hálózatok közötti kapcsolat kapcsolatok mind VPN-átjárót használnak.

Mindegyik virtuális hálózat csak egy virtuális hálózati átjáróval rendelkezhet átjárótípusonként. Rendelkezhet például egy virtuális hálózati átjáróval, amely a -GatewayType ExpressRoute típust, és egy másikkal, amelyik a -GatewayType Vpn típust használja. Ez a cikk elsősorban a VPN Gateway átjáróval foglalkozik. További információ az ExpressRoute-tal kapcsolatban: [Az ExpressRoute technikai áttekintése](../expressroute/expressroute-introduction.md).

## <a name="pricing"></a>Díjszabás
[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

## <a name="gateway-skus"></a>Gateway termékváltozatok
[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

További információ a VPN Gateway átjáró-termékváltozatokról: [Gateway SKUs](vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Átjáró-termékváltozatok).

### <a name="estimated-aggregate-throughput-by-sku"></a>A termékváltozat becsült összesített átviteli sebessége
[!INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="configuring-a-vpn-gateway"></a>VPN Gateway átjáró konfigurálása
A VPN Gateway konfigurálásakor a használt utasítások a virtuális hálózat létrehozásához használt üzemi modelltől függenek. Ha például a virtuális hálózatot a klasszikus üzembe helyezési modellel hozta létre, a VPN-átjáró beállításinak létrehozására és konfigurálására a klasszikus üzembe helyezési modell irányelvei és utasításai vonatkoznak. További információk az üzembe helyezési modellekről: [Az erőforrás-kezelői és a klasszikus üzembe helyezési modellek ismertetése](../azure-resource-manager/resource-manager-deployment-model.md).

A VPN-átjárós kapcsolatok több erőforrást használnak, amelyek speciális beállításokkal konfigurálhatók. Az erőforrások többsége külön konfigurálható, néhány esetben azonban egy bizonyos sorrendben kell őket konfigurálni. Az erőforrások létrehozásának és konfigurálásának megkezdéséhez használjon egy konfigurációs eszközt, például az Azure Portalt. Később átválthat egy másik eszközre, például a PowerShellre, a további erőforrások konfigurálásához, vagy adott esetekben a létező erőforrások módosításához. Jelenleg nem lehet a minden erőforrást és erőforrás-beállítást az Azure Portalon konfigurálni. Az egyes kapcsolati topológiákhoz tartozó cikkekben lévő utasítások egyértelműsítik, hogy mikor van szükség egy speciális konfigurációs eszközre. További információ a VPN Gateway önálló erőforrásairól és beállításairól: [About VPN Gateway settings](vpn-gateway-about-vpn-gateway-settings.md) (Információk a VPN Gateway beállításairól).

Az alábbi szakaszok olyan táblázatokat tartalmaznak, amelyek a következő információkat sorolják fel:

* elérhető üzemi modell
* elérhető konfigurációs eszközök
* hivatkozások, amelyek közvetlenül egy cikkre mutatnak, ha van ilyen

Az ábrák és a leírások segítségével kiválaszthatja az igényeinek megfelelő kapcsolati topológiát. Az ábrák bemutatják a fő alapvető topológiákat, az ábrákat útmutatásként használva azonban lehetséges összetettebb konfigurációk létrehozása is.

## <a name="site-to-site-and-multi-site"></a>Helyek közötti és többhelyes
### <a name="site-to-site"></a>Helyek közötti kapcsolat
A helyek közötti (Site-to-Site, S2S) VPN Gateway-kapcsolat egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-alagúton keresztüli kapcsolat. Ehhez a típusú kapcsolathoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy nyilvános IP-cím, és nem NAT mögött helyezkedik el. A helyek közötti kapcsolatok létesítmények közötti és hibrid konfigurációk esetében is alkalmazhatók.   

![Webhelyek közötti kapcsolat](./media/vpn-gateway-about-vpngateways/demos2s.png "site-to-site")

### <a name="multi-site"></a>Többhelyes kapcsolat
Létrehozhat és konfigurálhat VPN Gateway-kapcsolatot a virtuális hálózat és több helyszíni hálózat között. Ha több kapcsolattal dolgozik, útvonalalapú VPN-típust (a klasszikus virtuális hálózatok dinamikus átjáróját) kell használnia. Mivel a virtuális hálózatok csak egy VPN-átjáróval rendelkezhetnek, az átjárón keresztüli összes kapcsolat osztozik a rendelkezésre álló sávszélességen. Ezt gyakran „többhelyes” kapcsolatnak nevezik.

![Többhelyes kapcsolat](./media/vpn-gateway-about-vpngateways/demomulti.png "multi-site")

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Üzembe helyezési modellek és módszerek a helyek közötti és többhelyes kapcsolatokhoz
[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="vnet-to-vnet"></a>Virtuális hálózatok közötti kapcsolat
Egy virtuális hálózat egy másikkal való összekapcsolása (a virtuális hálózatok közötti kapcsolat) nagyon hasonlít egy virtuális hálózat egy helyszíni helyhez való csatlakoztatásához. Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE használatával való kialakításához. A virtuális hálózatok közötti kommunikációt kombinálhatja többhelyes kapcsolati konfigurációkkal is. Így létrehozhat olyan hálózati topológiákat, amelyek a létesítmények közötti kapcsolatokat a virtuális hálózatok közötti kapcsolatokkal kombinálják.

Az összekapcsolt virtuális hálózatok lehetnek:

* azonos vagy eltérő régiókban
* azonos vagy eltérő előfizetésekben 
* azonos vagy eltérő üzemi modellekben

![Virtuális hálózatok közötti kapcsolat](./media/vpn-gateway-about-vpngateways/demov2v.png "vnet-to-vnet")

#### <a name="connections-between-deployment-models"></a>Üzemi modellek közötti kapcsolat
Az Azure jelenleg kétféle üzemi modellt kínál: a klasszikust és a Resource Managert. Ha már egy ideje használja az Azure-t, valószínűleg futtat Azure virtuális gépeket és példányszerepköröket egy klasszikus virtuális hálózaton. Lehetséges, hogy az újabb virtuális gépek és szerepkörpéldányok egy Resource Managerben létrehozott virtuális hálózatban futnak. Létrehozhat egy kapcsolatot a virtuális hálózatok között, így lehetővé teheti, hogy az egyik virtuális hálózatban lévő erőforrások közvetlenül kommunikáljanak a másikban lévő erőforrásokkal.

#### <a name="vnet-peering"></a>Virtuális hálózatok közötti társviszony
A kapcsolat létrehozására használhat virtuális hálózatok közötti társviszonyt, ha a virtuális hálózat megfelel bizonyos követelményeknek. A virtuális hálózatok közötti társviszony nem használ virtuális hálózati átjárót. További információ: [Társviszony létesítése virtuális hálózatok között](../virtual-network/virtual-network-peering-overview.md).

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Üzembe helyezési modellek és módszerek virtuális hálózatok közötti kapcsolatokhoz
[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="point-to-site"></a>Pont–hely kapcsolat
A pont–hely (P2S) VPN-átjátókonfiguráció lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat egy SSTP (Secure Socket Tunneling Protocol) használatával működő VPN-kapcsolat. A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet a működéshez. A VPN-kapcsolat létesítéséhez a kapcsolatot az ügyfélszámítógépről kell elindítani. Ez a megoldás akkor hasznos, ha távoli helyről szeretne csatlakozni a virtuális hálózatához, például otthonról vagy konferenciáról, és akkor is, ha csak néhány, a virtuális hálózathoz csatlakoztatandó ügyfele van. A pont–hely (P2S) kapcsolatok helyek közötti kapcsolatokkal együtt is használhatók ugyanazon a VPN-átjárón keresztül, feltéve, hogy a kapcsolatok minden konfigurációs követelménye kompatibilis egymással.

![Pont–hely kapcsolat](./media/vpn-gateway-about-vpngateways/demop2s.png "point-to-site")

### <a name="deployment-models-and-methods-for-point-to-site"></a>Üzembe helyezési modellek és módszerek pont–hely kapcsolatokhoz
[!INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="expressroute"></a>ExpressRoute
[!INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

Az ExpressRoute-kapcsolatokban a virtuális hálózati átjáró a „VPN” helyett az „ExpressRoute” átjárótípussal van konfigurálva. További információ az ExpressRoute-tal kapcsolatban: [Az ExpressRoute technikai áttekintése](../expressroute/expressroute-introduction.md).

## <a name="site-to-site-and-expressroute-coexisting-connections"></a>Egyidejű helyek közötti és ExpressRoute-kapcsolatok
Az ExpressRoute egy közvetlen, dedikált kapcsolat a Microsoft-szolgáltatásokkal (köztük az Azure-ral) WAN-on, és nem a nyilvános interneten keresztül. A helyek közötti VPN-adatforgalom titkosítva, a nyilvános interneten halad át. A helyek közötti VPN- és az ExpressRoute-kapcsolatok azonos virtuális hálózathoz való konfigurálásának lehetősége több előnnyel jár.

A helyek közötti VPN-t konfigurálhatja biztonságos feladatátvételi útvonalként az ExpressRoute számára, vagy használhat helyek közötti VPN-eket is a hálózat részét nem képező, az ExpressRoute-on keresztül kapcsolódó helyekhez való csatlakozáshoz. Megjegyzés: Ebben a konfigurációban két virtuális hálózati átjáróra van szükség ugyanahhoz a virtuális hálózathoz, amelyek közül egy a -GatewayType Vpn típust, a másik pedig a -GatewayType ExpressRoute típust használja.

![Egyidejű kapcsolat](./media/vpn-gateway-about-vpngateways/demoer.png "expressroute-site2site")

### <a name="deployment-models-and-methods-for-s2s-and-expressroute"></a>Üzembe helyezési modellek és módszerek a helyek közti (S2S) és ExpressRoute-kapcsolatokhoz
[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="next-steps"></a>Következő lépések
A VPN-átjáró konfigurációjának megtervezése Lásd még: [VPN Gateway tervezése és kialakítása](vpn-gateway-plan-design.md) és [Helyszíni hálózat csatlakoztatása az Azure-hoz](../guidance/guidance-connecting-your-on-premises-network-to-azure.md).




<!--HONumber=Jan17_HO1-->


