---
title: 'Virtuális központ tényleges útvonalának megtekintése: Azure Virtual WAN | Microsoft Docs'
description: Az Azure Virtual WAN-ban lévő virtuális központ tényleges útvonalának megtekintése
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 1173da81736661048d1e4e12d9919bc2aadf73ee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "73515849"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>Virtuális központ tényleges útvonalának megtekintése

A Azure Portalban megtekintheti a virtuális WAN-központ összes útvonalát. Az útvonalak megtekintéséhez navigáljon a virtuális hubhoz, majd válassza az **Útválasztás – > a hatályos útvonalak megtekintése**lehetőséget.

## <a name="understanding-routes"></a><a name="understand"></a>Az útvonalak ismertetése

A következő példa segít jobban megérteni, hogyan jelenik meg a virtuális WAN-útválasztás.

Ebben a példában egy virtuális WAN-t, három hubokat tartalmaz. Az első hub az USA keleti régiójában található, a második központ a Nyugat-európai régióban található, a harmadik pedig az USA nyugati régiójában található. Egy virtuális WAN-ban az összes hub összekapcsolódik egymáshoz. Ebben a példában feltételezzük, hogy az USA keleti régiójában és a Nyugat-európai hubokban a helyszíni ágak (küllők) és az Azure Virtual Network (küllők) kapcsolatai vannak.

Az Azure VNet (10.4.0.0/16) egy hálózati virtuális berendezéssel (10.4.0.6) továbbra is egy VNet (10.5.0.0/16) van társítva. A központi útválasztási táblázattal kapcsolatos további információkért tekintse meg a cikk későbbi részében található [További információkat](#abouthubroute) .

Ebben a példában azt is feltételezzük, hogy az 1. Nyugat-európai ág az USA keleti régiójában, valamint a Nyugat-európai hubhoz csatlakozik. Az USA keleti régiójában a 2. ExpressRoute áramkör az USA keleti régiójában csatlakozik.

![diagram](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view-effective-routes"></a><a name="view"></a>Érvényes útvonalak megtekintése

Ha a portálon a "hatályos útvonalak megtekintése" lehetőséget választja, az az USA keleti régiójának [hub Route táblájában](#routetable) látható kimenetet hozza létre.

Ahhoz, hogy ez perspektívába kerüljön, az első sor azt jelenti, hogy az USA keleti régiója az 10.20.1.0/24 (1. ág) útvonalát a VPN *következő ugrás típusa* kapcsolat ("Next hop" VPN Gateway Instance0 IP 10.1.0.6, peldany1 "elemet IP 10.1.0.7) miatt megtanulta. Az *útvonal kezdőpontja* az erőforrás-azonosítóra mutat. Az *elérési* út az 1. ág elérési útját jelöli.

### <a name="hub-route-table"></a><a name="routetable"></a>Hub-útválasztási táblázat

A táblázat alján található görgetősáv használatával megtekintheti az "AS Path" kifejezést.

| **Előtag** |  **Következő ugrási típus** | **Következő ugrás** |  **Útvonal forrása** |**Elérési út** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|VPN |10.1.0.6, 10.1.0.7| /Subscriptions/`<sub>`/resourceGroups/`<rg>`/Providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-GW| 20000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/Subscriptions/`<sub>`/resourceGroups/`<rg>`/Providers/Microsoft.Network/expressRouteGateways/4444a6ac74e4d85555-eastus-GW|21000|
|10.23.1.0/24| VPN |10.1.0.6, 10.1.0.7|/Subscriptions/`<sub>`/resourceGroups/`<rg>`/Providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-GW|23000|
|10.4.0.0/16|Virtual Network-kapcsolatok| Hivatkozáson keresztül |  |  |
|10.5.0.0/16| IP-cím| 10.4.0.6|/Subscriptions/`<sub>`/resourceGroups/`<rg>`/Providers/Microsoft.Network/virtualhubs/easthub_1/routetables/table_1| |
|0.0.0.0/0| IP-cím| `<Azure Firewall IP>` |/Subscriptions/`<sub>`/resourceGroups/`<rg>`/Providers/Microsoft.Network/virtualhubs/easthub_1/routetables/table_1| |
|10.22.1.0/16| Távoli központ|10.8.0.6, 10.8.0.7|/Subscriptions/`<sub>`/resourceGroups/`<rg>`/Providers/Microsoft.Network/virtualhubs/westhub_| 4848-22000 |
|10.9.0.0/16| Távoli központ|  Hivatkozáson keresztül |/Subscriptions/`<sub>`/resourceGroups/`<rg>`/Providers/Microsoft.Network/virtualhubs/westhub_1| |

>[!NOTE]
> Ha az USA keleti régiója és a Nyugat-európai hubok nem kommunikálnak egymással a példában szereplő topológiában, a megtanult útvonal (10.9.0.0/16) nem létezik. A hubok csak azokat a hálózatokat hirdetik, amelyek közvetlenül csatlakoznak hozzájuk.
>

## <a name="additional-information"></a><a name="additional"></a>További információ

### <a name="about-the-hub-route-table"></a><a name="abouthubroute"></a>Tudnivalók a hub útválasztási táblájáról

Létrehozhatja a virtuális központ útvonalát, és alkalmazhatja az útvonalat a virtuális központ útválasztási táblájára. A virtuális központ útválasztási táblázatán több útvonalat is alkalmazhat. Ezzel a beállítással megadhat egy útvonalat a cél VNet egy IP-cím (jellemzően a hálózati virtuális berendezés (NVA) által küllős VNet) használatával. A NVA kapcsolatos további információkért lásd: [forgalom átirányítása egy virtuális központból egy NVA](virtual-wan-route-table-portal.md).

### <a name="about-default-route-00000"></a><a name="aboutdefaultroute"></a>Az alapértelmezett útvonal (0.0.0.0/0)

A virtuális központ képes propagálni egy megtanult alapértelmezett útvonalat egy virtuális hálózatra, egy helyek közötti VPN-re és egy ExpressRoute-kapcsolatra, ha a jelző "enabled" (engedélyezve) van a kapcsolaton. Ez a jelző látható a virtuális hálózati kapcsolat, a VPN-kapcsolat vagy egy ExpressRoute-kapcsolat szerkesztésekor. A "EnableInternetSecurity" mindig hamis alapértelmezés szerint a hub VNet, a ExpressRoute és a VPN-kapcsolatokon.

Az alapértelmezett útvonal nem a virtuális WAN-hubhoz származik. A rendszer az alapértelmezett útvonalat propagálja, ha a virtuális WAN-központ már megismerte a tűzfal központi telepítésének eredményeképpen, vagy ha egy másik csatlakoztatott hely esetében engedélyezve van a bújtatás.

## <a name="next-steps"></a>További lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintését](virtual-wan-about.md).