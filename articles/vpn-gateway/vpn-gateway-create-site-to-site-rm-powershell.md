---
title: "A helyszíni hálózat csatlakoztatása egy Azure-beli virtuális hálózathoz: Helyek közötti VPN: PowerShell | Microsoft Docs"
description: "A helyszíni hálózatot az Azure-beli virtuális hálózattal a nyilvános interneten keresztül összekötő IPsec-kapcsolat létrehozásának lépései. Ezen lépéseket követve létrehozhat egy helyek közötti VPN-átjáró kapcsolatot a PowerShell segítségével."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fcc2fda5-4493-4c15-9436-84d35adbda8e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/24/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 4ec11ffeae94b4a8e5a65566f0f0c067f45a0134
ms.lasthandoff: 04/25/2017


---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>Helyek közötti VPN-kapcsolattal rendelkező virtuális hálózat létrehozása a PowerShell használatával

Ez a cikk bemutatja, hogyan használhatja a PowerShellt egy helyek közötti VPN-átjárókapcsolat létrehozására egy helyszíni hálózat és a VNet között. A cikkben ismertetett lépések a Resource Manager-alapú üzemi modellre vonatkoznak. Ezt a konfigurációt más üzembehelyezési eszközzel vagy üzemi modellel is létrehozhatja, ha egy másik lehetőséget választ az alábbi listáról:

> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager – parancssori felület](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Klasszikus – Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Klasszikus – Klasszikus portál](vpn-gateway-site-to-site-create.md)
> 
>


![Helyek közötti VPN Gateway létesítmények közötti kapcsolathoz – diagram](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-connection-diagram.png)

A helyek közötti VPN-átjárókapcsolat használatával kapcsolat hozható létre a helyszíni hálózat és egy Azure-beli virtuális hálózat között egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-alagúton keresztül. Az ilyen típusú kapcsolatokhoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy kifelé irányuló, nyilvános IP-cím. További információk a VPN-átjárókról: [Információk a VPN Gatewayről](vpn-gateway-about-vpngateways.md).

## <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg a következő feltételek teljesüléséről:

* Erősítse meg, hogy a Resource Manager-alapú üzemi modellt kívánja használni. [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* Egy kompatibilis VPN-eszköz és egy azt konfigurálni képes személy. További információk a kompatibilis VPN-eszközökről és az eszközkonfigurációról: [Tudnivalók a VPN-eszközökről](vpn-gateway-about-vpn-devices.md).
* Egy kifelé irányuló, nyilvános IPv4-cím a VPN-eszköz számára. Ez az IP-cím nem lehet NAT mögötti.
* Ha nem ismeri a helyszíni hálózati konfigurációjában található IP-címtereket, egyeztessen valakivel, aki ezeket az adatokat megadhatja Önnek. Amikor létrehozza ezt a konfigurációt, meg kell határoznia az IP-címtartományok előtagjait, amelyeket az Azure majd a helyszínre irányít. A helyszíni hálózat egyik alhálózata sem lehet átfedésben azokkal a virtuális alhálózatokkal, amelyekhez csatlakozni kíván.
* Az Azure Resource Manager PowerShell-parancsmagjainak legújabb verziója. A PowerShell-parancsmagok telepítésével kapcsolatban további információ: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).

### <a name="example-values"></a>Példaértékek

A cikkben szereplő példák a következő értékeket használják. Ezekkel az értékekkel létrehozhat egy tesztkörnyezetet, vagy a segítségükkel értelmezheti a cikkben szereplő példákat.

```
#Example values

VnetName                = testvnet 
ResourceGroup           = testrg 
Location                = West US 
AddressSpace            = 10.0.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.0.1.0/28 
GatewaySubnet           = 10.0.0.0/27
LocalNetworkGatewayName = LocalSite
LNG Public IP           = <VPN device IP address> 
Local Address Prefixes  = 10.0.0.0/24','20.0.0.0/24
Gateway Name            = vnetgw1
PublicIP                = gwpip
Gateway IP Config       = gwipconfig1 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = myGWConnection
```

## <a name="Login"></a>1. Csatlakozás az előfizetéshez
A Resource Manager parancsmagjainak használatához váltson át PowerShell módba. További információ: [A Windows PowerShell használata a Resource Managerrel](../powershell-azure-resource-manager.md).

1. Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

  ```powershell
  Login-AzureRmAccount
  ```
2. Keresse meg a fiókot az előfizetésekben.

  ```powershell
  Get-AzureRmSubscription
  ```
3. Válassza ki a használni kívánt előfizetést.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

## <a name="VNet"></a>2. Virtuális hálózat és átjáró-alhálózat létrehozása

Ha még nem rendelkezik virtuális hálózattal, akkor hozzon létre egyet. Virtuális hálózat létrehozásakor győződjön meg róla, hogy a megadott címterek nincsenek átfedésben a helyszíni hálózaton található egyéb címterekkel. Ehhez a konfigurációhoz átjáróalhálózat is szükséges. A virtuális hálózati átjáró átjáróalhálózatot használ, amely a VPN Gateway szolgáltatások által használt IP-címeket tartalmazza. A létrehozott átjáróalhálózatnak a „GatewaySubnet” nevet kell adnia. Ha ezt az alhálózatot máshogy nevezi el, az létrejön ugyan, de az Azure nem kezeli átjáró-alhálózatként.

Az Ön által megadott átjáróalhálózat mérete a létrehozni kívánt VPN-átjárókonfigurációtól függ. Bár akár /29-es átjáróalhálózatot is létrehozhat, javasolt egy ennél nagyobb, több címmel rendelkező alhálózatot létrehozni: /27-eset vagy /28-asat. Nagyobb átjáróalhálózat használatával elegendő IP-cím áll rendelkezésre az esetleges jövőbeni konfigurációk megvalósításához.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-virtual-network-and-a-gateway-subnet"></a>Virtuális hálózat és átjáró-alhálózat létrehozása

Ebben a példában egy virtuális hálózatot és egy átjáróalhálózatot hozunk létre. Ha már rendelkezik virtuális hálózattal, amelyhez hozzá szeretne adni egy átjáróalhálózatot, lásd: [Átjáróalhálózat hozzáadása már létrehozott virtuális hálózathoz](#gatewaysubnet).

Hozzon létre egy erőforráscsoportot:

```powershell
New-AzureRmResourceGroup -Name testrg -Location 'West US'
```

Hozza létre a virtuális hálózatot. 

1. Állítsa be a változókat.

  ```powershell
  $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/27
  $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
  ```
2. Hozza létre a virtuális hálózatot.

  ```powershell
  New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg `
  -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2
  ```

### <a name="gatewaysubnet"></a>Átjáró-alhálózat hozzáadása már létrehozott virtuális hálózathoz

1. Állítsa be a változókat.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
  ```
2. Hozza létre az átjáró-alhálózatot.

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/27 -VirtualNetwork $vnet
  ```
3. Állítsa be a konfigurációt.

  ```powershell
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```

## 3. <a name="localnet"></a>A helyi hálózati átjáró létrehozása

A helyi hálózati átjáró általában a helyszínt jelenti. Olyan nevet adjon a helynek, amellyel az Azure hivatkozhat rá, majd határozza meg annak a helyszíni VPN-eszköznek az IP-címét, amellyel létre kívánja hozni a kapcsolatot. Emellett megadhatja azokat az IP-címelőtagokat, amelyek a VPN-átjárón keresztül a VPN-eszközre lesznek irányítva. Az Ön által meghatározott címelőtagok a helyszíni hálózatán található előtagok. A helyszíni hálózat módosításakor az előtagok egyszerűen frissíthetők.

Használja a következő értékeket:

* A helyszíni VPN-eszköz IP-címe: *GatewayIPAddress*. A VPN-eszköz nem lehet NAT mögött.
* A helyszíni címtér az *AddressPrefix*.

- Helyi hálózati átjáró hozzáadása egyetlen címelőtaggal:

  ```powershell
  New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
  -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix   '10.0.0.0/24'
  ```

- Helyi hálózati átjáró hozzáadása több címelőtaggal:

  ```powershell
  New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
  -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')
  ```

- Helyi hálózati átjáró IP-cím előtagjainak módosítása:<br>
A helyi hálózati átjáró előtagjai bizonyos esetekben változnak. Az IP-címelőtagok módosításának lépései attól függően változnak, hogy előzőleg létre lett-e hozva VPN Gateway-kapcsolat. Lásd a jelen cikk [Helyi hálózati átjáró IP-címelőtagjainak módosítása](#modify) című szakaszát.

## <a name="PublicIP"></a>4. Nyilvános IP-cím kérése

Kérjen egy nyilvános IP-címeta a virtuális hálózat VPN-átjárójához. Ez az az IP-cím, amelyhez a VPN-eszközt csatlakoztatni kívánja.

A Resource Manager-alapú üzemi modell virtuális hálózati átjárója jelenleg kizárólag a nyilvános IP-címeket támogatja a dinamikus kiosztási módszer használatával. Ez azonban nem jelenti azt, hogy az IP-cím változik. A VPN-átjáró IP-címe kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. A virtuális hálózati átjáró nyilvános IP-címe nem módosul átméretezés, alaphelyzetbe állítás, illetve a VPN-átjáró belső karbantartása/frissítése során.

Használja a következő PowerShell-példát:

```powershell
$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>5. Az átjáró IP-címkonfigurációjának létrehozása
Az átjáró konfigurációja meghatározza az alhálózatot és a használandó nyilvános IP-címet. A következő példa használatával hozza létre az átjáró konfigurációját.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

## <a name="CreateGateway"></a>6. A VPN-átjáró létrehozása

Hozza létre a virtuális hálózat VPN-átjáróját. A VPN-átjáró létrehozása akár 45 percet vagy többet is igénybe vehet.

Használja a következő értékeket:

* A webhelyek közötti konfiguráció *-GatewayType* paraméterének értéke: *Vpn*. Az átjáró típusa mindig a kiépítendő konfigurációra jellemző. Más átjáró-konfigurációk például -GatewayType ExpressRoute típus alkalmazását igényelhetik.
* A *-VpnType* típus a következők valamelyike lehet: *RouteBased* (egyes dokumentumokban Dinamikus átjáró néven szerepel) vagy *PolicyBased* (egyes dokumentumokban Statikus átjáró néven szerepel). További információk a VPN-átjárótípusokról: [Információk a VPN Gateway-ről](vpn-gateway-about-vpngateways.md).
* A *-GatewaySku* paraméter lehetséges értékei: Alapszintű, Standard vagy HighPerformance. Egyes termékváltozatok konfigurációs korlátokkal rendelkeznek. További információkért lásd: [Az átjárók termékváltozatai](vpn-gateway-about-vpngateways.md#gateway-skus).

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku Standard
```

## <a name="ConfigureVPNDevice"></a>7. VPN-eszköz konfigurálása

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

A virtuális hálózati átjáró IP-címét az alábbi példa alapján keresheti meg a PowerShell használatával:

```powershell
Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG
```

## <a name="CreateConnection"></a>8. VPN-kapcsolat létrehozása
Ezután hozzon létre egy helyek közötti VPN-kapcsolatot a virtuális hálózati átjáró és a VPN-eszköz között. Ne felejtse el ezeket az értékeket a saját értékeire cserélni. A megosztott kulcsnak meg kell egyeznie a VPN-eszköze konfigurálásakor használt értékkel. Vegye figyelembe, hogy helyek közötti kapcsolat esetében a „-ConnectionType” értéke: *IPsec*.

1. Állítsa be a változókat.
  ```powershell
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
  $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
  ```

2. Hozza létre a kapcsolatot.
  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName testrg `
  -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```

A kapcsolat rövid időn belül létrejön.

## <a name="toverify"></a>9. A VPN-kapcsolat ellenőrzése
A VPN-kapcsolat ellenőrzése többféleképpen is történhet.

[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="modify"></a>Helyi hálózati átjáró IP-címelőtagjainak módosítása
Ha módosítania kell a helyi hálózati átjáró előtagjait, használja a következő utasításokat. Két utasításcsoportot adtunk meg. Ezek közül aszerint választhat, hogy az átjárókapcsolat létre lett-e már hozva.

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Helyi hálózati átjáró IP-címének módosítása
[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Következő lépések
*  Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Információk a BGP-ről: [A BGP áttekintése](vpn-gateway-bgp-overview.md) és [A BGP konfigurálása](vpn-gateway-bgp-resource-manager-ps.md).

