<properties 
   pageTitle="Létesítmények közötti biztonságos kapcsolatok a virtuális hálózatokban | Microsoft Azure"
   description="Megismerheti a létesítmények közötti biztonságos kapcsolatok típusait a virtuális hálózatok esetében, többek között a helyek közötti, a pont–hely és az ExpressRoute-kapcsolatokat."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="cherylmc" />

# Létesítmények közötti biztonságos kapcsolatok a virtuális hálózatokban

Ez a cikk azt mutatja be, milyen módokon csatlakoztathatja helyszíni helyét egy Azure Virtual Networkhöz. A cikk a Resource Managerrel végzett és a klasszikus üzemi modellre is érvényes. Ha VPN Gateway-kapcsolati diagramokat keres, tekintse meg az [Azure VPN Gateway-kapcsolattopológiák](vpn-gateway-topology.md) című cikket.

Háromféle kapcsolat lehetséges: a helyek közötti, a pont–hely és az ExpressRoute-kapcsolat. Az, hogy melyik a legmegfelelőbb az Ön számára, több tényezőtől függhet, például:


- Mekkora átviteli sebesség szükséges a megoldásához?
- A nyilvános interneten kíván kommunikálni biztonságos VPN-en keresztül, vagy privát kapcsolatot szeretne használni?
- Rendelkezik használható nyilvános IP-címmel?
- Kíván VPN-eszközt használni? Ha igen, ez kompatibilis a rendszerrel?
- Csak néhány számítógépet csatlakoztatna, vagy állandó kapcsolatra van szüksége a helyéhez?
- Milyen típusú VPN-átjáró szükséges az Ön által létrehozni kívánt megoldáshoz?

Az alábbi táblázat segíthet eldönteni, melyik az Ön megoldásához legmegfelelőbb kapcsolat.

[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]                                                                    

## Helyek közötti kapcsolatok

A helyek közötti VPN biztonságos kapcsolatot tesz lehetővé a helyszíni hely és a virtuális hálózat között. A helyek közötti kapcsolat létrehozásához konfigurálni kell egy, a helyszíni hálózaton található VPN-eszközt, hogy az biztonságos kapcsolatot létesítsen az Azure VPN Gateway-jel. Miután a kapcsolat létrejött, a helyi és a virtuális hálózatok erőforrásai közvetlenül és biztonságosan kommunikálhatnak egymással. A helyek közötti kapcsolatok esetében a virtuális hálózat erőforrásaihoz való hozzáféréshez nem szükséges külön kapcsolatok létesítése a helyi hálózaton található minden egyes ügyfélszámítógéphez.

**Akkor használjon helyek közötti kapcsolatot, ha:**

- Hibrid megoldást szeretne létrehozni.
- Ügyféloldali konfiguráció nélkül szeretne kapcsolatot létesíteni a helyszíni hely és a virtuális hálózat között.
- Állandó kapcsolatot szeretne. 

**Követelmények**

- A helyszíni VPN-eszköznek rendelkeznie kell egy internetes IPv4 IP-címmel. Ez nem lehet NAT mögötti.
- A VPN-eszköznek kompatibilisnek kell lennie a rendszerrel. Lásd: [About VPN Devices](vpn-gateway-about-vpn-devices.md) (Tudnivalók a VPN-eszközökről). 
- A használt VPN-eszköznek kompatibilisnek kell lennie a megoldáshoz szükséges átjárótípussal. Lásd: [About VPN Gateway](vpn-gateway-about-vpngateways.md) (Tudnivalók a VPN Gateway-ről).
- Az átjáró termékváltozata szintén hatással van az összesített átviteli sebességre. További információkért lásd: [Az átjárók termékváltozatai](vpn-gateway-about-vpngateways.md#gwsku). 

**Elérhető üzemi modellek és módszerek a helyek közötti (S2S) kapcsolatokhoz**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)] 


## Pont–hely kapcsolatok

A pont–hely VPN szintén lehetővé teszi a biztonságos kapcsolatot a virtuális hálózattal. A pont–hely konfigurációban a kapcsolat külön van konfigurálva minden olyan ügyfélszámítógépen, amelyet csatlakoztatni szeretne a virtuális hálózathoz. A pont–hely kapcsolat nem igényel VPN-eszközt. Ez a kapcsolattípus olyan VPN-ügyfelet használ, amelyet az egyes ügyfélszámítógépekre kell telepíteni. A VPN létrehozásához manuálisan kell kezdeményezni a kapcsolatot a helyszíni ügyfélszámítógépről.

A pont–hely és a helyek közötti konfigurációk létezhetnek egymás mellett is, viszont a helyek közötti kapcsolatokkal ellentétben a pont–hely kapcsolatok nem konfigurálhatók, ha az adott virtuális hálózaton már létesítve lett egy ExpressRoute-kapcsolat.

**Akkor használjon pont–hely kapcsolatot, ha:**

- Csak néhány ügyfél csatlakozását szeretné konfigurálni a virtuális hálózathoz.

- Távoli helyről szeretne csatlakozni a virtuális hálózatához, például egy kávézóból vagy konferenciaközpontból.

- Helyek közötti kapcsolatot használ, de vannak olyan ügyfelei, amelyeket távoli helyről kell csatlakoztatni.

- Nincs hozzáférése egy olyan VPN-eszközhöz, amely megfelel a helyek közötti kapcsolat minimális követelményeinek.

- Nem rendelkezik internetes IPv4 IP-címmel a VPN-eszközéhez.

**Elérhető üzemi modellek és módszerek a pont–hely (P2S) kapcsolatokhoz**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## ExpressRoute-kapcsolatok

Az Azure ExpressRoute használatával privát kapcsolatok hozhatók létre az Azure-adatközpontok és a helyszíni vagy a bérelt kiszolgálói környezetben üzemelő infrastruktúra között. Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át, így az általános internetes kapcsolatoknál megbízhatóbbak, gyorsabbak, gyorsabb a válaszidejük, és biztonságosabbak.

Egyes esetekben jelentős költségmegtakarítással járhat az ExpressRoute-kapcsolatok használata a helyszíni hely és az Azure közötti adatátvitelhez. Az ExpressRoute használatával egy ExpressRoute-helyen (egy adatcsere-szolgáltatói létesítményben) kapcsolatot létesíthet az Azure-ral, vagy közvetlenül csatlakozhat az Azure-hoz egy hálózati szolgáltató által biztosított meglévő WAN hálózatról (például MPLS VPN).

További információ az ExpressRoute-tal kapcsolatban: ExpressRoute [Technical Overview](../expressroute/expressroute-introduction.md) (Az ExpressRoute műszaki áttekintése).


## Következő lépések

- További információk a VPN Gateway-ről: [About VPN Gateway](vpn-gateway-about-vpngateways.md) (Tudnivalók a VPN Gateway-ről), VPN Gateway – [gyakori kérdések](vpn-gateway-vpn-faq.md) és a [Tervezéssel és kialakítással kapcsolatos](vpn-gateway-plan-design.md) cikk.

- További információ az ExpressRoute-tal kapcsolatban: ExpressRoute [Technical Overview](../expressroute/expressroute-introduction.md) (Az ExpressRoute műszaki áttekintése), a [GYIK](../expressroute/expressroute-faqs.md) oldal és a [munkafolyamatok](../expressroute/expressroute-workflows.md).







<!--HONumber=jun16_HO2-->


