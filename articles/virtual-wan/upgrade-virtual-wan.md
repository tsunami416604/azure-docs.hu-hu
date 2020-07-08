---
title: Azure-beli virtuális WAN frissítése alapszintről standardra – Azure Portal | Microsoft Docs
description: A virtuális WAN-típust a nagyobb funkcionalitáshoz is frissítheti.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 769aa6c0d546b7a9bcbe355136bad811fb4f47b3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753306"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>Virtuális WAN frissítése alapszintről standard verzióra

Ebből a cikkből megtudhatja, hogyan frissíthet egy alapszintű WAN-t egy standard WAN-ra. Az "alapszintű" WAN-típus az alapszintű SKU-hubokként hozza létre az összes csomópontot. Alapszintű központban csak a helyek közötti VPN-funkciókra korlátozódik. A "standard" WAN-típus a benne található összes hubokat szabványos SKU-hubokként hozza létre. A standard szintű hubok használatakor engedélyezheti a ExpressRoute, a felhasználó (pont – hely) VPN-t, a teljes szembőségű hubot és a VNet-VNet továbbítást az Azure-hubokon keresztül.

A következő táblázat az egyes WAN-típusok számára elérhető konfigurációkat mutatja be:

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>A virtuális WAN típusának módosítása

1. A virtuális WAN lapon válassza a **konfiguráció** elemet a konfigurációs lap megnyitásához.

   ![Virtuális WAN ábrája](./media/upgrade-virtual-wan/1.png)
2. A virtuális WAN típusnál válassza a **standard** lehetőséget a legördülő listából.

   ![Virtuális WAN ábrája](./media/upgrade-virtual-wan/2.png)
3. Ismerje meg, hogy ha standard virtuális WAN-ra frissít, nem térhet vissza alapszintű virtuális WAN-ra. Válassza a **megerősítés** lehetőséget, ha frissíteni szeretné.

   ![Virtuális WAN ábrája](./media/upgrade-virtual-wan/4.png)
4. A módosítás mentése után a virtuális WAN-oldal ehhez a példához hasonlóan néz ki.

   ![Virtuális WAN ábrája](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>További lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.