---
title: 'Az Azure Site-to-Site-kapcsolatok kényszerített bújtatás konfigurálása: Resource Manager |} A Microsoft Docs'
description: Hogyan lehet irányítani, vagy "kényszerítéséhez" internetre irányuló összes forgalmat a helyszíni helyre.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38308152"
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Kényszerített bújtatás konfigurálása az Azure Resource Manager-alapú üzemi modellel

Kényszerített bújtatás lehetővé teszi az átirányítási vagy "kényszerített" internetre irányuló összes forgalmat a helyszíni helyre biztonsági és vizsgálati és naplózási Site-to-Site VPN-alagúton keresztül. Ez a legtöbb vállalati informatikai kritikus fontosságú biztonsági követelményeket a szabályzatokat. Nem kényszerített bújtatás, internetre irányuló forgalmat a virtuális gépekről az Azure-ban mindig csatlakozik az internethez, ezáltal lehetővé teszi a forgalmat, vagy vizsgálja meg a beállítás nélkül az Azure hálózati infrastruktúráról közvetlenül meg traverses. Jogosulatlan Internet-hozzáférés potenciálisan vezethet információfelfedés vagy más biztonsági résekkel szemben.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Ez a cikk konfigurálásán vezeti végig kényszerített bújtatás a Resource Manager-alapú üzemi modellel létrehozott virtuális hálózatok esetében. Kényszerített bújtatás PowerShell-lel, nem a portálon keresztül konfigurálható. Ha azt szeretné, a klasszikus üzemi modell esetében a kényszerített bújtatás konfigurálása, az alábbi legördülő listából válassza a klasszikus cikk:

> [!div class="op_single_selector"]
> * [PowerShell – Klasszikus](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="about-forced-tunneling"></a>Információk a kényszerített bújtatásról

A következő ábra szemlélteti, hogyan kényszerített bújtatás működik. 

![Alagúthasználat kényszerítése](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

A fenti példában az előtérbeli alhálózat nem kényszerítetten bújtatott. Az előtérben levő alhálózathoz számítási feladatokhoz továbbra is fogadni és válaszol ügyfelek az internetről közvetlenül. A középső rétegbeli és a háttérbeli alhálózat kényszerítve bújtatott. Az internethez az alábbi két alhálózat bármely kimenő kapcsolatok kényszerített vagy átirányítva az S2S VPN-alagúton keresztül egy helyszíni helyhez lesz.

Ez lehetővé teszi, hogy korlátozza és vizsgálja meg az Internet-hozzáférést a virtuális gépek vagy felhőszolgáltatások az Azure-ban, miközben továbbra is szükséges a többrétegű szolgáltatások architektúra engedélyezése. Ha a virtuális hálózatok nem internetkapcsolattal rendelkező számítási feladatok, is alkalmazhat a teljes virtuális hálózatok kényszerített bújtatás.

## <a name="requirements-and-considerations"></a>Követelmények és szempontok

Kényszerített bújtatás az Azure-beli virtuális hálózati felhasználó által megadott útvonalak keresztül van konfigurálva. Egy alapértelmezett útvonalat az Azure VPN gateway kifejezése egy helyszíni helyre irányítja a forgalmat. További információ a felhasználó által meghatározott útválasztást és a virtuális hálózatok: [felhasználó által megadott útvonalak és IP-továbbítás](../virtual-network/virtual-networks-udr-overview.md).

* Minden egyes virtuális hálózat alhálózatához rendelkezik egy beépített, rendszer-útválasztási táblázatához. A rendszer útválasztási tábla az útvonalak a következő három csoport rendelkezik:
  
  * **Helyi VNet-útvonal:** közvetlenül és a cél virtuális gépek ugyanazon a virtuális hálózaton.
  * **A helyszíni útvonalakat:** , az Azure VPN-átjáró.
  * **Alapértelmezett útvonal:** közvetlenül az internethez való. Nem fedi le az előző két útvonalak privát IP-címekre irányuló csomagokat a rendszer elveti.
* Ezzel az eljárással hozzon létre egy útválasztási táblázatot, adjon hozzá egy alapértelmezett útvonalat, és társíthatja az útválasztási táblázatban, felhasználó által megadott útvonalak (UDR) használatával a virtuális hálózat alhálózat(ok) ezekhez az alhálózatokhoz a kényszerített bújtatás engedélyezése.
* Kényszerített bújtatás kell rendelni egy virtuális hálózattal, amely rendelkezik egy útvonalalapú VPN-átjárót. Meg kell állítania egy "alapértelmezett webhely" a létesítmények közötti helyek között a virtuális hálózathoz csatlakozik. Emellett a helyszíni VPN-eszköznek kell konfigurálni forgalomválasztóinak 0.0.0.0/0 használatával. 
* Kényszerített bújtatás ExpressRoute nincs konfigurálva ez a mechanizmus keresztül, de ehelyett szerint engedélyezve van a társviszony-létesítési ExpressRoute BGP-munkamenetek használatával egy alapértelmezett útvonalat hirdet. További információkért lásd: a [az ExpressRoute dokumentációja](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Konfiguráció áttekintése

Az alábbi eljárás segítségével hozhat létre egy erőforráscsoportot és a egy virtuális hálózathoz. Ezután hozzon létre egy VPN-átjáró és a kényszerített bújtatás konfigurálása. Ebben az eljárásban a virtuális hálózat MultiTier – VNet három alhálózatot tartalmaz: "Előtér", "Midtier" és "Háttér", a negyedik létesítmények közötti kapcsolatok: "DefaultSiteHQ", és három ágat.

Az eljárás lépéseit kényszerített bújtatás a hely alapértelmezett kapcsolat, a "DefaultSiteHQ", és a "Midtier' állítja be, és a"Háttér"alhálózatok használata kényszerített bújtatás.

## <a name="before"></a>Előkészületek

Telepítse az Azure Resource Manager PowerShell-parancsmagjainak legújabb verzióját. A PowerShell-parancsmagok telepítésével kapcsolatban további információ: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).

> [!IMPORTANT]
> A PowerShell-parancsmagok legújabb verziójának telepítéséhez szükség. Ellenkező esetben ellenőrzési hibák léphetnek fel, néhány, a parancsmagok futtatásakor.
>
>

### <a name="to-log-in"></a>Jelentkezzen be

[!INCLUDE [To log in](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="configure-forced-tunneling"></a>Kényszerített bújtatás konfigurálása

> [!NOTE]
> Közli, hogy "Ez a parancsmag kimenete objektumtípusát módosul egy későbbi kiadásban" figyelmeztetés jelenhet meg. Ez az elvárt működés, és biztonságosan figyelmen kívül hagyhatja ezeket a figyelmeztetéseket.
>
>


1. Hozzon létre egy erőforráscsoportot.

  ```powershell
  New-AzureRmResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
  ```
2. Hozzon létre egy virtuális hálózatot, és adjon meg alhálózatokat.

  ```powershell 
  $s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
  $s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
  $s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
  $s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
  $vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
  ```
3. Hozza létre a helyi hálózati átjárókat.

  ```powershell
  $lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
  $lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
  $lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
  $lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
  ```
4. Az útvonaltábla és útvonal szabály létrehozásához.

  ```powershell
  New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
  $rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
  Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
  Set-AzureRmRouteTable -RouteTable $rt
  ```
5. A Midtier és háttérbeli alhálózataihoz az útválasztási táblázat hozzárendelése.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
  Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
  Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
6. A virtuális hálózati átjáró létrehozásához. Ebben a lépésben eltarthat egy ideig, egyes esetekben akár 45 percet, mert a létrehozandó és az átjáró konfigurálása. Ha hibába ütközik, ValidateSet GatewaySKU értéke kapcsolatban, győződjön meg arról, hogy telepítette a [a PowerShell-parancsmagok legújabb verzióját](#before). A PowerShell-parancsmagok legújabb verzióját a legújabb átjáró-termékváltozatok új ellenőrzött értékeket tartalmazza.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
  $gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
  New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
  ```
7. Rendelje hozzá egy alapértelmezett hely a virtuális hálózati átjárót. A **- GatewayDefaultSite** a parancsmag paraméter, amely lehetővé teszi, hogy működjenek, ezért ügyeljen arra, hogy ezt a beállítást helyesen konfigurálása kényszerített útválasztási konfigurációja. 

  ```powershell
  $LocalGateway = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
  $VirtualGateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
  Set-AzureRmVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
  ```
8. A Site-to-Site VPN-kapcsolatok létesítéséhez.

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
