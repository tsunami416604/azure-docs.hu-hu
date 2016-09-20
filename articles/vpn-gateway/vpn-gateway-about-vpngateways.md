<properties 
   pageTitle="Információk a VPN Gateway-ről | Microsoft Azure"
   description="Ismerje meg az Azure Virtual Networks számára készült VPN Gateway-kapcsolatokat."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/22/2016"
   ms.author="cherylmc" />

# Információk a VPN Gateway-ről


A VPN Gateway a virtuális hálózatok és a helyszínek közötti hálózati adatforgalom elküldésére használható erőforrás-gyűjtemény. Az átjárók a helyek közötti, a pont–hely és az ExpressRoute-kapcsolatokhoz használhatók. A VPN Gateway másik használati módja az Azure virtuális hálózatai közötti (VNet–VNet) adatküldés. 

Egy kapcsolat létrehozásához hozzá kell adnia egy virtuális hálózati átjárót egy virtuális hálózathoz, és konfigurálnia kell a további VPN Gateway-erőforrásokat, valamint azok beállításait. Mindegyik virtuális hálózat csak egy virtuális hálózati átjáróval rendelkezhet átjárótípusonként. Rendelkezhet például egy virtuális hálózati átjáróval, amely a -GatewayType Vpn típust, és egy másikkal, amelyik a -GatewayType ExpressRoute típust használja.

Az átjárók követelményeivel kapcsolatos információkért tekintse meg a[Gateway Requirements](vpn-gateway-about-vpn-gateway-settings.md#requirements) (Az átjáróra vonatkozó követelmények) című részt. A becsült összesített átviteli sebességgel kapcsolatban tekintse meg az [About VPN Gateway Settings](vpn-gateway-about-vpn-gateway-settings.md#aggthroughput) (Információk a VPN Gateway beállításairól) című cikket. Díjszabással kapcsolatos információk: [VPN Gateway Díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway). Az előfizetésekkel és a szolgáltatásra vonatkozó korlátozásokkal kapcsolatban tekintse meg a [Networking Limits](../articles/azure-subscription-service-limits.md#networking-limits) (Hálózati korlátok) című részt.

A VPN Gateway konfigurálásakor a használt utasítások a virtuális hálózat létrehozásához használt üzemi modelltől függenek. Ha például a virtuális hálózatot a klasszikus üzembe helyezési modellel hozta létre, a VPN-átjáró beállításinak létrehozására és konfigurálására a klasszikus üzembe helyezési modell irányelvei és utasításai vonatkoznak. További információ: [Understanding Resource Manager and classic deployment models](../resource-manager-deployment-model.md) (Az erőforrás-kezelői és a klasszikus üzemi modell ismertetése).

Az alábbi szakaszok olyan táblázatokat tartalmaznak, amelyek a konfiguráció alábbi információit sorolják fel:

- elérhető üzemi modell
- elérhető konfigurációs eszközök
- hivatkozások, amelyek közvetlenül egy cikkre mutatnak, ha van ilyen


Az ábrák és a leírások segítségével kiválaszthatja az igényeinek megfelelő konfigurációs topológiát. Az ábrák bemutatják a fő alapvető topológiákat, az ábrákat útmutatásként használva azonban lehetséges összetettebb konfigurációk létrehozása is. Mindegyik konfiguráció a kiválasztott VPN Gateway-beállításokra támaszkodik.

### A VPN Gateway beállításainak konfigurálása

Mivel a VPN Gateway egy erőforrás-gyűjtemény, az erőforrások egy részét az egyik eszközzel konfigurálhatja, majd egy másik eszközre váltva konfigurálhat más erőforrás-beállításokat. Jelenleg nem lehet a VPN Gateway mindegyik erőforrás-beállítását az Azure Portalon konfigurálni. Az egyes konfigurációkhoz tartozó cikkekben lévő utasítások meghatározzák, hogy szükség van-e egy adott eszközre. Ha a klasszikus üzemi modellel dolgozik, ekkor célszerű a klasszikus portált vagy a PowerShellt használni. Az egyes elérhető beállításokkal kapcsolatos információkért tekintse meg az [About VPN Gateway settings](vpn-gateway-about-vpn-gateway-settings.md) (Információk a VPN Gateway beállításairól) című cikket.



## Helyek közötti és többhelyes

### Helyek közötti kapcsolat

A helyek közötti (Site-to-Site, S2S) kapcsolat egy IPsec/IKE (S2S vagy IKEv1) VPN-átjárón keresztüli kapcsolat. Ehhez a típusú kapcsolathoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy nyilvános IP-cím, és nem NAT mögött helyezkedik el. A helyek közötti kapcsolatok létesítmények közötti és hibrid konfigurációk esetében is alkalmazhatók.   

![S2S kapcsolat](./media/vpn-gateway-about-vpngateways/demos2s.png "site-to-site")


### Többhelyes kapcsolat

Létrehozhat és konfigurálhat VPN-kapcsolatot a virtuális hálózat és több helyszíni hálózat között. Ha több kapcsolattal dolgozik, útvonalalapú VPN-típust (a klasszikus virtuális hálózatok dinamikus átjáróját) kell használnia. Mivel a virtuális hálózatok csak egy virtuális hálózati átjáróval rendelkezhetnek, az átjárón keresztüli összes kapcsolat osztozik a rendelkezésre álló sávszélességen. Ezt a konfigurációtípust gyakran „többhelyes” kapcsolatnak nevezik.
 

![Többhelyes kapcsolat](./media/vpn-gateway-about-vpngateways/demomulti.png "multi-site")

### Üzemi modellek és módszerek

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)] 

## Virtuális hálózatok közötti kapcsolat

Egy virtuális hálózat egy másikkal való összekapcsolása (a virtuális hálózatok közötti kapcsolat) nagyon hasonlít egy virtuális hálózat egy helyszíni helyhez való csatlakoztatásához. Mindkét kapcsolattípus Azure VPN-átjárót használ biztonságos alagút kialakításához IPsec/IKE használatával. A virtuális hálózatok közötti kommunikációt kombinálhatja többhelyes konfigurációkkal is. Így létrehozhat olyan hálózati topológiákat, amelyek a létesítmények közötti kapcsolatokat a virtuális hálózatok közötti kapcsolatokkal kombinálják.

Az összekapcsolt virtuális hálózatok lehetnek:

- azonos vagy eltérő régiókban
- azonos vagy eltérő előfizetésekben 
- azonos vagy eltérő üzemi modellekben



![Virtuális hálózatok közötti kapcsolat](./media/vpn-gateway-about-vpngateways/demov2v.png "vnet-to-vnet")



### Üzemi modellek közötti kapcsolat

Az Azure jelenleg kétféle üzemi modellt kínál: a klasszikust és a Resource Managert. Ha már egy ideje használja az Azure-t, valószínűleg futtat Azure virtuális gépeket és példányszerepköröket egy klasszikus virtuális hálózaton. Lehetséges, hogy az újabb virtuális gépek és szerepkörpéldányok egy Resource Managerben létrehozott virtuális hálózatban futnak. Létrehozhat egy kapcsolatot a virtuális hálózatok között, így lehetővé teheti, hogy az egyik virtuális hálózatban lévő erőforrások közvetlenül kommunikáljanak a másikban lévő erőforrásokkal.


### Üzemi modellek és módszerek

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 

### Virtuális hálózatok közötti társviszony

Használhat virtuális hálózatok közötti társviszonyt a kapcsolat létrehozására, ha a virtuális hálózat konfigurációja megfelel bizonyos követelményeknek. A virtuális hálózatok közötti társviszony nem használ virtuális hálózati átjárót. A [virtuális hálózatok közötti társviszony](../virtual-network/virtual-network-peering-overview.md) jelenleg előzetes verzióban üzemel.



## Pont–hely kapcsolat

A pont–hely konfiguráció lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat egy SSTP (Secure Socket Tunneling Protocol) használatával működő VPN-kapcsolat. A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet a működéshez. A VPN-kapcsolat létesítéséhez a kapcsolatot az ügyfélszámítógépről kell elindítani. Ez a megoldás akkor hasznos, ha távoli helyről szeretne csatlakozni a virtuális hálózatához, például otthonról vagy konferenciáról, és akkor is, ha csak néhány, a virtuális hálózathoz csatlakoztatandó ügyfele van. 


![Pont–hely kapcsolat](./media/vpn-gateway-about-vpngateways/demop2s.png "point-to-site")

### Üzemi modellek és módszerek

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 


## ExpressRoute

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute technical overview](../expressroute/expressroute-introduction.md) (Az ExpressRoute műszaki áttekintése).


## Egyidejű helyek közötti és ExpressRoute-kapcsolatok

Az ExpressRoute egy közvetlen, dedikált kapcsolat a Microsoft-szolgáltatásokkal (köztük az Azure-ral) WAN-on, és nem a nyilvános interneten keresztül. A helyek közötti VPN-adatforgalom titkosítva, a nyilvános interneten halad át. A helyek közötti VPN- és az ExpressRoute-kapcsolatok azonos virtuális hálózathoz való konfigurálásának lehetősége több előnnyel jár.

A helyek közötti VPN-t konfigurálhatja biztonságos feladatátvételi útvonalként az ExpressRoute számára, vagy használhat helyek közötti VPN-eket is a hálózat részét nem képező, az ExpressRoute-on keresztül kapcsolódó helyekhez való csatlakozáshoz. Ebben a konfigurációban két virtuális hálózati átjáróra van szükség ugyanahhoz a virtuális hálózathoz, amelyek közül egy a -GatewayType Vpn típust, a másik pedig a -GatewayType ExpressRoute típust használja.


![Egyidejű kapcsolat](./media/vpn-gateway-about-vpngateways/demoer.png "expressroute-site2site")


### Üzemi modellek és módszerek

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)] 


## Következő lépések

További információk a VPN Gateway átjárókról: [VPN Gateway – gyakori kérdések](vpn-gateway-vpn-faq.md).

Helyszíni hely csatlakoztatása virtuális hálózathoz. Lásd: [Helyek közötti kapcsolat létrehozása](vpn-gateway-howto-site-to-site-resource-manager-portal.md).





 



<!--HONumber=sep16_HO1-->


