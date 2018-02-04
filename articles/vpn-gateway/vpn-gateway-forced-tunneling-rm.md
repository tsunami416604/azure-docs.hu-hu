---
title: "Az Azure-webhelyek kapcsolatok kényszerített bújtatás konfigurálása: erőforrás-kezelő |} Microsoft Docs"
description: "Hogyan lehet irányítani, vagy \"kényszerített\" minden internetre irányuló forgalomnak a helyszíni helyre."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cbe58db8-b598-4c9f-ac88-62c865eb8721
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2018
ms.author: cherylmc
ms.openlocfilehash: 00330f49d4acc9bd2d720a60b743b78c86b08f86
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Kényszerített bújtatás konfigurálása az Azure Resource Manager-alapú üzemi modellel

A kényszerített bújtatás lehetővé teszi az átirányítási vagy "kényszerített" minden internetre irányuló forgalomnak biztonsági másolatot a helyszíni helyre vizsgálati és naplózási pont-pont VPN-alagúton keresztül. Ez az kritikus fontosságú biztonsági előfeltétele annak, hogy a legtöbb vállalati informatikai házirendek. Nélkül a kényszerített bújtatás, kötött internetes forgalmat a virtuális gépek Azure-ban mindig traverses Azure hálózati infrastruktúráról közvetlenül kimenő csatlakozik az internethez, a beállítás lehetővé teszi vizsgálja meg, vagy a forgalom naplózása nélkül. Jogosulatlan Internet-hozzáférés is eredményezhet, információfelfedés vagy más típusú biztonsági problémákat.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Ez a cikk bemutatja, hogyan konfigurálása, a kényszerített bújtatás a Resource Manager üzembe helyezési modellel használatával létrehozott virtuális hálózatokat. A kényszerített bújtatás PowerShell, nem a portálon keresztül használatával konfigurálható. Ha azt szeretné, a klasszikus üzembe helyezési modell kényszerített bújtatás konfigurálása, a következő legördülő listából válassza a klasszikus cikk:

> [!div class="op_single_selector"]
> * [PowerShell – Klasszikus](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="about-forced-tunneling"></a>Hamarosan kényszerített bújtatás

A következő ábra bemutatja, hogyan kényszerített bújtatás működik. 

![Alagúthasználat kényszerítése](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

A fenti példában a Frontend alhálózathoz nem kényszeríti a bújtatott. A munkaterhelések Frontend alhálózaton fogadja el, és a felhasználói kérésekre válaszol az internetről közvetlenül is. A közepes réteg és a háttérkiszolgáló alhálózatok kényszerítve vannak bújtatott. A kimenő kapcsolatokat ezek két alhálózat-Internet kell kényszerített vagy egy helyszíni hely keresztül az S2S VPN-alagutat átirányítva.

Ez lehetővé teszi, hogy korlátozza és vizsgálja meg a virtuális gépek Internet-hozzáféréssel, vagy miközben továbbra is a többrétegű szolgáltatás architektúrája szükséges engedélyezése a felhőalapú Azure-szolgáltatások. Ha a virtuális hálózatok egyetlen internetre munkaterhelés, is alkalmazhat a teljes virtuális hálózatok a kényszerített bújtatás.

## <a name="requirements-and-considerations"></a>Követelmények és szempontok

Az Azure-ban kényszerített bújtatás konfigurálása virtuális hálózati felhasználó által definiált útvonalak keresztül. Egy helyszíni hely irányít át forgalmat az Azure VPN gatewayhez alapértelmezett útvonalat kifejezve. További információ a felhasználó által definiált útválasztást és a virtuális hálózatok: [felhasználó által definiált útvonalak és IP-továbbítás](../virtual-network/virtual-networks-udr-overview.md).

* Minden egyes virtuális hálózati alhálózat van egy beépített, rendszer-útválasztási táblázatához. A rendszer útválasztási táblázatban az útvonalak a következő három csoport rendelkezik:
  
  * **Helyi VNet útvonalak:** közvetlenül és a cél virtuális gépek ugyanabban a virtuális hálózatban.
  * **A helyi útvonalak:** számára az Azure VPN gateway.
  * **Alapértelmezett útvonal:** közvetlenül kell az internethez. A privát IP-címek nem fedi le az előző két útvonalakat a csomagok megszakadnak.
* Ez az eljárás adjon hozzá egy alapértelmezett útvonalat, és társíthatja az útvonaltábla útválasztási táblázatot hozhat létre felhasználó által definiált útvonalak (UDR) segítségével a virtuális hálózat alhálózat ezek alhálózatok kényszerített bújtatás engedélyezése.
* A kényszerített bújtatás kell rendelni egy Vnetet, amelynek az útvonalalapú VPN-átjáró. Egy "alapértelmezett"nevű hely a létesítmények közötti helyi helyek között a virtuális hálózathoz be kell. Emellett a helyszíni VPN-eszköz kell konfigurálni 0.0.0.0/0 forgalom választók használatával. 
* A kényszerített bújtatás ExpressRoute a mechanizmus révén nincs konfigurálva, de ehelyett szerint engedélyezve van egy alapértelmezett útvonalat hirdet a ExpressRoute BGP társviszony-létesítési munkameneteket keresztül. További információkért lásd: a [ExpressRoute dokumentációja](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Konfigurálása – áttekintés

Az alábbi eljárás segítségével hozhat létre, az erőforráscsoportot és egy virtuális hálózatot. Majd hozhat létre a VPN-átjáró, és konfigurálja a kényszerített bújtatást. Ezzel az eljárással a virtuális hálózat MultiTier – VNet három alhálózatok rendelkezik: "Előtér", "Midtier" és "Háttér", a négy létesítmények közötti kapcsolatok: "DefaultSiteHQ", és három ágak.

Az eljárás lépéseit a kényszerített bújtatást, az alapértelmezett hely kapcsolat a "DefaultSiteHQ", és állítja be a "Midtier" és "Háttér" alhálózatok használandó kényszerített bújtatás.

## <a name="before"></a>Előkészületek

Telepítse az Azure Resource Manager PowerShell-parancsmagjainak legújabb verzióját. A PowerShell-parancsmagok telepítésével kapcsolatban további információ: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).

> [!IMPORTANT]
> A PowerShell-parancsmagok legújabb verziójának telepítése szükség. Ellenkező esetben érvényesítési hibák léphetnek fel, néhány parancsmag futtatásakor.
>
>

### <a name="to-log-in"></a>A bejelentkezéshez

[!INCLUDE [To log in](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="configure-forced-tunneling"></a>Kényszerített bújtatás konfigurálása

> [!NOTE]
> "Ez a parancsmag kimenete objektumtípusának módosulnak egy későbbi kiadásban" arról figyelmeztetések jelenhetnek meg. Ez az elvárt működés, és biztonságosan figyelmen kívül hagyhatja a figyelmeztetéseket.
>
>


1. Hozzon létre egy erőforráscsoportot.

  ```powershell
  New-AzureRmResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
  ```
2. Hozzon létre egy virtuális hálózatot, és adja meg az alhálózatot.

  ```powershell 
  $s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
  $s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
  $s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
  $s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
  $vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
  ```
3. Helyi hálózati átjáró létrehozása.

  ```powershell
  $lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
  $lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
  $lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
  $lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
  ```
4. Az útvonaltábla és útvonal-szabály létrehozása.

  ```powershell
  New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
  $rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
  Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
  Set-AzureRmRouteTable -RouteTable $rt
  ```
5. A Midtier és a háttérkiszolgáló alhálózatokra útvonaltábla társítani.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
  Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
  Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
6. A virtuális hálózati átjáró létrehozása. Ebben a lépésben egy ideig, néha 45 perc vagy több, mert a létrehozandó és az átjáró konfigurálása vesz igénybe. Ha a GatewaySKU érték vonatkozó ValidateSet hibaüzenet jelenik meg, győződjön meg arról, hogy telepítette a [legújabb verzióját a PowerShell-parancsmagok](#before). A PowerShell-parancsmagok legújabb verzióját a legújabb Gateway SKU-n az új érvényesített értékeket tartalmazza.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
  $gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
  New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
  ```
7. Rendelje hozzá egy alapértelmezett hely a virtuális hálózati átjáró. A **- GatewayDefaultSite** a parancsmag paraméter, amely lehetővé teszi, hogy működjenek, ezért ügyeljen arra, ezt a beállítást megfelelően konfigurálja a kényszerített útválasztási konfigurációja. 

  ```powershell
  $LocalGateway = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
  $VirtualGateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
  Set-AzureRmVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
  ```
8. A telephelyek közötti VPN-kapcsolatok létesítéséhez.

  ```powershell
  $gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
  $lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
  $lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
  $lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
  $lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
  Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
  ```
