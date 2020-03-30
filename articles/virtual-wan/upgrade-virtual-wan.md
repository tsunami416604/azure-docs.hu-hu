---
title: Az Azure Virtual WAN frissítése alapról standardra – Azure portál | Microsoft dokumentumok
description: A nagyobb funkcionalitás érdekében frissítheti a virtuális WAN-típust.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 83fd5bafb5496908403c50dc0e000fd33a836c95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515810"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>Virtuális WAN frissítése alapról standardra

Ez a cikk segít az alapszintű WAN standard WAN-ra való frissítésében. Az "Alapszintű" WAN-típus az összes benne lévő elosztót alapszintű termékváltozat-központként hozza létre. Az alapszintű hubokcsak a helyek közötti VPN-funkciókra korlátozódnak. A "Standard" WAN-típus szabványos Termékváltozat-elosztóként hozza létre az összes benne lévő elosztót. Standard elosztók használata esetén engedélyezheti az ExpressRoute, a Felhasználó (point-to-site) VPN-t, a teljes hálóközpontot és a virtuális hálózat közötti átvitelt az Azure hubokon keresztül.

Az alábbi táblázat az egyes WAN-típusokhoz elérhető konfigurációkat mutatja be:

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>A virtuális WAN-típus módosítása

1. A virtuális WAN lapján válassza a **Konfiguráció** lehetőséget a Konfiguráció lap megnyitásához.

   ![Virtuális WAN ábrája](./media/upgrade-virtual-wan/1.png)
2. A Virtual WAN típushoz válassza a **Standard** lehetőséget a legördülő menüből.

   ![Virtuális WAN ábrája](./media/upgrade-virtual-wan/2.png)
3. Értse meg, hogy ha szabványos virtuális WAN-ra frissít, nem állíthatja vissza az alapszintű virtuális WAN-t. Válassza **a Megerősítés** lehetőséget, ha frissíteni szeretne.

   ![Virtuális WAN ábrája](./media/upgrade-virtual-wan/4.png)
4. A módosítás mentése után a virtuális WAN-oldal hasonlónak tűnik ehhez a példához.

   ![Virtuális WAN ábrája](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>További lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.