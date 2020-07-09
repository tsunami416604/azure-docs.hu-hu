---
title: 'Forgatókönyv: forgalom irányítása a NVA-n keresztül egyéni beállítások használatával'
titleSuffix: Azure Virtual WAN
description: Ez a forgatókönyv segítséget nyújt a forgalom NVA-n keresztüli átirányításához egy másik NVA az internethez kötött forgalomhoz.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3fcad59bcfb75b8de6af14eae7e0bcb4962b17eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568826"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>Forgatókönyv: forgalom irányítása NVA – egyéni (előzetes verzió)

A virtuális WAN virtuális hub útválasztásával kapcsolatban igen sok lehetőség áll rendelkezésre. Ebben a NVA (hálózati virtuális berendezés) forgatókönyvben a cél az, hogy átirányítsa a forgalmat egy VNet <-> ág NVA, és egy másik NVA használjon az internethez kötött forgalomhoz. További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Forgatókönyv-architektúra

Az **1. ábrán**egy hub, **1. hub**található.

* Az **1. hub** közvetlenül kapcsolódik a NVA virtuális hálózatok **VNET 4** és a **VNET 5**rendszerhez.

* Az 1., 2. és 3. virtuális hálózatok és az ágak (VPN/ER/P2S) közötti adatforgalmat a rendszer a **VNET 4 NVA** -10.4.0.5 keresztül elvárta.

* Az 1., 2. és 3. virtuális hálózatok összes internetre kötött forgalma a **VNET 5 NVA** -10.5.0.5 keresztül várható.

**1. ábra**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="1. ábra":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Forgatókönyv-munkafolyamat

Az Útválasztás NVA-n keresztüli beállításához a következő lépéseket kell figyelembe venni:

1. Ahhoz, hogy az internetre kötött forgalom a VNET5 hálózat-on keresztül haladjon, az 1, 2 és 3 virtuális hálózatok kell csatlakoznia, hogy közvetlenül kapcsolódjon a VNet-alapú VNET 5-öt. A 0.0.0.0/0 és a Next hop 10.5.0.5 virtuális hálózatok beállított UDR is szüksége van. A virtuális WAN jelenleg nem engedélyezi a következő ugrási NVA a 0.0.0.0/0 virtuális központban.

1. A Azure Portal navigáljon a virtuális hubhoz, és hozzon létre egy egyéni útválasztási táblázatot **RT_Shared** amely az útvonalakat az összes virtuális hálózatok-és ág-kapcsolaton keresztül továbbítja. A **2. ábrán**ezt a rendszer üres egyéni útválasztási táblázatként ábrázolja **RT_Shared**.

   * **Útvonalak:** Nem kell statikus útvonalakat hozzáadnia.

   * **Társítás:** Válassza a 4. és az 5. virtuális hálózatok, ami azt jelenti, hogy a virtuális hálózatok 4 és 5 kapcsolat az útválasztási táblázat **RT_Shared**van hozzárendelve.

   * **Propagálás:** Mivel azt szeretné, hogy az összes ág és VNet kapcsolata dinamikusan terjessze az útvonalakat az útválasztási táblázatba, válassza az ágak és az összes virtuális hálózatok lehetőséget.

1. Hozzon létre egy egyéni útválasztási táblázatot **RT_V2B** az 1., 2. és 3. virtuális hálózatok érkező forgalom az ágakba való irányításához.

   * **Útvonalak:** Vegyen fel egy összesített statikus útválasztási bejegyzést az ágakhoz (VPN/ER/P2S) (a **2. ábrán**10.2.0.0/16) a következő ugrással, a VNET 4 kapcsolattal. Egy statikus útvonalat is konfigurálnia kell a VNET 4 kapcsolatához a fiókirodák előtagjaihoz, és a következő ugrást kell megadnia a NVA adott IP-címéhez a VNET 4-ben.

   * **Társítás:** Válassza az összes virtuális hálózatok 1, 2 és 3 elemet. Ez azt jelenti, hogy az 1., 2. és 3. VNet-kapcsolatok ehhez az útválasztási táblázathoz lesznek hozzárendelve, és képesnek kell lenniük arra, hogy megismerjék az útvonalakat (statikus és dinamikus propagálással) ebben az útválasztási táblázatban.

   * **Propagálás:** A kapcsolatok propagálják az útvonalakat az útválasztási táblákhoz. Az 1., 2. és 3. virtuális hálózatok kiválasztásával engedélyezheti a terjesztési útvonalakat az 1., 2. és 3. virtuális hálózatok az útválasztási táblázatba. Nincs szükség a fiókirodák kapcsolatainak RT_V2Bra való propagálására, mivel a VNet-forgalom a VNET4 felé NVA keresztül megy keresztül.
  
1. Szerkessze az útválasztási táblázat alapértelmezett **DefaultRouteTable**.

   Az összes VPN-, ExpressRoute-és felhasználói VPN-kapcsolat az alapértelmezett útválasztási táblázathoz van társítva. Az összes VPN-, ExpressRoute-és felhasználói VPN-kapcsolat továbbítja az útvonalakat ugyanahhoz az útválasztási táblákhoz.

   * **Útvonalak:** Vegyen fel egy összesített statikus útvonal-bejegyzést az 1., 2. és 3. számú virtuális hálózatok (10.1.0.0/16 a **2. ábrán**) a következő ugrással, mint a VNET 4 kapcsolat. A VNET 1, 2 és 3 összesítő előtagjaihoz is konfigurálnia kell egy statikus útvonalat a VNET 4 kapcsolata esetében, és a következő ugrást kell megadnia a NVA megadott IP-címére a VNET 4-ben.

   * **Társítás:** Győződjön meg arról, hogy az ágak (VPN/ER/P2S) beállítás van kiválasztva, és gondoskodjon arról, hogy a helyszíni fiókirodák kapcsolatai a *defaultroutetable*legyenek társítva.

   * **Propagálás forrása:** Győződjön meg arról, hogy az ágak (VPN/ER/P2S) beállítás van kiválasztva, így biztosítva, hogy a helyi kapcsolatok propagálják az útvonalakat a *defaultroutetable*.

**2. ábra**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="2. ábra":::

## <a name="next-steps"></a>További lépések

* A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
* További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).
