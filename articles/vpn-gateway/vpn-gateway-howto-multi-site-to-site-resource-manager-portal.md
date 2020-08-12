---
title: 'Több VPN Gateway-helyek közötti kapcsolat hozzáadása egy VNet: Azure Portal'
description: Többhelyes S2S-kapcsolatok hozzáadása meglévő kapcsolattal rendelkező VPN-átjáróhoz
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: 0561be00f50dad0fe89ca47428802f45ee44f50d
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121425"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Helyek közötti kapcsolat hozzáadása egy meglévő VPN Gateway-kapcsolattal rendelkező VNet

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klasszikus)](vpn-gateway-multi-site.md)
>
> 

Ebből a cikkből megtudhatja, hogyan adhat helyek közötti (S2S) kapcsolatokat egy olyan VPN-átjáróhoz, amely meglévő kapcsolattal rendelkezik a Azure Portal használatával. Ezt a kapcsolattípust gyakran "többhelyes" konfigurációnak nevezzük. Hozzáadhat S2S-kapcsolatokat olyan VNet, amely már rendelkezik S2S kapcsolattal, pont – hely kapcsolattal vagy VNet – VNet kapcsolattal. A kapcsolatok hozzáadásakor bizonyos korlátozások vonatkoznak. A konfigurálás megkezdése előtt ellenőrizze a cikk [elkezdése](#before) című szakaszát. 

Ez a cikk olyan Resource Manager-virtuális hálózatok vonatkozik, amelyek Útvonalalapú VPN-átjáróval rendelkeznek. Ezek a lépések nem vonatkoznak az új ExpressRoute vagy helyek közötti kapcsolati konfigurációkra. Ha azonban csupán új VPN-kapcsolattal bővít egy már meglévő, egyidejű konfigurációt, ezeket a lépéseket használhatja. Az egyidejű kapcsolatokkal kapcsolatos információkért lásd: [ExpressRoute/S2S](../expressroute/expressroute-howto-coexist-resource-manager.md) egyidejű kapcsolatok.

### <a name="deployment-models-and-methods"></a>Üzemi modellek és módszerek
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Ezt a táblázatot úgy frissítjük, ahogy új cikkek és további eszközök válnak elérhetővé ehhez a konfigurációhoz. Ha egy cikk elérhetővé válik, közvetlenül a táblából csatoljuk.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before-you-begin"></a><a name="before"></a>Előkészületek
Ellenőrizze a következő elemeket:

* Nem konfigurál új, meglévő ExpressRoute és VPN Gateway konfigurációt.
* Van olyan virtuális hálózata, amely a Resource Manager-alapú üzemi modellel lett létrehozva egy meglévő kapcsolatban.
* A VNet virtuális hálózati átjárója Útvonalalapú. Ha házirendalapú VPN-átjáróval rendelkezik, törölnie kell a virtuális hálózati átjárót, és létre kell hoznia egy új VPN-átjárót Útvonalalapú.
* A címtartományok egyike sem fedi át a virtuális hálózatok, amelyhez ez a VNet csatlakozik.
* Kompatibilis a VPN-eszközzel, és valaki, aki be tudja állítani. Lásd: [About VPN Devices](vpn-gateway-about-vpn-devices.md) (Tudnivalók a VPN-eszközökről). Ha nem jártas a VPN-eszköz konfigurálásában, vagy nem ismeri a helyszíni hálózati konfigurációjában található IP-címtereket, együtt kell működnie valakivel, aki ezeket az adatokat megadhatja Önnek.
* A VPN-eszközhöz külsőleg megtekintett nyilvános IP-címet kell megnéznie.

## <a name="part-1---configure-a-connection"></a><a name="part1"></a>1. rész – kapcsolat konfigurálása
1. Egy böngészőből keresse fel az [Azure Portalt](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Kattintson a **minden erőforrás** lehetőségre, és keresse meg a **virtuális hálózati átjárót** az erőforrások listájából, és kattintson rá.
3. A **virtuális hálózati átjáró** lapon kattintson a **kapcsolatok**elemre.
   
    ![Kapcsolatok lap](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Kapcsolatok lap")<br>
4. A **kapcsolatok** lapon kattintson a **+ Hozzáadás**gombra.
   
    ![Kapcsolatok hozzáadása gomb](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Kapcsolatok hozzáadása gomb")<br>
5. A **kapcsolatok hozzáadása** lapon töltse ki a következő mezőket:
   
   * **Név:** Az a név, amelyet a helyhez szeretne adni, amelyhez a kapcsolódást hozza létre.
   * **Kapcsolattípus:** Válassza **a helyek közötti (IPSec)** lehetőséget.
     
     ![A kapcsolatok hozzáadása lap](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "A kapcsolatok hozzáadása lap")<br>

## <a name="part-2---add-a-local-network-gateway"></a><a name="part2"></a>2. rész – helyi hálózati átjáró hozzáadása
1. Kattintson a **helyi hálózati átjáró** elemre, és ***válassza ki a helyi hálózati átjárót***. Ekkor megnyílik a **helyi hálózati átjáró kiválasztása** lap.
   
    ![Helyi hálózati átjáró kiválasztása](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Helyi hálózati átjáró kiválasztása")<br>
2. Kattintson az **új létrehozása** elemre a **helyi hálózati átjáró létrehozása** lap megnyitásához.
   
    ![Helyi hálózati átjáró létrehozása lap](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Helyi hálózati átjáró létrehozása")<br>
3. A **helyi hálózati átjáró létrehozása** lapon töltse ki a következő mezőket:
   
   * **Név:** A helyi hálózati átjáró erőforrásához adni kívánt név.
   * **IP-cím:** Azon a helyen található VPN-eszköz nyilvános IP-címe, amelyhez csatlakozni szeretne.
   * **Címterület:** Az új helyi hálózati helyre átirányítani kívánt címtartomány.
4. A módosítások mentéséhez kattintson az **OK** gombra a **helyi hálózati átjáró létrehozása** lapon.

## <a name="part-3---add-the-shared-key-and-create-the-connection"></a><a name="part3"></a>3. rész – a megosztott kulcs hozzáadása és a kapcsolat létrehozása
1. A **Kapcsolódás hozzáadása** lapon adja meg a kapcsolatok létrehozásához használni kívánt megosztott kulcsot. Lekérheti a megosztott kulcsot a VPN-eszközről, vagy létrehozhat egyet, majd beállíthatja, hogy a VPN-eszköz ugyanazt a megosztott kulcsot használja. A lényeg az, hogy a kulcsok pontosan ugyanazok.
   
    ![Megosztott kulcs](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Megosztott kulcs")<br>
2. A lap alján kattintson az **OK** gombra a kapcsolódás létrehozásához.

## <a name="part-4---verify-the-vpn-connection"></a><a name="part4"></a>4. rész – a VPN-kapcsolat ellenőrzése


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>További lépések

Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információért tekintse meg a [Virtual Machines képzési](/learn/paths/deploy-a-website-with-azure-virtual-machines/) tervét.
