---
title: 'Azure ExpressRoute: áramkörök és peering'
description: Ez az oldal ExpressRoute-Kapcsolatcsoportok és útválasztási tartományok/társviszony-létesítés áttekintést nyújt.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mialdrid
ms.openlocfilehash: c68ffd019937f902567c3deda8d879448dc082da
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281352"
---
# <a name="expressroute-circuits-and-peering"></a>Az ExpressRoute-Kapcsolatcsoportok és a társviszony-létesítés

Az ExpressRoute-Kapcsolatcsoportok a helyszíni infrastruktúra csatlakoztatni a Microsoft egy kapcsolatszolgáltatón keresztül. Ez a cikk segítségével megismerheti az ExpressRoute-Kapcsolatcsoportok és útválasztási tartományok/társviszony-létesítés. A következő ábrán látható a WAN és a Microsoft közötti kapcsolat logikai megfelelője.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Az Azure nyilvános társ-kezelési szolgáltatás elavult, és nem érhető el az új ExpressRoute-áramkörökhöz. Az új áramkörök támogatják a Microsoft társ-és magánszféra általi használatát.  
>

## <a name="circuits"></a>ExpressRoute-áramkörök

ExpressRoute-kapcsolatcsoport a helyszíni infrastruktúra és a egy kapcsolatszolgáltatón keresztül a Microsoft-felhőszolgáltatások közötti logikai kapcsolatot jelöli. Több ExpressRoute-Kapcsolatcsoportok rendezheti. Minden kapcsolatnak az azonos vagy eltérő régiókban is lehetnek, és a helyszíni eredetű különböző kapcsolatszolgáltatók keresztül lehet csatlakoztatni.

Az ExpressRoute-Kapcsolatcsoportok nem feleltethető meg a fizikai entitások. Kapcsolatcsoport egyedileg azonosít egy GUID nevű szolgáltatás kulcsként (s-kulcs), standard. A kulcs az egyetlen adat, Microsoft, a kapcsolatszolgáltató és Ön között. Nincs titkos kulcs biztonsági okokból az s-kulcsot. Van egy 1:1 megfeleltetését között egy ExpressRoute-kapcsolatcsoportot, és az s-kulcsot.

Új ExpressRoute-kapcsolatcsoportot is tartalmazhat két független társviszonyok: magánhálózati társviszony-létesítés és Microsoft társviszony-létesítés. Mivel a meglévő ExpressRoute-Kapcsolatcsoportok tartalmazhat mindhárom társviszony-létesítést: Azure-beli nyilvános, az Azure privát és a Microsoft. Minden társviszony két független BGP-munkamenetek, azok redundantly magas rendelkezésre állásúként konfigurálva. Van egy 1: n (1 < = N < = 3) az ExpressRoute-Kapcsolatcsoportok közötti megfeleltetés és útválasztási tartományok. ExpressRoute-kapcsolatcsoport is rendelkezik egy, kettő vagy engedélyezve van az ExpressRoute-kapcsolatcsoportonként minden három társviszony.

Minden egyes kapcsolatcsoport rögzített sávszélességű (50 MB/s, 100 MB/s, 200 MB/s, 500 MB/s, 1 GB/s, 10 GB/s), és le van képezve egy kapcsolatszolgáltatón és a egy társviszony-létesítési helyszínen. A sávszélesség választja közösen használja minden kapcsolatcsoport társviszony

### <a name="quotas"></a>Kvóták, korlátozások és korlátozások

Alapértelmezett kvótái és korlátozásai érvényesek minden egyes ExpressRoute-kapcsolatcsoporthoz. Tekintse meg az [Azure-előfizetések és-szolgáltatások korlátozásait, kvótáit és megkötéseit](../azure-resource-manager/management/azure-subscription-service-limits.md) ismertető oldalt a kvótákkal kapcsolatos naprakész információkhoz.

## <a name="routingdomains"></a>ExpressRoute-társítás

ExpressRoute-kapcsolatcsoport több útválasztási tartományok/társviszonyok társítva van: az Azure nyilvános, az Azure privát és a Microsoft. Minden társviszony-létesítés konfigurálva van azonos módon egy útválasztókat pár (aktív – aktív vagy a betöltés megosztása a konfiguráció) a magas rendelkezésre állás érdekében. Az Azure-szolgáltatások az IP-címzési sémákat képviselő Azure-beli *nyilvános* és *Azure Private* -ként vannak kategorizálva.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="privatepeering"></a>Azure-beli privát peering

Azure számítási szolgáltatások, azaz a virtuális gépek (IaaS), és a egy virtuális hálózaton belül üzembe helyezett cloud services (PaaS), a privát társviszony-létesítési tartományon keresztül csatlakozhat. A privát társviszony-létesítési tartomány egy megbízható kiterjesztése, a Microsoft Azure-bA az alaphálózat minősül. Beállíthatja a kétirányú kapcsolatokat a központi hálózat és az Azure virtuális hálózatok (Vnetek) között. A társviszony lehetővé teszi, hogy csatlakozhat a virtuális gépek és felhőszolgáltatások közvetlenül a saját magánhálózati IP-címek.  

A privát társviszony-létesítési tartomány egynél több virtuális hálózat kapcsolódhat. A korlátozásokkal és korlátozásokkal kapcsolatos információkért tekintse át a [GYIK oldalt](expressroute-faqs.md) . Az [Azure-előfizetések és-szolgáltatások korlátozásait, kvótáit és megkötéseit](../azure-resource-manager/management/azure-subscription-service-limits.md) ismertető oldalt megtekintheti a korlátozásokkal kapcsolatos naprakész információkhoz.  Az útválasztási konfigurációval kapcsolatos részletes információkért tekintse meg az [Útválasztás](expressroute-routing.md) lapot.

### <a name="microsoftpeering"></a>Microsoft-partnerek

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

A Microsoft online szolgáltatásokhoz (Office 365 és az Azure Pásti Services) való kapcsolódás a Microsoft-partneri kapcsolaton keresztül történik. Engedélyezzük a kétirányú kapcsolatokat a WAN és a Microsoft cloud services révén a Microsoft társviszony-létesítési útválasztási tartomány között. Csak keresztül, vagy a kapcsolatszolgáltató tulajdonában lévő nyilvános IP-címek és a Microsoft felhőszolgáltatásai csatlakoznia kell, és az összes meghatározott szabálynak meg kell felelnie. További információkért tekintse meg a [ExpressRoute előfeltételek](expressroute-prerequisites.md) oldalát.

A támogatott szolgáltatásokkal, a költségekkel és a konfigurációs adatokkal kapcsolatos további információkért tekintse meg a [GYIK oldalt](expressroute-faqs.md) . A Microsoft társközi támogatását kínáló kapcsolati szolgáltatók listáját a [ExpressRoute helye](expressroute-locations.md) oldalon tekintheti meg.

## <a name="peeringcompare"></a>Egyenrangú összehasonlítás

Az alábbi táblázat összehasonlítja a mindhárom társviszony-létesítést:

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

Egy vagy több útválasztási tartománynak lehet, hogy engedélyezi az ExpressRoute-kapcsolatcsoport részeként. Ha szeretné az útválasztási tartományok az azonos VPN elhelyezése, ha azt szeretné, úgy, hogy egyetlen útválasztási tartomány rendelkezik. Helyezheti őket a különböző útválasztási tartományok, a diagram hasonló. Az ajánlott konfigurációs, hogy a privát társviszony-létesítés közvetlenül kapcsolódik az alapvető hálózatra, és a nyilvános és Microsoft társviszony-létesítési csatolások csatlakozik a DMZ-t.

Minden társviszony-létesítés külön BGP-munkamenetek (mindegyik társviszony-létesítési típus egy pár) van szükség. A BGP-munkamenet párok adja meg a magas rendelkezésre állású hivatkozást. Réteg 2 kapcsolatszolgáltatók keresztül kapcsolódik, ha Ön felelős útválasztási konfigurálásának és kezelésének. További információ: a ExpressRoute beállításához szükséges [munkafolyamatok](expressroute-workflows.md) áttekintése.

## <a name="health"></a>ExpressRoute állapota

A ExpressRoute-áramköröket a rendelkezésre állás, a virtuális hálózatok és a sávszélesség-használat között a [Network Performance monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) használatával lehet figyelni.

Npm-et az Azure privát társviszony-létesítés és a Microsoft társviszony-létesítés állapotát figyeli. További információért tekintse meg a [hozzászólását](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) .

## <a name="next-steps"></a>Következő lépések

* Találjon egy szolgáltatót. Lásd: [ExpressRoute Service Providers és Locations](expressroute-locations.md).
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).
* Az ExpressRoute-kapcsolat konfigurálása.
  * [ExpressRoute-kapcsolatcsoportok létrehozása és kezelése](expressroute-howto-circuit-portal-resource-manager.md)
  * [Útválasztás (társviszony-létesítés) konfigurálása ExpressRoute-kapcsolatcsoportok számára](expressroute-howto-routing-portal-resource-manager.md)
