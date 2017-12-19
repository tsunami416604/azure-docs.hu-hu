---
title: "Több VPN-átjáró webhelyek kapcsolatot hozzáadni a virtuális hálózat: Azure-portálon: erőforrás-kezelő |} Microsoft Docs"
description: "Többhelyes S2S-kapcsolatok hozzáadása, amely rendelkezik egy létező kapcsolat VPN-átjáró"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f3e8b165-f20a-42ab-afbb-bf60974bb4b1
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/20/2017
ms.author: cherylmc
ms.openlocfilehash: 7ec57789ee76f4ec54e4f7b68ea75c19522f3d7c
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Pont-pont kapcsolat hozzáadása a virtuális hálózatot egy meglévő VPN-átjáró kapcsolattal

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klasszikus)](vpn-gateway-multi-site.md)
>
> 

Ez a cikk végigvezeti az Azure portál használatával, amely rendelkezik egy létező kapcsolat VPN-átjáró webhelyek (közötti S2S) kapcsolatok hozzáadása. Ilyen típusú kapcsolat gyakran egy "több" Helykonfiguráció nevezik. S2S kapcsolatot adhat hozzá egy virtuális hálózatot, amely már rendelkezik egy S2S kapcsolatot, a pont – hely típusú kapcsolatot vagy a VNet – VNet-kapcsolatot. Bizonyos korlátozások is kapcsolatok hozzáadása során. Ellenőrizze a [megkezdése előtt](#before) szakasz ebben a cikkben ellenőrzése a konfiguráció megkezdése előtt. 

Ez a cikk a Resource Manager telepítési modellel készült Vnetek RouteBased VPN-átjáró rendelkező vonatkozik. Ezeket a lépéseket nem vonatkoznak a ExpressRoute/pont-pont vizsgálatát a kísérő kapcsolati beállításokat. Lásd: [vizsgálatát a kísérő ExpressRoute és az S2S-kapcsolatok](../expressroute/expressroute-howto-coexist-resource-manager.md) vizsgálatát a kísérő kapcsolatok kapcsolatos információkat.

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
3. Az a **virtuális hálózati átjáró** panelen kattintson a **kapcsolatok**.
   
    ![Kapcsolatpanel](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Kapcsolatpanel")<br>
4. Az a **kapcsolatok** panelen kattintson a **+ Hozzáadás**.
   
    ![Hozzáadás kapcsolat gombra](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Hozzáadás kapcsolat gombra")<br>
5. Az a **kapcsolat hozzáadása a** panelen kitöltése során a következő mezőket:
   
   * **Name:** a nevét, a helyhez hozzárendelni kívánt hoz létre a kapcsolatot.
   * **Kapcsolat típusa:** válasszon **pont-pont (IPsec)**.
     
     ![Hozzáadás kapcsolat panelen](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Hozzáadás kapcsolat panelen")<br>

## <a name="part2"></a>2. rész – a helyi hálózati átjáró hozzáadása
1. Kattintson a **helyi hálózati átjáró** ***helyi hálózati átjáró kiválasztása***. Ekkor megnyílik a **válassza a helyi hálózati átjáró** panelen.
   
    ![Válassza a helyi hálózati átjáró](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "helyi hálózati átjáró kiválasztása")<br>
2. Kattintson a **hozzon létre új** megnyitásához a **helyi hálózati átjáró létrehozása** panelen.
   
    ![Létrehozás helyi hálózati átjáró panel](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "helyi hálózati átjáró létrehozása")<br>
3. Az a **helyi hálózati átjáró létrehozása** panelen kitöltése során a következő mezőket:
   
   * **Name:** kíván adni a helyi hálózati átjáró erőforrás nevét.
   * **IP-cím:** a helyet, amelyhez csatlakozni szeretne a VPN-eszköz nyilvános IP-címét.
   * **Címtér:** a címtér, amelyet irányítása az új helyi hálózati telephely.
4. Kattintson a **OK** a a **helyi hálózati átjáró létrehozása** panelt, és menti a módosításokat.

## <a name="part3"></a>3. rész - hozzáadása a megosztott kulcsot, és a kapcsolat létrehozása
1. A a **kapcsolat hozzáadása a** panelen, vegye fel a VPN-kapcsolat létrehozásához használni kívánt megosztott kulcsot. A megosztott kulcs beszerzése a VPN-eszköz, vagy itt egyet, majd válassza ki a VPN-eszköz az azonos megosztott kulcsot használ. Fontos, hogy a kulcs pontosan megegyezik.
   
    ![Megosztott kulcs](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Megosztott kulcs")<br>
2. A panel alján kattintson **OK** a VPN-kapcsolat létrehozásához.

## <a name="part4"></a>Rész 4 – a VPN-kapcsolat ellenőrzése


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Következő lépések

Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd a virtuális gépekkel foglalkozó [képzési tervet](https://azure.microsoft.com/documentation/learning-paths/virtual-machines).
