---
title: "Vegyen fel egy virtuális hálózat virtuális hálózati átjáró ExpressRoute: Portal: Azure |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan ExpressRoute egy már létrehozott erőforrás-kezelő virtuális hálózatot a virtuális hálózati átjárót ad hozzá."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2017
ms.author: cherylmc
ms.openlocfilehash: 2bd0cf8be87937044ad515a2c6f253b1711bb2bf
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>A virtuális hálózati átjáró konfigurálása az Azure portál használatával ExpressRoute
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasszikus - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Azure-portálon](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Ez a cikk végigvezeti a virtuális hálózati átjáró hozzáadása egy meglévő vnet. Ez a cikk végigvezeti a lépéseken hozzáadásához átméretezése, és távolítsa el a virtuális hálózathoz (VNet) átjáró egy már meglévő vnet. Ehhez a konfigurációhoz lépésekre, kifejezetten a Vnetek létrehozott erőforrás-kezelő telepítési modellel, amely egy ExpressRoute-konfigurációt fogja használni. További információ a virtuális hálózati átjárók és az átjáró konfigurációs beállításainak ExpressRoute: [kapcsolatos az ExpressRoute virtuális hálózati átjárók](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Mielőtt hozzálát

Ez a feladat lépéseit a következő konfigurációs hivatkozás listában található értékek alapján a VNet használja. Ebben a listában a példa lépései használjuk. A listában referenciaként, az értékeket cserélje le a saját másolhatja.

**Konfigurációs hivatkozáslista**

* Virtuális hálózati név = "TestVNet"
* Virtuális hálózati címterület = 192.168.0.0/16
* Alhálózati név = "Előtér" 
    * Alhálózati címtartományt = "192.168.1.0/24"
* Erőforráscsoport = "TestRG"
* Hely = "USA keleti régiója"
* Átjáró alhálózati név: "GatewaySubnet" mindig neve egy átjáró-alhálózatot kell *GatewaySubnet*.
    * Átjáró alhálózati címtartományt = "192.168.200.0/26"
* Átjáró Name = "ERGW"
* Átjáró IP-név = "MyERGWVIP"
* Átjáró típusa = "ExpressRoute" Ez a típus egy ExpressRoute-konfiguráció szükséges.
* Átjáró nyilvános IP-név = "MyERGWVIP"

Megtekintheti a [videó](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) , mielőtt megkezdené a konfigurációs lépéseket.

## <a name="create-the-gateway-subnet"></a>Az átjáróalhálózat létrehozása

1. Keresse meg a [portálon](http://portal.azure.com) azt a Resource Manager-alapú virtuális hálózatot, amelyhez létre kíván hozni egy virtuális hálózati átjárót.
2. A virtuális hálózat paneljének **Beállítások** részén az **Alhálózatok** elemre kattintva bontsa ki az Alhálózatok panelt.
3. Az **Alhálózatok** panel **+Átjáróalhálózat** elemére kattintva nyissa meg az **Alhálózat hozzáadása** panelt. 
   
    ![Az átjáró alhálózatának hozzáadása](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "Az átjáró alhálózatának hozzáadása")


4. Az alhálózat **nevénél** automatikusan megjelenik a „GatewaySubnet” érték. Ez az érték szükséges ahhoz, hogy az Azure felismerje, hogy az alhálózat egy átjáró alhálózata. Módosítsa úgy a **címtartomány** automatikusan kitöltött értékeit, hogy megfeleljenek a konfigurációs követelményeinek. Javasoljuk, hogy egy átjáróalhálózat létrehozásához, egy /27 vagy nagyobb (26, / / 25, stb.). Kattintson a **OK** értékek mentéséhez, és hozza létre az átjáró-alhálózatot.

    ![Az alhálózat hozzáadása](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "Az alhálózat hozzáadása")

## <a name="create-the-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása

1. A portálon a bal oldalon kattintson a **+** gombra, és írja be a „Virtuális hálózati átjáró” kifejezést a keresőmezőbe. A keresési eredmények között keresse meg a **Virtuális hálózati átjáró** elemet, és kattintson a bejegyzésre. A **Virtuális hálózati átjáró** panel alsó részén kattintson a **Létrehozás** gombra. Ez megnyitja a **Virtuális hálózati átjáró létrehozása** panelt.
2. A **Virtuális hálózati átjáró létrehozása** panelen töltse ki a virtuális hálózati átjáró értékeit.

    ![Virtuális hálózati átjáró létrehozása panel mezői](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "Virtuális hálózati átjáró létrehozása panel mezői")
3. **Név**: adjon nevet az átjárónak. Ez nem ugyanaz, mint egy átjáró alhálózatának elnevezése. Ez a létrehozni kívánt átjáróobjektum neve.
4. **Átjáró típusa**: válasszon **ExpressRoute**.
5. **SKU**: válassza ki az átjáró SKU-ját a legördülő listából.
6. **Hely**: Állítsa be úgy a **Hely** mezőt, hogy a virtuális hálózat helyére mutasson. Ha a hely nem egyezik meg azzal a régióval, ahol a virtuális hálózat található, a virtuális hálózat nem jelenik meg a Virtuális hálózat választása legördülő menüben.
7. Válassza ki azt a virtuális hálózatot, amelyhez hozzá kívánja adni az átjárót. Kattintson a **Virtuális hálózat** elemre a **Virtuális hálózat választása** panel megnyitásához. Válassza ki a VNet elemet. Ha a VNet nem jelenik meg, ellenőrizze, hogy a **Hely** mező arra a régióra mutat-e, amelyikben a virtuális hálózata található.
9. Válasszon egy nyilvános IP-címet. A **Nyilvános IP-cím** elemre kattintva nyissa meg a **Nyilvános IP-cím választása** panelt. Ezután az **+Új létrehozása** elemre kattintva nyissa meg a **Nyilvános IP-cím létrehozása** panelt. Adjon egy nevet a nyilvános IP-címnek. Ez a panel létrehoz egy nyilvános IP-cím-objektumot, amelyhez dinamikusan hozzá lesz rendelve egy nyilvános IP-cím. Kattintson az **OK** gombra a panel módosításainak mentéséhez.
10. **Előfizetés**: ellenőrizze, hogy a megfelelő előfizetés van-e kiválasztva.
11. **Erőforráscsoport**: ezt a beállítást a kiválasztott virtuális hálózat határozza meg.
12. Ne módosítsa a **Helyet** az előbbi beállítások megadása után.
13. Ellenőrizze a beállításokat. Ha azt szeretné, hogy az átjáró megjelenjen az irányítópulton, válassza a panel alján lévő **Rögzítés az irányítópulton** elemet.
14. A **Létrehozás** gombra kattintva hozzákezdhet az átjáró létrehozásához. A rendszer érvényesíti a beállításokat, és az átjáró üzembe helyezése megtörténik. Virtuális hálózati átjáró létrehozása akár 45 percet is igénybe vehet.

## <a name="next-steps"></a>További lépések
Miután létrehozta a virtuális hálózat átjáró, a virtuális hálózat hozzákapcsolhatja egy ExpressRoute-kapcsolatcsoportot. Lásd: [virtuális hálózat csatolása ExpressRoute-kapcsolatcsoportot](expressroute-howto-linkvnet-portal-resource-manager.md).