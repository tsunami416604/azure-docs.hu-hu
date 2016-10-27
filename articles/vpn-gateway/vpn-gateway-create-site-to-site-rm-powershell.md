<properties
   pageTitle="Virtuális hálózat létrehozása helyek közötti VPN-kapcsolattal az Azure Resource Manager és a PowerShell használatával | Microsoft Azure"
   description="Ez a cikk lépésről lépésre bemutatja, hogyan hozható létre virtuális hálózat Resource Manager-alapú üzemi modellel, és ez hogyan csatlakoztatható helyszíni hálózathoz egy S2S VPN-átjárói kapcsolat használatával."
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
   ms.date="08/31/2016"
   ms.author="cherylmc"/>


# Virtuális hálózat létrehozása helyek közötti kapcsolattal a PowerShell használatával

> [AZURE.SELECTOR]
- [Resource Manager – Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Klasszikus – Klasszikus portál](vpn-gateway-site-to-site-create.md)

Ez a cikk lépésről lépésre bemutatja, hogyan hozható létre egy virtuális hálózat és egy helyek közötti VPN-kapcsolat a helyszíni hálózathoz az **Azure Resource Manager-alapú üzemi modell** használatával. A helyek közötti kapcsolatok létesítmények közötti és hibrid konfigurációk esetében is alkalmazhatók.

![Helyek közötti diagram](./media/vpn-gateway-create-site-to-site-rm-powershell/s2srmps.png "site-to-site") 


### Üzembe helyezési modellek és eszközök a helyek közötti kapcsolatokhoz

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

Ha csatlakoztatni szeretné egymáshoz a virtuális hálózatokat, de nem szeretne létrehozni kapcsolatot egy hellyel: [Virtuális hálózatok közötti kapcsolat konfigurálása](vpn-gateway-vnet-vnet-rm-ps.md).


## Előkészületek

A konfigurálás megkezdése előtt győződjön meg arról, hogy rendelkezik a következő elemekkel.

- Egy kompatibilis VPN-eszköz és egy azt konfigurálni képes személy. Lásd: [About VPN Devices](vpn-gateway-about-vpn-devices.md) (Tudnivalók a VPN-eszközökről). Ha nem jártas a VPN-eszköz konfigurálásában, vagy nem ismeri a helyszíni hálózati konfigurációjában található IP-címtereket, együtt kell működnie valakivel, aki ezeket az adatokat megadhatja Önnek.

- Egy kifelé irányuló, nyilvános IP-cím a VPN-eszközhöz. Ez az IP-cím nem lehet NAT mögötti.
    
- Azure-előfizetés. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
    
- Az Azure Resource Manager PowerShell-parancsmagjainak legújabb verziója. A PowerShell-parancsmagok telepítésével kapcsolatban további információ: [Az Azure PowerShell telepítése és konfigurálása](../powershell-install-configure.md).


## 1. Csatlakozás az előfizetéshez 

A Resource Manager parancsmagjainak használatához váltson át PowerShell módba. További információ: [A Windows PowerShell használata a Resource Managerrel](../powershell-azure-resource-manager.md).

Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. A következő minta segíthet a kapcsolódásban:

    Login-AzureRmAccount

Keresse meg a fiókot az előfizetésekben.

    Get-AzureRmSubscription 

Válassza ki a használni kívánt előfizetést.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## 2. Virtuális hálózat és átjáró-alhálózat létrehozása

A példák egy /28-as átjáró-alhálózatot használnak. Bár lehetséges akár /29-es átjáróalhálózatot is létrehozni, javasolt egy ennél nagyobb, több címmel rendelkező alhálózatot létrehozni: legalább /28-asat vagy /27-eset. Ez elegendő címet biztosít ahhoz, hogy az esetleges további konfigurációkat is elbírják.

Ha már rendelkezik /29-es vagy nagyobb átjáró-alhálózattal ellátott virtuális hálózattal, továbbléphet a [Helyi hálózati átjáró hozzáadása](#localnet) részre.


[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]  

### Virtuális hálózat és átjáró-alhálózat létrehozása

A következő minta segítségével hozzon létre egy virtuális hálózatot és egy átjáró-alhálózatot. Az itt szerepelő értékeket cserélje ki a sajátjaival. 

Először is hozzon létre egy erőforráscsoportot:
    
    New-AzureRmResourceGroup -Name testrg -Location 'West US'

A következő lépés a virtuális hálózat létrehozása. Ellenőrizze, hogy a megadott címterek nincsenek átfedésben a helyszíni hálózaton található egyéb címterekkel.

A következő minta létrehoz egy *testvnet* nevű virtuális hálózatot és két alhálózatot a következő neveken: *GatewaySubnet* és *Subnet1*. Fontos, hogy létrehozzon egy *GatewaySubnet* nevű alhálózatot. Ha ezt az alhálózatot máshogy nevezi el, a kapcsolat konfigurálása sikertelen lesz. 

Állítsa be a változókat.

    $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
    $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'

Hozza létre a virtuális hálózatot.

    New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg `
    -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Átjáró-alhálózat hozzáadása már létrehozott virtuális hálózathoz

Erre a lépésre csak akkor van szükség, ha egy korábban már létrehozott virtuális hálózathoz kell hozzáadni átjáró-alhálózatot.

A következő minta használatával létrehozhatja saját átjáró-alhálózatát. Győződjön meg arról, hogy az átjáró-alhálózat neve „GatewaySubnet” legyen. Ha ezt az alhálózatot máshogy nevezi el, az létrejön ugyan, de az Azure nem kezeli átjáró-alhálózatként.

Állítsa be a változókat.

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet

Hozza létre az átjáró-alhálózatot.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Állítsa be a konfigurációt. 

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3. <a name="localnet"></a>Helyi hálózati átjáró hozzáadása

Virtuális hálózatokban a helyi hálózati átjáró általában a helyszínt jelenti. Olyan nevet adjon a helynek, amellyel az Azure hivatkozhat rá, valamint adja meg a helyi hálózati átjáró címtér-előtagját. 

Az Azure a megadott IP-címelőtagot a helyszíni helyre küldendő adatforgalom azonosítására használja. Ez azt jelenti, hogy meg kell adnia minden egyes olyan címelőtagot, amelyet a helyi hálózati átjáróhoz kíván társítani. A helyszíni hálózat módosításakor ezek az előtagok egyszerűen frissíthetők. 

PowerShell-példák használatakor vegye figyelembe a következőket:
    
- A helyszíni VPN-eszköz IP-címe: *GatewayIPAddress*. A VPN-eszköz nem lehet NAT mögött. 
- A helyszíni címtér az *AddressPrefix*.

Helyi hálózati átjáró hozzáadása egyetlen címelőtaggal:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Helyi hálózati átjáró hozzáadása több címelőtaggal:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### Helyi hálózati átjáró IP-címelőtagjainak módosítása

A helyi hálózati átjáró előtagjai bizonyos esetekben változnak. Az IP-címelőtagok módosításának lépései attól függően változnak, hogy előzőleg létre lett-e hozva VPN Gateway-kapcsolat. Lásd a jelen cikk [Helyi hálózati átjáró IP-címelőtagjainak módosítása](#modify) című szakaszát.


## 4. Nyilvános IP-cím kérése a VPN-átjáró számára

Ezután kérje egy nyilvános IP-cím kiosztását az Azure virtuális hálózat VPN-átjárója számára. Ez a cím nem azonos a VPN-eszközhöz rendelt IP-címmel; a cím magához az Azure VPN-átjáróhoz van rendelve. A használni kívánt IP-címet nem adhatja meg. Ennek kiosztása az átjáró számára dinamikusan történik. Ezt az IP-címet a helyszíni VPN-eszköz átjáróhoz történő csatlakoztatásának konfigurálásához kell használni.

Az Erőforrás-kezelő üzembe helyezési modelljének Azure VPN-átjárója jelenleg kizárólag a nyilvános IP-címeket támogatja a dinamikus kiosztási módszer használatával. Ez azonban nem jelenti azt, hogy az IP-cím változni fog. Az Azure VPN-átjáró IP-címe kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. Az átjáró nyilvános IP-címe nem módosul átméretezés, alaphelyzetbe állítás, illetve az Azure VPN-átjáró belső karbantartása/frissítése során.

Használja a következő PowerShell-mintát:

    $gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## 5. Az átjáró IP-címkonfigurációjának létrehozása

Az átjáró konfigurációja meghatározza az alhálózatot és a használandó nyilvános IP-címet. A következő minta használatával hozza létre az átjáró konfigurációját.

    $vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6. Virtuális hálózati átjáró létrehozása

Ebben a lépésben a virtuális hálózati átjárót fogja létrehozni. Az átjáró létrehozása hosszú időt vehet igénybe. Gyakran 45 percig vagy tovább is tarthat. 

Használja a következő értékeket:

- A webhelyek közötti konfiguráció *-GatewayType* paraméterének értéke: *Vpn*. Az átjáró típusa mindig a kiépítendő konfigurációra jellemző. Más átjáró-konfigurációk például -GatewayType ExpressRoute típus alkalmazását igényelhetik. 

- A *-VpnType* típus a következők valamelyike lehet: *RouteBased* (egyes dokumentumokban Dinamikus átjáró néven szerepel) vagy *PolicyBased* (egyes dokumentumokban Statikus átjáró néven szerepel). További információk a VPN-átjárótípusokról: [Információk a VPN-átjárókról](vpn-gateway-about-vpngateways.md#vpntype).
- A *-GatewaySku* paraméter lehetséges értékei: *Alapszintű*, *Standard* vagy *HighPerformance*.   

        New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
        -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

## 7. VPN-eszköz konfigurálása

Most szüksége lesz a virtuális hálózati átjáró nyilvános IP-címére, hogy konfigurálni tudja a helyszíni VPN-eszközt. A konkrét konfigurációs információkért forduljon az eszköz gyártójához. További információkért lásd: [VPN-eszközök](vpn-gateway-about-vpn-devices.md).

A virtuális hálózati átjáró IP-címét az alábbi minta alapján keresheti meg:

    Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## 8. VPN-kapcsolat létrehozása

Ezután hozzon létre egy helyek közötti VPN-kapcsolatot a virtuális hálózati átjáró és a VPN-eszköz között. Ne felejtse el ezeket az értékeket a saját értékeire cserélni. A megosztott kulcsnak meg kell egyeznie a VPN-eszköze konfigurálásakor használt értékkel. Vegye figyelembe, hogy helyek közötti kapcsolat esetében a `-ConnectionType` értéke: *IPsec*. 

Állítsa be a változókat.

    $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

Hozza létre a kapcsolatot.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

A kapcsolat rövid időn belül létrejön. 

## <a name="toverify"></a>VPN-kapcsolat ellenőrzése

A VPN-kapcsolat ellenőrzése többféleképpen is történhet.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="modify"></a>Helyi hálózati átjáró IP-címelőtagjainak módosítása

Ha módosítania kell a helyi hálózati átjáró előtagjait, használja a következő utasításokat. Két utasításcsoportot adtunk meg. Ezek közül aszerint választhat, hogy az átjárókapcsolat létre lett-e már hozva. 

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Helyi hálózati átjáró IP-címének módosítása

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## Következő lépések

- A virtuális hálózatokhoz hozzáadhat virtuális gépeket. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

- Információk a BGP-ről: [A BGP áttekintése](vpn-gateway-bgp-overview.md) és [A BGP konfigurálása](vpn-gateway-bgp-resource-manager-ps.md).




<!--HONumber=Oct16_HO3-->


