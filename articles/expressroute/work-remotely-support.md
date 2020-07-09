---
title: Az Azure ExpressRoute használata a távoli felhasználók támogatásához
description: Ez az oldal azt ismerteti, hogyan használhatja az Azure ExpressRoute-t a COVID-19 világjárvány miatti távoli működéshez.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: how-to
ms.date: 03/22/2020
ms.author: ajitbhu
ms.openlocfilehash: 18b30380ede0c95c7d039749a4d47f65e5824058
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84738073"
---
# <a name="using-azure-expressroute-to-create-hybrid-connectivity-to-support-remote-users"></a>Hibrid kapcsolat létrehozása az Azure ExpressRoute használatával a távoli felhasználók támogatásához

Ez a cikk azt ismerteti, hogyan használható a ExpressRoute, az Azure, a Microsoft Network és az Azure partneri ökoszisztéma a távoli működéshez.

## <a name="connecting-to-azure-services-with-expressroute"></a>Csatlakozás az Azure-szolgáltatásokhoz a ExpressRoute

>[!NOTE]
>Ez a cikk azt ismerteti, hogyan használhatja a ExpressRoute, az Azure, a Microsoft Network és az Azure partner-ökoszisztémát a COVID-19-válság miatt felmerülő hálózati problémák megoldásához.
>

A [ExpressRoute](expressroute-introduction.md) egy olyan Azure-szolgáltatás, amely lehetővé teszi a Microsoft-adatközpontok és a helyszíni vagy közös elhelyezési létesítményben lévő infrastruktúra közötti magánhálózati kapcsolódást. Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. Biztonságos kapcsolattal, megbízhatósággal és sebességgel rendelkeznek, és az interneten keresztüli szokásos kapcsolatoknál alacsonyabb és konzisztens késéssel rendelkeznek.

## <a name="useful-links"></a>Hasznos hivatkozások

* [A meglévő ExpressRoute-áramkör sávszélességének javítása](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [ExpressRoute-figyelés, mérőszámok és riasztások](expressroute-monitoring-metrics-alerts.md#expressroute-gateway-connections-in-bitsseconds)
* [Útvonal-optimalizálás ExpressRoute](expressroute-optimize-routing.md)
* [Azure-ExpressRoute a O365-hez](https://docs.microsoft.com/office365/enterprise/azure-expressroute?redirectSourcePath=%252farticle%252f6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Aszimmetrikus útválasztási megfontolások](expressroute-asymmetric-routing.md)
* [Támogatási kérelem megnyitása a Azure Portalban](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="troubleshoot"></a>Hibaelhárítás

* [Az ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
* ARP-tábla lekérése a [Resource Managerben](expressroute-troubleshooting-arp-resource-manager.md) és a [Klasszikusban](expressroute-troubleshooting-arp-classic.md)
* [Sikertelen áramkör alaphelyzetbe állítása](reset-circuit.md)
* [Hálózati teljesítmény – problémamegoldás](expressroute-troubleshooting-network-performance.md)

## <a name="next-steps"></a>Következő lépések

* [További tudnivalók a ExpressRoute kapcsolati modelljeiről](expressroute-connectivity-models.md)
* Ismerje meg az ExpressRoute-kapcsolatokat és útválasztási tartományokat. Lásd: [ExpressRoute-áramkörök és útválasztási tartományok](expressroute-circuit-peerings.md)
* Találjon egy szolgáltatót. Lásd: [ExpressRoute Partners és peering Locations](expressroute-locations.md)
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).
* [Az ExpressRoute-kapcsolatcsoport létrehozása és módosítása](expressroute-howto-circuit-portal-resource-manager.md)
* [ExpressRoute-kapcsolatcsoport társviszonyának létrehozása és módosítása](expressroute-howto-routing-portal-resource-manager.md)
* [Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal](expressroute-howto-linkvnet-portal-resource-manager.md)
