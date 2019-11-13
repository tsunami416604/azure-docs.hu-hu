---
title: 'Virtuális WAN: virtuális központ útválasztási táblázatának létrehozása a NVA: Azure Portal'
description: Virtuális WAN virtuális központ útválasztási táblázata, amely a portál használatával irányítja át a forgalmat egy hálózati virtuális berendezésre.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 8f24b94226daffb769993c9f6659909fdff039b6
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014985"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Hozzon létre egy virtuális WAN hub útválasztási táblázatot a NVA: Azure Portal

Ez a cikk bemutatja, hogyan irányíthatja át a forgalmat egy hubhoz egy hálózati virtuális berendezésre (NVA).

![Virtuális WAN ábrája](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Előkészületek

Ellenőrizze, hogy teljesültek-e az alábbi feltételek:

*  Rendelkezik egy hálózati virtuális berendezéssel (NVA). A hálózati virtuális berendezés egy olyan külső gyártótól származó szoftver, amelyet általában az Azure Marketplace-en kell kiépíteni egy virtuális hálózaton.

    * A NVA hálózati adapterhez hozzá kell rendelni egy magánhálózati IP-címet.

    * A NVA nincs telepítve a virtuális központban. Ezt külön VNet kell telepíteni.

    *  A NVA VNet egy vagy több virtuális hálózata is lehet. Ebben a cikkben a NVA VNet "közvetett küllős VNet" nevezzük. Ezek a virtuális hálózatok a VNet-társítás használatával csatlakoztathatók a NVA-VNet.
*  Létrehozta a 2 virtuális hálózatok. A rendszer küllős virtuális hálózatok fogja használni őket.

    * Ebben a gyakorlatban a VNet küllős címtartomány a következő: VNet1:10.0.2.0/24 és VNet2:10.0.3.0/24. Ha a VNet létrehozásával kapcsolatos információkra van szüksége, tekintse meg [a virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md)című témakört.

    * Győződjön meg arról, hogy nincsenek virtuális hálózati átjárók a virtuális hálózatok egyikében sem.
    * Ennél a konfigurációnál ezek a virtuális hálózatok nem igényelnek átjáró-alhálózatot.

## <a name="signin"></a>1. bejelentkezés

Egy böngészőből lépjen az [Azure Portalra](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

## <a name="vwan"></a>2. virtuális WAN létrehozása

Hozzon létre egy virtuális WAN-t. A gyakorlat szempontjából a következő értékeket használhatja:

* **Virtuális WAN neve:** myVirtualWAN
* **Erőforráscsoport:** testRG
* **Hely:** USA nyugati régiója

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. hub létrehozása

Hozza létre a hubot. A gyakorlat szempontjából a következő értékeket használhatja:

* **Hely:** USA nyugati régiója
* **Név:** westushub
* **Központi magánhálózati címtartomány:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4. hub-útválasztási táblázat létrehozása és alkalmazása

Frissítse a hubot egy hub útválasztási táblázattal. A gyakorlat szempontjából a következő értékeket használhatja:

* **Közvetett küllő VNet:** (VNet1 és VNet2) 10.0.2.0/24 és 10.0.3.0/24
* **DMZ NVA hálózati adapter magánhálózati IP-címe:** 10.0.4.5

1. Navigáljon a virtuális WAN-ra.
2. Kattintson arra a hubhoz, amelyhez útválasztási táblázatot kíván létrehozni.
3. Kattintson a **...** , majd a **virtuális központ szerkesztése**elemre.
4. A **virtuális központ szerkesztése** lapon görgessen le, és jelölje be a **tábla használata az útválasztáshoz**jelölőnégyzetet.
5. A **Ha cél előtagja** oszlopban adja hozzá a Címterület mezőt. A **Küldés a következő ugrásra** oszlopba adja hozzá a DMZ NVA hálózati adapter magánhálózati IP-címét.
6. Kattintson a **Confirm (megerősítés** ) gombra a központ erőforrásának az útválasztási táblázat beállításaival való frissítéséhez.

## <a name="connections"></a>5. hozza létre az VNet-kapcsolatokat

Hozzon létre egy-egy kapcsolatokat az egyes közvetett küllős VNet (VNet1 és VNet2) a hubhoz. Ezután hozzon létre egy kapcsolódást a NVA VNet a hubhoz.

 Ebben a lépésben a következő értékeket használhatja:

| VNet neve| Kapcsolat neve|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Ismételje meg az alábbi eljárást minden olyan VNet esetében, amelyhez csatlakozni szeretne.

1. A virtuális WAN lapján kattintson a **Virtuális hálózati kapcsolatok** elemre.
2. A virtuális hálózati kapcsolat lapján kattintson a **+Kapcsolat hozzáadása** elemre.
3. A **Kapcsolat hozzáadása** lapon töltse ki a következő mezőket:

    * **Kapcsolat neve** – Nevezze el a kapcsolatot.
    * **Elosztók** – Válassza ki azt az elosztót, amelyet a kapcsolattal társítani kíván.
    * **Előfizetés** – Ellenőrizze az előfizetést.
    * **Virtuális hálózat** – Válassza ki azt a virtuális hálózatot, amelyet az elosztóhoz csatlakoztatni kíván. A virtuális hálózat nem rendelkezhet már meglévő virtuális hálózati átjáróval.
4. A kapcsolódás létrehozásához kattintson **az OK** gombra.

## <a name="next-steps"></a>Következő lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.