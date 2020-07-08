---
title: 'Forgatókönyv: forgalom irányítása NVA keresztül'
titleSuffix: Azure Virtual WAN
description: Forgalom irányítása NVA-n keresztül
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 0716ca8f0457ca801098c97dd7a5e68751822d4d
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85848091"
---
# <a name="scenario-route-traffic-through-an-nva"></a>Forgatókönyv: forgalom irányítása NVA keresztül

A virtuális WAN virtuális hub útválasztásával kapcsolatban igen sok lehetőség áll rendelkezésre. Ebben a NVA-forgatókönyvben a cél az, hogy átirányítsa a forgalmat egy NVA (hálózati virtuális készüléken) a VNet és a VNet ágat. További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Forgatókönyv-architektúra

Az **1. ábrán**két hub van; **1** . és **2**. központ.

* Az **1** . és a **2** . hubot közvetlenül csatlakoztatják a NVA virtuális hálózatok **VNET 2** és a **VNET 4**szolgáltatáshoz.

* A **VNET 5** és a **VNET 6** a **VNET 2**.

* A **VNET 7** és a **VNET 8** a **VNET 4**-vel van összetársítva.

* Az **5, 6, 7, 8 virtuális hálózatok** közvetett küllők, amelyek nem kapcsolódnak közvetlenül a virtuális hubhoz.

**1. ábra**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="1. ábra":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Forgatókönyv-munkafolyamat

Az Útválasztás NVA-n keresztüli beállításához a következő lépéseket kell figyelembe venni:

1. Azonosítsa a NVA küllős VNet-kapcsolatokat. Az **1. ábrán**a **VNET 2 (eastusconn)** és a **VNET 4 (weconn) kapcsolatok**szerepelnek.

   Győződjön meg arról, hogy a UDR beállítása megtörtént:
   * 5 és 6 közötti VNET – VNET 2 NVA IP
   * 7 és 8 közötti VNET VNET 4 NVA IP-címről 
   
   Az 5, 6, 7, 8 VNET nem kell közvetlenül a virtuális hubhoz csatlakoznia. Győződjön meg arról, hogy a virtuális hálózatok 5, 6, 7, 8 NSG engedélyezi az ág (VPN/ER/P2S) forgalmának, illetve a távoli virtuális hálózatok csatlakoztatott virtuális hálózatok. Például az VNET 5, 6 esetében biztosítania kell, hogy a NSG engedélyezze a helyszíni címek előtagjainak és a 2., 8. virtuális hálózatok, amelyek a távoli hubhoz csatlakoznak. 

2. Adjon hozzá egy összesített statikus útvonal-bejegyzést az virtuális hálózatok 2, 5, 6 és az 1. hub alapértelmezett útválasztási táblázatához. 

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="Példa":::

3. Konfiguráljon egy statikus útvonalat a virtuális hálózatok 5, 6 a VNET 2 virtuális hálózati kapcsolatban. A virtuális hálózati kapcsolatok útválasztási konfigurációjának beállításához lásd: [virtuális központ útválasztása](how-to-virtual-hub-routing.md#routing-configuration).

4. Adjon hozzá egy összesített statikus útválasztási bejegyzést a 4., 7. és 8. virtuális hálózatok az 1. hub alapértelmezett útválasztási táblájához.

5. Ismételje meg a 2., 3. és 4. lépést a hub alapértelmezett útválasztási táblázatához.

Ez azt eredményezi, hogy az útválasztási konfiguráció megváltozik az alábbi ábrán látható módon.

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="Eredmény":::

## <a name="next-steps"></a>További lépések

* A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
* További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).
