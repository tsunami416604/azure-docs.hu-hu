---
title: Az Azure ExpressRoute-Kapcsolatcsoportok és útválasztási tartományok |} Microsoft Docs
description: Ezen a lapon egy áttekintést nyújt az ExpressRoute-Kapcsolatcsoportok és útválasztási tartományok.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
ms.assetid: 6f0c5d8e-cc60-4a04-8641-2c211bda93d9
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: ganesr,cherylmc
ms.openlocfilehash: be04a1cd723cf27e764daa468607d6495baf0291
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849930"
---
# <a name="expressroute-circuits-and-routing-domains"></a>ExpressRoute-Kapcsolatcsoportok és útválasztási tartományok
 Kell rendeznie egy *ExpressRoute-kapcsolatcsoportot* a helyszíni infrastruktúra csatlakozni a Microsoft a kapcsolat-szolgáltató használatával. Az alábbi ábrán a WAN és a Microsoft közötti kapcsolat egy logikai ábrázolását.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

## <a name="expressroute-circuits"></a>ExpressRoute-kapcsolatcsoportok
Egy *ExpressRoute-kapcsolatcsoportot* jelenti. a helyszíni infrastruktúra és a kapcsolat-szolgáltató használatával a Microsoft felhőszolgáltatásai között. Több ExpressRoute-Kapcsolatcsoportok sorba rendezheti. A kapcsolatok azonos vagy különböző régiókban lehet, és a helyi kapcsolat különböző szolgáltatók keresztül lehet csatlakoztatni. 

ExpressRoute-Kapcsolatcsoportok nem felelnek meg a fizikai entitások. Expressroute-kapcsolatcsoporthoz egyedileg azonosít egy szabványos GUID neve (s-kulcs) szolgáltatás kulcsként. A szolgáltatás kulcsa az egyetlen adat, Microsoft, a kapcsolat szolgáltatóját, és Ön között. Az s-kulcs nincs titkos kulcs biztonsági okokból. Van egy ExpressRoute-kapcsolatcsoportot és az s-kulcs közötti társítás 1:1.

Egy ExpressRoute-kapcsolatcsoportot rendelkezhet legfeljebb három független esetében: az Azure nyilvános, Azure személyes és a Microsoft. Két független BGP minden társviszony-létesítés munkamenetek minden azok redundantly magas rendelkezésre állásúként konfigurálva. Van egy 1: n (1 < = N < = 3) ExpressRoute-kapcsolatcsoportot közötti megfeleltetés és útválasztási tartományok. Egy ExpressRoute-kapcsolatcsoportot rendelkezhet közül legalább egy, a két vagy ExpressRoute-kapcsolatcsoportot / engedélyezve az összes három esetében.

A kapcsolatok rögzített sávszélességű (50 MB/s, 100 MB/s, 200 MB/s, 500 MB/s, 1 GB/s, 10 GB/s), és a kapcsolat szolgáltatóját, és társviszony-létesítési helye van leképezve. A sávszélesség választja a kör összes társviszony által megosztott. 

### <a name="quotas-limits-and-limitations"></a>Kvóták korlátai és korlátozásai
Alapértelmezett kvótái és korlátai érvényes minden ExpressRoute-kapcsolatcsoportot. Tekintse meg a [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../azure-subscription-service-limits.md) kvóták naprakész információkat lapját.

## <a name="expressroute-routing-domains"></a>ExpressRoute útválasztási tartományok
Egy ExpressRoute-kapcsolatcsoportot több útválasztási tartomány társítva van: az Azure nyilvános, Azure személyes és a Microsoft. Az útválasztási tartományok mindegyikében útválasztók két konfigurált azonos (aktív-aktív vagy terheléselosztási megosztása konfigurációs) a magas rendelkezésre állás érdekében. Azure-szolgáltatások vannak üzletiként besorolva *Azure nyilvános* és *Azure saját* az IP-címzési séma képviseli.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="azure-private-peering"></a>Az Azure privát társviszony-létesítés
Azure számítási szolgáltatások, azaz a virtuális gépek (IaaS), és a magánhálózati társviszony-létesítési tartomány keresztül csatlakozhatnak felhőszolgáltatásokat (PaaS), amely a virtuális hálózaton belül vannak telepítve. A magánhálózati társviszony-létesítési tartomány tekinthető az alaphálózat a Microsoft Azure megbízható kiterjesztését. A központi hálózat és az Azure virtuális hálózatokról (Vnetekről) közötti kétirányú kapcsolatot állíthat be. A társviszony lehetővé teszi a virtuális gépek csatlakoztatásához és a felhőalapú szolgáltatások közvetlenül a saját privát IP-címek.  

Több virtuális hálózat csatlakozhat a magánhálózati társviszony-létesítési tartományhoz. Tekintse át a [gyakori kérdéseket tartalmazó oldal](expressroute-faqs.md) korlátai és korlátozásai információt. Letöltheti a [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../azure-subscription-service-limits.md) lap korlátozások naprakész információkat.  Tekintse meg a [útválasztás](expressroute-routing.md) lap részletes információkat a útválasztási konfigurációjáról.

### <a name="azure-public-peering"></a>Az Azure nyilvános társviszony-létesítés

> [!IMPORTANT]
> Az Azure PaaS szolgáltatások mindegyike a Microsoft társviszony-létesítési szolgáltatáson keresztül érhető el. Javasoljuk a Microsoft társviszony-létesítés létrehozását, és Azure PaaS szolgáltatásokhoz Microsoft társviszony-létesítésen keresztül történő kapcsolódást.  
>   


A nyilvános IP-szolgáltatások, például az Azure tárolás, az SQL-adatbázisok és a webhelyek érhető el. Nyilvános IP-címek, beleértve a virtuális IP-címek a felhőalapú szolgáltatások, a nyilvános társviszony-létesítési útválasztási tartomány segítségével üzemeltetett szolgáltatások közvetlenül kapcsolódni. A nyilvános társviszony-létesítési tartományhoz csatlakozni a Szegélyhálózaton, és csatlakozzon az összes Azure-szolgáltatások a nyilvános IP-címeket az a WAN anélkül, hogy az interneten keresztül csatlakozni. 

Kapcsolat mindig kezdeményezni a WAN Microsoft Azure-szolgáltatásokhoz. Microsoft Azure-szolgáltatások nem tudnak kezdeményeznek kapcsolatokat a hálózatra az útválasztási tartomány segítségével. Nyilvános társviszony engedélyezése után is elérheti az összes Azure-szolgáltatásokhoz. Jelenleg nem engedélyezi, hogy szelektív válasszon, amelynek azt hirdetési útvonalakat szolgáltatások.

Csak a szükséges útvonalakat használ a hálózaton belüli egyéni útvonal szűrőket adhat meg. Tekintse meg a [útválasztás](expressroute-routing.md) lap részletes információkat a útválasztási konfigurációjáról. 

A nyilvános társviszony-létesítési útválasztási tartomány támogatott szolgáltatásokkal kapcsolatos további információkért lásd: a [gyakran ismételt kérdések](expressroute-faqs.md).

### <a name="microsoft-peering"></a>Microsoft társviszony-létesítés
[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Microsoft online szolgáltatásokhoz (Office 365, Dynamics 365 és Azure PaaS szolgáltatások) való kapcsolódás a Microsoft társviszony-létesítés keresztül történik. Engedélyezzük a WAN és a Microsoft felhőszolgáltatások keresztül a Microsoft társviszony-létesítési útválasztási tartomány közötti kétirányú kapcsolatot. Csatlakoztatni kell a Microsoft csak nyilvános IP-címek, amelynek a tulajdonosa, vagy a kapcsolat szolgáltatójánál keresztül, és meg kell felelnie a definiált szabályokat. További információkért lásd: a [ExpressRoute Előfeltételek](expressroute-prerequisites.md) lap.

Tekintse meg a [gyakori kérdéseket tartalmazó oldal](expressroute-faqs.md) további információk a támogatott szolgáltatások, a költségek és a konfigurációs adatait. Tekintse meg a [helyek ExpressRoute](expressroute-locations.md) lap kapcsolat szolgáltatók ajánlat a Microsoft társviszony-létesítés támogatása a listán szereplő információkat.

## <a name="routing-domain-comparison"></a>Útválasztási tartomány összehasonlítása
Az alábbi táblázat összehasonlítja a három útválasztási tartományok:

|  | **Magánhálózati társviszony-létesítés** | **Nyilvános társviszony-létesítés** (az új és elavult) | **A Microsoft társviszony-létesítés** |
| --- | --- | --- | --- |
| **Max. támogatott társviszony-létesítés # előtagok** |Alapértelmezés szerint 10 000-re az ExpressRoute Premium 4000 |200 |200 |
| **Az IP-címtartományok támogatott** |Bármilyen érvényes IP-címet a WAN hálózaton belül. |Nyilvános IP-címet, vagy a kapcsolat szolgáltatóját. |Nyilvános IP-címet, vagy a kapcsolat szolgáltatóját. |
| **Követelmények száma szerint** |Privát és nyilvános SZÁMAIT. A nyilvános kell saját SZÁMOT, ha úgy dönt, hogy egyetlen módszer alkalmazása. |Privát és nyilvános SZÁMAIT. Azonban igazolnia kell tulajdonjogát, a nyilvános IP-címeket. |Privát és nyilvános SZÁMAIT. Azonban igazolnia kell tulajdonjogát, a nyilvános IP-címeket. |
| **Támogatott IP-protokollok**| IPv4 | IPv4 | IPv4, IPv6 |
| **Útválasztó illesztő IP-címek** |RFC1918 és nyilvános IP-címek |Nyilvános IP-címeket az útválasztási nyilvántartó regisztrálva. |Nyilvános IP-címeket az útválasztási nyilvántartó regisztrálva. |
| **Az MD5 kivonatoló támogatása** |Igen |Igen |Igen |



Ha szeretné, legalább egy, az útválasztási tartományok engedélyezéséhez az ExpressRoute-kapcsolatcsoportot részeként. Ha szeretné az útválasztási tartomány elhelyezése a azonos VPN, ha egyetlen útválasztási tartományról való egyesíthetők. Akkor helyezheti is őket másik útválasztási tartományok, a diagram hasonló. Az ajánlott konfiguráció a magánhálózati társviszony-létesítés közvetlenül kapcsolódik a központi hálózathoz, és a nyilvános és a Microsoft társviszony-létesítési hivatkozások csatlakoznak a Szegélyhálózaton.

Ha minden három társviszony-létesítési munkamenetet választja, a BGP-munkamenetek (az egyes társviszony-létesítési egy pár) három párok kell rendelkeznie. A BGP-munkamenet Értékpárokat adjon meg egy magas rendelkezésre állású hivatkozást. Ha réteg 2 kapcsolat szolgáltatók keresztül kapcsolódik, telepítésért felelős, konfigurálásához és kezeléséhez útválasztást. Megtekintésével többet is megtudhat a [munkafolyamatok](expressroute-workflows.md) ExpressRoute beállításához.

## <a name="expressroute-health"></a>Az ExpressRoute-állapot
ExpressRoute-Kapcsolatcsoportok figyelhetik a rendelkezésre állás érdekében a kapcsolatot a virtuális hálózatokat és a sávszélesség kihasználtsága használatával [hálózati Teljesítményfigyelő](https://docs.microsoft.com/en-us/azure/networking/network-monitoring-overview) (NPM).

NPM Azure magánhálózati társviszony-létesítés, és a Microsoft társviszony-létesítés állapotát figyeli.  Tekintse meg a [utáni](https://azure.microsoft.com/en-in/blog/monitoring-of-azure-expressroute-in-preview/) további információt.

## <a name="next-steps"></a>További lépések
* Találjon egy szolgáltatót. Lásd: [ExpressRoute szolgáltatás szolgáltatók és a helyek](expressroute-locations.md).
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).
* Az ExpressRoute-kapcsolat konfigurálása.
  * [ExpressRoute-kapcsolatcsoportok létrehozása és kezelése](expressroute-howto-circuit-portal-resource-manager.md)
  * [Útválasztás (társviszony-létesítés) konfigurálása ExpressRoute-kapcsolatcsoportok számára](expressroute-howto-routing-portal-resource-manager.md)

