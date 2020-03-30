---
title: Kényszerített bújtatás konfigurálása a helyek közötti kapcsolatokhoz
description: Az összes internethez kötött forgalom átirányítása vagy kényszerítése a helyszíni helyre.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/01/2018
ms.author: cherylmc
ms.openlocfilehash: fc35654403bbe1375d4188476b11fd0453f74345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244627"
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Kényszerített bújtatás konfigurálása az Azure Resource Manager-alapú üzemi modellel

A kényszerített bújtatással a helyek közötti VPN-alagúton keresztül az internetre irányuló összes forgalom visszairányítható (kényszeríthető) a helyszíni helyre vizsgálat és naplózás céljából. Ez a legtöbb vállalati informatikai házirend kritikus biztonsági követelménye. Kényszerített bújtatás nélkül az Azure-beli virtuális gépekről érkező, internethez kötött forgalom mindig közvetlenül az internetről az internetre halad át anélkül, hogy lehetővé tenné a forgalom vizsgálatát vagy naplózását. A jogosulatlan internet-hozzáférés potenciálisan információhoz való illetéktelen hozzáféréshez vagy a biztonság más típusú megsértéséhez vezethet.



[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Ez a cikk bemutatja az Erőforrás-kezelő telepítési modelljével létrehozott virtuális hálózatok kényszerített bújtatásának konfigurálását. Kényszerített bújtatás konfigurálható a PowerShell használatával, nem a portálon keresztül. Ha a klasszikus telepítési modell kényszerített bújtatását szeretné konfigurálni, válassza ki a klasszikus cikket az alábbi legördülő listából:

> [!div class="op_single_selector"]
> * [PowerShell – Klasszikus](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="about-forced-tunneling"></a>Kényszerített bújtatás

Az alábbi ábra bemutatja, hogyan működik a kényszerített bújtatás. 

![Kényszerített bújtatás](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

A fenti példában az előtér-alhálózat nincs kényszerített bújtatás. Az előtér-alhálózat munkaterhelései továbbra is közvetlenül fogadhatják és válaszolhatják az ügyfelek kéréseit az internetről. A középső rétegés háttér-alhálózatok kényszerített bújtatás. A két alhálózatról az internetre irányuló kimenő kapcsolatokat a rendszer kényszeríti vagy visszairányítja egy helyszíni helyre az S2S VPN-alagutak egyikén keresztül.

Ez lehetővé teszi, hogy korlátozza és vizsgálja meg az internet-hozzáférést a virtuális gépekről vagy felhőszolgáltatásokból az Azure-ban, miközben továbbra is engedélyezi a többrétegű szolgáltatásarchitektúrát. Ha a virtuális hálózatokban nincsenek internetalapú munkaterhelések, a teljes virtuális hálózatokra kényszerített bújtatás is alkalmazható.

## <a name="requirements-and-considerations"></a>Követelmények és megfontolások

Kényszerített bújtatás az Azure-ban van konfigurálva a virtuális hálózati felhasználó által meghatározott útvonalakon. A forgalom átirányítása egy helyszíni helyre az Azure VPN-átjáró alapértelmezett útvonalaként van kifejezve. A felhasználó által definiált útválasztásról és a virtuális hálózatokról a [Felhasználó által definiált útvonalak és AZ IP-továbbítás](../virtual-network/virtual-networks-udr-overview.md)című témakörben talál további információt.

* Minden virtuális hálózati alhálózat rendelkezik egy beépített rendszer-útválasztási táblával. A rendszerútválasztási tábla a következő három útvonalcsoportot sorasztja:
  
  * **Helyi virtuális hálózat útvonalai:** Közvetlenül a cél virtuális gépek ugyanabban a virtuális hálózatban.
  * **Helyszíni útvonalak:** Az Azure VPN-átjáróhoz.
  * **Alapértelmezett útvonal:** Közvetlenül az internetre. Az előző két útvonal által nem lefedett privát IP-címekre szánt csomagokat a rendszer elejti.
* Ez az eljárás a felhasználó által definiált útvonalak (UDR) segítségével hoz létre egy útválasztási táblát egy alapértelmezett útvonal hozzáadásához, majd társítja az útválasztási táblát a virtuális hálózat alhálózata(i)hoz, hogy lehetővé tegye a kényszerített bújtatást ezeken az alhálózatokon.
* A kényszerített bújtatást olyan virtuális hálózathoz kell társossá tenni, amely útvonalalapú VPN-átjáróval rendelkezik. Be kell állítania egy "alapértelmezett helyet" a virtuális hálózathoz kapcsolódó helyi telephelyek között. Emellett a helyszíni VPN-eszközt a 0.0.0.0/0-s forgalomválasztóként történő használatával kell konfigurálni. 
* ExpressRoute kényszerített bújtatás nem konfigurált ezzel a mechanizmussal, hanem engedélyezve van a reklám egy alapértelmezett útvonal at ExpressRoute BGP társviszony-létesítési munkamenetek. További információt az [ExpressRoute dokumentációjában talál.](https://azure.microsoft.com/documentation/services/expressroute/)

## <a name="configuration-overview"></a>Konfiguráció áttekintése

Az alábbi eljárás segít egy erőforráscsoport és egy virtuális hálózat létrehozásában. Ezután hozzon létre egy VPN-átjárót, és konfigurálja a kényszerített bújtatást. Ebben az eljárásban a "MultiTier-VNet" virtuális hálózat három alhálózattal rendelkezik: "Előtér", "Midtier" és "Háttérrendszer", négy telephelyközi kapcsolattal: "DefaultSiteHQ" és három ág.

Az eljárás lépései a "DefaultSiteHQ" beállítást a kényszerített bújtatás alapértelmezett helykapcsolataként határozzák meg, és konfigurálják a "Midtier" és a "Háttérhálózat" alhálózatokat a kényszerített bújtatás használatára.

## <a name="before-you-begin"></a><a name="before"></a>Mielőtt elkezdené

Telepítse az Azure Resource Manager PowerShell-parancsmagjainak legújabb verzióját. A PowerShell-parancsmagok telepítésével kapcsolatban további információ: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).

> [!IMPORTANT]
> A PowerShell-parancsmagok legújabb verziójának telepítése szükséges. Ellenkező esetben előfordulhat, hogy a parancsmagok futtatásakor ellenőrzési hibák at.
>
>

### <a name="to-log-in"></a>Bejelentkezés

[!INCLUDE [To log in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="configure-forced-tunneling"></a>Kényszerített bújtatás konfigurálása

> [!NOTE]
> A következő figyelmeztetések jelenhetnek meg: "A parancsmag kimeneti objektumtípusa egy későbbi kiadásban módosul". Ez a várt viselkedés, és nyugodtan figyelmen kívül hagyhatja ezeket a figyelmeztetéseket.
>
>


1. Hozzon létre egy erőforráscsoportot.

   ```powershell
   New-AzResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
   ```
2. Hozzon létre egy virtuális hálózatot, és adja meg az alhálózatokat.

   ```powershell 
   $s1 = New-AzVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
   $s2 = New-AzVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
   $s3 = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
   $s4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
   $vnet = New-AzVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
   ```
3. Hozza létre a helyi hálózati átjárókat.

   ```powershell
   $lng1 = New-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
   $lng2 = New-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
   $lng3 = New-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
   $lng4 = New-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
   ```
4. Hozza létre az útvonaltáblát és az útvonalszabályt.

   ```powershell
   New-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
   $rt = Get-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
   Add-AzRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
   Set-AzRouteTable -RouteTable $rt
   ```
5. Társítsa az útvonaltáblát a Midtier és a Backend alhálózatokhoz.

   ```powershell
   $vnet = Get-AzVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
   Set-AzVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Hozza létre a virtuális hálózati átjárót. Ez a lépés némi időt vesz igénybe, néha 45 perc vagy több, mert létrehozza és konfigurálja az átjárót. Ha a GatewaySKU-értékkel kapcsolatos ValidateSet hibák jelennek meg, ellenőrizze, hogy [telepítette-e a PowerShell-parancsmagok legújabb verzióját.](#before) A PowerShell-parancsmagok legújabb verziója tartalmazza a legújabb átjáró termékváltozatának új érvényesített értékeit.

   ```powershell
   $pip = New-AzPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
   $gwsubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $ipconfig = New-AzVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
   New-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
   ```
7. Rendeljen alapértelmezett helyet a virtuális hálózati átjáróhoz. A **-GatewayDefaultSite** a parancsmag paraméter, amely lehetővé teszi a kényszerített útválasztási konfiguráció működését, ezért ügyeljen arra, hogy ezt a beállítást megfelelően konfigurálja. 

   ```powershell
   $LocalGateway = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
   $VirtualGateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
   ```
8. A helyek közötti VPN-kapcsolatok létrehozása.

   ```powershell
   $gateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   $lng1 = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
   $lng2 = Get-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
   $lng3 = Get-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
   $lng4 = Get-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
   New-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
   Get-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
   ```
