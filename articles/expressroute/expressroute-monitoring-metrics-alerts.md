---
title: 'Azure ExpressRoute: figyelés, mérőszámok és riasztások'
description: Ismerje meg az Azure ExpressRoute-figyelést, metrikákat és riasztásokat a Azure Monitor használatával, amely az Azure-ban az összes mérőszámot, riasztást és diagnosztikai naplót használja.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: how-to
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 49e5acb7fc0cfe947d846f2943fb5071d6554ea5
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192477"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Az ExpressRoute monitorozása, mérőszámai és riasztásai

Ez a cikk segít megérteni a ExpressRoute-figyelést, metrikákat és riasztásokat a Azure Monitor használatával. Azure Monitor az összes Azure-beli mérőszámok, riasztások és diagnosztikai naplók egyikének leállítása.
 
>[!NOTE]
>A **klasszikus metrikák** használata nem ajánlott.
>

## <a name="expressroute-metrics"></a>ExpressRoute metrikák

A **metrikák**megtekintéséhez navigáljon a *Azure monitor* lapra, és kattintson a *metrikák*elemre. Az **ExpressRoute** -metrikák megtekintéséhez a szűrés erőforrás típusú *ExpressRoute-áramkörök*alapján. **Global REACH** mérőszámok megtekintéséhez válassza az erőforrás típusa ExpressRoute- *áramkörök* lehetőséget, és válasszon ki egy Global REACH engedélyezett ExpressRoute-áramköri erőforrást. A **közvetlen ExpressRoute** -metrikák megtekintéséhez *ExpressRoute-portok*alapján szűrheti az erőforrás típusát. 

Ha kijelöl egy mérőszámot, a rendszer az alapértelmezett összesítést alkalmazza. Igény szerint a felosztást is alkalmazhatja, amely a metrikát különböző dimenziókkal jeleníti meg.

### <a name="available-metrics"></a>Elérhető metrikák
|**Metrika**|**Kategória**|**Dimenzió (k)**|**Szolgáltatás (ok)**|
| --- | --- | --- | --- |
|ARP rendelkezésre állása|Rendelkezésre állás|<ui><li>Társ (elsődleges/másodlagos ExpressRoute útválasztó)</ui></li><ui><li> Egyenrangú típus (privát/nyilvános/Microsoft)</ui></li>|ExpressRoute|
|BGP rendelkezésre állása|Rendelkezésre állás|<ui><li> Társ (elsődleges/másodlagos ExpressRoute útválasztó)</ui></li><ui><li> Egyenrangú típus</ui></li>|ExpressRoute|
|BitsInPerSecond|Adatforgalom|<ui><li> Egyenrangúság típusa (ExpressRoute)</ui></li><ui><li>Hivatkozás (ExpressRoute Direct)</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|Adatforgalom| <ui><li>Egyenrangúság típusa (ExpressRoute)</ui></li><ui><li> Hivatkozás (ExpressRoute Direct) | <ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li> |
|GlobalReachBitsInPerSecond|Adatforgalom|<ui><li>Egyenrangú áramköri Sgomb (szolgáltatás kulcsa)</ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|Adatforgalom|<ui><li>Egyenrangú áramköri Sgomb (szolgáltatás kulcsa)</ui></li>|Global Reach|
|AdminState|Fizikai kapcsolat|Hivatkozás|ExpressRoute Direct|
|LineProtocol|Fizikai kapcsolat|Hivatkozás|ExpressRoute Direct|
|RxLightLevel|Fizikai kapcsolat|<ui><li>Hivatkozás</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
|TxLightLevel|Fizikai kapcsolat|<ui><li>Hivatkozás</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
>[!NOTE]
>A *GlobalGlobalReachBitsInPerSecond* és a *GlobalGlobalReachBitsOutPerSecond* használata csak akkor látható, ha legalább egy Global REACH-kapcsolatok létrejöttek.
>

## <a name="circuits-metrics"></a>Áramkörök metrikái

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>BITS be-és kifelé – mérőszámok az összes társon belül

A mérőszámokat megtekintheti egy adott ExpressRoute-áramkörön található összes társon.

![áramköri metrikák](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>BITS be-és kimenő metrikák

A privát, nyilvános és Microsoft-partnerek mérőszámait BITS/másodpercben tekintheti meg.

![metrikák felhasználónként](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>BGP rendelkezésre állása – megosztás társ szerint  

Megtekintheti közel valós idejű rendelkezésre állását a BGP és a társai között (elsődleges és másodlagos ExpressRoute útválasztók). Ez az irányítópult az elsődleges BGP-munkamenetet jeleníti meg a privát partnereknél, a második BGP-munkamenetet pedig a privát társak számára. 

![BGP rendelkezésre állása csoportonként](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>ARP rendelkezésre állása – megosztás társítás szerint  

Megtekintheti a közel valós idejű rendelkezésre állást az [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) -ben a különböző társítások és társaik (elsődleges és másodlagos ExpressRoute útválasztók) között. Ez az irányítópult a privát társ-összevonási ARP-munkamenetet jeleníti meg mindkét társon, de a Microsoft társközi szolgáltatásban való kitöltését. A rendszer az alapértelmezett összesítést (átlag) használta mindkét társban.  

![ARP rendelkezésre állása egyenrangúként](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>Közvetlen ExpressRoute metrikák

### <a name="admin-state---split-by-link"></a>Rendszergazdai állapot – felosztás hivatkozás alapján
Megtekintheti a ExpressRoute Direct port pár hivatkozásának rendszergazdai állapotát.

![er Direct rendszergazdai állapot](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>Bitek másodpercenként – felosztás hivatkozás alapján
A biteket másodpercenként megtekintheti a ExpressRoute Direct port pár hivatkozásán keresztül. 

![közvetlen bitek másodpercenkénti száma](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>Bitek másodpercenkénti felosztása hivatkozás alapján
A másodpercenkénti biteket is megtekintheti a ExpressRoute Direct port pár hivatkozásán keresztül. 

![er Direct bitek másodpercenként](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>Sor protokoll – felosztás hivatkozás alapján
A ExpressRoute Direct port pár hivatkozásán keresztül megtekintheti a sor protokollját.

![er Direct Line protokoll](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>Rx fényszint-felosztás hivatkozás alapján
Megtekintheti az RX-fény szintjét (a ExpressRoute közvetlen portot **fogadó**fény szintjét) az egyes portokhoz. Az egészséges Rx-fény szintje általában a-10 és 0 km közötti tartományba esik

![er Direct line Rx fény szintje](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>TX Light Level-felosztás hivatkozás alapján
Megtekintheti a TX-fény szintjét (a ExpressRoute közvetlen port által **közvetített**fény szintjét) az egyes portokhoz. Az egészséges TX-fény szintje általában a-10 és 0 km közötti tartományba esik

![er Direct line Rx fény szintje](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute bit/másodpercben

![átjáró kapcsolatai](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg )

## <a name="alerts-for-expressroute-gateway-connections"></a>Riasztások a ExpressRoute-átjáró kapcsolataihoz

1. A riasztások konfigurálásához navigáljon **Azure monitor**, majd kattintson a **riasztások**elemre.

   ![riasztások](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Kattintson a **+ cél kiválasztása** elemre, és válassza ki a ExpressRoute-átjáróhoz tartozó kapcsolatok erőforrását.

   ![cél]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Adja meg a riasztás részleteit.

   ![műveleti csoport](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. Adja meg és adja hozzá a műveleti csoportot.

   ![műveleti csoport hozzáadása](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Riasztások az egyes társak alapján

 ![mi](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Riasztások konfigurálása a tevékenységi naplókhoz az áramkörön

A **riasztási feltételek**között kiválaszthatja a jel típusa **tevékenység naplóját** , és kiválaszthatja a jelet.

  ![másik](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>További lépések

Az ExpressRoute-kapcsolat konfigurálása.
  
  * [Kapcsolatcsoport létrehozása és módosítása](expressroute-howto-circuit-arm.md)
  * [Társhálózati konfiguráció létrehozása és módosítása](expressroute-howto-routing-arm.md)
  * [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-arm.md)
