---
title: 'Forgatókönyv: Azure Firewall egyéni útválasztás a virtuális WAN-hoz'
titleSuffix: Azure Virtual WAN
description: Forgatókönyvek az útválasztáshoz – közvetlenül a virtuális hálózatok között, de a VNet->az Internet/ág és a VNet Azure Firewall használata a forgalom forgalmának átirányításához
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: f48b30e0e4e76a4cf4c855008776f6b7541ad5a2
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87850651"
---
# <a name="scenario-azure-firewall---custom"></a>Forgatókönyv: Azure Firewall-Custom

A virtuális WAN virtuális hub útválasztásával kapcsolatban igen sok lehetőség áll rendelkezésre. Ebben a forgatókönyvben a cél az, hogy közvetlenül a virtuális hálózatok között irányítsa a forgalmat, de a VNet-hoz-Internet, az ág és a VNet adatforgalmához használja a Azure Firewall.

## <a name="design"></a><a name="design"></a>Tervezés

Ha szeretné megtudni, hogy hány útválasztási táblázatra van szükség, létrehozhat egy kapcsolati mátrixot, ahol minden cella azt jelöli, hogy a forrás (sor) képes-e kommunikálni egy adott céllal (oszlop). A kapcsolati mátrix ebben a forgatókönyvben triviális, de más forgatókönyvekkel összhangban is megtekinthető.

**Kapcsolati mátrix**

| Forrás           | Címzett:      | *Virtuális hálózatok*      | *Ágak*    | *Internet*   |
|---             |---       |---           |---            |---           |
| **Virtuális hálózatok**      |   &#8594;|     X        |     AzFW      |     AzFW     |
| **Ágak**   |   &#8594;|    AzFW      |       X       |       X      |

Az előző táblázatban a "X" közvetlen kapcsolódást jelent két kapcsolat között anélkül, hogy a Azure Firewall a virtuális WAN-ban halad át, és a "AzFW" azt jelzi, hogy a folyamat áthalad a Azure Firewall. Mivel két különálló kapcsolati minta van a mátrixban, két útválasztási táblázatra lesz szükség, amely a következőképpen lesz konfigurálva:

* Virtuális hálózatok:
  * Társított útválasztási táblázat: **RT_VNet**
  * Propagálás az útválasztási táblákba: **RT_VNet**
* Ágak
  * Társított útválasztási táblázat: **alapértelmezett**
  * Propagálás az útválasztási táblákba: **alapértelmezett**


További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>Munkafolyamat

Ebben a forgatókönyvben a forgalmat a VNet, Azure Firewall a VNet és a VNet közötti forgalomhoz irányítja, de közvetlenül a VNet-VNet adatforgalomhoz szeretne fordulni. Ha a Azure Firewall Managert használta, a rendszer automatikusan kitölti az útvonal beállításait az **alapértelmezett útválasztási táblázatba**. A privát forgalom a VNet és az ágakra vonatkozik, és az internetes forgalom a 0.0.0.0/0-ra vonatkozik.

A VPN-, a ExpressRoute-és a felhasználói VPN-kapcsolatok együttes neve ágak, és ugyanahhoz az (alapértelmezett) útválasztási táblázathoz van hozzárendelve. Az összes VPN-, ExpressRoute-és felhasználói VPN-kapcsolat továbbítja az útvonalakat ugyanahhoz az útválasztási táblákhoz. Ennek a forgatókönyvnek a konfigurálásához a következő lépéseket kell figyelembe vennie:

1. Hozzon létre egy egyéni útválasztási táblázatot **RT_VNet**.
1. Hozzon létre egy útvonalat a VNet és az VNet közötti aktiváláshoz: 0.0.0.0/0 a következő ugrással a Azure Firewallra mutat. A propagálás szakaszban győződjön meg arról, hogy a virtuális hálózatok van kiválasztva, amelyek pontosabb útvonalakat biztosítanak, így lehetővé téve a VNet-VNet Direct forgalom áramlását.

   * A **társítás alatt:** válassza a virtuális hálózatok lehetőséget, amely azt jelenti, hogy a virtuális hálózatok az útválasztási táblázat útvonalai alapján éri el a célhelyet.
   * A **propagálás során:** válassza ki a virtuális hálózatok, amely azt jelenti, hogy a virtuális hálózatok propagálja ezt az útválasztási táblázatot; Ez azt jelenti, hogy az útválasztási táblázatba több konkrét útvonal is továbbítódik, így biztosítva, hogy a VNet és a VNet közötti közvetlen forgalom legyen elérhető.

1. Adjon hozzá egy összesített statikus útvonalat a virtuális hálózatok az **alapértelmezett útválasztási táblázathoz** , hogy aktiválja a VNet folyamatát a Azure Firewallon keresztül.

   * Ne feledje, hogy az ágak az alapértelmezett útválasztási táblázathoz vannak társítva és propagálva.
   * Az ágak nem továbbítódnak RT_VNet útválasztási táblázatba. Ez biztosítja a VNet közötti adatforgalom áramlását a Azure Firewallon keresztül.

Ez azt eredményezi, hogy az útválasztási konfiguráció megváltozik az **1. ábrán**látható módon.

**1. ábra**

:::image type="content" source="./media/routing-scenarios/between-vnets-firewall/routing.png" alt-text="1. ábra":::

> [!NOTE]
> A virtuális WAN-huboknak és a csatlakoztatott virtuális hálózatoknak ugyanabban az Azure-régióban kell lenniük.

## <a name="next-steps"></a>Következő lépések

* A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
* További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).
* A virtuális központ útválasztásának konfigurálásával kapcsolatos további információkért lásd: [virtuális központ útválasztásának konfigurálása](how-to-virtual-hub-routing.md).
