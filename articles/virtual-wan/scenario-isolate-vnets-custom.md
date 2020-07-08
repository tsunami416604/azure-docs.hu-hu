---
title: 'Forgatókönyv: a virtuális hálózatok egyéni elkülönítése'
titleSuffix: Azure Virtual WAN
description: Az Útválasztás forgatókönyvei – megakadályozza, hogy a kiválasztott virtuális hálózatok egymáshoz jussanak
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3719956df0dce62ee69d8e306ff2cad27197616d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568707"
---
# <a name="scenario-custom-isolation-for-vnets"></a>Forgatókönyv: a virtuális hálózatok egyéni elkülönítése

A virtuális WAN virtuális hub útválasztásával kapcsolatban igen sok lehetőség áll rendelkezésre. A virtuális hálózatok egyéni elkülönítési forgatókönyvében a cél annak megakadályozása, hogy a virtuális hálózatok adott készlete képes legyen a virtuális hálózatok más konkrét készletének elérésére. A virtuális hálózatok azonban az összes ág (VPN/ER/User VPN) eléréséhez szükséges.

Ebben az esetben a VPN-, ExpressRoute-és felhasználói VPN-kapcsolatok (együttesen ágak) ugyanahhoz az útválasztási táblához vannak társítva (az alapértelmezett útválasztási táblázat). Az összes VPN-, ExpressRoute-és felhasználói VPN-kapcsolat továbbítja az útvonalakat ugyanahhoz az útválasztási táblákhoz. További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="architecture"></a>Forgatókönyv-munkafolyamat

Az **1. ábrán**kék és piros VNet kapcsolatok találhatók.

* A kék kapcsolatban álló virtuális hálózatok elérheti egymással, valamint elérheti az összes ág (VPN/ER/P2S) kapcsolatait.
* A piros virtuális hálózatok elérheti egymást, valamint elérheti az összes ág (VPN/ER/P2S) kapcsolatait.

Az Útválasztás beállításakor vegye figyelembe a következő lépéseket.

1. Hozzon létre két egyéni útválasztási táblázatot a Azure Portalban, **RT_BLUE** és **RT_RED**.
2. Az útválasztási táblázat **RT_BLUE**a következő alatt:
   * **Társítás**: válassza az összes kék virtuális hálózatok
   * **Propagálás**: ágak esetében válassza a fiókok, a hozzá tartozó ág (VPN/er/P2S) kapcsolatok lehetőséget, majd propagálja az útvonalakat ebbe az útválasztási táblába.
3. Ismételje meg ugyanezeket a lépéseket a vörös virtuális hálózatok és ágakhoz **RT_RED** útválasztási táblázathoz (VPN/er/P2S).

Ez azt eredményezi, hogy az útválasztási konfiguráció megváltozik az alábbi ábrán látható módon.

**1. ábra**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="1. ábra":::

## <a name="next-steps"></a>További lépések

* A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
* További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).
