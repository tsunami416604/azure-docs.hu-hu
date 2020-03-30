---
title: 'Virtuális WAN: Virtuális központ útvonaltáblájának létrehozása az NVA-hoz: Azure portal'
description: Virtual WAN virtuális központi útvonaltábla a forgalmat egy hálózati virtuális berendezésa portál használatával.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 0807b535adc45093b439dba5ab8a0ea26b2a0721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402949"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Virtuális WAN-központ útvonaltáblájának létrehozása nem-

Ez a cikk bemutatja, hogyan irányíthatja a forgalmat egy ág (helyszíni hely) csatlakozik a virtuális WAN hub egy küllővirtuális hálózat (VNet) egy hálózati virtuális berendezés (NVA) keresztül.

![Virtuális WAN ábrája](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Előkészületek

Ellenőrizze, hogy teljesítette-e az alábbi feltételeket:

*  Hálózati virtuális készülék (NVA) van. A hálózati virtuális berendezés egy harmadik féltől származó szoftver, amely általában az Azure Marketplace-en egy virtuális hálózatban van kiépítve.

    * Az NVA hálózati adapterhez magánhálózati IP-címet kell rendelni.

    * Az NVA nincs telepítve a virtuális központban. Külön virtuális hálózatban kell telepíteni.

    *  Lehet, hogy az NVA virtuális hálózathoz egy vagy több virtuális hálózat csatlakozik. Ebben a cikkben az NVA virtuális hálózatot "közvetett küllős virtuális hálózatként" hivatkozunk. Ezek a virtuális hálózatok virtuális hálózathoz virtuális társviszony-létesítés használatával csatlakoztathatók. A virtuális hálózatok társviszony-létesítési hivatkozásait fekete nyilak ábrázolják a fenti ábrán a VNet 1, a VNet 2 és az NVA virtuális hálózat között.
*  Két virtuális hálózatot hozott létre. Ezeket küllős virtuális hálózatként fogják használni.

    * A vnet küllős címterek a következők: VNet1: 10.0.2.0/24 és VNet2: 10.0.3.0/24. Ha információra van szüksége a virtuális hálózat létrehozásáról, olvassa el [a Virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md)című témakört.

    * Győződjön meg arról, hogy nincsenek virtuális hálózati átjárók a virtuális hálózatok egyikében sem.

    * A virtuális hálózatok nem igényelnek átjáró-alhálózatot.

## <a name="1-sign-in"></a><a name="signin"></a>1. Bejelentkezés

Egy böngészőből lépjen az [Azure Portalra](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

## <a name="2-create-a-virtual-wan"></a><a name="vwan"></a>2. Hozzon létre egy virtuális WAN

Hozzon létre egy virtuális WAN.Create a virtual WAN. Használja a következő példaértékeket:

* **Virtuális WAN név:** myVirtualWAN
* **Erőforráscsoport:** testRG
* **Helyszín:** USA nyugati

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="3-create-a-hub"></a><a name="hub"></a>3. Hub létrehozása

Hozza létre a központot. Használja a következő példaértékeket:

* **Helyszín:** USA nyugati
* **Név:** westushub
* **Hub privát címtér:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="4-create-and-apply-a-hub-route-table"></a><a name="route"></a>4. Hub útvonaltábla létrehozása és alkalmazása

Frissítse a hubot egy központi útvonaltáblával. Használja a következő példaértékeket:

* **Küllős virtuális hálózat címterei:** (VNet1 és VNet2) 10.0.2.0/24 és 10.0.3.0/24
* **DMZ NVA hálózati adapter privát IP-címe:** 10.0.4.5

1. Keresse meg a virtuális WAN-t.
2. Kattintson arra a hubra, amelyhez útvonaltáblát szeretne létrehozni.
3. Kattintson a **...**, majd a **Virtuális központ szerkesztése parancsra.**
4. A **Virtuális központi központ szerkesztése** lapon görgessen le, és jelölje be a **Tábla használata útválasztáshoz**jelölőnégyzetet.
5. Az **Ha a cél előtag oszlopban** adja hozzá a címtereket. A **Küldés** a következő ugrásra oszlopban adja hozzá a DMZ NVA hálózati adapter magánIP-címét.
6. Kattintson a **Megerősítés** gombra a hub-erőforrás nak az útvonaltábla beállításaival való frissítéséhez.

## <a name="5-create-the-vnet-connections"></a><a name="connections"></a>5. A virtuális hálózat kapcsolatainak létrehozása

Hozzon létre egy virtuális hálózati kapcsolatot minden közvetett küllővirtuális hálózat (VNet1 és VNet2) a hubhoz. Ezeket a virtuális hálózati kapcsolatokat a fenti ábrán látható kék nyilak ábrázolják. Ezután hozzon létre egy virtuális hálózat kapcsolatot az NVA virtuális hálózatról a hubra (fekete nyíl az ábrán).

 Ehhez a lépéshez a következő értékeket használhatja:

| Virtuális hálózat neve| Kapcsolat neve|
| --- | --- |
| VNet1 | tesztkapcsolat1 |
| VNet2 | tesztkapcsolat2 |
| NVAVNet | tesztkapcsolat3 |

Ismételje meg az alábbi eljárást minden csatlakoztatni kívánt virtuális hálózaton.

1. A virtuális WAN lapján kattintson a **Virtuális hálózati kapcsolatok** elemre.
2. A virtuális hálózati kapcsolat lapján kattintson a **+Kapcsolat hozzáadása** elemre.
3. A **Kapcsolat hozzáadása** lapon töltse ki a következő mezőket:

    * **Kapcsolat neve** – Nevezze el a kapcsolatot.
    * **Elosztók** – Válassza ki azt az elosztót, amelyet a kapcsolattal társítani kíván.
    * **Előfizetés** – Ellenőrizze az előfizetést.
    * **Virtuális hálózat** – Válassza ki azt a virtuális hálózatot, amelyet az elosztóhoz csatlakoztatni kíván. A virtuális hálózat nem rendelkezhet már meglévő virtuális hálózati átjáróval.
4. A kapcsolat létrehozásához kattintson az **OK** gombra.

## <a name="next-steps"></a>További lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.
