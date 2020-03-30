---
title: Az Azure ExpressRoute használata távoli felhasználók támogatása érdekében
description: Ez a lap bemutatja, hogyan használhatja ki az Azure ExpressRoute-ot a covid-19-es világjárvány miatt távolról történő munkavégzés engedélyezéséhez.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/22/2020
ms.author: ajitbhu
ms.openlocfilehash: 2fe990fd9c5922dd2e059dbb212cd8bb1cd3726e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336652"
---
# <a name="using-azure-expressroute-to-create-hybrid-connectivity-to-support-remote-users"></a>Hibrid kapcsolat létrehozása az Azure ExpressRoute használatával a távoli felhasználók támogatása érdekében

Ez a cikk azt ismerteti, hogy miként használhatja ki az ExpressRoute, az Azure, a Microsoft-hálózat és az Azure-partneri ökoszisztéma távoli munkavégzését.

## <a name="connecting-to-azure-services-with-expressroute"></a>Csatlakozás Azure-szolgáltatásokhoz az ExpressRoute segítségével

>[!NOTE]
>Ez a cikk bemutatja, hogyan használhatja ki az ExpressRoute, az Azure, a Microsoft-hálózat és az Azure-partneri ökoszisztéma segítségével a távmunka és a COVID-19-es válság miatt felmerülő hálózati problémák enyhítése.
>

[Az ExpressRoute](expressroute-introduction.md) egy Azure-szolgáltatás, amely lehetővé teszi a Microsoft adatközpontok és az infrastruktúra közötti privát kapcsolatot, amely az Ön telephelyén vagy egy helymegosztási létesítményben található. Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. Biztonságos kapcsolatot, megbízhatóságot és sebességet kínálnak, alacsonyabb és konzisztens késést biztosítva, mint az interneten keresztüli tipikus kapcsolatok.

## <a name="useful-links"></a>Hasznos hivatkozások

* [A meglévő ExpressRoute-kapcsolat sávszélességének növelése](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [ExpressRoute figyelése, metrikák és riasztások](expressroute-monitoring-metrics-alerts.md#expressroute-gateway-connections-in-bitsseconds)
* [Útvonaloptimalizálás az ExpressRoute-on keresztül](expressroute-optimize-routing.md)
* [Azure ExpressRoute for O365](https://docs.microsoft.com/office365/enterprise/azure-expressroute?redirectSourcePath=%252farticle%252f6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Aszimmetrikus útválasztással kapcsolatos szempontok](expressroute-asymmetric-routing.md)
* [Támogatási kérelem megnyitása az Azure Portalon](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="troubleshoot"></a>Hibaelhárítás

* [Az ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
* ARP-tábla beszerzése az [Erőforrás-kezelőben](expressroute-troubleshooting-arp-resource-manager.md) és a [Klasszikus](expressroute-troubleshooting-arp-classic.md)
* [Hibás áramkör alaphelyzetbe állítása](reset-circuit.md)
* [Hálózati teljesítmény – problémamegoldás](expressroute-troubleshooting-network-performance.md)

## <a name="next-steps"></a>Következő lépések

* [További információ az ExpressRoute-kapcsolati modellekről](expressroute-connectivity-models.md)
* Ismerje meg az ExpressRoute-kapcsolatokat és útválasztási tartományokat. Lásd: [ExpressRoute-áramkörök és útválasztási tartományok](expressroute-circuit-peerings.md)
* Találjon egy szolgáltatót. Lásd: [ExpressRoute-partnerek és társviszony-létesítési helyek](expressroute-locations.md)
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).
* [Az ExpressRoute-kapcsolatcsoport létrehozása és módosítása](expressroute-howto-circuit-portal-resource-manager.md)
* [ExpressRoute-kapcsolatcsoport társviszonyának létrehozása és módosítása](expressroute-howto-routing-portal-resource-manager.md)
* [Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal](expressroute-howto-linkvnet-portal-resource-manager.md)
