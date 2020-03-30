---
title: 'Azure ExpressRoute: Átjáró hozzáadása virtuális hálózathoz: portál'
description: Ez a cikk bemutatja egy virtuális hálózati átjáró hozzáadása egy már létrehozott Erőforrás-kezelő virtuális hálózat ExpressRoute az Azure Portalhasználatával.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 87b656f0ef999b3b15a89476f5cba4c4fcfc2b1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264829"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Virtuális hálózati átjáró konfigurálása az ExpressRoute számára az Azure Portal használatával
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasszikus - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Videó – Azure-portál](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Ez a cikk végigvezeti a lépéseket egy virtuális hálózati átjáró hozzáadása egy már meglévő virtuális hálózathoz. Ez a cikk végigvezeti a virtuális hálózati (VNet) átjáró hozzáadásának, átméretezésének és eltávolításának lépésein egy már meglévő virtuális hálózathoz. Ennek a konfigurációnak a lépései kifejezetten az ExpressRoute-konfigurációban használt Erőforrás-kezelő telepítési modell használatával létrehozott virtuális hálózatokra vonatkoznak. Az ExpressRoute virtuális hálózati átjáróiról és átjáró-konfigurációs beállításairól az [ExpressRoute virtuális hálózati átjáróinak betekintése](expressroute-about-virtual-network-gateways.md)című témakörben olvashat bővebben. 


## <a name="before-beginning"></a>Mielőtt hozzálát

A feladat lépései virtuális hálózatot használnak a következő konfigurációs hivatkozási lista értékei alapján. Ezt a listát a példalépésekben használjuk. A listát átmásolhatja referenciaként, és az értékeket a sajátjára cserélheti.

**Konfigurációs hivatkozási lista**

* Virtuális hálózat neve = "TestVNet"
* Virtuális hálózat címterülete = 192.168.0.0/16
* Alhálózat neve = "Előtér" 
    * Alhálózati címtér = "192.168.1.0/24"
* Erőforráscsoport = "TestRG"
* Helyszín = "USA keleti része"
* Átjáró-alhálózat neve: "GatewaySubnet" Mindig el kell neveznie egy átjáró *alhálózatát GatewaySubnet*.
    * Átjáró-alhálózati címtér = "192.168.200.0/26"
* Átjáró neve = "ERGW"
* Átjáró nyilvános IP-neve = "Myergwvip"
* Átjáró típusa = "ExpressRoute" Ez a típus szükséges egy ExpressRoute-konfigurációhoz.

A konfiguráció megkezdése előtt megtekintheti az ezekről a lépésekről [készült videót.](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)

## <a name="create-the-gateway-subnet"></a>Az átjáróalhálózat létrehozása

1. A [portálon](https://portal.azure.com)keresse meg azt az Erőforrás-kezelő virtuális hálózatot, amelyhez virtuális hálózati átjárót szeretne létrehozni.
2. A virtuális hálózat paneljének **Beállítások** részén az **Alhálózatok** elemre kattintva bontsa ki az Alhálózatok panelt.
3. Az **Alhálózatok** panel **+Átjáróalhálózat** elemére kattintva nyissa meg az **Alhálózat hozzáadása** panelt. 
   
    ![Az átjáró alhálózatának hozzáadása](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "Az átjáró alhálózatának hozzáadása")


4. Az alhálózat **nevénél** automatikusan megjelenik a „GatewaySubnet” érték. Ez az érték szükséges ahhoz, hogy az Azure felismerje, hogy az alhálózat egy átjáró alhálózata. Módosítsa úgy a **címtartomány** automatikusan kitöltött értékeit, hogy megfeleljenek a konfigurációs követelményeinek. Javasoljuk, hogy hozzon létre egy átjáró alhálózatot /27 vagy nagyobb (/26, /25 stb.) értékkel. Ezután kattintson az **OK** gombra az értékek mentéséhez és az átjáró alhálózatának létrehozásához.

    ![Az alhálózat hozzáadása](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "Az alhálózat hozzáadása")

## <a name="create-the-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása

1. A portálon, a bal **+** oldalon, kattintson, és írja be a "Virtual Network Gateway" a keresésben. A keresési eredmények között keresse meg a **Virtuális hálózati átjáró** elemet, és kattintson a bejegyzésre. A **Virtuális hálózati átjáró** panel alsó részén kattintson a **Létrehozás** gombra. Ez megnyitja a **Virtuális hálózati átjáró létrehozása** panelt.
2. A **Virtuális hálózati átjáró létrehozása** panelen töltse ki a virtuális hálózati átjáró értékeit.

    ![A Virtuális hálózati átjáró létrehozása panel mezői](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "A Virtuális hálózati átjáró létrehozása panel mezői")
3. **Név**: adjon nevet az átjárónak. Ez nem ugyanaz, mint egy átjáró alhálózatának elnevezése. Ez a létrehozni kívánt átjáróobjektum neve.
4. **Átjáró típusa**: Válassza **az ExpressRoute**lehetőséget.
5. **SKU**: válassza ki az átjáró SKU-ját a legördülő listából.
6. **Hely**: Állítsa be úgy a **Hely** mezőt, hogy a virtuális hálózat helyére mutasson. Ha a hely nem egyezik meg azzal a régióval, ahol a virtuális hálózat található, a virtuális hálózat nem jelenik meg a Virtuális hálózat választása legördülő menüben.
7. Válassza ki azt a virtuális hálózatot, amelyhez hozzá kívánja adni az átjárót. Kattintson a **Virtuális hálózat** elemre a **Virtuális hálózat választása** panel megnyitásához. Válassza ki a VNet elemet. Ha a VNet nem jelenik meg, ellenőrizze, hogy a **Hely** mező arra a régióra mutat-e, amelyikben a virtuális hálózata található.
9. Válasszon egy nyilvános IP-címet. A **Nyilvános IP-cím** elemre kattintva nyissa meg a **Nyilvános IP-cím választása** panelt. Ezután az **+Új létrehozása** elemre kattintva nyissa meg a **Nyilvános IP-cím létrehozása** panelt. Adjon egy nevet a nyilvános IP-címnek. Ez a panel létrehoz egy nyilvános IP-cím-objektumot, amelyhez dinamikusan hozzá lesz rendelve egy nyilvános IP-cím. Kattintson az **OK** gombra a panel módosításainak mentéséhez.
10. **Előfizetés**: ellenőrizze, hogy a megfelelő előfizetés van-e kiválasztva.
11. **Erőforráscsoport**: ezt a beállítást a kiválasztott virtuális hálózat határozza meg.
12. Ne módosítsa a **Helyet** az előbbi beállítások megadása után.
13. Ellenőrizze a beállításokat. Ha azt szeretné, hogy az átjáró megjelenjen az irányítópulton, válassza a panel alján lévő **Rögzítés az irányítópulton** elemet.
14. A **Létrehozás** gombra kattintva hozzákezdhet az átjáró létrehozásához. A rendszer érvényesíti a beállításokat, és az átjáró üzembe helyezése megtörténik. A virtuális hálózati átjáró létrehozása akár 45 percet is igénybe vehet.

## <a name="next-steps"></a>További lépések
A virtuális hálózat átjárólétrehozása után a virtuális hálózatot egy ExpressRoute-kapcsolathoz csatolhatja. Lásd: [Virtuális hálózat csatolása ExpressRoute-kapcsolattal](expressroute-howto-linkvnet-portal-resource-manager.md).
