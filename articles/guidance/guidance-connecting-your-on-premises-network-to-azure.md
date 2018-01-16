---
title: "A helyszíni hálózati csatlakozás az Azure-bA |} Microsoft Docs"
description: "Ismerteti, és összehasonlítja a különböző módszer áll rendelkezésre a Microsoft felhőszolgáltatások, például a Azure, a Office 365 és a Dynamics CRM Online-hoz való kapcsolódáshoz."
services: 
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: 
ms.assetid: 294d39ad-40e0-476a-a362-57911b1172b7
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: cherylmc
ms.openlocfilehash: 37d83d3b6dea1763d85f2411816ba2fee4279100
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2018
---
# <a name="connecting-your-on-premises-network-to-azure"></a>A helyszíni hálózati csatlakozás az Azure-bA
A Microsoft számos különböző típusú felhőszolgáltatások biztosít. A nyilvános interneten keresztül kapcsolódhatnak az összes szolgáltatáshoz, miközben keresztül is csatlakozhat egyes szolgáltatások használatával egy virtuális magánhálózati (VPN) alagúton, az interneten keresztül, vagy egy közvetlen, személyes Microsoft-kapcsolaton keresztül. Ez a cikk segít meghatározni, mely csatlakozási lehetőség lesz felelnek meg a Microsoft más felhőszolgáltatásaival felhasznált típusú alapján igényeinek. A legtöbb szervezet hasznosítani az alábbiakban több kapcsolattípusokat.

## <a name="connecting-over-the-public-internet"></a>A nyilvános interneten keresztül csatlakozó
A kapcsolat típusa hozzáférést biztosít azokhoz a Microsoft közvetlenül az interneten keresztül alább látható módon.

![Internet](./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "Internet")

Ez a kapcsolat nem általában az első Microsoft felhőszolgáltatásra való csatlakozáshoz használt típus. Az alábbi táblázat sorolja fel, és a kapcsolat típusa.

| **Előnyei** | **Megfontolások** |
| --- | --- |
| Módosítás nélkül a helyszíni hálózatra van szükség, mindaddig, amíg az összes ügyfél eszközök korlátlan hozzáféréssel összes IP-címek és portok az interneten. |Bár a forgalom gyakran titkosított HTTPS protokoll használatával, azt megszerezhetik az átvitel során óta azt halad át a nyilvános internethez. |
| A nyilvános internethez elérhetővé tett összes Microsoft felhőszolgáltatás csatlakozhat. |Előre nem látható késés, mert a kapcsolat halad át az interneten. |
| A meglévő internetes kapcsolatot használ. | |
| Minden kapcsolat eszközök kezeléséhez nincs szükség. | |

Ez a kapcsolat nélküli kapcsolatot vagy a sávszélességgel kapcsolatos költségek rendelkezik óta a meglévő internetkapcsolat használatának.

## <a name="connecting-with-a-point-to-site-connection"></a>Kapcsolódás a pont – hely kapcsolat
A kapcsolat típusa bizonyos Microsoft-felhőszolgáltatásokba, a Secure Socket Tunneling Protocol (SSTP) alagúton keresztül hozzáférést biztosít az interneten keresztül alább látható módon.

![P2S](./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "pont – hely kapcsolat")

A kapcsolat a meglévő internetkapcsolaton keresztül történik, de az Azure VPN Gateway használata szükséges. Az alábbi táblázat sorolja fel, és a kapcsolat típusa.

| **Előnyei** | **Megfontolások** |
| --- | --- |
| Módosítás nélkül a helyszíni hálózatra van szükség, mindaddig, amíg az összes ügyfél eszközök korlátlan hozzáféréssel összes IP-címek és portok az interneten. |Bár a forgalom titkosítása IPSec a, azt megszerezhetik az átvitel során, mivel a nyilvános internethez bejárja. |
| A meglévő internetes kapcsolatot használ. |Előre nem látható késés, mert a kapcsolat halad át az interneten. |
| Átviteli sebesség legfeljebb 200 Mb/s-átjárón. |Szükséges, létrehozását és kezelését az egyes eszközök a helyszíni hálózat és minden egyes eszköz kapcsolódnia kell átjáró közötti külön kapcsolatok. |
| Használható az Azure-szolgáltatásokhoz, amely képes csatlakozni az Azure Virtual Network (VNet) például az Azure virtuális gépek és az Azure Cloud Services. |Minimális adminisztrációt folyamatban lévő az Azure VPN-átjáró. |
| Nem használható a Microsoft Office 365 vagy a Dynamics CRM Online-hoz való csatlakozáshoz. | |
| Nem használható az Azure-szolgáltatásokhoz, amelyek nem csatlakoztatható a virtuális hálózat. | |

További információ a [VPN-átjáró](../vpn-gateway/vpn-gateway-about-vpngateways.md) szolgáltatás, a [árképzési](https://azure.microsoft.com/pricing/details/vpn-gateway), és a kimenő adatforgalom [árképzési](https://azure.microsoft.com/pricing/details/data-transfers).

## <a name="connecting-with-a-site-to-site-connection"></a>Webhelyek kapcsolaton keresztül csatlakozó
A kapcsolat típusa bizonyos Microsoft-felhőszolgáltatásokba, IPSec-alagutat keresztül hozzáférést biztosít az interneten keresztül alább látható módon.

![S2S](./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "– hely kapcsolat")

A kapcsolat létrejön, a meglévő internetkapcsolaton keresztül, de az Azure VPN Gateway társított árképzési és árképzési kimenő adatátvitel használatát igényli. Az alábbi táblázat sorolja fel, és a kapcsolat típusa.

| **Előnyei** | **Megfontolások** |
| --- | --- |
| Minden eszköz a helyi hálózaton tud kommunikálni az Azure-szolgáltatásokhoz csatlakozik egy Vnetet, így nincs szükség minden eszközhöz egyedi kapcsolatait konfiguráló. |Bár a forgalom titkosítása IPSec a, azt megszerezhetik az átvitel során, mivel a nyilvános internethez bejárja. |
| A meglévő internetes kapcsolatot használ. |Előre nem látható késés, mert a kapcsolat halad át az interneten. |
| Azure-szolgáltatásokat, amely képes csatlakozni egy Vnetet, például a virtuális gépek és Felhőszolgáltatások csatlakozni használható. |Konfigurálnia kell, és kezelheti a érvényesített VPN eszköz * helyszíni. |
| Átviteli sebesség legfeljebb 200 Mb/s-átjárón. |Minimális adminisztrációt folyamatban lévő az Azure VPN-átjáró. |
| A felhő virtuális gépekről a vizsgálati és naplózási felhasználó által definiált útvonalak vagy a Border Gateway Protocol (BGP) használatával a helyszíni hálózaton keresztül kezdeményezett kimenő forgalom kényszerítheti **. |Nem használható a Microsoft Office 365 vagy a Dynamics CRM Online-hoz való csatlakozáshoz. |
| Nem használható az Azure-szolgáltatásokhoz, amelyek nem csatlakoztatható a virtuális hálózat. | |
| Ha vissza a helyszíni eszközök kapcsolatokat kezdeményező szolgáltatást használ, és a biztonsági házirendje megköveteli azt, szükség lehet a helyszíni hálózat és az Azure között tűzfal. | |

* Megtekintheti a listáját [VPN-eszközök érvényesítve](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable).
* ** Használatával kapcsolatos további [felhasználó által definiált útvonalak](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) vagy [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) kényszerítése a helyszíni eszközök számára az Azure Vnetekhez útválasztást.

## <a name="connecting-with-a-dedicated-private-connection"></a>Csatlakozás egy dedikált magánhálózati kapcsolattal
A kapcsolat típusa hozzáférést biztosít az összes Microsoft-felhőszolgáltatásokba egy dedikált titkos kapcsolaton keresztül a Microsoft az interneten nem áthaladó alább látható módon.

![ER](./media/guidance-connecting-your-on-premises-network-to-azure/er.png "ExpressRoute-kapcsolat")

A kapcsolat az ExpressRoute-szolgáltatás és a kapcsolat szolgáltatójánál kapcsolat használatát igényli. Az alábbi táblázat sorolja fel, és a kapcsolat típusa.

| **Előnyei** | **Megfontolások** |
| --- | --- |
| Forgalom nem megszerezhetik az átvitel során a nyilvános interneten keresztül, mert a szolgáltató egy dedikált kapcsolaton keresztül. |A helyi útválasztó kezelése szükséges. |
| Sávszélesség legfeljebb 10 Gb/s ExpressRoute-kapcsolatcsoportot és legfeljebb 2 Gb/s minden átjáró átviteli sebességet. |A kapcsolat szolgáltatójánál dedikált kapcsolatot igényel. |
| Előre jelezhető késés mert dedikált kapcsolódni a Microsoft az interneten nem áthaladó. |Egy vagy több Azure VPN Gatewayek minimális adminisztrációra lehet szükség, (Ha a csatlakozás a kapcsolatcsoport Vnetekről). |
| Titkosíthatja a forgalmat, ha szükséges, ha nem igényel titkosított kommunikációt. |Ha vissza a helyszíni eszközök kapcsolatot kezdeményezni a felhőalapú szolgáltatásokat használ, szükség lehet a helyszíni hálózat és az Azure között tűzfal. |
| Az összes Microsoft felhőalapú szolgáltatások, néhány kivételek * közvetlenül kapcsolódhatnak. |Hálózati címfordítás (NAT) igényel a helyi IP-címek megadása a Microsoft azon adatközpontjainak a szolgáltatásokhoz, amely nem lehet csatlakozni a VNet.* * |
| Kényszerítheti a kimenő adatforgalmat a felhő virtuális gépek ellenőrzési és a naplózás a BGP használatával a helyszíni hálózaton keresztül kezdeményezett. | |

* * Megtekintheti egy [szolgáltatások listája](../expressroute/expressroute-faqs.md#supported-services) , ExpressRoute nem használható. Az Azure-előfizetéssel kapcsolódni az Office 365 jóvá kell hagyni.  Tekintse meg a [Azure ExpressRoute az Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1) cikkben alább.
* ** Tudhat meg többet az ExpressRoute [NAT](../expressroute/expressroute-nat.md) követelményeinek.

További információ [ExpressRoute](../expressroute/expressroute-introduction.md), hozzá van rendelve [árképzési](https://azure.microsoft.com/pricing/details/expressroute), és [kapcsolat szolgáltatók](../expressroute/expressroute-locations.md#locations).

## <a name="additional-considerations"></a>Néhány fontos megjegyzés
* A fenti lehetőségek számos rendelkeznek, különböző határok VNet kapcsolatok, átjárókapcsolatokhoz és egyéb feltételek is támogatják. Javasoljuk, hogy tekintse át az Azure [hálózatkezelés korlátok](../azure-subscription-service-limits.md#networking-limits) megértéséhez, ha valamelyiket hatással kapcsolatot használ.
* Ha azt tervezi, a pont-pont VPN-kapcsolat csatlakozni egy átjáró ugyanazt a virtuális hálózatot, mint egy ExpressRoute-átjárót, tanulmányozza át a fontos megkötések először. Tekintse meg a [ExpressRoute konfigurálása és a pont-pont vizsgálatát a kísérő kapcsolatok](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations) cikkben olvashat.

## <a name="next-steps"></a>További lépések
Az alábbi források valósítja meg a cikkben szereplő kapcsolattípusokat ismerteti.

* [Alkalmazzon egy pont – hely kapcsolat](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [A pont-pont kapcsolat megvalósítása](guidance-hybrid-network-vpn.md)
* [Dedikált kapcsolatot megvalósítása](guidance-hybrid-network-expressroute.md)
* [A magas rendelkezésre állású webhelyek kapcsolattal rendelkező dedikált kapcsolatot megvalósítása](guidance-hybrid-network-expressroute-vpn-failover.md)
