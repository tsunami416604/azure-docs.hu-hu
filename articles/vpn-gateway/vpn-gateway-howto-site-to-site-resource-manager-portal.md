---
title: "A helyszíni hálózat csatlakoztatása egy Azure-beli virtuális hálózathoz: Helyek közötti VPN: Portal | Microsoft Docs"
description: "A helyszíni hálózatot az Azure-beli virtuális hálózattal a nyilvános interneten keresztül összekötő IPsec-kapcsolat létrehozásának lépései. Ezen lépéseket követve létrehozhat egy helyek közötti VPN-átjáró kapcsolatot a portál segítségével."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 827a4db7-7fa5-4eaf-b7e1-e1518c51c815
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 37311ea17004d8c5cfe9bfc9360c3972e39fb7f5
ms.lasthandoff: 03/17/2017


---
# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-portal"></a>Virtuális hálózat létrehozása helyek közötti kapcsolattal az Azure Portal használatával
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Klasszikus – Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Klasszikus – Klasszikus portál](vpn-gateway-site-to-site-create.md)
>
>

Ez a cikk részletesen bemutatja, hogyan hozható létre egy virtuális hálózat és egy helyek közötti VPN-átjárókapcsolat a helyszíni hálózathoz az Azure Resource Manager-alapú üzemi modell és az Azure Portal használatával. A helyek közötti kapcsolatok létesítmények közötti és hibrid konfigurációk esetében is alkalmazhatók.

![Helyek közötti VPN Gateway létesítmények közötti kapcsolathoz – diagram](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Üzembe helyezési modellek és módszerek a helyek közötti kapcsolatokhoz
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A helyek közötti konfigurációkhoz elérhető üzemi modellek és módszerek az alábbi táblázatban láthatók. Amint a konfigurációs lépeseket tartalmazó cikk elérhetővé válik, egy arra mutató közvetlen hivatkozás szerepel majd ebben a táblázatban.

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>További konfigurációk
Ha csatlakoztatni szeretné egymáshoz a virtuális hálózatokat, de nem szeretne létrehozni kapcsolatot egy hellyel: [Virtuális hálózatok közötti kapcsolat konfigurálása](vpn-gateway-vnet-vnet-rm-ps.md). Ha szeretne helyek közötti kapcsolatot hozzáadni olyan virtuális hálózathoz, amely már rendelkezik egy kapcsolattal, olvassa el a következőt: [Helyek közötti kapcsolat hozzáadása meglévő VPN-átjárókapcsolattal rendelkező virtuális hálózathoz](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Előkészületek
A konfigurálás megkezdése előtt győződjön meg arról, hogy rendelkezik a következőkkel:

* Egy kompatibilis VPN-eszköz és egy azt konfigurálni képes személy. Lásd: [About VPN Devices](vpn-gateway-about-vpn-devices.md) (Tudnivalók a VPN-eszközökről). Ha nem jártas a VPN-eszköz konfigurálásában, vagy nem ismeri a helyszíni hálózati konfigurációjában található IP-címtereket, együtt kell működnie valakivel, aki ezeket az adatokat megadhatja Önnek.
* Egy kifelé irányuló, nyilvános IP-cím a VPN-eszközhöz. Ez az IP-cím nem lehet NAT mögötti.
* Azure-előfizetés. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](http://azure.microsoft.com/pricing/free-trial).

### <a name="values"></a>Konfigurációs mintaértékek ehhez a gyakorlathoz
Ha gyakorlatként használja ezeket a lépéseket, ezeket a konfigurációs mintaértékeket használhatja:

* **Virtuális hálózat neve:** TestVNet1
* **Címtér:** 10.11.0.0/16 és 10.12.0.0/16
* **Alhálózatok:**
  * Előtér: 10.11.0.0/24
  * Háttér: 10.12.0.0/24
  * Átjáróalhálózat: 10.12.255.0/27
* **Erőforráscsoport:** TestRG1
* **Hely:** az USA keleti régiója
* **DNS-kiszolgáló:** 8.8.8.8
* **Átjáró neve:** VNet1GW
* **Nyilvános IP-cím:** VNet1GWIP
* **VPN típusa:** útvonalalapú
* **Kapcsolat típusa:** helyek közötti kapcsolat (IPsec)
* **Átjáró típusa:** VPN
* **Helyi hálózati átjáró neve:** Site2
* **Kapcsolat neve:** VNet1toSite2

## <a name="CreatVNet"></a>1. Virtuális hálózat létrehozása
Ha már rendelkezik egy virtuális hálózattal, győződjön meg arról, hogy a beállításai kompatibilisek a VPN-átjáró kialakításával. Különösen ügyeljen az esetleges olyan alhálózatokra, amelyek átfedésbe kerülhetnek más hálózatokkal. Egymást átfedő alhálózatok esetén a kapcsolat nem fog megfelelően működni. Ha a virtuális hálózat a megfelelő beállításokkal lett konfigurálva, folytassa a [DNS-kiszolgáló megadása](#dns) szakaszban leírt lépésekkel.

### <a name="to-create-a-virtual-network"></a>Virtuális hálózat létrehozása
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="subnets"></a>2. További címterek és alhálózatok felvétele
Miután létrehozta a virtuális hálózatot, további címtereket és alhálózatokat adhat hozzá.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="dns"></a>3. DNS-kiszolgáló megadása
### <a name="to-specify-a-dns-server"></a>DNS-kiszolgáló megadása
[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>4. Átjáróalhálózat létrehozása
Mielőtt csatlakoztatja virtuális hálózatát egy átjáróhoz, létre kell hoznia az átjáróalhálózatot ahhoz a virtuális hálózathoz, amelyhez csatlakozni szeretne. Ha lehetséges, a legjobb megoldás egy átjáróalhálózat létrehozása /28 vagy /27 CIDR-blokk használatával annak érdekében, hogy a jövőbeli további konfigurációs követelmények számára elegendő IP-címet biztosíthasson.

Ha gyakorlatként hozza létre ezt a konfigurációt, használja ezeket az [értékeket](#values) az átjáróalhálózat létrehozásakor.

### <a name="to-create-a-gateway-subnet"></a>Átjáróalhálózat létrehozása
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="VNetGateway"></a>5. Virtuális hálózati átjáró létrehozása
Ha gyakorlatként hozza létre ezt a konfigurációt, használja ezeket a [mintakonfiguráció-értékeket](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>6. Helyi hálózati átjáró létrehozása
A „helyi hálózati átjáró” a helyszíni helyet jelenti. A helyi hálózati átjárónak meg kell adni egy nevet, amellyel az Azure hivatkozhat rá. 

Ha gyakorlatként hozza létre ezt a konfigurációt, használja ezeket a [mintakonfiguráció-értékeket](#values).

### <a name="to-create-a-local-network-gateway"></a>Helyi hálózati átjáró létrehozása
[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="VPNDevice"></a>7. VPN-eszköz konfigurálása
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>8. Helyek közötti VPN-kapcsolat létrehozása
Hozzon létre egy helyek közötti VPN-kapcsolatot a virtuális hálózati átjáró és a VPN-eszköz között. Ne felejtse el ezeket az értékeket a saját értékeire cserélni. A megosztott kulcsnak meg kell egyeznie a VPN-eszköze konfigurálásakor használt értékkel. 

A szakasz lépéseinek megkezdése előtt győződjön meg arról, hogy a virtuális hálózati átjáró és a helyi hálózati átjárók sikeresen létrejöttek. Ha gyakorlatként hozza létre ezt a konfigurációt, használja ezeket az [értékeket](#values) a kapcsolat létrehozásakor.

### <a name="to-create-the-vpn-connection"></a>VPN-kapcsolat létrehozása
[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## <a name="VerifyConnection"></a>9. A VPN-kapcsolat ellenőrzése
A VPN-kapcsolatot ellenőrizheti a portálon, vagy a PowerShell használatával is.

[!INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="next-steps"></a>Következő lépések
*  Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
*  Információk a BGP-ről: [A BGP áttekintése](vpn-gateway-bgp-overview.md) és [A BGP konfigurálása](vpn-gateway-bgp-resource-manager-ps.md).


