<properties
   pageTitle="Virtuális hálózat létrehozása helyek közötti VPN-kapcsolattal az Azure Resource Manager és a PowerShell használatával | Microsoft Azure"
   description="Ez a cikk lépésről lépésre bemutatja, hogyan hozható létre virtuális hálózat a Resource Manager modellel, és ez hogyan csatlakoztatható helyszíni hálózathoz egy S2S VPN-átjárói kapcsolat használatával."
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
   ms.date="05/13/2016"
   ms.author="cherylmc"/>

# Virtuális hálózat létrehozása helyek közötti VPN-kapcsolattal az Azure Resource Manager és a PowerShell használatával

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Klasszikus Azure portál](vpn-gateway-site-to-site-create.md)
- [PowerShell – Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

Ez a cikk lépésről lépésre bemutatja, hogyan hozható létre egy virtuális hálózat és egy helyek közötti VPN-kapcsolat a helyszíni hálózathoz az Azure Resource Manager-alapú üzemi modell használatával. A helyek közötti kapcsolatok létesítmények közötti és hibrid konfigurációk esetében is alkalmazhatók. 


**Tudnivalók az Azure üzembehelyezési modellekről**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Kapcsolati diagram 

![Helyek közötti diagram](./media/vpn-gateway-create-site-to-site-rm-powershell/site2site.png "site-to-site")

**Üzembe helyezési modellek és eszközök a helyek közötti kapcsolatokhoz**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

Ha csatlakoztatni szeretné egymáshoz a virtuális hálózatokat, de nem szeretne létrehozni kapcsolatot egy hellyel: [Virtuális hálózatok közötti kapcsolat konfigurálása](vpn-gateway-vnet-vnet-rm-ps.md). Ha más típusú kapcsolati konfigurációt keres, tekintse meg a [VPN Gateway kapcsolati topológiák](vpn-gateway-topology.md) című cikket.


## Előkészületek

A konfigurálás megkezdése előtt győződjön meg arról, hogy rendelkezik a következő elemekkel.

- Egy kompatibilis VPN-eszköz és egy azt konfigurálni képes személy. Lásd: [About VPN Devices](vpn-gateway-about-vpn-devices.md) (Tudnivalók a VPN-eszközökről). Ha nem jártas a VPN-eszköz konfigurálásában, vagy nem ismeri a helyszíni hálózati konfigurációjában található IP-címtereket, együtt kell működnie egy olyan személlyel, aki ezeket az adatokat megadhatja Önnek.

- Egy kifelé irányuló, nyilvános IP-cím a VPN-eszközhöz. Ez az IP-cím nem lehet NAT mögötti.
    
- Azure-előfizetés. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
    
- Az Azure Resource Manager PowerShell-parancsmagjainak legújabb verzióit kell telepítenie. A PowerShell-parancsmagok telepítésével kapcsolatban további információ: [Az Azure PowerShell telepítése és konfigurálása](../powershell-install-configure.md).


## 1. Csatlakozás az előfizetéshez 

A Resource Manager parancsmagjainak használatához váltson át PowerShell módba. További információ: [A Windows PowerShell használata a Resource Managerrel](../powershell-azure-resource-manager.md).

Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. A következő minta segíthet a kapcsolódásban:

    Login-AzureRmAccount

Keresse meg a fiókot az előfizetésekben.

    Get-AzureRmSubscription 

Válassza ki a használni kívánt előfizetést.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## 2. Virtuális hálózat és átjáró-alhálózat létrehozása

Az alábbi példák egy /28-as átjáró-alhálózatot szemléltetnek. Ugyan létrehozható átjáró-alhálózat kisebb, akár /29-es alhálózat használatával is, ez nem ajánlott. A további szolgáltatások követelményeinek való megfelelés érdekében javasoljuk /27-es vagy nagyobb (például /26-os vagy /25-ös) átjáró-alhálózat létrehozását. 

Ha már rendelkezik /29-es vagy nagyobb átjáró-alhálózattal ellátott virtuális hálózattal, továbbléphet a [Helyi hálózati átjáró hozzáadása](#localnet) részre.


[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]  

### Virtuális hálózat és átjáró-alhálózat létrehozása

Virtuális hálózat és átjáró-alhálózat létrehozásához használja az alábbi mintát. Az itt szerepelő értékeket cserélje ki a sajátjaival. 

Először is hozzon létre egy erőforráscsoportot:
    
    New-AzureRmResourceGroup -Name testrg -Location 'West US'

A következő lépés a virtuális hálózat létrehozása. Ellenőrizze, hogy a megadott címterek nincsenek átfedésben a helyszíni hálózaton található egyéb címterekkel.

Az alábbi minta létrehoz egy *testvnet* nevű virtuális hálózatot és két alhálózatot a következő neveken: *GatewaySubnet* és *Subnet1*. Fontos, hogy létrehozzon egy *GatewaySubnet* nevű alhálózatot. Ha ezt az alhálózatot máshogy nevezi el, a kapcsolat konfigurálása sikertelen lesz. 

    $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
    $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
    New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Átjáró-alhálózat hozzáadása már létrehozott virtuális hálózathoz

Erre a lépésre csak akkor van szükség, ha egy korábban már létrehozott virtuális hálózathoz kell hozzáadni átjáró-alhálózatot.

Az alábbi minta használatával létrehozhatja saját átjáró-alhálózatát. Győződjön meg arról, hogy az átjáró-alhálózat neve „GatewaySubnet” legyen. Ha ezt az alhálózatot máshogy nevezi el, az létrejön ugyan, de az Azure nem kezeli majd átjáró-alhálózatként.

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Most a konfiguráció megadása van soron. 

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3. <a name="localnet"></a>Helyi hálózati átjáró hozzáadása

Virtuális hálózatokban a helyi hálózati átjáró általában a helyszínt jelenti. Olyan nevet fog adni a helynek, amellyel az Azure hivatkozhat rá, valamint megadja a helyi hálózati átjáró címtér-előtagját. 

Az Azure a megadott IP-címelőtagot a helyszínre küldendő adatforgalom azonosítására használja. Ez azt jelenti, hogy meg kell adnia minden egyes olyan címelőtagot, amelyet a helyi hálózati átjáróhoz kíván társítani. A helyszíni hálózat módosításakor ezek az előtagok egyszerűen frissíthetők. 

PowerShell-példák használatakor vegye figyelembe a következőket:
    
- A helyszíni VPN-eszköz IP-címe: *GatewayIPAddress*. A VPN-eszköz nem lehet NAT mögött. 
- A helyszíni címtér az *AddressPrefix*.

Helyi hálózati átjáró hozzáadása egyetlen címelőtaggal:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Helyi hálózati átjáró hozzáadása több címelőtaggal:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### Helyi hálózati átjáró IP-címelőtagjainak módosítása

A helyi hálózati átjáró előtagjai bizonyos esetekben változnak. Az IP-címelőtagok módosításának lépései attól függően változnak, hogy előzőleg létre lett-e hozva VPN Gateway-kapcsolat. Lásd a jelen cikk [Helyi hálózati átjáró IP-címelőtagjainak módosítása](#modify) című szakaszát.


## 4. Nyilvános IP-cím kérése a VPN-átjáró számára

Ezután egy nyilvános IP-cím kiosztását kéri majd az Azure virtuális hálózat VPN-átjárójához. Ez a cím nem azonos a VPN-eszközhöz rendelt IP-címmel; a cím magához az Azure VPN-átjáróhoz van rendelve. A használni kívánt IP-címet nem adhatja meg; ez dinamikusan kerül kiosztásra az átjárójához. Ezt az IP-címet a helyszíni VPN-eszköz átjáróhoz történő csatlakoztatásának konfigurálásához fogja használni.

Ehhez használja az alábbi PowerShell-mintát. A Kiosztási módszernek ehhez a címhez Dinamikusnak kell lennie. 

    $gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

>[AZURE.NOTE] Az Erőforrás-kezelő üzembe helyezési modelljének Azure VPN-átjárója jelenleg kizárólag a nyilvános IP-címeket támogatja a dinamikus kiosztási módszer használatával. Ez azonban nem jelenti azt, hogy az IP-cím változni fog. Az Azure VPN-átjáró IP-címe kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. Az átjáró nyilvános IP-címe nem módosul átméretezés, alaphelyzetbe állítás, illetve az Azure VPN-átjáró belső karbantartása/frissítése során.

## 5. Az átjáró IP-címkonfigurációjának létrehozása

Az átjáró konfigurációja meghatározza az alhálózatot és a használandó nyilvános IP-címet. Az alábbi minta használatával hozza létre az átjáró konfigurációját. 

    $vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6. Virtuális hálózati átjáró létrehozása

Ebben a lépésben a virtuális hálózati átjárót fogja létrehozni. Vegye figyelembe, hogy az átjáró létrehozása hosszú időt vehet igénybe. Gyakran 20 percig vagy tovább is tarthat. 

Használja a következő értékeket:

- A webhelyek közötti konfiguráció *-GatewayType* paraméterének értéke: *Vpn*. Az átjáró típusa mindig a kiépítendő konfigurációra jellemző. Más átjáró-konfigurációk például -GatewayType ExpressRoute típus alkalmazását igényelhetik. 

- A *-VpnType* típus a következők valamelyike lehet: *RouteBased* (egyes dokumentumokban Dinamikus átjáró néven szerepel) vagy *PolicyBased* (egyes dokumentumokban Statikus átjáró néven szerepel). További információk a VPN-átjárótípusokról: [Információk a VPN-átjárókról](vpn-gateway-about-vpngateways.md#vpntype).
- A *-GatewaySku* paraméter lehetséges értékei: *Alapszintű*, *Standard* vagy *HighPerformance*.   

        New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

## 7. VPN-eszköz konfigurálása

Most szüksége lesz a virtuális hálózati átjáró nyilvános IP-címére, hogy konfigurálni tudja a helyszíni VPN-eszközt. A konkrét konfigurációs információkért forduljon az eszköz gyártójához. További információkért lásd: [VPN-eszközök](vpn-gateway-about-vpn-devices.md).

A virtuális hálózati átjáró IP-címét az alábbi minta alapján keresheti meg:

    Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## 8. VPN-kapcsolat létrehozása

Ezután létre fog hozni egy helyek közötti VPN-kapcsolatot a virtuális hálózati átjárója és a VPN-eszköze között. Ne felejtse el ezeket az értékeket a saját értékeire cserélni. A megosztott kulcsnak meg kell egyeznie a VPN-eszköze konfigurálásakor használt értékkel. Vegye figyelembe, hogy Helyek közötti kapcsolat esetében a `-ConnectionType` értéke: *IPsec*. 

    $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

A kapcsolat rövid időn belül létrejön. 

## 9. VPN-kapcsolat ellenőrzése

A VPN-kapcsolat ellenőrzése többféleképpen is történhet. Az alábbiakban az alapszintű ellenőrzés végrehajtásának módját tárgyaljuk az Azure portál és a PowerShell használatával.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="modify"></a>Helyi hálózati átjáró IP-címelőtagjainak módosítása

A helyi hálózati átjáró előtagjainak módosításához használja az alábbi utasításokat.  Két utasításcsoportot adtunk meg. Ezek közül aszerint választhat, hogy a VPN Gateway kapcsolat létre lett-e már hozva. 

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]


## Következő lépések

- Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

- Információk a BGP-ről: [A BGP áttekintése](vpn-gateway-bgp-overview.md) és [A BGP konfigurálása](vpn-gateway-bgp-resource-manager-ps.md).




<!--HONumber=jun16_HO2-->


