---
title: 'Azure ExpressRoute: Figyelés, metrikák és riasztások'
description: Ez az oldal az ExpressRoute figyelésével kapcsolatos információkat tartalmazza
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 268a7e7c94285d3c4fdcb0c5fb91b685c09b58c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436918"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Az ExpressRoute monitorozása, mérőszámai és riasztásai

Ez a cikk segít megérteni ExpressRoute figyelés, metrikák és riasztások az Azure Monitor használatával. Az Azure Monitor egyablakos az összes metrikák, riasztási, diagnosztikai naplók az egész Azure-ban.
 
>[!NOTE]
>**Klasszikus metrikák** használata nem ajánlott.
>

## <a name="expressroute-metrics"></a>ExpressRoute-mérőszámok

A **Metrikák**megtekintéséhez keresse meg az *Azure Monitor* lapot, és kattintson a *Metrikák gombra.* **Az** ExpressRoute-metrikák megtekintéséhez szűrje erőforrástípus *ExpressRoute-áramkörök*szerint. A **Globális elérés** metrikák megtekintéséhez szűrjön erőforrástípus *ExpressRoute-áramkörök* szerint, és válasszon ki egy ExpressRoute-áramköri erőforrást, amelynek globális elérése engedélyezve van. Az **ExpressRoute Direct** mérőszámok megtekintéséhez szűrje az Erőforrástípust *ExpressRoute-portok*szerint. 

Ha egy metrika van kiválasztva, az alapértelmezett összesítés lesz alkalmazva. Szükség esetén felosztást is alkalmazhat, amely a különböző dimenziókkal rendelkező metrikát jeleníti meg.

### <a name="available-metrics"></a>Elérhető mérőszámok
|**Metrika**|**Kategória**|**Dimenzió(k)**|**Jellemző(k)**|
| --- | --- | --- | --- |
|ARP elérhetősége|Rendelkezésre állás|<ui><li>Társ (elsődleges/másodlagos ExpressRoute-útválasztó)</ui></li><ui><li> Társviszony-létesítés típusa (privát/nyilvános/Microsoft)</ui></li>|ExpressRoute|
|BGP elérhetősége|Rendelkezésre állás|<ui><li> Társ (elsődleges/másodlagos ExpressRoute-útválasztó)</ui></li><ui><li> Társviszony-létesítés típusa</ui></li>|ExpressRoute|
|BitsInPerSz|Adatforgalom|<ui><li> Társviszony-létesítés típusa (ExpressRoute)</ui></li><ui><li>Hivatkozás (ExpressRoute Direct)</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSz|Adatforgalom| <ui><li>Társviszony-létesítés típusa (ExpressRoute)</ui></li><ui><li> Hivatkozás (ExpressRoute Direct) | <ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li> |
|GlobalReachBitsInPerSecond|Adatforgalom|<ui><li>Társviszony-létesített áramköri skey (szolgáltatáskulcs)</ui></li>|Globális elérés|
|GlobalReachBitsOutPerSecond|Adatforgalom|<ui><li>Társviszony-létesített áramköri skey (szolgáltatáskulcs)</ui></li>|Globális elérés|
|AdminÁllam|Fizikai kapcsolat|Hivatkozás|ExpressRoute Direct|
|LineProtocol (LineProtocol)|Fizikai kapcsolat|Hivatkozás|ExpressRoute Direct|
|RxLightLevel|Fizikai kapcsolat|<ui><li>Hivatkozás</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
|TxLightLevel|Fizikai kapcsolat|<ui><li>Hivatkozás</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
>[!NOTE]
>A *GlobalGlobalReachBitsInPerSecond* és *a GlobalGlobalGlobalReachBitsOutPerSecond* használata csak akkor lesz látható, ha legalább egy globális elérési kapcsolat létrejött.
>

## <a name="circuits-metrics"></a>Áramköri mutatók

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Be- és ki- és be- és kikerekítés – Mérőszámok az összes társviszonyban

Metrikák at egy adott ExpressRoute-kapcsolati körök ben megtekintheti.

![áramköri mutatók](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>Be- és ki- és be- és kikerekítés – Társviszony-létesítésenkénti mutatók

Megtekintheti a magán-, nyilvános és microsoftos társviszony-létesítési mutatóit bit/másodpercben.

![mutatók társviszony-létesítésenként](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>BGP elérhetősége – társszerint felosztás  

Megtekintheti a BGP közel valós idejű rendelkezésre állását társviszony-létesítések és társak (elsődleges és másodlagos ExpressRoute-útválasztók) között. Ez az irányítópult az elsődleges BGP-munkamenetet jeleníti meg a privát társviszony-létesítéshez, a második BGP-munkamenetet pedig lefelé a privát társviszony-létesítéshez. 

![BGP rendelkezésre állása társonként](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>ARP elérhetősége – társviszony-létesítés szerint felosztás  

Megtekintheti az [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) közel valós idejű rendelkezésre állását társviszony-létesítések és társak (elsődleges és másodlagos ExpressRoute-útválasztók) között. Ez az irányítópult a privát társviszony-létesítési ARP-munkamenetet jeleníti meg mindkét társközött, de a Microsoft társviszony-létesítése ily mértékben befejeződik. Az alapértelmezett összesítés (Átlag) mindkét társon keresztül volt használva.  

![ARP rendelkezésre állása társonként](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>ExpressRoute közvetlen metrikák

### <a name="admin-state---split-by-link"></a>Felügyeleti állapot – hivatkozás szerinti felosztás
Megtekintheti az ExpressRoute Direct portpár egyes hivatkozásainak felügyeleti állapotát.

![er közvetlen rendszergazdai állapot](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>Bitek másodpercenként – hivatkozással felosztva
A bitek másodpercenként az ExpressRoute Direct portpár mindkét kapcsolatában megtekinthetők. 

![közvetlen bitek másodpercenként](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>Bitek másodpercenként – hivatkozással felosztva
Az ExpressRoute Direct portpár mindkét kapcsolatában megtekintheti a másodpercenként kifelé mutató biteket is. 

![er direct bitek másodpercenként](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>Line Protocol - Hivatkozás szerint felosztás
A vonalprotokoll az ExpressRoute Direct portpár minden egyes kapcsolatán megtekinthető.

![er direct line protokoll](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>Rx fényszint - link szerint felosztva
Az Rx fényszintjét (az ExpressRoute Direct port **által kapott**fényszintet) tekintheti meg az egyes portoknál. Az egészséges Rx fényszintek általában -10 és 0 dBm közötti tartományba esnek

![er direct line Rx fényszint](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>Tx fényszint - link szerint felosztva
Megtekintheti a Tx fényszintjét (az ExpressRoute Direct port **által továbbított**fényszintet) az egyes portoknál. Az egészséges Tx fényszintek általában -10 és 0 dBm közötti tartományba esnek

![er direct line Rx fényszint](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute-átjárókapcsolatok bit/másodpercben

![átjárókapcsolatok](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg )

## <a name="alerts-for-expressroute-gateway-connections"></a>Az ExpressRoute-átjárókapcsolatok riasztásai

1. A riasztások konfigurálásához keresse meg az **Azure Monitor**t, majd kattintson a **Riasztások gombra.**

   ![riasztások](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Kattintson **a +Cél kiválasztása** gombra, és válassza ki az ExpressRoute átjárókapcsolat-erőforrást.

   ![Cél]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Adja meg a riasztás részleteit.

   ![műveletcsoport](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. A műveletcsoport definiálása és hozzáadása.

   ![műveletcsoport hozzáadása](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Az egyes társviszony-létesítéseken alapuló riasztások

 ![mi](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Riasztások konfigurálása az áramkörök tevékenységnaplóihoz

A **riasztási feltételek ben**kiválaszthatja a **Tevékenységnaplót** a Jeltípushoz, és kiválaszthatja a Jelet.

  ![Másik](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>További lépések

Az ExpressRoute-kapcsolat konfigurálása.
  
  * [Kapcsolatcsoport létrehozása és módosítása](expressroute-howto-circuit-arm.md)
  * [Társhálózati konfiguráció létrehozása és módosítása](expressroute-howto-routing-arm.md)
  * [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-arm.md)
