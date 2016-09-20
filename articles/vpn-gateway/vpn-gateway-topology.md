<properties 
   pageTitle="VPN Gateway-kapcsolatok topológiája | Microsoft Azure"
   description="VPN Gateway-kapcsolatok topológiája, elérhető konfigurációs eszközök és üzemi modellek."
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
   ms.date="07/19/2016"
   ms.author="cherylmc" />

# Azure VPN Gateway-kapcsolatok

Ez a cikk bemutatja az alapvető VPN Gateway-kapcsolati topológiákat. Az ábrák és leírások segítségével kiválaszthatja az igényeinek megfelelő konfigurációs topológiát. A cikk a fő alapvető topológiákat tárgyalja, de a diagramokat útmutatásként használva lehetséges összetettebb topológiák létrehozása is.

Minden topológiához tartozik egy táblázat, amelyben szerepel az üzemi modell, amelyhez a topológia elérhető, a topológia konfigurálásához használható üzembe helyezési eszközök, valamint, ha elérhető, közvetlen hivatkozás egy cikkre. A táblázatokat rendszeresen frissítjük, ahogy az új cikkek és üzembe helyezési eszközök elérhetővé válnak.

Miután eldöntötte, hogy milyen kapcsolatot szeretne létrehozni, a VPN-átjáró létrehozásához kapcsolódó utasítások a virtuális hálózat létrehozásához használt üzemi modelltől függenek. Ha például a virtuális hálózatot a klasszikus üzembe helyezési modellel hozta létre, a VPN-átjáró létrehozására és konfigurálására a klasszikus üzembe helyezési modell irányelvei és utasításai vonatkoznak. A klasszikus üzembe helyezési modellt használó virtuális hálózatokhoz nem hozhat létre erőforrás-kezelői VPN-átjárót. További információk az üzembe helyezési modellekről: [Az erőforrás-kezelői és a klasszikus üzembe helyezési modellek ismertetése](../resource-manager-deployment-model.md).

## Helyek közötti és többhelyes

A helyek közötti kapcsolat egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-átjárón keresztüli kapcsolat. Ez a kapcsolattípus helyszíni VPN-eszközt vagy Windows Server RRAS-t igényel. A helyek közötti kapcsolatok létesítmények közötti és hibrid konfigurációk esetében is alkalmazhatók.   


**S2S diagram**

![S2S kapcsolat](./media/vpn-gateway-topology/site2site.png "site-to-site")

A helyszíni hálózataihoz több S2S VPN-kapcsolatot is létrehozhat és konfigurálhat, ha Azure útvonalalapú VPN-eket használ. Ezt a konfigurációtípust gyakran „többhelyes” kapcsolatnak nevezik.
 

**Többhelyes diagram**

![Többhelyes kapcsolat](./media/vpn-gateway-topology/multisite.png "multi-site")


**Elérhető üzemi modellek és módszerek**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)] 

## Virtuális hálózatok közötti kapcsolat

Egy virtuális hálózat egy másikkal való összekapcsolása (virtuális hálózatok közötti kapcsolat) egy virtuális hálózat egy helyszíni helyhez való csatlakoztatásához hasonló. Mindkét kapcsolattípus Azure VPN-átjárót használ biztonságos alagút kialakításához IPsec/IKE használatával. A csatlakoztatott virtuális hálózatok lehetnek különböző régiókban vagy különböző előfizetésekben is. A virtuális hálózatok közötti kommunikációt kombinálhatja többhelyes konfigurációkkal is. Így létrehozhat olyan hálózati topológiákat, amelyek a létesítmények közötti kapcsolatokat a virtuális hálózatok közötti kapcsolatokkal kombinálják.

Az Azure jelenleg kétféle üzemi modellt kínál: az Azure szolgáltatásfelügyeletet (ezt nevezik klasszikusnak) és az Azure Resource Managert. Ha már egy ideje használja az Azure-t, valószínűleg futtat Azure virtuális gépeket és példányszerepköröket egy klasszikus virtuális hálózaton, az újabb virtuális gépei pedig egy, az ARM-ben létrehozott virtuális hálózaton futnak. A virtuális hálózatok között akkor is létesíthet kapcsolatot, ha azok különböző üzemi modellekben találhatóak.


**Virtuális hálózatok közötti diagram**

![Virtuális hálózatok közötti kapcsolat](./media/vpn-gateway-topology/vnet2vnet.png "vnet-to-vnet")


**Elérhető üzemi modellek és módszerek**

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 


## Pont–hely kapcsolat

A pont–hely konfiguráció lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal külön-külön az egyes ügyfélszámítógépekről. VPN-kapcsolat létesítéséhez manuálisan kell kezdeményezni a kapcsolatot az ügyfélszámítógépről. Ez a megoldás akkor hasznos, ha távoli helyről szeretne csatlakozni a virtuális hálózatához, például otthonról vagy konferenciáról, és akkor is, ha csak néhány, a virtuális hálózathoz csatlakoztatandó ügyfele van. 

A pont–hely kapcsolat egy SSTP (Secure Socket Tunneling Protocol) használatával működő VPN-kapcsolat. A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet a működéshez. 

**P2S diagram**

![Pont–hely kapcsolat](./media/vpn-gateway-topology/point2site.png "point-to-site")

**Elérhető üzemi modellek és módszerek**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 


##ExpressRoute

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

További információ az ExpressRoute-tal kapcsolatban: [Az ExpressRoute technikai áttekintése](../expressroute/expressroute-introduction.md).



## Egyidejű helyek közötti és ExpressRoute-kapcsolatok

Az ExpressRoute egy közvetlen, dedikált kapcsolat a Microsoft-szolgáltatásokkal (köztük az Azure-ral) WAN-on, nem a nyilvános interneten keresztül. A helyek közötti VPN-adatforgalom titkosítva, a nyilvános interneten halad át. A helyek közötti VPN- és az ExpressRoute-kapcsolatok azonos virtuális hálózathoz való konfigurálásának lehetősége több előnnyel jár. A helyek közötti VPN-t konfigurálhatja biztonságos feladatátvételi útvonalként az ExpressRoute számára, vagy használhat helyek közötti VPN-eket is a hálózat részét nem képező, az ExpressRoute-on keresztül kapcsolódó helyekhez való csatlakozáshoz. 


**Egyidejű kapcsolatok diagram**

![Egyidejű kapcsolat](./media/vpn-gateway-topology/expressroutes2s.png "expressroute-site2site")


**Elérhető üzemi modellek és módszerek**

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)] 




## Következő lépések

Célszerű áttekinteni az [About VPN Gateways](vpn-gateway-about-vpngateways.md) (Információk a VPN-átjárókról) és a [VPN Gateway – gyakori kérdések](vpn-gateway-vpn-faq.md) című cikkeket a VPN-átjárók beállításainak alaposabb megismerése érdekében.





 



<!--HONumber=sep16_HO1-->


