---
title: 'Forgatókönyv: Azure Firewall egyéni útválasztás a virtuális WAN-hoz'
titleSuffix: Azure Virtual WAN
description: Forgatókönyvek az útválasztáshoz – közvetlenül a virtuális hálózatok között, de a VNet->az Internet/ág és a VNet Azure Firewall használata a forgalom forgalmának átirányításához
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: cdaa594a87d960688638591e6edd525aa3b048f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568842"
---
# <a name="scenario-azure-firewall---custom"></a>Forgatókönyv: Azure Firewall-Custom

A virtuális WAN virtuális hub útválasztásával kapcsolatban igen sok lehetőség áll rendelkezésre. Ebben a forgatókönyvben a cél az, hogy közvetlenül a virtuális hálózatok között irányítsa a forgalmat, de a VNet-hoz-Internet, az ág és a VNet adatforgalmához használja a Azure Firewall. További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="workflow"></a>Forgatókönyv-munkafolyamat

Ebben a forgatókönyvben a forgalmat a VNet, Azure Firewall a VNet és a VNet közötti forgalomhoz irányítja, de közvetlenül a VNet-VNet adatforgalomhoz szeretne fordulni. Ha a Azure Firewall Managert használta, a rendszer automatikusan kitölti az útvonal beállításait az **alapértelmezett útválasztási táblázatba**. A privát forgalom a VNet és az ágakra vonatkozik, és az internetes forgalom a 0.0.0.0/0-ra vonatkozik.

A VPN-, a ExpressRoute-és a felhasználói VPN-kapcsolatok együttes neve ágak, és ugyanahhoz az (alapértelmezett) útválasztási táblázathoz van hozzárendelve. Az összes VPN-, ExpressRoute-és felhasználói VPN-kapcsolat továbbítja az útvonalakat ugyanahhoz az útválasztási táblákhoz. Ennek a forgatókönyvnek a konfigurálásához a következő lépéseket kell figyelembe vennie:

1. Hozzon létre egy egyéni útválasztási táblázatot **RT_VNET**.
1. Hozzon létre egy útvonalat a VNet és az VNet közötti aktiváláshoz: 0.0.0.0/0 a következő ugrással a Azure Firewallra mutat. A propagálás szakaszban győződjön meg arról, hogy a virtuális hálózatok van kiválasztva, amelyek pontosabb útvonalakat biztosítanak, így lehetővé téve a VNet-VNet Direct forgalom áramlását.

   * A **társítás alatt:** válassza a virtuális hálózatok lehetőséget, amely azt jelenti, hogy a virtuális hálózatok az útválasztási táblázat útvonalai alapján éri el a célhelyet.
   * A **propagálás során:** válassza a virtuális hálózatok lehetőséget, amely azt jelenti, hogy a virtuális hálózatok propagálja az útválasztási táblázatba; Ez azt jelenti, hogy az útválasztási táblázatba több konkrét útvonal is továbbítódik, így biztosítva, hogy a VNet és a VNet közötti közvetlen forgalom legyen elérhető.

1. Adjon hozzá egy összesített statikus útvonalat a virtuális hálózatok az **alapértelmezett útválasztási táblázathoz** , hogy aktiválja a VNet folyamatát a Azure Firewallon keresztül. 

   * Ne feledje, hogy az ágak az alapértelmezett útválasztási táblázathoz vannak társítva és propagálva.
   * Az ágak nem továbbítódnak RT_VNET útválasztási táblázatba. Ez biztosítja a VNet közötti adatforgalom áramlását a Azure Firewallon keresztül.

Ez azt eredményezi, hogy az útválasztási konfiguráció megváltozik az **1. ábrán**látható módon.

**1. ábra**

:::image type="content" source="./media/routing-scenarios/between-vnets-firewall/routing.png" alt-text="1. ábra":::

## <a name="next-steps"></a>További lépések

* A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
* További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).
* A virtuális központ útválasztásának konfigurálásával kapcsolatos további információkért lásd: [virtuális központ útválasztásának konfigurálása](how-to-virtual-hub-routing.md).
