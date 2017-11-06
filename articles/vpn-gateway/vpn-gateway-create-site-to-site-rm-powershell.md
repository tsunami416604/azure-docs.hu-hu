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
ms.date: 10/12/2017
ms.author: cherylmc
ms.openlocfilehash: 177c7474a69ba550c5e7984354d7f309b3bedb20
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>Helyek közötti VPN-kapcsolattal rendelkező virtuális hálózat létrehozása a PowerShell használatával

Ez a cikk bemutatja, hogyan használhatja a PowerShellt egy helyek közötti VPN-átjárókapcsolat létrehozására egy helyszíni hálózat és a VNet között. A cikkben ismertetett lépések a Resource Manager-alapú üzemi modellre vonatkoznak. Ezt a konfigurációt más üzembehelyezési eszközzel vagy üzemi modellel is létrehozhatja, ha egy másik lehetőséget választ az alábbi listáról:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Parancssori felület](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>


A helyek közötti VPN-átjárókapcsolat használatával kapcsolat hozható létre a helyszíni hálózat és egy Azure-beli virtuális hálózat között egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-alagúton keresztül. Az ilyen típusú kapcsolatokhoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy kifelé irányuló, nyilvános IP-cím. További információk a VPN-átjárókról: [Információk a VPN Gatewayről](vpn-gateway-about-vpngateways.md).

![Helyek közötti VPN Gateway létesítmények közötti kapcsolathoz – diagram](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-diagram.png)

## <a name="before"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg a következő feltételek teljesüléséről:

* Győződjön meg arról, hogy rendelkezésre áll egy kompatibilis VPN-eszköz és egy azt konfigurálni képes személy. További információk a kompatibilis VPN-eszközökről és az eszközkonfigurációról: [Tudnivalók a VPN-eszközökről](vpn-gateway-about-vpn-devices.md).
* Győződjön meg arról, hogy rendelkezik egy kifelé irányuló, nyilvános IPv4-címmel a VPN-eszköz számára. Ez az IP-cím nem lehet NAT mögötti.
* Ha nem ismeri a helyszíni hálózati konfigurációjában található IP-címtereket, egyeztessen valakivel, aki ezeket az adatokat megadhatja Önnek. Amikor létrehozza ezt a konfigurációt, meg kell határoznia az IP-címtartományok előtagjait, amelyeket az Azure majd a helyszínre irányít. A helyszíni hálózat egyik alhálózata sem lehet átfedésben azokkal a virtuális alhálózatokkal, amelyekhez csatlakozni kíván.
* Telepítse az Azure Resource Manager PowerShell-parancsmagjainak legújabb verzióját. A PowerShell-parancsmagok gyakran frissülnek, és általában frissítenie kell a PowerShell-parancsmagokat a legújabb szolgáltatásfunkciók eléréséhez. Ha nem frissíti a PowerShell-parancsmagokat, a megadott értékek sikertelenek lehetnek. A PowerShell-parancsmagok letöltéséről és telepítéséről további információért lásd: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).

### <a name="example"></a>Példaértékek

A cikkben szereplő példák a következő értékeket használják. Ezekkel az értékekkel létrehozhat egy tesztkörnyezetet, vagy a segítségükkel értelmezheti a cikkben szereplő példákat.

```
#Example values

VnetName                = TestVNet1
ResourceGroup           = TestRG1
Location                = East US 
AddressSpace            = 10.11.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.11.1.0/28 
GatewaySubnet           = 10.11.0.0/27
LocalNetworkGatewayName = Site2
LNG Public IP           = <VPN device IP address> 
Local Address Prefixes  = 10.0.0.0/24, 20.0.0.0/24
Gateway Name            = VNet1GW
PublicIP                = VNet1GWIP
Gateway IP Config       = gwipconfig1 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2

```


## <a name="Login"></a>1. Csatlakozás az előfizetéshez

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="VNet"></a>2. Virtuális hálózat és átjáró-alhálózat létrehozása

Ha még nem rendelkezik virtuális hálózattal, akkor hozzon létre egyet. Virtuális hálózat létrehozásakor győződjön meg róla, hogy a megadott címterek nincsenek átfedésben a helyszíni hálózaton található egyéb címterekkel. 

>[!NOTE]
>Ahhoz, hogy ez a VNet egy helyszíni helyhez csatlakozzon, egyeztetnie kell a helyszíni hálózati rendszergazdájával, hogy különítsen el egy IP-címtartományt, amit kifejezetten ehhez a virtuális hálózathoz használhat. Ha a VPN-kapcsolat mindkét oldalán ismétlődő címtartomány található, a rendszer esetleg nem a várt módon irányítja a forgalmat. Ráadásul ha ezt a VNetet egy másik VNethez szeretné csatlakoztatni, a címtér nem lehet átfedésben másik VNettel. Ügyeljen arra, hogy a hálózati konfigurációt ennek megfelelően tervezze meg.
>
>

### <a name="about-the-gateway-subnet"></a>Az átjáró alhálózatának ismertetése

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

[!INCLUDE [No NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="vnet"></a>Virtuális hálózat és átjáró-alhálózat létrehozása

Ebben a példában egy virtuális hálózatot és egy átjáróalhálózatot hozunk létre. Ha már rendelkezik virtuális hálózattal, amelyhez hozzá szeretne adni egy átjáróalhálózatot, lásd: [Átjáróalhálózat hozzáadása már létrehozott virtuális hálózathoz](#gatewaysubnet).

Hozzon létre egy erőforráscsoportot:

```powershell
New-AzureRmResourceGroup -Name TestRG1 -Location 'East US'
```

Hozza létre a virtuális hálózatot.

1. Állítsa be a változókat.

  ```powershell
  $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.11.0.0/27
  $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix 10.11.1.0/28
  ```
2. Hozza létre a virtuális hálózatot.

  ```powershell
  New-AzureRmVirtualNetwork -Name TestVNet1 -ResourceGroupName TestRG1 `
  -Location 'East US' -AddressPrefix 10.11.0.0/16 -Subnet $subnet1, $subnet2
  ```

### <a name="gatewaysubnet"></a>Átjáró-alhálózat hozzáadása már létrehozott virtuális hálózathoz

1. Állítsa be a változókat.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG1 -Name TestVet1
  ```
2. Hozza létre az átjáró-alhálózatot.

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.11.0.0/27 -VirtualNetwork $vnet
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

Helyi hálózati átjáró hozzáadása egyetlen címelőtaggal:

  ```powershell
  New-AzureRmLocalNetworkGateway -Name Site2 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.0.0.0/24'
  ```

Helyi hálózati átjáró hozzáadása több címelőtaggal:

  ```powershell
  New-AzureRmLocalNetworkGateway -Name Site2 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')
  ```

Helyi hálózati átjáró IP-cím előtagjainak módosítása:<br>
A helyi hálózati átjáró előtagjai bizonyos esetekben változnak. Az IP-címelőtagok módosításának lépései attól függően változnak, hogy előzőleg létre lett-e hozva VPN Gateway-kapcsolat. Lásd a jelen cikk [Helyi hálózati átjáró IP-címelőtagjainak módosítása](#modify) című szakaszát.

## <a name="PublicIP"></a>4. Nyilvános IP-cím kérése

Egy VPN Gateway-nek rendelkeznie kell nyilvános IP-címmel. Először az IP-cím típusú erőforrást kell kérnie, majd hivatkoznia kell arra, amikor létrehozza a virtuális hálózati átjárót. Az IP-címet a rendszer dinamikusan rendeli hozzá az erőforráshoz a VPN Gateway létrehozásakor. A VPN Gateway jelenleg csak a *Dinamikus* nyilvános IP-cím lefoglalását támogatja. Nem kérheti statikus IP-cím hozzárendelését. Ez azonban nem jelenti azt, hogy az IP-cím módosul a VPN Gateway-hez való hozzárendelése után. A nyilvános IP-cím kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. Nem módosul átméretezés, alaphelyzetbe állítás, illetve a VPN Gateway belső karbantartása/frissítése során.

Kérjen egy nyilvános IP-címet, amelyet a virtuális hálózatban a VPN Gateway-hez társít.

```powershell
$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>5. Az átjáró IP-címkonfigurációjának létrehozása

Az átjáró konfigurációja meghatározza az alhálózatot és a használandó nyilvános IP-címet. A következő példa használatával hozza létre az átjáró konfigurációját.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name TestVNet1 -ResourceGroupName TestRG1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

## <a name="CreateGateway"></a>6. A VPN-átjáró létrehozása

Hozza létre a virtuális hálózat VPN-átjáróját. A VPN-átjáró létrehozása akár 45 percet vagy többet is igénybe vehet.

Használja a következő értékeket:

* A webhelyek közötti konfiguráció *-GatewayType* paraméterének értéke: *Vpn*. Az átjáró típusa mindig a kiépítendő konfigurációra jellemző. Más átjáró-konfigurációk például -GatewayType ExpressRoute típus alkalmazását igényelhetik.
* A *-VpnType* típus a következők valamelyike lehet: *RouteBased* (egyes dokumentumokban Dinamikus átjáró néven szerepel) vagy *PolicyBased* (egyes dokumentumokban Statikus átjáró néven szerepel). További információk a VPN-átjárótípusokról: [Információk a VPN Gateway-ről](vpn-gateway-about-vpngateways.md).
* Válassza ki az átjáró használni kívánt termékváltozatát. Egyes termékváltozatok konfigurációs korlátokkal rendelkeznek. További információkért lásd: [Az átjárók termékváltozatai](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Ha hibaüzenetet kap a -GatewaySku paraméterrel kapcsolatban a VPN-átjáró létrehozásakor, győződjön meg arról, hogy telepítette a PowerShell-parancsmagok legújabb verzióját.

```powershell
New-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="ConfigureVPNDevice"></a>7. VPN-eszköz konfigurálása

A helyszíni hálózaton a helyek közötti kapcsolatok létesítéséhez VPN-eszközre van szükség. Ebben a lépésben a VPN-eszköz konfigurálása következik. A VPN-eszköz konfigurálásakor a következőkre van szüksége:

- Megosztott kulcs. Ez ugyanaz a megosztott kulcs, amelyet a helyek közötti VPN-kapcsolat létrehozásakor ad meg. A példákban alapvető megosztott kulcsot használunk. Javasoljuk egy ennél összetettebb kulcs létrehozását.
- A virtuális hálózati átjáró nyilvános IP-címe. A nyilvános IP-címet az Azure Portalon, valamint a PowerShell vagy a CLI használatával is megtekintheti. A virtuális hálózati átjáró IP-címét az alábbi példa alapján keresheti meg a PowerShell használatával:

  ```powershell
  Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG1
  ```

[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="CreateConnection"></a>8. VPN-kapcsolat létrehozása

Ezután hozzon létre egy helyek közötti VPN-kapcsolatot a virtuális hálózati átjáró és a VPN-eszköz között. Ne felejtse el ezeket az értékeket a saját értékeire cserélni. A megosztott kulcsnak meg kell egyeznie a VPN-eszköze konfigurálásakor használt értékkel. Vegye figyelembe, hogy helyek közötti kapcsolat esetében a „-ConnectionType” értéke: *IPsec*.

1. Állítsa be a változókat.
  ```powershell
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
  $local = Get-AzureRmLocalNetworkGateway -Name Site2 -ResourceGroupName TestRG1
  ```

2. Hozza létre a kapcsolatot.
  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite2 -ResourceGroupName TestRG1 `
  -Location 'East US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```

A kapcsolat rövid időn belül létrejön.

## <a name="toverify"></a>9. A VPN-kapcsolat ellenőrzése

A VPN-kapcsolat ellenőrzése többféleképpen is történhet.

[!INCLUDE [Verify connection](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="connectVM"></a>Csatlakozás virtuális géphez

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]


## <a name="modify"></a>Helyi hálózati átjáró IP-címelőtagjainak módosítása

Ha a helyszínre átirányítani kívánt IP-címelőtagok módosulnak, módosíthatja a helyi hálózati átjárót. Két utasításcsoportot adtunk meg. Ezek közül aszerint választhat, hogy az átjárókapcsolat létre lett-e már hozva.

[!INCLUDE [Modify prefixes](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Helyi hálózati átjáró IP-címének módosítása

[!INCLUDE [Modify gateway IP address](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Következő lépések

*  Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Információk a BGP-ről: [A BGP áttekintése](vpn-gateway-bgp-overview.md) és [A BGP konfigurálása](vpn-gateway-bgp-resource-manager-ps.md).
* A helyek közötti VPN-kapcsolatok Azure Resource Manager-sablonok használatával történő létrehozásáról további információt a [helyek közötti VPN-kapcsolat létrehozását](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/) ismertető cikkben talál.
* A virtuális hálózatok közötti VPN-kapcsolatok Azure Resource Manager-sablonok használatával történő létrehozásáról további információt a [HBase-georeplikáció üzembe helyezését](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/) ismertető cikkben talál.
