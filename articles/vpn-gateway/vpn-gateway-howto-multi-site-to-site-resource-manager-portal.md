---
title: 'Adja hozzá a VPN-átjáró több helyek közötti kapcsolatok egy virtuális hálózathoz: Azure Portal: Resource Manager| Microsoft Docs'
description: Többhelyes S2S kapcsolat hozzáadása VPN-átjáró, amely rendelkezik egy meglévő kapcsolat
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: f3e8b165-f20a-42ab-afbb-bf60974bb4b1
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: 4b9f007e00d0912687b723bd4f7e747da893948d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57998832"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Helyek közötti kapcsolat hozzáadása virtuális hálózathoz meglévő VPN gateway-kapcsolattal

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klasszikus)](vpn-gateway-multi-site.md)
>
> 

Ez a cikk segítséget nyújt a Site-to-Site (S2S) kapcsolat hozzáadása VPN-átjáró, amely rendelkezik egy meglévő kapcsolat az Azure portal használatával. Ez a kapcsolattípus egy "többhelyes" konfigurációs van néven. S2S kapcsolat is hozzáadhat egy virtuális hálózathoz, amely már rendelkezik egy S2S kapcsolat, a pont – hely kapcsolat vagy a VNet – VNet kapcsolat. Vannak bizonyos korlátai kapcsolatok hozzáadása során. Ellenőrizze a [megkezdése előtt](#before) szakasz ebben a cikkben, mielőtt elkezdené a konfiguráció ellenőrzéséhez. 

Ez a cikk a Resource Manager-alapú virtuális hálózatokhoz, amelyeken az Útvonalalapú VPN-átjáró vonatkozik. Ezeket a lépéseket az ExpressRoute és Site-to-Site egyidejű kapcsolat konfigurációk nem vonatkoznak. Lásd: [ExpressRoute/S2S egyidejű kapcsolatok](../expressroute/expressroute-howto-coexist-resource-manager.md) egyidejű kapcsolatokról információt.

### <a name="deployment-models-and-methods"></a>Üzemi modellek és módszerek
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Ezt a táblázatot frissítjük, ahogy új cikkek és további eszközök válnak elérhetővé ehhez a konfigurációhoz. Új cikk érhető el, ha arra mutató közvetlen hivatkozás, ebből a táblázatból.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before"></a>Előkészületek
Ellenőrizze a következő elemeket:

* Nem hoz létre egy ExpressRoute/S2S egyidejű kapcsolat.
* Rendelkezik egy meglévő kapcsolat a Resource Manager üzemi modell használatával létrehozott virtuális hálózat.
* A virtuális hálózat számára a virtuális hálózati átjáróhoz RouteBased. Ha rendelkezik egy házirendalapú VPN-átjárót, akkor a virtuális hálózati átjáró törölje, majd hozzon létre egy új VPN-átjárót, Útvonalalapú.
* Címtartományok nincs átfedés, ehhez a virtuális hálózathoz csatlakozik, hogy a virtuális hálózatok bármelyikéhez.
* Hogy a kompatibilis VPN-eszköz, és azt konfigurálni képes személy. Lásd: [About VPN Devices](vpn-gateway-about-vpn-devices.md) (Tudnivalók a VPN-eszközökről). Ha nem jártas a VPN-eszköz konfigurálásában, vagy nem ismeri a helyszíni hálózati konfigurációjában található IP-címtereket, együtt kell működnie valakivel, aki ezeket az adatokat megadhatja Önnek.
* Egy kifelé irányuló, nyilvános IP-cím a VPN-eszköz tartozik. Ez az IP-cím nem lehet NAT mögötti.

## <a name="part1"></a>1. rész – a kapcsolat konfigurálása
1. Egy böngészőből keresse fel az [Azure portált](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Kattintson a **összes erőforrás** , és keresse meg a **virtuális hálózati átjáró** az erőforrások listájából, és kattintson rá.
3. Az a **virtuális hálózati átjáró** kattintson **kapcsolatok**.
   
    ![Kapcsolatok oldal](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Kapcsolatok oldal")<br>
4. Az a **kapcsolatok** kattintson **+ Hozzáadás**.
   
    ![Hozzáadás kapcsolat gomb](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Hozzáadás kapcsolat gomb")<br>
5. Az a **kapcsolat hozzáadása** lap, adja meg a következő mezőket:
   
   * **név:** A kapcsolat létrehozásakor a helyhez hozzárendelni kívánt nevét.
   * **Kapcsolat típusa:** Válassza ki **Site-to-site (IPsec)**.
     
     ![Hozzáadás kapcsolódási oldalán](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "hozzáadása kapcsolat lap")<br>

## <a name="part2"></a>2. rész – a helyi hálózati átjáró hozzáadása
1. Kattintson a **helyi hálózati átjáró** ***helyi hálózati átjáró kiválasztása***. Ekkor megnyílik a **helyi hálózati átjáró kiválasztása** lapot.
   
    ![Helyi hálózati átjáró kiválasztása](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "helyi hálózati átjáró kiválasztása")<br>
2. Kattintson a **új létrehozása** megnyitásához a **helyi hálózati átjáró létrehozása** lapot.
   
    ![Létrehozás helyi hálózati átjáró oldalának](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "helyi hálózati átjáró létrehozása")<br>
3. Az a **helyi hálózati átjáró létrehozása** lap, adja meg a következő mezőket:
   
   * **név:** Kíván adni a helyi hálózati átjáró erőforrás neve.
   * **IP-cím:** A helyet, amelyhez csatlakozni szeretne a VPN-eszköz nyilvános IP-címét.
   * **Címtér:** A címtér lesznek irányítva az új helyi hálózati telephely kívánt.
4. Kattintson a **OK** a a **helyi hálózati átjáró létrehozása** lapon a módosítások mentéséhez.

## <a name="part3"></a>3. rész – adja meg a megosztott kulcsot, és a kapcsolat létrehozása
1. Az a **kapcsolat hozzáadása** lap, adja hozzá a megosztott kulcsot, amelyet a kapcsolat létrehozásához használni szeretne. A megosztott kulcs lekérése a VPN-eszköz, vagy hozzon létre egyet, itt, majd válassza a VPN-eszközét használja ugyanazt a megosztott kulcsot. A lényeg az, hogy a kulcsokat a pontosan ugyanaz.
   
    ![Megosztott kulcs](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Megosztott kulcs")<br>
2. Kattintson a lap alján **OK** a kapcsolat létrehozásához.

## <a name="part4"></a>4. lépés – a VPN-kapcsolat ellenőrzése


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>További lépések

Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. Tekintse meg a [virtuális gépek – képzési terv](/learn/paths/deploy-a-website-with-azure-virtual-machines/) további információt.
