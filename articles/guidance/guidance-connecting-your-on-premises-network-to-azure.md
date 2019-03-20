---
title: A helyszíni hálózat csatlakoztatása az Azure-bA |} A Microsoft Docs
description: Ismerteti, és összehasonlítja a különböző módszereket a Microsoft felhőalapú szolgáltatásához – például az Azure, Office 365 és Dynamics CRM Online-hoz való kapcsolódáshoz.
services: ''
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: ''
ms.assetid: 294d39ad-40e0-476a-a362-57911b1172b7
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: cherylmc
ms.openlocfilehash: 9361c0b2ee84d7459bd432f5a8ce13fc5fd85ef8
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "23851014"
---
# <a name="connecting-your-on-premises-network-to-azure"></a>A helyszíni hálózat csatlakoztatása az Azure-bA
A Microsoft számos különböző típusú felhőszolgáltatásokat biztosít. Amíg az összes szolgáltatás csatlakozhat a nyilvános interneten keresztül, is csatlakozhat a szolgáltatások használatával egy virtuális magánhálózati (VPN) alagúton, az interneten vagy a Microsoft közvetlen, privát kapcsolaton keresztül. Ez a cikk segít meghatározni, hogy mely kapcsolódási beállítást választja a rendszer a felhasznált Microsoft-felhőszolgáltatások típusai alapján igényeinek leginkább megfelelő. A legtöbb szervezet több, az alább ismertetett kapcsolattípusok használatára.

## <a name="connecting-over-the-public-internet"></a>A nyilvános interneten keresztül csatlakozó
Ez a kapcsolattípus Microsoft-felhőszolgáltatáshoz hozzáférést biztosít a közvetlenül az interneten keresztül alább látható módon.

![Internet](./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "Internet")

Ez a kapcsolat nem általában az első típusú Microsoft-felhőszolgáltatásokhoz való csatlakozáshoz használt. Az alábbi táblázatban áttekintheti, és a hátrányai, ez a kapcsolattípus.

| **Előnyök** | **Megfontolások** |
| --- | --- |
| A helyszíni hálózathoz módosítás nem szükséges mindaddig, amíg az ügyfél minden eszköz rendelkezik korlátlan hozzáférést az összes IP-címek és portok az interneten. |Bár a forgalom gyakran titkosítva vannak HTTPS-en keresztül, azt megszerezhetik az átvitel során óta bejárja a nyilvános interneten. |
| Csatlakozhat a nyilvános interneten elérhetővé tett összes Microsoft felhőszolgáltatáshoz. |Előre nem látható késés, mert a kapcsolat is járja az interneten. |
| Használja a meglévő internetkapcsolat. | |
| Nincs szükség minden olyan kapcsolat eszközök kezelése. | |

Ez a kapcsolat nem rendelkezik kapcsolódási vagy sávszélességgel kapcsolatos költségek óta használja a meglévő internetkapcsolat.

## <a name="connecting-with-a-point-to-site-connection"></a>Pont – hely kapcsolattal csatlakozó
Ez a kapcsolattípus bizonyos Microsoft-felhőszolgáltatásokhoz, Secure Socket Tunneling Protocol (SSTP) alagúton keresztüli hozzáférést biztosít az interneten keresztül, ahogy az alábbi.

![P2S](./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "pont – hely kapcsolat")

A kapcsolat jön létre a meglévő internetkapcsolaton keresztül, de az Azure VPN Gateway használatát igényli. Az alábbi táblázatban áttekintheti, és a hátrányai, ez a kapcsolattípus.

| **Előnyök** | **Megfontolások** |
| --- | --- |
| A helyszíni hálózathoz módosítás nem szükséges mindaddig, amíg az ügyfél minden eszköz rendelkezik korlátlan hozzáférést az összes IP-címek és portok az interneten. |Forgalom IPSec használatával titkosított, bár azt is megszerezhetik az átvitel során óta bejárja a nyilvános interneten. |
| Használja a meglévő internetkapcsolat. |Előre nem látható késés, mert a kapcsolat is járja az interneten. |
| Átviteli sebesség akár 200 Mb/s / átjáró. |Szükséges létrehozásának és felügyeletének külön kapcsolatok a helyszíni hálózaton lévő összes eszköz és minden egyes eszköz kell csatlakoznia átjáró között. |
| Használható az Azure-szolgáltatásokhoz, amely egy Azure-beli virtuális hálózatok (VNet) lehet csatlakoztatni, például az Azure Virtual Machines és az Azure Cloud Services. |Folyamatban lévő minimális felügyelet mellett az Azure VPN-átjáró szükséges. |
| Nem használható a Microsoft Office 365 vagy Dynamics CRM Online-hoz való csatlakozáshoz. | |
| Nem használható az Azure-szolgáltatásokhoz, amely nem csatlakozik virtuális hálózathoz. | |

Tudjon meg többet a [VPN-átjáró](../vpn-gateway/vpn-gateway-about-vpngateways.md) service, a [díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway), és a kimenő adatforgalom [díjszabás](https://azure.microsoft.com/pricing/details/data-transfers).

## <a name="connecting-with-a-site-to-site-connection"></a>Hely – hely kapcsolattal csatlakozó
Ez a kapcsolattípus bizonyos Microsoft-felhőszolgáltatásokhoz, IPSec-alagút használatával hozzáférést biztosít az interneten keresztül, ahogy az alábbi.

![S2S](./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "helyek közötti kapcsolat")

A kapcsolat jön létre a meglévő internetkapcsolaton keresztül, de az Azure VPN Gateway a kapcsolódó díjszabás és a kimenő adatforgalom díjszabását az használatát igényli. Az alábbi táblázatban áttekintheti, és a hátrányai, ez a kapcsolattípus.

| **Előnyök** | **Megfontolások** |
| --- | --- |
| Minden eszköz a helyszíni hálózat egy virtuális hálózathoz kapcsolódik, így nem kell minden eszközhöz egyedi kapcsolatok konfigurálása Azure-szolgáltatások kommunikálhat. |Forgalom IPSec használatával titkosított, bár azt is megszerezhetik az átvitel során óta bejárja a nyilvános interneten. |
| Használja a meglévő internetkapcsolat. |Előre nem látható késés, mert a kapcsolat is járja az interneten. |
| Azure-szolgáltatások, mely csatlakoztatható egy virtuális hálózathoz, például a virtuális gépek és Felhőszolgáltatások való csatlakozáshoz használható. |Konfigurálására és felügyeletére egy ellenőrzött VPN eszköz * helyszíni. |
| Átviteli sebesség akár 200 Mb/s / átjáró. |Folyamatban lévő minimális felügyelet mellett az Azure VPN-átjáró szükséges. |
| Kényszerítheti a kimenő forgalom kezdeményezése a felhőbeli virtuális gépekhez, ellenőrzés és naplózás, felhasználó által megadott útvonalakat vagy a Border Gateway Protocol (BGP) használatával a helyszíni hálózaton keresztül **. |Nem használható a Microsoft Office 365 vagy Dynamics CRM Online-hoz való csatlakozáshoz. |
| Nem használható az Azure-szolgáltatásokhoz, amely nem csatlakozik virtuális hálózathoz. | |
| Ha a szolgáltatások vissza a helyszíni eszközök kapcsolatokat használ, és a biztonsági szabályzatokat szükség van rá, szükség lehet a helyszíni hálózat és az Azure között tűzfal. | |

* Megtekintheti listáját [ellenőrzött VPN-eszközök](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable).
* ** Használatáról további [felhasználó által megadott útvonalak](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) vagy [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) útválasztás az Azure virtuális hálózat egy helyszíni eszköz a kényszerítéséhez.

## <a name="connecting-with-a-dedicated-private-connection"></a>Egy dedikált privát kapcsolaton csatlakoztatása
Ez a kapcsolattípus hozzáférést biztosít a Microsoft összes felhőalapú szolgáltatásához egy dedikált privát kapcsolaton keresztül, amely sem halad át az interneten, a Microsoft alább látható módon.

![ER](./media/guidance-connecting-your-on-premises-network-to-azure/er.png "ExpressRoute-kapcsolat")

A kapcsolat az ExpressRoute szolgáltatással és a egy kapcsolat kapcsolati használatát igényli. Az alábbi táblázatban áttekintheti, és a hátrányai, ez a kapcsolattípus.

| **Előnyök** | **Megfontolások** |
| --- | --- |
| Forgalom nem foghatók el az átvitel során a nyilvános interneten keresztül, mert egy dedikált kapcsolaton keresztül egy szolgáltató szolgál. |A helyszíni útválasztót kezeléséhez szükséges. |
| Akár 10 Gb/s / ExpressRoute-kapcsolatcsoportot, és akár 2 Gb/s minden átjáró átviteli sávszélesség. |A kapcsolatszolgáltató dedikált kapcsolatot igényel. |
| Kiszámítható késés dedikált kapcsolat a Microsoft, sem halad át az internethez, mert. |Egy vagy több Azure VPN Gateway átjárók minimális folyamatos felügyelet lehet szükség, (Ha a kapcsolatcsoport csatlakoztatása virtuális hálózatokhoz). |
| Titkosíthatja a forgalomra, ha szükséges, ha nincs szükség a titkosított kommunikáció. |Ha a cloud services vissza a helyszíni eszközök kapcsolatokat használ, szükség lehet a helyszíni hálózat és az Azure között tűzfal. |
| Közvetlenül is csatlakozhat a Microsoft összes felhőalapú szolgáltatásához, és néhány kivétel *. |Hálózati címfordítás (NAT) igényel a helyszíni IP-címeket adjon meg a Microsoft adatközpontjainak a szolgáltatások, amelyek nem lehet csatlakozni egy VNet.* * |
| Kényszerítheti a kimenő forgalom kezdeményezése a felhőbeli virtuális gépekhez, ellenőrzés és naplózás, a BGP használatával a helyszíni hálózaton keresztül. | |

* * Megtekintése egy [szolgáltatások listája](../expressroute/expressroute-faqs.md#supported-services) , amely nem használható az expressroute-tal. Az Azure-előfizetéshez szeretne csatlakozni az Office 365 jóvá kell hagyni.  Tekintse meg a [Office 365-höz készült Azure ExpressRoute](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1) részleteivel.
* ** Tudjon meg többet az ExpressRoute [NAT](../expressroute/expressroute-nat.md) követelményeinek.

Tudjon meg többet [ExpressRoute](../expressroute/expressroute-introduction.md), hozzá van rendelve [díjszabás](https://azure.microsoft.com/pricing/details/expressroute), és [kapcsolatszolgáltatók](../expressroute/expressroute-locations.md#locations).

## <a name="additional-considerations"></a>Néhány fontos megjegyzés
* Különféle, a virtuális hálózatok közötti kapcsolatok, gateway-kapcsolatokat és egyéb feltételek támogatása is maximális korlátokkal rendelkeznek a fenti lehetőségek közül. Javasoljuk, hogy tekintse át az Azure [hálózatkezelési korlátok](../azure-subscription-service-limits.md#networking-limits) megértéséhez, ha ezek közül bármelyik hatással van a kapcsolattípus használja.
* Ha azt tervezi, a site-to-site VPN-kapcsolat átjáró csatlakoztatása egy ExpressRoute-átjárót, ugyanazon a vneten, meg kell ismerkednie a fontos megkötések először. Tekintse meg a [konfigurálása ExpressRoute- és helyek közötti egyidejű kapcsolatok](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations) további részleteivel.

## <a name="next-steps"></a>További lépések
Az alábbi erőforrások megvalósítása a kapcsolattípusok közül a cikkben szereplő ismertetik.

* [A pont – hely kapcsolat megvalósítása](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Alkalmazzon egy helyek közötti kapcsolat](guidance-hybrid-network-vpn.md)
* [Alkalmazzon egy dedikált privát kapcsolaton](guidance-hybrid-network-expressroute.md)
* [Magas rendelkezésre állású helyek közötti kapcsolattal egy dedikált privát kapcsolaton megvalósítása](guidance-hybrid-network-expressroute-vpn-failover.md)
