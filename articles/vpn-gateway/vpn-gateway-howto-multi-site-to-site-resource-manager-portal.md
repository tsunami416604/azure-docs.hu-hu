---
title: 'Több VPN-átjáró helyek közötti kapcsolat hozzáadása virtuális hálózathoz: Azure Portal'
description: Többhelyes S2S-kapcsolatok hozzáadása meglévő kapcsolattal rendelkező VPN-átjáróhoz
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: 7b438f2b966dc43d41b91a138b39193d230d5546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779688"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Helyek közötti kapcsolat hozzáadása meglévő VPN-átjárókapcsolattal rendelkező virtuális hálózathoz

> [!div class="op_single_selector"]
> * [Azure-portál](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klasszikus)](vpn-gateway-multi-site.md)
>
> 

Ez a cikk segít a helyek közötti (S2S) kapcsolatok hozzáadása egy VPN-átjáró, amely egy meglévő kapcsolat az Azure Portalhasználatával. Ezt a típusú kapcsolatot gyakran nevezik "többhelyes" konfigurációnak. S2S-kapcsolatot adhat hozzá egy olyan virtuális hálózathoz, amely már rendelkezik S2S-kapcsolattal, pont-hely vagy virtuális hálózat közötti kapcsolattal. A kapcsolatok hozzáadásakor vannak bizonyos korlátozások. A konfiguráció megkezdése előtt ellenőrizze a Cikk [Mielőtt elkezdje](#before) című szakaszát. 

Ez a cikk a RouteBased VPN-átjáróval rendelkező Erőforrás-kezelő virtuális hálózatokra vonatkozik. Ezek a lépések nem vonatkoznak az új ExpressRoute/Helyek közötti kapcsolati kapcsolatok ra. Ha azonban csak egy új VPN-kapcsolatot ad hozzá egy már meglévő egymás mellett létező konfigurációhoz, használhatja ezeket a lépéseket. Az együtt létező kapcsolatokról az [ExpressRoute/S2S együtt létező kapcsolatok](../expressroute/expressroute-howto-coexist-resource-manager.md) című témakörben talál információt.

### <a name="deployment-models-and-methods"></a>Üzemi modellek és módszerek
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Frissítjük ezt a táblázatot, amint új cikkek és további eszközök válnak elérhetővé ehhez a konfigurációhoz. Amikor egy cikk elérhető, közvetlenül erre a táblára hivatkozunk.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before-you-begin"></a><a name="before"></a>Mielőtt elkezdené
Ellenőrizze a következő elemeket:

* Nem konfigurál egy új, egymás mellett létező ExpressRoute- és VPN-átjárókonfigurációt.
* Olyan virtuális hálózattal rendelkezik, amelyet az Erőforrás-kezelő telepítési modelljével egy meglévő kapcsolattal hoztak létre.
* A virtuális hálózat virtuális hálózati átjárója RouteBased. Ha házirendalapú VPN-átjáróval rendelkezik, törölnie kell a virtuális hálózati átjárót, és útvonalalapúként létre kell hoznia egy új VPN-átjárót.
* Egyik címtartomány sem fedi át azokat a virtuális hálózatokat, amelyekhez ez a virtuális hálózat csatlakozik.
* Van kompatibilis VPN-eszköz, és valaki, aki képes beállítani. Lásd: [About VPN Devices](vpn-gateway-about-vpn-devices.md) (Tudnivalók a VPN-eszközökről). Ha nem jártas a VPN-eszköz konfigurálásában, vagy nem ismeri a helyszíni hálózati konfigurációjában található IP-címtereket, együtt kell működnie valakivel, aki ezeket az adatokat megadhatja Önnek.
* A VPN-eszközhöz külsőleg néző nyilvános IP-címmel rendelkezik. Ez az IP-cím nem lehet NAT mögötti.

## <a name="part-1---configure-a-connection"></a><a name="part1"></a>1. rész - Kapcsolat konfigurálása
1. Egy böngészőből keresse fel az [Azure Portalt](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Kattintson **az Összes erőforrás elemre,** és keresse meg a **virtuális hálózati átjárót** az erőforrások listájából, és kattintson rá.
3. A **Virtuális hálózati átjáró** lapon kattintson a **Kapcsolatok gombra.**
   
    ![Kapcsolatok lap](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Kapcsolatok lap")<br>
4. A **Kapcsolatok** lapon kattintson a **+Hozzáadás gombra.**
   
    ![Kapcsolat hozzáadása gomb](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Kapcsolat hozzáadása gomb")<br>
5. A **Kapcsolat hozzáadása** lapon töltse ki a következő mezőket:
   
   * **Név:** Az a név, amelyet annak a helynek szeretne adni, amelyhez kapcsolatot hoz létre.
   * **Kapcsolat típusa:** Válassza a helyek közötti **(IPsec)** lehetőséget.
     
     ![Kapcsolat lap hozzáadása](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Kapcsolat lap hozzáadása")<br>

## <a name="part-2---add-a-local-network-gateway"></a><a name="part2"></a>2. rész - Helyi hálózati átjáró hozzáadása
1. Kattintson **a Helyi hálózati átjáró** kiválasztása helyi hálózati átjáró ***elemre.*** Ekkor megnyílik a **Helyi hálózati átjáró kiválasztása** lap.
   
    ![Helyi hálózati átjáró kiválasztása](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Helyi hálózati átjáró kiválasztása")<br>
2. Kattintson az **Új létrehozása** gombra a **Helyi hálózati átjáró létrehozása** lap megnyitásához.
   
    ![Helyi hálózati átjáró lap létrehozása](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Helyi hálózati átjáró létrehozása")<br>
3. A **Create local network gateway (Helyi hálózati átjáró létrehozása)** lapon töltse ki a következő mezőket:
   
   * **Név:** A helyi hálózati átjáró erőforrásnak megadni kívánt név.
   * **IP-cím:** A VPN-eszköz nyilvános IP-címe azon a helyen, amelyhez csatlakozni szeretne.
   * **Címterület:** Az a címtér, amelyet az új helyi hálózati helyre szeretne irányítani.
4. A módosítások mentéséhez kattintson a **Helyi hálózati átjáró létrehozása** lap **OK** gombjára.

## <a name="part-3---add-the-shared-key-and-create-the-connection"></a><a name="part3"></a>3. rész - Adja hozzá a megosztott kulcsot, és hozza létre a kapcsolatot
1. A **Kapcsolat hozzáadása** lapon adja hozzá a kapcsolat létrehozásához használni kívánt megosztott kulcsot. A megosztott kulcsot beszerezheti a VPN-eszközről, vagy létrehozhat egyet itt, majd beállíthatja a VPN-eszközt, hogy ugyanazt a megosztott kulcsot használja. A lényeg az, hogy a kulcsok pontosan ugyanaz.
   
    ![Megosztott kulcs](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Megosztott kulcs")<br>
2. A kapcsolat létrehozásához kattintson a lap alján az **OK** gombra.

## <a name="part-4---verify-the-vpn-connection"></a><a name="part4"></a>4. rész - A VPN-kapcsolat ellenőrzése


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>További lépések

Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért tekintse meg a [virtuális gépek tanulási útvonalát.](/learn/paths/deploy-a-website-with-azure-virtual-machines/)
