<properties
   pageTitle="Virtuális hálózat létrehozása helyek közötti VPN-kapcsolattal az Azure Resource Manager és az Azure Portal használatával | Microsoft Azure"
   description="Virtuális hálózat létrehozása a Resource Manager-alapú üzemi modellel, és a hálózat csatlakoztatása a helyszíni hálózathoz egy S2S VPN-átjárói kapcsolat használatával."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/03/2016"
   ms.author="cherylmc"/>


# Virtuális hálózat létrehozása helyek közötti kapcsolattal az Azure Portal használatával

> [AZURE.SELECTOR]
- [Resource Manager – Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Klasszikus – Klasszikus portál](vpn-gateway-site-to-site-create.md)


Ez a cikk részletesen bemutatja, hogyan hozható létre egy virtuális hálózat és egy helyek közötti VPN-kapcsolat a helyszíni hálózathoz az **Azure Resource Manager-alapú üzemi modell** és az Azure Portal használatával. A helyek közötti kapcsolatok létesítmények közötti és hibrid konfigurációk esetében is alkalmazhatók.

![Ábra](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/s2srmportal.png)



### Üzembe helyezési modellek és eszközök a helyek közötti kapcsolatokhoz

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

[AZURE.INCLUDE [vpn-gateway-table-site-to-site-table](../../includes/vpn-gateway-table-site-to-site-include.md)] 

Ha csatlakoztatni szeretné egymáshoz a virtuális hálózatokat, de nem szeretne létrehozni kapcsolatot egy hellyel: [Virtuális hálózatok közötti kapcsolat konfigurálása](vpn-gateway-vnet-vnet-rm-ps.md).

## Előkészületek

A konfigurálás megkezdése előtt győződjön meg arról, hogy rendelkezik a következőkkel:

- Egy kompatibilis VPN-eszköz és egy azt konfigurálni képes személy. Lásd: [About VPN Devices](vpn-gateway-about-vpn-devices.md) (Tudnivalók a VPN-eszközökről). Ha nem jártas a VPN-eszköz konfigurálásában, vagy nem ismeri a helyszíni hálózati konfigurációjában található IP-címtereket, együtt kell működnie valakivel, aki ezeket az adatokat megadhatja Önnek.

- Egy kifelé irányuló, nyilvános IP-cím a VPN-eszközhöz. Ez az IP-cím nem lehet NAT mögötti.
    
- Azure-előfizetés. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](http://azure.microsoft.com/pricing/free-trial/).

### <a name="values"></a>Konfigurációs mintaértékek ehhez a gyakorlathoz


Ha gyakorlatként használja ezeket a lépéseket, ezeket a konfigurációs mintaértékeket használhatja:

- **Virtuális hálózat neve:** TestVNet1
- **Címtér:** 10.11.0.0/16 és 10.12.0.0/16
- **Alhálózatok:**
    - Előtér: 10.11.0.0/24
    - Háttér: 10.12.0.0/24
    - Átjáróalhálózat: 10.12.255.0/27
- **Erőforráscsoport:** TestRG1
- **Hely:** az USA keleti régiója
- **DNS-kiszolgáló:** 8.8.8.8
- **Átjáró neve:** VNet1GW
- **Nyilvános IP-cím:** VNet1GWIP
- **VPN típusa:** útvonalalapú
- **Kapcsolat típusa:** helyek közötti kapcsolat (IPsec)
- **Átjáró típusa:** VPN
- **Helyi hálózati átjáró neve:** Site2
- **Kapcsolat neve:** VNet1toSite2


## 1. Virtuális hálózat létrehozása 

Ha már rendelkezik egy virtuális hálózattal, győződjön meg arról, hogy a beállításai kompatibilisek a VPN-átjáró kialakításával. Különösen ügyeljen az esetleges olyan alhálózatokra, amelyek átfedésbe kerülhetnek más hálózatokkal. Egymást átfedő alhálózatok esetén a kapcsolat nem fog megfelelően működni. Ha a virtuális hálózat a megfelelő beállításokkal lett konfigurálva, folytassa a [DNS-kiszolgáló megadása](#dns) szakaszban leírt lépésekkel.

### Virtuális hálózat létrehozása

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

## 2. További címterek és alhálózatok felvétele

Miután létrehozta a virtuális hálózatot, további címtereket és alhálózatokat adhat hozzá.

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)] 

## <a name="dns"></a>3. DNS-kiszolgáló megadása

### DNS-kiszolgáló megadása

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## 4. Átjáróalhálózat létrehozása

Mielőtt csatlakoztatja virtuális hálózatát egy átjáróhoz, létre kell hoznia az átjáróalhálózatot ahhoz a virtuális hálózathoz, amelyhez csatlakozni szeretne. Ha lehetséges, a legjobb megoldás egy átjáróalhálózat létrehozása /28 vagy /27 CIDR-blokk használatával annak érdekében, hogy a jövőbeli további konfigurációs követelmények számára elegendő IP-címet biztosíthasson.

Ha gyakorlatként hozza létre ezt a konfigurációt, használja ezeket az [értékeket](#values) az átjáróalhálózat létrehozásakor.

### Átjáróalhálózat létrehozása


[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## 5. Virtuális hálózati átjáró létrehozása

Ha gyakorlatként hozza létre ezt a konfigurációt, használja ezeket a [mintakonfiguráció-értékeket](#values).

### Virtuális hálózati átjáró létrehozása

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## 6. Helyi hálózati átjáró létrehozása

A „helyi hálózati átjáró” a helyszíni helyet jelenti. A helyi hálózati átjárónak meg kell adni egy nevet, amellyel az Azure hivatkozhat rá. 

Ha gyakorlatként hozza létre ezt a konfigurációt, használja ezeket a [mintakonfiguráció-értékeket](#values).

### Helyi hálózati átjáró létrehozása

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## 7. VPN-eszköz konfigurálása

[AZURE.INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## 8. Helyek közötti VPN-kapcsolat létrehozása

Hozzon létre egy helyek közötti VPN-kapcsolatot a virtuális hálózati átjáró és a VPN-eszköz között. Ne felejtse el ezeket az értékeket a saját értékeire cserélni. A megosztott kulcsnak meg kell egyeznie a VPN-eszköze konfigurálásakor használt értékkel. 

A szakasz lépéseinek megkezdése előtt győződjön meg arról, hogy a virtuális hálózati átjáró és a helyi hálózati átjárók sikeresen létrejöttek. Ha gyakorlatként hozza létre ezt a konfigurációt, használja ezeket az [értékeket](#values) a kapcsolat létrehozásakor.

### VPN-kapcsolat létrehozása


[AZURE.INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## 9. A VPN-kapcsolat ellenőrzése

A VPN-kapcsolatot ellenőrizheti a portálon, vagy a PowerShell használatával is.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## Következő lépések

- Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd a virtuális gépekkel foglalkozó [képzési tervet](https://azure.microsoft.com/documentation/learning-paths/virtual-machines).

- Információk a BGP-ről: [A BGP áttekintése](vpn-gateway-bgp-overview.md) és [A BGP konfigurálása](vpn-gateway-bgp-resource-manager-ps.md).



<!--HONumber=Oct16_HO1-->


