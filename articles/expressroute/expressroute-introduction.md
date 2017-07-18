---
title: "Az ExpressRoute áttekintése: A helyszíni hálózat kiterjesztése az Azure-ra privát kapcsolaton keresztül | Microsoft Docs"
description: "Az ExpressRoute technikai áttekintése ismerteti, hogyan terjeszthető ki a helyszíni hálózatát az Azure-ra egy ExpressRoute-kapcsolattal egy privát kapcsolaton keresztül."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: fd95dcd5-df1d-41d6-85dd-e91d0091af05
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/14/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: d1e513933ea647a1afe9a4eb214bb9216d3bb20a
ms.contentlocale: hu-hu
ms.lasthandoff: 07/06/2017

---
# <a name="expressroute-overview"></a>ExpressRoute – áttekintés
A Microsoft Azure ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft-felhőbe egy privát kapcsolaton keresztül, amelyet egy kapcsolatszolgáltató biztosít. Az ExpressRoute használatával kapcsolatokat létesíthet olyan Microsoft-felhőszolgáltatásokkal, mint például a Microsoft Azure, az Office 365 és a Dynamics 365.

A kapcsolatok lehetnek: bármely elemek közötti (IP VPN) hálózat, pontok közötti Ethernet-hálózat vagy egy virtuális keresztkapcsolat egy kapcsolatszolgáltatón keresztül egy közös elhelyezési létesítményben. Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. Így az ExpressRoute-kapcsolatok a tipikus internetes kapcsolatoknál megbízhatóbbak, gyorsabbak, gyorsabb a válaszidejük, és biztonságosabbak. További információk a hálózat a Microsofthoz való csatlakoztatásáról az ExpressRoute használatával: [ExpressRoute kapcsolati modellek](expressroute-connectivity-models.md).

![](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>Főbb előnyök

* 3 rétegbeli kapcsolatok a helyszíni hálózat és a Microsoft Cloud közt egy kapcsolatszolgáltatón keresztül. A kapcsolatok lehetnek: bármely elemek közötti (IPVPN) hálózat, pontok közötti Ethernet-kapcsolat vagy egy virtuális keresztkapcsolat egy Ethernet-adatcserélőn keresztül.
* Kapcsolódás a Microsoft-felhőszolgáltatásokhoz az adott geopolitikai régió minden régiójában.
* Globális kapcsolódás a Microsoft-szolgáltatásokhoz az összes régióban az ExpressRoute prémium bővítmény használatával.
* Dinamikus útválasztás a hálózata és a Microsoft közt iparági szabványnak megfelelő protokollokon (BGP) keresztül.
* Beépített redundancia minden társviszony-létesítési helyszínen a nagyobb megbízhatóság érdekében.
* Kapcsolat-üzemidőre vonatkozó [SLA](https://azure.microsoft.com/support/legal/sla/).
* QoS-támogatás a Skype Vállalati verziójához.

További információ: [ExpressRoute GYIK](expressroute-faqs.md).

## <a name="features"></a>Szolgáltatások

### <a name="layer-3-connectivity"></a>3. rétegbeli kapcsolatok
A Microsoft iparági szabványnak megfelelő dinamikus útválasztási protokollt (BGP) alkalmaz az útvonalak cseréjéhez a helyszíni hálózat, az Azure-ban lévő példányai és a Microsoft nyilvános címek között.  Több BGP-munkamenetet létesítünk a hálózattal, különböző forgalomprofilokkal. További részletek az [ExpressRoute circuit and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-kapcsolatcsoportok és útválasztási tartományok) című cikkben találhatók.

### <a name="redundancy"></a>Redundancia
Minden egyes ExpressRoute-kapcsolatcsoport két kapcsolattal rendelkezik két Microsoft Enterprise peremhálózati útválasztóhoz (MSEE) a kapcsolatszolgáltatótól / a hálózata peremétől. A Microsoft kettős BGP-kapcsolatot igényel a kapcsolatszolgáltatótól / az Ön oldaláról – egyet-egyet mindkét MSEE-hez. Dönthet úgy, hogy nem helyez üzembe redundáns eszközöket / Ethernet-kapcsolatcsoportokat az Ön oldalán. A kapcsolatszolgáltatók azonban redundáns eszközöket használnak annak biztosítása érdekében, hogy az Ön kapcsolatai redundáns módon jussanak el a Microsofthoz. Az [SLA](https://azure.microsoft.com/support/legal/sla/) érvényességének előfeltétele a redundáns 3. rétegbeli kapcsolódási konfiguráció.

### <a name="connectivity-to-microsoft-cloud-services"></a>Kapcsolódás a Microsoft-felhőszolgáltatásokhoz
[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Az ExpressRoute-kapcsolatok a következő szolgáltatásokhoz biztosítanak hozzáférést:

* Microsoft Azure-szolgáltatások
* Microsoft Office 365-szolgáltatások
* Microsoft Dynamics 365

Az ExpressRoute-on keresztül támogatott szolgáltatások részletes listáját az [ExpressRoute – Gyakori kérdések](expressroute-faqs.md) oldalon tekintheti meg.

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Kapcsolódás az összes régióhoz egy geopolitikai régión belül
Csatlakozhat a Microsofthoz a [társviszony-létesítési helyszínek](expressroute-locations.md) egyikén, és elérheti az összes régiót a geopolitikai régióban. 

Ha például Amszterdamban csatlakozik a Microsofthoz az ExpressRoute-on keresztül, az Észak-Európában és a Nyugat-Európában üzemeltetett összes Microsoft-felhőszolgáltatáshoz hozzáférhet. A geopolitikai régiók, a hozzájuk rendelt Microsoft felhő-régiók és a megfelelő ExpressRoute társviszony-létesítési helyszínek áttekintését az [ExpressRoute partnerek és társviszony-létesítési helyszínek](expressroute-locations.md) oldalon találja.

### <a name="global-connectivity-with-expressroute-premium-add-on"></a>Globális kapcsolódás az ExpressRoute prémium bővítmény használatával
Az ExpressRoute prémium bővítménnyel kiterjesztheti a kapcsolódási lehetőségeket a geopolitikai határokon túl. Ha például Amsterdamban csatlakozik a Microsofthoz az ExpressRoute-on keresztül, a világszerte az összes régióban (az országos felhők kivételével) üzemeltetett összes Microsoft-felhőszolgáltatáshoz hozzáféréssel fog rendelkezni. A Dél-Amerikában vagy Ausztráliában üzemeltetett szolgáltatásokat ugyanúgy eléri majd, mint az észak- vagy nyugat-európai régióban lévőket.

### <a name="rich-connectivity-partner-ecosystem"></a>A kapcsolati partnerek gazdag ökoszisztémája
Az ExpressRoute kapcsolati és SI-partnerek egyre bővülő ökoszisztémájával rendelkezik. A legfrissebb információkért lásd az [ExpressRoute-szolgáltatókat és helyeket](expressroute-locations.md) ismertető cikket.

### <a name="connectivity-to-national-clouds"></a>Kapcsolódás országos felhőkhöz
A Microsoft elkülönített felhőkörnyezeteket tart fenn speciális geopolitikai régiók és ügyfélszegmensek számára. Az országos felhők és szolgáltatók listájáért lásd az [ExpressRoute-szolgáltatókat és -helyeket](expressroute-locations.md) ismertető lapot.

### <a name="bandwidth-options"></a>Sávszélesség-lehetőségek
A sávszélességek széles választékához vásárolhat ExpressRoute-kapcsolatcsoportokat. A támogatott sávszélességeket az alábbi lista tartalmazza. Mindenképp egyeztessen kapcsolatszolgáltatójával a támogatott sávszélességekről.

* 50 Mbps
* 100 Mbps
* 200 Mbps
* 500 Mbps
* 1 Gbps
* 2 Gbps
* 5 Gbps
* 10 Gbps

### <a name="dynamic-scaling-of-bandwidth"></a>Dinamikus sávszélesség-méretezés
Az ExpressRoute-kapcsolatcsoport sávszélességét (az elérhető legjobb lehetőség alapján) anélkül növelheti, hogy le kellene bontania a kapcsolatokat. 

### <a name="flexible-billing-models"></a>Rugalmas számlázási modellek
Kiválaszthatja az Ön számára optimális számlázási modellt. Az alábbi számlázási modellek közül választhat. További információ: [ExpressRoute GYIK](expressroute-faqs.md).

* **Korlátlan adatforgalom**. Az ExpressRoute-kapcsolatcsoport számlázása havi díj alapján történik, amely az összes bejövő és kimenő adatátvitelt ingyenesen tartalmazza. 
* **Forgalmi díjas adatforgalom**. Az ExpressRoute-kapcsolatcsoport számlázása havi díj alapján történik. Az összes bejövő adatátvitel ingyenes. A kimenő adatátvitel számlázása az átvitt adatok GB-jai alapján történik. Az adatátviteli árak régiónként eltérnek.
* **ExpressRoute prémium bővítmény**. Az ExpressRoute prémium az ExpressRoute-kapcsolatcsoport bővítménye. Az ExpressRoute prémium bővítmény az alábbi képességeket biztosítja: 
  * Az Azure nyilvános és az Azure privát társviszony-létesítés útvonalkorlátja 4000 útvonalról 10 000 útvonalra nő.
  * Globális kapcsolódás a szolgáltatásokhoz. A bármely régióban (az országos felhők kivételével) létrehozott ExpressRoute-kapcsolatcsoportok a világ bármely más régiójában elérik az erőforrásokat. Egy Nyugat-Európában létrehozott virtuális hálózat például elérhető egy, a Szilícium-völgyben kiosztott ExpressRoute-kapcsolatcsoportról is.
  * A VNet-hivatkozások száma ExpressRoute-kapcsolatcsoportonként 10-ről egy magasabb korlátra nő a kör sávszélességének függvényében.

## <a name="faq"></a>GYIK

Az ExpressRoute-ra vonatkozó gyakori kérdésekért lásd az [ExpressRoute gyakori kérdéseit](expressroute-faqs.md).

## <a name="next-steps"></a>Következő lépések

* [Az ExpressRoute kapcsolati modelljeinek](expressroute-connectivity-models.md) ismertetése.
* Ismerje meg az ExpressRoute-kapcsolatokat és útválasztási tartományokat. Lásd: [ExpressRoute-kapcsolatcsoportok és útválasztási tartományok](expressroute-circuit-peerings.md).
* Találjon egy szolgáltatót. Lásd: [ExpressRoute-partnerek és társviszony-létesítési helyszínek](expressroute-locations.md).
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).
* Tekintse meg az [Útválasztás](expressroute-routing.md), a [NAT](expressroute-nat.md) és a [QoS](expressroute-qos.md) követelményeit.
* Az ExpressRoute-kapcsolat konfigurálása.
  * [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-portal-resource-manager.md)
  * [Az ExpressRoute-kapcsolatcsoport társviszony-létesítésének konfigurálása](expressroute-howto-routing-portal-resource-manager.md)
  * [Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal](expressroute-howto-linkvnet-portal-resource-manager.md)

