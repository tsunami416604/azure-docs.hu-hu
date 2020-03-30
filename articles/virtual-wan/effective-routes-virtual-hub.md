---
title: 'Virtuális központ hatékony útvonalainak megtekintése: Azure Virtual WAN | Microsoft dokumentumok'
description: Hatékony útvonalak az Azure Virtual WAN virtuális elosztójának hatékony útvonalaihoz
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 1173da81736661048d1e4e12d9919bc2aadf73ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515849"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>Virtuális központ hatékony útvonalainak megtekintése

Megtekintheti a Virtual WAN hub összes útvonalát az Azure Portalon. Az útvonalak megtekintéséhez keresse meg a virtuális központot, majd válassza **az Útválasztás -> Hatékony útvonalak megtekintése**lehetőséget.

## <a name="understanding-routes"></a><a name="understand"></a>Az útvonalak ismertetése

A következő példa segítségével jobban megismerheti, hogyan jelenik meg a Virtuális WAN útválasztás.

Ebben a példában van egy virtuális WAN három hubkal. Az első csomópont az USA keleti régiójában található, a második a nyugat-európai régióban, a harmadik pedig az USA nyugati régiójában. A virtuális WAN-ban az összes hub össze van kapcsolva. Ebben a példában azt feltételezzük, hogy az USA keleti és nyugat-európai központjai helyszíni ágakból (küllők) és az Azure virtuális hálózatokból (küllők) származó kapcsolatokkal rendelkeznek.

Egy Azure virtuális hálózat küllő (10.4.0.0/16) egy hálózati virtuális berendezés (10.4.0.6) tovább peered egy virtuális hálózat (10.5.0.0/16). A központi útvonaltáblával kapcsolatos további információkért lásd a cikk későbbi részében található [További információkat.](#abouthubroute)

Ebben a példában azt is feltételezzük, hogy a Nyugat-európai 1-es fiók az USA keleti részéhez, valamint a nyugat-európai központhoz kapcsolódik. Az USA keleti részén található ExpressRoute-áramkör a 2- es részleget köti össze az USA keleti központi központjával.

![diagram](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view-effective-routes"></a><a name="view"></a>Hatékony útvonalak megtekintése

Ha a portálon a "Hatékony útvonalak megtekintése" lehetőséget választja, az a Kelet-USA-központ [Hub-útvonaltáblájában](#routetable) látható kimenetet hozza létre.

Ennek perspektívába való bekerüléséhez az első sor azt jelenti, hogy az USA keleti része hubja megtanulta a 10.20.1.0/24 (1. ág) útvonalát a VPN *Next ugrás típusú* kapcsolat miatt ('Következő ugrás" VPN-átjárópéldány0 IP 10.1.0.6, Instance1 IP 10.1.0.7). *Az Origin-pontok útvonala* az erőforrás-azonosítóhoz. *Az AS elérési út* az 1.

### <a name="hub-route-table"></a><a name="routetable"></a>Hub útvonaltábla

Az "AS Path" mező megtekintéséhez használja a táblázat alján található görgetősávot.

| **Előtag** |  **Következő ugrási típus** | **Következő ugrás** |  **Útvonal eredete** |**AS elérési út** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|VPN |10.1.0.6, 10.1.0.7| /subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw| 20000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/expressRouteGateways/4444a6ac74d85555-eastus-gw|21000|
|10.23.1.0/24| VPN |10.1.0.6, 10.1.0.7|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw|23000|
|10.4.0.0/16|Virtuális hálózati kapcsolat| Kapcsolaton |  |  |
|10.5.0.0/16| IP-cím| 10.4.0.6|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|0.0.0.0/0| IP-cím| `<Azure Firewall IP>` |/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|10.22.1.0/16| Távoli központ|10.8.0.6, 10.8.0.7|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_| 4848-22000 |
|10.9.0.0/16| Távoli központ|  Kapcsolaton |/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_1| |

>[!NOTE]
> Ha az USA keleti és nyugat-európai csomópontjai nem kommunikálnának egymással a példatopológiában, a megtanult útvonal (10.9.0.0/16) nem létezne. A hubok csak olyan hálózatokat hirdetnek, amelyek közvetlenül kapcsolódnak hozzájuk.
>

## <a name="additional-information"></a><a name="additional"></a>További információk

### <a name="about-the-hub-route-table"></a><a name="abouthubroute"></a>A központi útvonaltábla

Létrehozhat egy virtuális hub útvonalat, és alkalmazhatja az útvonalat a virtuális hub útvonaltáblájára. A virtuális központ útválasztási táblázatán több útvonalat is alkalmazhat. Ez lehetővé teszi, hogy egy IP-címen keresztül állítsa be a célvirtuális hálózat útvonalát (általában a hálózati virtuális berendezés (NVA) egy küllős virtuális hálózaton keresztül). Az NVA-król további információt a [Forgalom irányítása virtuális csomópontról NVA-ba](virtual-wan-route-table-portal.md)című témakörben talál.

### <a name="about-default-route-00000"></a><a name="aboutdefaultroute"></a>Az alapértelmezett útvonal (0.0.0/0)

A virtuális elosztó képes a megtanult alapértelmezett útvonal at egy virtuális hálózatra, egy helyek közötti VPN-re és egy ExpressRoute-kapcsolatra propagálni, ha a jelző "Engedélyezve" a kapcsolaton. Ez a jelző virtuális hálózati kapcsolat, VPN-kapcsolat vagy ExpressRoute-kapcsolat szerkesztésekor látható. Az "EnableInternetSecurity" alapértelmezés szerint mindig hamis a Hub VNet, ExpressRoute és VPN-kapcsolatokon.

Az alapértelmezett útvonal nem a virtuális WAN hubból származik. Az alapértelmezett útvonal akkor lesz propagálva, ha a virtuális WAN hub már megtanulta a tűzfal központi telepítése miatt, vagy ha egy másik csatlakoztatott hely kényszerített bújtatási engedélyezése.

## <a name="next-steps"></a>További lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintését](virtual-wan-about.md).