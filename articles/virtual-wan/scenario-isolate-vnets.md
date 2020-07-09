---
title: 'Forgatókönyv: a virtuális hálózatok elkülönítése'
titleSuffix: Azure Virtual WAN
description: Forgatókönyvek az útválasztási elkülönítéshez virtuális hálózatok
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: f43f17a0f3742831920836e448de3ef757f2dfa6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568703"
---
# <a name="scenario-isolating-vnets"></a>Forgatókönyv: a virtuális hálózatok elkülönítése

A virtuális WAN virtuális hub útválasztásával kapcsolatban igen sok lehetőség áll rendelkezésre. Ebben a forgatókönyvben a cél az, hogy megakadályozza, hogy a virtuális hálózatok más elérésű legyen. Ez az úgynevezett virtuális hálózatok elkülönítése. A VNet belüli munkaterhelések elkülönítettek maradnak, és nem tudnak kommunikálni más virtuális hálózatok, mint bármely más esetben. A virtuális hálózatok azonban az összes ág (VPN, ÖÖÖ és felhasználói VPN) eléréséhez szükséges. Ebben az esetben minden VPN-, ExpressRoute-és felhasználói VPN-kapcsolat ugyanahhoz és egy útválasztási táblához van társítva. Az összes VPN-, ExpressRoute-és felhasználói VPN-kapcsolat továbbítja az útvonalakat ugyanahhoz az útválasztási táblákhoz. További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="workflow"></a>Forgatókönyv-munkafolyamat

Ennek a forgatókönyvnek a konfigurálásához a következő lépéseket kell figyelembe vennie:

1. Hozzon létre egy egyéni útválasztási táblázatot. A példában az útválasztási táblázat **RT_VNet**. Útválasztási táblázat létrehozásával kapcsolatban lásd: [a virtuális központ útválasztásának konfigurálása](how-to-virtual-hub-routing.md). További információ az útválasztási táblákról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).
2. A **RT_VNet** útválasztási táblázat létrehozásakor adja meg a következő beállításokat:

   * **Társítás**: válassza ki az elkülöníteni kívánt virtuális hálózatok.
   * **Propagálás**: válassza ki az ágak, a hozzá tartozó ág (VPN/er/P2S) kapcsolatok lehetőséget, majd propagálja az útvonalakat ebbe az útválasztási táblába.

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="Elkülönített virtuális hálózatok":::

## <a name="next-steps"></a>További lépések

* A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
* További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).
