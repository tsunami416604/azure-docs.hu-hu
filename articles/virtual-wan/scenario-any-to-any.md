---
title: 'Forgatókönyv: bármilyen'
titleSuffix: Azure Virtual WAN
description: Forgatókönyvek az útválasztáshoz – bármely – bármely
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: ecc2b3cf236cb2a78fd595189649e7f6b176d709
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568714"
---
# <a name="scenario-any-to-any"></a>Forgatókönyv: bármilyen

A virtuális WAN virtuális hub útválasztásával kapcsolatban igen sok lehetőség áll rendelkezésre. Bármilyen szituációban bármely küllő elérheti egy másikat. Ha több központ létezik, a hub – hub útválasztás (más néven az Inter-hub) alapértelmezés szerint engedélyezve van a standard virtuális WAN-ban. 

Ebben a forgatókönyvben a VPN-, a ExpressRoute-és a felhasználói VPN-kapcsolatok ugyanahhoz az útválasztási táblához vannak társítva. Az összes VPN-, ExpressRoute-és felhasználói VPN-kapcsolat továbbítja az útvonalakat ugyanahhoz az útválasztási táblákhoz. További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Forgatókönyv-architektúra

Az **1. ábrán**minden virtuális hálózatok és ág (VPN, EXPRESSROUTE, P2S) elérheti egymást. Egy virtuális központban a kapcsolatok a következőképpen működnek:

* A VPN-kapcsolat VPN-helyet csatlakoztat egy VPN-átjáróhoz.
* A virtuális hálózati kapcsolat virtuális hálózatot csatlakoztat egy virtuális hubhoz. A virtuális központ útválasztója biztosítja a virtuális hálózatok közötti átviteli funkciót.
* Az ExpressRoute-kapcsolat egy ExpressRoute áramkört csatlakoztat egy ExpressRoute-átjáróhoz.

Ezek a kapcsolatok (alapértelmezés szerint a létrehozáskor) az alapértelmezett útválasztási táblázathoz vannak társítva, hacsak nem állítja be a kapcsolat útválasztási konfigurációját **sem vagy**egy egyéni útválasztási táblázatot. Ezek a kapcsolatok a útvonalakat is propagálják, alapértelmezés szerint az alapértelmezett útválasztási táblázatra. Ez azt teszi lehetővé, hogy a bármilyen küllős (VNet, VPN, ER, P2S) típusú forgatókönyvben elérhetők legyenek egymással.

**1. ábra**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="1. ábra":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Forgatókönyv-munkafolyamat

Ez a forgatókönyv alapértelmezés szerint engedélyezve van a standard szintű virtuális WAN esetében. Ha a ág – ág beállítás le van tiltva a WAN-konfigurációban, ez letiltja a fiókirodák közötti kapcsolatot. A VPN/ExpressRoute/User VPN a virtuális WAN ág-küllői.

## <a name="next-steps"></a>További lépések

* A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
* További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).
