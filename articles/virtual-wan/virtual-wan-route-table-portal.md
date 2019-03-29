---
title: Hozzon létre egy Azure virtuális WAN virtuális központ útválasztási táblázat – Azure portal |} A Microsoft Docs
description: Virtuális WAN virtuális központ útválasztási táblázatot figyelmeztetik a forgalmat egy hálózati virtuális berendezésre, a portál használatával.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 2c8b3b4671fd14f9b10b8491861ae2c652f0188b
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580583"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Az nva-k virtuális WAN hub útválasztási táblázat létrehozása: Azure Portal

Ez a cikk bemutatja, hogyan figyelmeztetik a hub forgalmát egy hálózati virtuális készüléket (NVA).

![A Virtual WAN ábrája](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy a következő feltételek teljesüléséről:

*  Egy hálózati virtuális készüléket (NVA) rendelkezik. Egy hálózati virtuális készüléket egy külső szoftver tetszőleges általában kiépített Azure Marketplace-ről egy virtuális hálózaton.

    * Magánhálózati IP-cím hozzá kell rendelni az NVA hálózati adapter.

    * Az nva-t a virtuális központ nem üzemel. Egy külön virtuális hálózatban kell telepíteni.

    *  Az NVA virtuális hálózatok közötti lehet, hogy rendelkezik egy, vagy túl sok virtuális hálózathoz csatlakozik hozzá. Ebben a cikkben azt tekintse meg az NVA virtuális hálózatok közötti, egy "közvetett küllő virtuális hálózat". Ezek a virtuális hálózatok virtuális hálózatok közötti társviszony segítségével csatlakoztathatók az NVA virtuális hálózathoz.
*  2 virtuális hálózatokat hozott létre. Akkor lesz küllő virtuális hálózatokhoz.

    * Ebben a gyakorlatban a VNet-küllő címterek a következők: VNet1: 10.0.2.0/24 és a VNet2: 10.0.3.0/24. Ha a virtuális hálózat létrehozása információra van szüksége, tekintse meg [hozzon létre egy virtuális hálózatot](../virtual-network/quick-create-portal.md).

    * Győződjön meg róla, nincsenek a nem virtuális hálózati átjárók a virtuális hálózatok valamelyikében.
    * Ehhez a konfigurációhoz a virtuális hálózatok nem szükséges egy átjáró-alhálózatot.

## <a name="signin"></a>1. Bejelentkezés

Egy böngészőből lépjen az [Azure Portalra](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

## <a name="vwan"></a>2. Virtuális WAN létrehozása

Hozzon létre egy virtuális WAN. Ebben a gyakorlatban az alkalmazásában használhatja a következő értékeket:

* **Virtuális WAN neve:** myVirtualWAN
* **Erőforráscsoport:** testRG
* **Hely:** USA nyugati régiója

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. Elosztó létrehozása

Az eseményközpont létrehozásához. Ebben a gyakorlatban az alkalmazásában használhatja a következő értékeket:

* **Hely:** USA nyugati régiója
* **Name:** westushub
* **Hub magánhálózati címtartomány:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4. Hozzon létre és alkalmazza a hub útválasztási táblázat

Frissítse a hub hub útválasztási táblázat. Ebben a gyakorlatban az alkalmazásában használhatja a következő értékeket:

* **Közvetett küllő virtuális hálózat címterei:** (A VNet1 és a VNet2) 10.0.2.0/24 és 10.0.3.0/24
* **DMZ NVA hálózati adapter magánhálózati IP-cím:** 10.0.4.5

1. Keresse meg a virtuális WAN.
2. Kattintson a hubot, amelyhez hozzá szeretne létrehozni egy útválasztási táblázatot.
3. Kattintson a **...** , és kattintson a **virtuális központ szerkesztése**.
4. Az a **virtuális központ szerkesztése** lapon görgessen le, jelölje be a **útválasztás táblázatból**.
5. Az a **Ha a cél-előtagot** oszlopban, adja hozzá a-címtereket. Az a **küldeni a következő Ugrás** oszlop, adja hozzá a DMZ NVA hálózati adapter magánhálózati IP-címet.
6. Kattintson a **megerősítése** a hub erőforrás frissítése az útválasztási táblázat beállításokkal.

## <a name="connections"></a>5. A virtuális hálózatok közötti kapcsolatok létrehozása

Minden egyes közvetett küllő virtuális hálózatok (a VNet1 és a VNet2) közötti kapcsolat létrehozásához a hubhoz. Ezután hozzon létre egy kapcsolatot az NVA virtuális hálózatról a hub.

 Ebben a lépésben a következő értékeket használhatja:

| Virtuális hálózat neve| Kapcsolat neve|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Ismételje meg a következő eljárást minden egyes virtuális hálózat, amely kapcsolódni szeretne.

1. A virtuális WAN lapján kattintson a **Virtuális hálózati kapcsolatok** elemre.
2. A virtuális hálózati kapcsolat lapján kattintson a **+Kapcsolat hozzáadása** elemre.
3. A **Kapcsolat hozzáadása** lapon töltse ki a következő mezőket:

    * **Kapcsolat neve** – Nevezze el a kapcsolatot.
    * **Elosztók** – Válassza ki azt az elosztót, amelyet a kapcsolattal társítani kíván.
    * **Előfizetés** – Ellenőrizze az előfizetést.
    * **Virtuális hálózat** – Válassza ki azt a virtuális hálózatot, amelyet az elosztóhoz csatlakoztatni kíván. A virtuális hálózat nem rendelkezhet már meglévő virtuális hálózati átjáróval.
4. Kattintson a **OK** a kapcsolat létrehozásához.

## <a name="next-steps"></a>További lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.