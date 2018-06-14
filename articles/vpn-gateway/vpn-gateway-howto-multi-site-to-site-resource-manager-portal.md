---
title: 'Több VPN-átjáró webhelyek kapcsolatot hozzáadni a virtuális hálózat: Azure-portálon: erőforrás-kezelő |} Microsoft Docs'
description: Többhelyes S2S-kapcsolatok hozzáadása, amely rendelkezik egy létező kapcsolat VPN-átjáró
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
ms.openlocfilehash: 5830b3a4bdcd12c01626d9ff3f814d2e7612eaaa
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29398607"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Pont-pont kapcsolat hozzáadása a virtuális hálózatot egy meglévő VPN-átjáró kapcsolattal

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klasszikus)](vpn-gateway-multi-site.md)
>
> 

Ez a cikk segítséget nyújt a helyek (közötti S2S) kapcsolatok hozzáadása egy VPN-átjáró, amely rendelkezik egy meglévő kapcsolat az Azure portál használatával. Ilyen típusú kapcsolat gyakran egy "több" Helykonfiguráció nevezik. S2S kapcsolatot adhat hozzá egy virtuális hálózatot, amely már rendelkezik egy S2S kapcsolatot, a pont – hely típusú kapcsolatot vagy a VNet – VNet-kapcsolatot. Bizonyos korlátozások is kapcsolatok hozzáadása során. Ellenőrizze a [megkezdése előtt](#before) szakasz ebben a cikkben ellenőrzése a konfiguráció megkezdése előtt. 

Ez a cikk RouteBased VPN-átjáró rendelkező erőforrás-kezelő Vnetek vonatkozik. Ezeket a lépéseket nem vonatkoznak a ExpressRoute/pont-pont vizsgálatát a kísérő kapcsolati beállításokat. Lásd: [vizsgálatát a kísérő ExpressRoute és az S2S-kapcsolatok](../expressroute/expressroute-howto-coexist-resource-manager.md) vizsgálatát a kísérő kapcsolatok kapcsolatos információkat.

### <a name="deployment-models-and-methods"></a>Üzemi modellek és módszerek
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Ebben a táblázatban, új cikket frissítjük, és további eszközök ehhez a konfigurációhoz elérhetővé válnak. Egy cikk nem érhető el, hogy csatolása közvetlenül ebből a táblázatból.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before"></a>Előkészületek
Ellenőrizze az alábbiakat:

* Nem hoz létre egy vizsgálatát a kísérő ExpressRoute és az S2S-kapcsolatot.
* A Resource Manager üzembe helyezési modellel meglévő internetkapcsolat használatával létrehozott virtuális hálózattal rendelkezik.
* A virtuális hálózati átjáró a Vnethez tartozó RouteBased. Ha PolicyBased VPN-átjáró, akkor törölje a virtuális hálózati átjáró, majd hozzon létre egy új VPN-átjáró mint RouteBased.
* Nincs a címtartomány átfedésben vannak a Vneteket, ez a virtuális hálózat csatlakozó bármelyikéhez.
* Kompatibilis VPN-eszközön, és képes konfigurálja, aki rendelkezik. Lásd: [About VPN Devices](vpn-gateway-about-vpn-devices.md) (Tudnivalók a VPN-eszközökről). Ha nem jártas a VPN-eszköz konfigurálásában, vagy nem ismeri a helyszíni hálózati konfigurációjában található IP-címtereket, együtt kell működnie valakivel, aki ezeket az adatokat megadhatja Önnek.
* A VPN-eszköz van külsőleg irányuló nyilvános IP-címet. Ez az IP-cím nem lehet NAT mögötti.

## <a name="part1"></a>1. rész - kapcsolat beállítása
1. Egy böngészőből keresse fel az [Azure portált](http://portal.azure.com), majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Kattintson a **összes erőforrás** , és keresse meg a **virtuális hálózati átjáró** erőforrások közül, és kattintson rá.
3. Az a **virtuális hálózati átjáró** kattintson **kapcsolatok**.
   
    ![Kapcsolatok oldal](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Kapcsolatok oldal")<br>
4. Az a **kapcsolatok** kattintson **+ Hozzáadás**.
   
    ![Hozzáadás kapcsolat gombra](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Hozzáadás kapcsolat gombra")<br>
5. Az a **kapcsolat hozzáadása a** lapján töltse ki a következő mezőket:
   
   * **Name:** a nevét, a helyhez hozzárendelni kívánt hoz létre a kapcsolatot.
   * **Kapcsolat típusa:** válasszon **pont-pont (IPsec)**.
     
     ![Hozzáadás kapcsolati oldal](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "hozzáadása kapcsolat lap")<br>

## <a name="part2"></a>2. rész – a helyi hálózati átjáró hozzáadása
1. Kattintson a **helyi hálózati átjáró** ***helyi hálózati átjáró kiválasztása***. Ekkor megnyílik a **válassza a helyi hálózati átjáró** lap.
   
    ![Válassza a helyi hálózati átjáró](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "helyi hálózati átjáró kiválasztása")<br>
2. Kattintson a **hozzon létre új** megnyitásához a **helyi hálózati átjáró létrehozása** lap.
   
    ![Hozzon létre helyi hálózati átjáró lap](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "helyi hálózati átjáró létrehozása")<br>
3. Az a **helyi hálózati átjáró létrehozása** lapján töltse ki a következő mezőket:
   
   * **Name:** kíván adni a helyi hálózati átjáró erőforrás nevét.
   * **IP-cím:** a helyet, amelyhez csatlakozni szeretne a VPN-eszköz nyilvános IP-címét.
   * **Címtér:** a címtér, amelyet irányítása az új helyi hálózati telephely.
4. Kattintson a **OK** a a **helyi hálózati átjáró létrehozása** lap menti a módosításokat.

## <a name="part3"></a>3. rész - hozzáadása a megosztott kulcsot, és a kapcsolat létrehozása
1. Az a **kapcsolat hozzáadása a** lapon, a megosztott kulcsot, amely a VPN-kapcsolat létrehozásához használni kívánt hozzáadni. A megosztott kulcs beszerzése a VPN-eszköz, vagy itt egyet, majd válassza ki a VPN-eszköz az azonos megosztott kulcsot használ. Fontos, hogy a kulcs pontosan megegyezik.
   
    ![Megosztott kulcs](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Megosztott kulcs")<br>
2. Kattintson a lap alján **OK** a VPN-kapcsolat létrehozásához.

## <a name="part4"></a>Rész 4 – a VPN-kapcsolat ellenőrzése


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>További lépések

Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd a virtuális gépekkel foglalkozó [képzési tervet](https://azure.microsoft.com/documentation/learning-paths/virtual-machines).
