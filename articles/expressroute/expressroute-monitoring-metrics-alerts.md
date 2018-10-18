---
title: Az Azure ExpressRoute figyelése, mérőszámok és riasztások |} A Microsoft Docs
description: Ez az oldal ismerteti az ExpressRoute monitorozása
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: fcdaf10ad30cee0e7b79304ff21d67560522875c
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377797"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Az ExpressRoute monitorozása, mérőszámai és riasztásai

 Ez a cikk segítségével megismerheti az ExpressRoute monitorozása, mérőszámok és riasztások. Az Azure Monitor riasztási, az összes Azure diagnosztikai naplók egy helyen érheti minden metrika.

## <a name="circuit-metrics"></a>Kapcsolatcsoport metrikák

Keresse meg a **metrikák**, az ExpressRoute lapon a kapcsolatcsoporthoz, amelyet figyelni szeretne. A **figyelés** megtekintheti a **metrikák**. Válassza ki a BitsInPerSecond vagy BitsOutPerSecond, és ilyenkor az összesítést. Opcionálisan alkalmazhatja felosztása, amely megjelenik a metrika társviszony-létesítés típusa.

![Kapcsolatcsoport metrikák](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

## <a name="metrics-per-peering"></a>Társviszony-létesítés metrika

Magánhálózati, nyilvános és a Microsoft a társviszony-létesítés bit/másodperc metrikáinak megtekintéséhez.

![Társviszony-létesítés metrika](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

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
* Az ExpressRoute-kapcsolat konfigurálása.
  
  * [Kapcsolatcsoport létrehozása és módosítása](expressroute-howto-circuit-arm.md)
  * [Társhálózati konfiguráció létrehozása és módosítása](expressroute-howto-routing-arm.md)
  * [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-arm.md)
