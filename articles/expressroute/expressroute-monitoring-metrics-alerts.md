---
title: Figyelés, mérőszámok és riasztások – az Azure ExpressRoute |} A Microsoft Docs
description: Ez az oldal ismerteti az ExpressRoute monitorozása
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 92ec03e20fb6e681a0afd14048449ad004ebca0c
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991487"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Az ExpressRoute monitorozása, mérőszámai és riasztásai

Ez a cikk segít megérteni a ExpressRoute-monitorozás, mérőszámok és riasztások az Azure Monitor használatával. Az Azure Monitor riasztási, az összes Azure diagnosztikai naplók egy helyen érheti minden metrika.
 
>[!NOTE]
>Használatával **klasszikus metrikák** nem ajánlott.
>

## <a name="circuit-metrics"></a>Kapcsolatcsoport metrikák

Keresse meg a **metrikák**, az ExpressRoute lapon a kapcsolatcsoporthoz, amelyet figyelni szeretne. A **figyelés**, megtekintheti a **metrikák**. Válasszon az alább felsorolt mérőszámokból. Az alapértelmezett összesítés lesz alkalmazva. Igény szerint a felosztást is alkalmazhatja, amely a különböző Dimenziókkal rendelkező metrikákat jeleníti meg.

### <a name="metrics-available"></a>Elérhető metrikák: 
* **Rendelkezésre állás** 
    * ARP rendelkezésre állása
      * Elérhető méretek:
        * Társ (elsődleges/másodlagos ExpressRoute útválasztó)
        * Egyenrangú típus (privát/nyilvános/Microsoft)
    * BGP rendelkezésre állása
      * Elérhető méretek:
        * Társ (elsődleges/másodlagos ExpressRoute útválasztó)
        * Egyenrangú típus (privát/nyilvános/Microsoft)
* **Forgalom**
    * BitsInPerSecond
      * Elérhető méretek:
        * Egyenrangú típus (privát/nyilvános/Microsoft)
    * BitsOutPerSecond
      * Elérhető méretek:
        * Egyenrangú típus (privát/nyilvános/Microsoft)
    * GlobalReachBitsInPerSecond
      * Elérhető méretek:
        * Egyenrangú áramköri Sgomb (szolgáltatás kulcsa)
    * GlobalReachBitsOutPerSecond
      * Elérhető méretek:
        * Egyenrangú áramköri Sgomb (szolgáltatás kulcsa)

>[!NOTE]
>A *GlobalGlobalReachBitsInPerSecond* és a *GlobalGlobalReachBitsOutPerSecond* használata csak akkor látható, ha legalább egy Global REACH-kapcsolatok létrejöttek.
>

## <a name="bits-in-and-out---metrics-across-all-peerings"></a>BITS be-és kifelé – mérőszámok az összes társon belül

A mérőszámokat megtekintheti egy adott ExpressRoute-áramkörön található összes társon.

![Kapcsolatcsoport metrikák](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

## <a name="bits-in-and-out---metrics-per-peering"></a>BITS be-és kimenő metrikák

Magánhálózati, nyilvános és a Microsoft a társviszony-létesítés bit/másodperc metrikáinak megtekintéséhez.

![Társviszony-létesítés metrika](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

## <a name="bgp-availability---split-by-peer"></a>BGP rendelkezésre állása – megosztás társ szerint  

Megtekintheti közel valós idejű rendelkezésre állását a BGP és a társai között (elsődleges és másodlagos ExpressRoute útválasztók). Ez az irányítópult az elsődleges BGP-munkamenetet jeleníti meg a privát partnereknél, a második BGP-munkamenetet pedig a privát társak számára. 

![BGP rendelkezésre állása csoportonként](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

## <a name="arp-availability---split-by-peering"></a>ARP rendelkezésre állása – megosztás társítás szerint  

Megtekintheti a közel valós idejű rendelkezésre állást az [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) -ben a különböző társítások és társaik (elsődleges és másodlagos ExpressRoute útválasztók) között. Ez az irányítópult a privát társ-összevonási ARP-munkamenetet jeleníti meg mindkét társon, de a Microsoft társközi szolgáltatásban való kitöltését. A rendszer az alapértelmezett összesítést (átlag) használta mindkét társban.  

![ARP rendelkezésre állása egyenrangúként](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Az ExpressRoute-átjáró kapcsolatok bit/másodperc

![Gateway-kapcsolatok](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg ) 

## <a name="alerts-for-expressroute-gateway-connections"></a>Az ExpressRoute-átjáró kapcsolatok kapcsolatos riasztások

1. Annak érdekében, hogy a riasztások konfigurálásához lépjen **Azure Monitor**, majd kattintson a **riasztások**.

   ![riasztások](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Kattintson a **+ cél kiválasztása** , és válassza ki az ExpressRoute-átjáró kapcsolati erőforrás.

   ![cél]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. A riasztás részleteinek megadása.

   ![Műveletcsoport](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. Határozza meg, és adja hozzá a műveleti csoport.

   ![Műveletcsoport hozzáadása](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Minden társviszony-létesítés alapuló riasztások

 ![mi](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>A Kapcsolatcsoportok tevékenységeket tartalmazó naplók riasztások konfigurálása

Az a **riasztási feltételek**, választhat **tevékenységnapló** a jel típusa, és válassza ki a jel.

  ![egy másik](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>További lépések

Az ExpressRoute-kapcsolat konfigurálása.
  
  * [Kapcsolatcsoport létrehozása és módosítása](expressroute-howto-circuit-arm.md)
  * [Társhálózati konfiguráció létrehozása és módosítása](expressroute-howto-routing-arm.md)
  * [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-arm.md)
