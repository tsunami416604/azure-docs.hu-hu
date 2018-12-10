---
title: 'QoS-követelmények – ExpressRoute: Azure |} A Microsoft Docs'
description: Ezen a lapon konfigurálásához és kezeléséhez a QoS részletes követelményeit ismerteti. Skype vállalati verzió üzleti és hangvétel szolgáltatások ismertetése.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: cb9acb5434fad6cd7898be14404c5ed4125ab96a
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141169"
---
# <a name="expressroute-qos-requirements"></a>Az ExpressRoute QoS-követelményei
A Skype Vállalati verzió különböző számítási feladatokat tartalmaz, amelyek különböző QoS-kezelést igényelnek. Ha hangszolgáltatásokat tervez használni az ExpressRoute-on keresztül, teljesítenie kell az alábbiakban ismertetett követelményeket.

![](./media/expressroute-qos/expressroute-qos.png)

> [!NOTE]
> A QoS-követelmények csak a Microsoft társviszony-létesítésre vonatkoznak. Az Azure nyilvános vagy Azure privát társviszony-létesítésen keresztül kapott hálózati forgalom DSCP-értékei 0-ra állnak. 
> 
> 

A következő táblázat a Skype Vállalati verzió által használt DSCP-jelöléseket sorolja fel. További információkért lásd: [A QoS kezelése a Skype Vállalati verzióhoz](https://technet.microsoft.com/library/gg405409.aspx).

| **Forgalomosztály** | **Kezelés (DSCP-jelölés)** | **Skype Vállalati verzió számítási feladata** |
| --- | --- | --- |
| **Hang** |EF (46) |Skype- / Lync-hang |
| **Interaktív** |AF41 (34) |Videó, VBSS |
| AF21 (18) |Alkalmazásmegosztás | |
| **Alapértelmezett** |AF11 (10) |Fájlátvitel |
| CS0 (0) |Bármi más | |

* Osztályoznia kell a munkaterheléseket, és meg kell jelölnie a megfelelő DSCP-értékeket. A DSCP-jelöléseket az [itt](https://technet.microsoft.com/library/gg405409.aspx) leírt útmutatások alapján állíthatja be a hálózatban.
* Több QoS várakozási sort kell konfigurálnia és támogatnia a hálózaton belül. A hangnak egy különálló osztálynak kell lennie, és az RFC 3246-ban meghatározott EF-kezelést kell fogadnia. 
* Forgalomosztályonként meghatározhatja a várakozásisor-kezelési mechanizmust, a torlódásészlelési házirendet és a sávszélesség-kiosztást. Skype Vállalati verzió számítási feladatainak DSCP-jelölését azonban meg kell tartania. Ha a fenti listában nem szereplő DSCP-jelöléseket használ, például az AF31 (26) jelölést, ezt a DSCP-értéket 0 értékre kell felülírnia, mielőtt elküldi a csomagot a Microsoftnak. A Microsoft csak a fenti táblázatban szereplő DSCP-értékkel jelölt csomagokat küld. 

## <a name="next-steps"></a>További lépések
* Tekintse meg az [Útválasztás](expressroute-routing.md) és a [NAT](expressroute-nat.md) követelményeit.
* Tekintse meg a következő hivatkozásokat az ExpressRoute-kapcsolat konfigurálásához:
  
  * [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-classic.md)
  * [Útválasztás konfigurálása](expressroute-howto-routing-classic.md)
  * [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-classic.md)

