---
title: 'Adjon hozzá egy virtuális hálózati átjárót egy virtuális hálózathoz az expressroute-hoz: Portal: Azure |} A Microsoft Docs'
description: Ez a cikk végigvezeti egy már létrehozott Resource Manager virtuális hálózathoz, az ExpressRoute virtuális hálózati átjáró hozzáadása.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2017
ms.author: cherylmc
ms.openlocfilehash: 6198c8723ccbb225a4cc60526e66f4651390b26c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51255693"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Az Azure portal használatával ExpressRoute virtuális hálózati átjáró konfigurálása
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasszikus – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Videó – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Ez a cikk végigvezeti a lépéseken virtuális hálózati átjáró hozzáadása egy már meglévő virtuális hálózat címtere. Ez a cikk végigvezeti a lépéseken hozzáadásához, méretezze át, és távolítsa el a virtuális hálózati (VNet) átjáró egy már meglévő virtuális hálózat címtere. Ez a konfiguráció lépései kifejezetten egy ExpressRoute-konfigurációban használt a Resource Manager üzemi modell használatával létrehozott virtuális hálózatok vonatkoznak. További információ a virtuális hálózati átjárók és az expressroute-hoz a gateway konfigurációs beállításairól: [kapcsolatos az ExpressRoute virtuális hálózati átjárók](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Mielőtt hozzálát

Ez a feladat lépései használják egy virtuális hálózathoz, a következő konfigurációs hivatkozás listában található értékek alapján. A példa lépései használja ezt a listát. A listában, hivatkozásként van listázva, használata és cserélje le az értékeket saját másolhatja.

**Konfigurációs referencialistája**

* Virtuális hálózat neve = "TestVNet"
* Virtuális hálózat címtere = 192.168.0.0/16
* Alhálózat neve "Előtér" = 
    * Alhálózat címtere = "192.168.1.0/24"
* Erőforráscsoport = "TestRG"
* Hely = "USA keleti RÉGIÓJA"
* Átjáróalhálózat neve: "GatewaySubnet" kell neve egy átjáró-alhálózatot *GatewaySubnet*.
    * Átjáró-alhálózat címtere = "192.168.200.0/26"
* Átjáró neve = "ERGW"
* Átjáró IP-név = "MyERGWVIP"
* Átjáró típusa "az ExpressRoute" = a típusát kötelező megadni egy ExpressRoute-konfigurációhoz.
* Átjáró nyilvános IP-cím neve = "MyERGWVIP"

Megtekintheti egy [videó](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) az alábbi lépéseket a konfigurálás megkezdése előtt.

## <a name="create-the-gateway-subnet"></a>Az átjáróalhálózat létrehozása

1. Keresse meg a [portálon](http://portal.azure.com) azt a Resource Manager-alapú virtuális hálózatot, amelyhez létre kíván hozni egy virtuális hálózati átjárót.
2. A virtuális hálózat paneljének **Beállítások** részén az **Alhálózatok** elemre kattintva bontsa ki az Alhálózatok panelt.
3. Az **Alhálózatok** panel **+Átjáróalhálózat** elemére kattintva nyissa meg az **Alhálózat hozzáadása** panelt. 
   
    ![Az átjáró alhálózatának hozzáadása](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "Az átjáró alhálózatának hozzáadása")


4. Az alhálózat **nevénél** automatikusan megjelenik a „GatewaySubnet” érték. Ez az érték szükséges ahhoz, hogy az Azure felismerje, hogy az alhálózat egy átjáró alhálózata. Módosítsa úgy a **címtartomány** automatikusan kitöltött értékeit, hogy megfeleljenek a konfigurációs követelményeinek. Javasolt létrehozni egy átjáró-alhálózat/27-es vagy nagyobb (/ 26-os vagy/25 stb.). Kattintson a **OK** mentse az értékeket, és az átjáró-alhálózat létrehozásához.

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
Miután létrehozta a virtuális hálózati átjáró, kapcsolat a virtuális hálózat csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz. Lásd: [virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoport](expressroute-howto-linkvnet-portal-resource-manager.md).