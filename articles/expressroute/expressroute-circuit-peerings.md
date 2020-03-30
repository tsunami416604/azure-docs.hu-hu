---
title: 'Azure ExpressRoute: áramkörök és társviszony-létesítés'
description: Ez a lap áttekintést nyújt az ExpressRoute-áramkörökről és az útválasztási tartományokról/társviszony-létesítésről.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mialdrid
ms.openlocfilehash: c68ffd019937f902567c3deda8d879448dc082da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281352"
---
# <a name="expressroute-circuits-and-peering"></a>ExpressRoute-kapcsolatcsoportok és -társviszonyok

Az ExpressRoute-áramkörök egy kapcsolatszolgáltatón keresztül csatlakoztatják a helyszíni infrastruktúrát a Microsofthoz. Ez a cikk segít megérteni ExpressRoute-áramkörök és útválasztási tartományok/társviszony-létesítés. Az alábbi ábra a WAN és a Microsoft közötti kapcsolat logikai ábrázolását mutatja be.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Az Azure nyilvános társviszony-létesítése elavult, és nem érhető el az új ExpressRoute-áramkörök. Az új áramkörök támogatják a Microsoft társ- és társviszony-létesítését.  
>

## <a name="expressroute-circuits"></a><a name="circuits"></a>ExpressRoute-kapcsolatcsoportok

Az ExpressRoute-áramkör a helyszíni infrastruktúra és a Microsoft felhőszolgáltatásai közötti logikai kapcsolatot jelöli egy kapcsolatszolgáltatón keresztül. Több ExpressRoute-áramkört is rendelhet. Minden kapcsolatcsoport lehet ugyanabban a régióban, és különböző kapcsolatszolgáltatókon keresztül csatlakoztatható a helyiséghez.

Az ExpressRoute-áramkörök nem képeznek le semmilyen fizikai entitást. Az áramkört egy szolgáltatáskulcsként (s-kulcsként) nevezett szabványos GUID azonosítja. A szolgáltatáskulcs az egyetlen olyan információ, amelyet a Microsoft, a kapcsolódási szolgáltató és Ön cserél. Az s-kulcs biztonsági okokból nem titok. Az ExpressRoute-kapcsolat és az s-kulcs között 1:1-es leképezés van.

Az új ExpressRoute-áramkörök két független társviszony-létesítést tartalmazhatnak: a privát társviszony-létesítést és a Microsoft-társviszony-létesítést. Mivel a meglévő ExpressRoute-áramkörök tartalmazhatnak három társviszony-létesítési: Azure Public, Azure Private és a Microsoft. Minden társviszony-létesítés egy pár független BGP-munkamenetek, mindegyik redundánsan konfigurálva a magas rendelkezésre állás. 1:N (1 <= N <= 3) leképezés van az ExpressRoute-kapcsolati és útválasztási tartományok között. Az ExpressRoute-áramkör expressroute-kapcsolatonként egy, kettő vagy mindhárom társviszony-létesítés engedélyezett lehet.

Minden kapcsolatcsoport rögzített sávszélességgel rendelkezik (50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 10 Gbps), és egy kapcsolatszolgáltatóhoz és egy társviszony-létesítési helyhez van leképezve. A kiválasztott sávszélesség meg van osztva az összes áramköri társviszony-létesítés között

### <a name="quotas-limits-and-limitations"></a><a name="quotas"></a>Kvóták, korlátok és korlátozások

Az alapértelmezett kvóták és korlátok minden ExpressRoute-kapcsolatra vonatkoznak. A kvótákkal kapcsolatos naprakész információkat az [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások](../azure-resource-manager/management/azure-subscription-service-limits.md) lapon találja.

## <a name="expressroute-peering"></a><a name="routingdomains"></a>ExpressRoute-társviszony-létesítés

Az ExpressRoute-áramkörhöz több útválasztási tartomány/társviszony-létesítés tartozik: nyilvános Azure, Azure private és Microsoft. Minden társviszony-létesítés azonos módon van konfigurálva egy útválasztópáron (aktív-aktív vagy terhelésmegosztási konfigurációban) a magas rendelkezésre állás érdekében. Az Azure-szolgáltatások *azure-ként, nyilvános* és *azure-beli azure-ként* vannak kategorizálva az IP-címzési sémák képviseletére.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="azure-private-peering"></a><a name="privatepeering"></a>Azure privát társviszony-létesítés

Az Azure számítási szolgáltatások, nevezetesen a virtuális gépek (IaaS) és a felhőszolgáltatások (PaaS), amelyek egy virtuális hálózaton belül vannak telepítve, a privát társviszony-létesítési tartományon keresztül csatlakoztathatók. A privát társviszony-létesítési tartomány a törzshálózat megbízható kiterjesztésének minősül a Microsoft Azure-ban. Kétirányú kapcsolatot állíthat be a törzshálózat és az Azure virtuális hálózatok (Virtuális hálózatok) között. Ez a társviszony-létesítés lehetővé teszi, hogy közvetlenül a saját IP-címükön csatlakozzon a virtuális gépekhez és a felhőszolgáltatásokhoz.  

A privát társviszony-létesítési tartományhoz több virtuális hálózatot is csatlakoztathat. A korlátozásokkal és korlátozásokkal kapcsolatos információkért tekintse át a [GYIK oldalt.](expressroute-faqs.md) Az [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások](../azure-resource-manager/management/azure-subscription-service-limits.md) lapon naprakész információkat a korlátokat.  Az útválasztási konfigurációval kapcsolatos részletes információkat az [Útválasztás](expressroute-routing.md) lapon találja.

### <a name="microsoft-peering"></a><a name="microsoftpeering"></a>Microsoft társviszony-létesítés

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

A Microsoft online szolgáltatásaihoz (Office 365- és Azure PaaS-szolgáltatásokhoz) való kapcsolódás a Microsoft társviszony-létesítésén keresztül történik. A Microsoft társviszony-elintézési útválasztási tartományán keresztül kétirányú kapcsolatot engedélyezünk a WAN és a Microsoft felhőszolgáltatásai között. A Microsoft felhőszolgáltatásaihoz csak az Ön vagy a kapcsolódási szolgáltató tulajdonában lévő nyilvános IP-címeken kapcsolódjon, és be kell tartania az összes meghatározott szabályt. További információt az [ExpressRoute előfeltételei](expressroute-prerequisites.md) lapon talál.

A támogatott szolgáltatásokkal, költségekkel és konfigurációval kapcsolatos további információkért tekintse meg a [GYIK oldalt.](expressroute-faqs.md) A Microsoft társviszony-létesítési támogatást nyújtó kapcsolatszolgáltatók listájáról az [ExpressRoute-helyek](expressroute-locations.md) lapon talál.

## <a name="peering-comparison"></a><a name="peeringcompare"></a>Társviszony-létesítés összehasonlítása

Az alábbi táblázat a három társviszony-létesítést hasonlítja össze:

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

Az ExpressRoute-kapcsolat egy vagy több útválasztási tartományát engedélyezheti. Ha egyetlen útválasztási tartományba szeretné egyesíteni az összes útválasztási tartományt, beállíthatja, hogy az összes útválasztási tartomány ugyanazon a VPN-en legyen. A diagramhoz hasonlóan különböző útválasztási tartományokba is helyezheti őket. Az ajánlott konfiguráció az, hogy a privát társviszony-létesítés közvetlenül kapcsolódik a törzshálózathoz, és a nyilvános és a Microsoft társviszony-létesítési kapcsolatok kapcsolódnak a DMZ-hez.

Minden társviszony-létesítéshez külön BGP-munkamenetszükséges (minden társviszony-létesítési típushoz egy pár). A BGP-munkamenetpárok magas rendelkezésre állású hivatkozást biztosítanak. Ha a 2. További információ az ExpressRoute beállításához vezető [munkafolyamatok](expressroute-workflows.md) áttekintésével.

## <a name="expressroute-health"></a><a name="health"></a>ExpressRoute állapota

Az ExpressRoute-áramkörök a [hálózati teljesítményfigyelő](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) használatával figyelhetők a rendelkezésre állás, a virtuális hálózatokhoz való kapcsolódás és a sávszélesség-kihasználtság miatt.

Az NPM figyeli az Azure privát társviszony-létesítésének és a Microsoft-társviszony-létesítés állapotát. Nézze meg [a post](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) további információkért.

## <a name="next-steps"></a>További lépések

* Találjon egy szolgáltatót. Lásd: [ExpressRoute-szolgáltatók és helyek](expressroute-locations.md).
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).
* Az ExpressRoute-kapcsolat konfigurálása.
  * [ExpressRoute-kapcsolatcsoportok létrehozása és kezelése](expressroute-howto-circuit-portal-resource-manager.md)
  * [Útválasztás (társviszony-létesítés) konfigurálása ExpressRoute-kapcsolatcsoportok számára](expressroute-howto-routing-portal-resource-manager.md)
