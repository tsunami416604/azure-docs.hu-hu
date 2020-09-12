---
title: 'Azure ExpressRoute: átjáró hozzáadása VNet: portál'
description: Ez a cikk bemutatja, hogyan adhat hozzá egy virtuális hálózati átjárót egy már létrehozott Resource Manager-VNet a ExpressRoute-hez a Azure Portal használatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2018
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 06f7e5d28017ee618adfeeec52c6f1226e1ae82c
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89396354"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Virtuális hálózati átjáró konfigurálása a ExpressRoute-hez a Azure Portal használatával
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasszikus – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Videó – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Ez a cikk végigvezeti a virtuális hálózati átjárók meglévő VNet való hozzáadásának lépésein. Ez a cikk végigvezeti a virtuális hálózati (VNet-) átjáró egy meglévő VNet való hozzáadásának, átméretezésének és eltávolításának lépésein. A konfiguráció lépései kifejezetten olyan virtuális hálózatok, amelyek a Resource Manager-alapú üzemi modellel lettek létrehozva, és egy ExpressRoute-konfigurációban lesznek használva. További információ a ExpressRoute virtuális hálózati átjárókkal és az átjáró konfigurációs beállításaival kapcsolatban: [a ExpressRoute virtuális hálózati átjáróinak ismertetése](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Mielőtt hozzálát

A feladat lépései a következő konfigurációs hivatkozási listán szereplő értékek alapján VNet használnak. Ezt a listát a példában szereplő lépésekben használjuk. A listát a hivatkozásként való használatra másolhatja, és az értékeket saját értékre cserélheti.

**Konfigurációs hivatkozások listája**

* Virtual Network neve = "TestVNet"
* Virtual Network címtartomány = 192.168.0.0/16
* Alhálózat neve = "FrontEnd" 
    * Alhálózat címtartomány = "192.168.1.0/24"
* Erőforráscsoport = "TestRG"
* Location = "USA keleti régiója"
* Átjáró alhálózatának neve: "GatewaySubnet" az átjáró alhálózatának *GatewaySubnet*kell lennie.
    * Átjáró-alhálózati címtartomány = "192.168.200.0/26"
* Átjáró neve = "ERGW"
* Átjáró nyilvános IP-neve = "MyERGWVIP"
* Átjáró típusa = "ExpressRoute" Ez a típus szükséges egy ExpressRoute-konfigurációhoz.

A konfigurálás megkezdése előtt tekintse meg az alábbi lépések [videóit](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) .

## <a name="create-the-gateway-subnet"></a>Az átjáróalhálózat létrehozása

1. A [portálon](https://portal.azure.com)navigáljon ahhoz a Resource Manager-beli virtuális hálózathoz, amelyhez virtuális hálózati átjárót szeretne létrehozni.
2. A virtuális hálózat paneljének **Beállítások** részén az **Alhálózatok** elemre kattintva bontsa ki az Alhálózatok panelt.
3. Az **Alhálózatok** panel **+Átjáróalhálózat** elemére kattintva nyissa meg az **Alhálózat hozzáadása** panelt. 
   
    ![Az átjáró alhálózatának hozzáadása](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "Az átjáró alhálózatának hozzáadása")


4. Az alhálózat **nevénél** automatikusan megjelenik a „GatewaySubnet” érték. Ez az érték szükséges ahhoz, hogy az Azure felismerje, hogy az alhálózat egy átjáró alhálózata. Módosítsa úgy a **címtartomány** automatikusan kitöltött értékeit, hogy megfeleljenek a konfigurációs követelményeinek. Javasoljuk, hogy hozzon létre egy átjáró-alhálózatot egy/27 vagy nagyobb (/26,/25 stb.). Ezután kattintson az **OK** gombra az értékek mentéséhez és az átjáró-alhálózat létrehozásához.

    ![Az alhálózat hozzáadása](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "Az alhálózat hozzáadása")

## <a name="create-the-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása

1. A portálon a bal oldalon kattintson **+** és írja be a "Virtual Network átjáró" kifejezést a keresőmezőbe. A keresési eredmények között keresse meg a **Virtuális hálózati átjáró** elemet, és kattintson a bejegyzésre. A **Virtuális hálózati átjáró** panel alsó részén kattintson a **Létrehozás** gombra. Ez megnyitja a **Virtuális hálózati átjáró létrehozása** panelt.
2. A **Virtuális hálózati átjáró létrehozása** panelen töltse ki a virtuális hálózati átjáró értékeit.

    ![A Virtuális hálózati átjáró létrehozása panel mezői](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "A Virtuális hálózati átjáró létrehozása panel mezői")
3. **Név**: adjon nevet az átjárónak. Ez nem ugyanaz, mint egy átjáró alhálózatának elnevezése. Ez a létrehozni kívánt átjáróobjektum neve.
4. **Átjáró típusa**: válassza a **ExpressRoute**lehetőséget.
5. **SKU**: válassza ki az átjáró SKU-ját a legördülő listából.
6. **Hely**: Állítsa be úgy a **Hely** mezőt, hogy a virtuális hálózat helyére mutasson. Ha a hely nem egyezik meg azzal a régióval, ahol a virtuális hálózat található, a virtuális hálózat nem jelenik meg a Virtuális hálózat választása legördülő menüben.
7. Válassza ki azt a virtuális hálózatot, amelyhez hozzá kívánja adni az átjárót. Kattintson a **Virtuális hálózat** elemre a **Virtuális hálózat választása** panel megnyitásához. Válassza ki a VNet elemet. Ha a VNet nem jelenik meg, ellenőrizze, hogy a **Hely** mező arra a régióra mutat-e, amelyikben a virtuális hálózata található.
9. Válasszon egy nyilvános IP-címet. A **Nyilvános IP-cím** elemre kattintva nyissa meg a **Nyilvános IP-cím választása** panelt. Ezután az **+Új létrehozása** elemre kattintva nyissa meg a **Nyilvános IP-cím létrehozása** panelt. Adjon egy nevet a nyilvános IP-címnek. Ez a panel létrehoz egy nyilvános IP-cím-objektumot, amelyhez dinamikusan hozzá lesz rendelve egy nyilvános IP-cím. Kattintson az **OK** gombra a panel módosításainak mentéséhez.
10. **Előfizetés**: ellenőrizze, hogy a megfelelő előfizetés van-e kiválasztva.
11. **Erőforráscsoport**: ezt a beállítást a kiválasztott virtuális hálózat határozza meg.
12. Ne módosítsa a **Helyet** az előbbi beállítások megadása után.
13. Ellenőrizze a beállításokat. Ha azt szeretné, hogy az átjáró megjelenjen az irányítópulton, válassza a panel alján lévő **Rögzítés az irányítópulton** elemet.
14. A **Létrehozás** gombra kattintva hozzákezdhet az átjáró létrehozásához. A rendszer érvényesíti a beállításokat, és az átjáró üzembe helyezése megtörténik. A virtuális hálózati átjáró létrehozása akár 45 percet is igénybe vehet.

## <a name="next-steps"></a>Következő lépések
Miután létrehozta a VNet-átjárót, összekapcsolhatja a VNet egy ExpressRoute-áramkörrel. Lásd: [Virtual Network csatolása ExpressRoute-áramkörhöz](expressroute-howto-linkvnet-portal-resource-manager.md).
