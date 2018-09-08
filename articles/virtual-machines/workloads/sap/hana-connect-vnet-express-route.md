---
title: SAP Hana-hoz az Azure-ban (nagyméretű példányok) a virtuális hálózati kapcsolatok konfigurálása |} A Microsoft Docs
description: Kapcsolatok konfigurálása virtuális hálózat használatához az SAP HANA az Azure-ban (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5efdda485e4e1f5013948c6636b267f0d388f4d5
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164717"
---
# <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>Virtuális hálózat csatlakoztatása HANA nagyméretű példányok az ExpressRoute

Meghatározott összes IP-címtartományokat, és most már rendelkezik az adatok vissza a Microsoft, megkezdése előtt létrehozott virtuális hálózat csatlakozik a nagyméretű HANA-példányokhoz. Az Azure virtuális hálózat létrehozása után a virtuális hálózat az ExpressRoute-kapcsolatcsoport, amely csatlakozik az ügyfélbérlőn, a nagyméretű szolgáltatáspéldányban a virtuális hálózat összekapcsolása egy ExpressRoute-átjárót kell létrehoznia.

> [!NOTE] 
> Ez a lépés végrehajtásához, az új átjáró be van-e a kijelölt Azure-előfizetésében létrehozott, és ezután csatlakozik a megadott Azure virtuális hálózat akár 30 percet is igénybe vehet.

Ha már létezik egy átjárót, ellenőrizze, hogy azt egy ExpressRoute-átjáróval vagy sem. Ha nem, akkor kell törölte és újra létre kell hozni egy ExpressRoute-átjárót, az átjáró. Ha egy ExpressRoute-átjárót már létrejött, mivel az Azure virtuális hálózat már csatlakoztatva van a helyszíni kapcsolat ExpressRoute-kapcsolatcsoporthoz, folytassa a virtuális hálózatok összekapcsolása az alábbi szakaszban.

- Használata vagy a (új) [az Azure portal](https://portal.azure.com/), vagy a PowerShell használatával egy ExpressRoute-VPN-átjáró létrehozása a virtuális hálózathoz csatlakozik.
  - Ha használja az Azure Portalon, vegyen fel egy új **virtuális hálózati átjáró** majd **ExpressRoute** , az átjáró típusa.
  - Ha ehelyett PowerShell, először töltse le és használja a legújabb [Azure PowerShell SDK](https://azure.microsoft.com/downloads/) annak biztosítása érdekében az optimális működés érdekében. A következő parancsok hozzon létre egy ExpressRoute-átjárót. A szövegek utasításnak egy _$_ is frissíteni kell az információkat a felhasználó által definiált változókat.

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

Ebben a példában a nagy teljesítményű átjárók Termékváltozatainak lett megadva. A lehetőségek a következők HighPerformance vagy UltraPerformance csak átjáró SKU-k az SAP Hana az Azure-ban (nagyméretű példányok) által támogatott.

> [!IMPORTANT]
> A HANA nagyméretű példányok az II. típusú classs Termékváltozat a használatát az UltraPerformance átjáró-Termékváltozat megadása kötelező.

**Virtuális hálózatok összekapcsolása**

Most, hogy az Azure virtuális hálózat egy ExpressRoute-átjáróval rendelkezik, a Microsoft által biztosított engedélyezési adatok használatával az ExpressRoute-átjárót, az SAP Hana-val létrehozott a kapcsolathoz (nagyméretű példányok) az Azure ExpressRoute-kapcsolatcsoport a csatlakozáshoz. Ebben a lépésben az Azure portal vagy a PowerShell használatával elvégezhető. A portál használata javasolt, de PowerShell-utasításokat az alábbiak szerint. 

- Akkor hajtsa végre a következő parancsokat minden egyes virtuális hálózati átjáró használatával egy másik AuthGUID minden kapcsolat. Az első két bejegyzés jelenik meg a következő szkriptet a Microsoft által biztosított adatokat származnak. Emellett a AuthGUID a jellemző minden virtuális hálózat és az átjáróhoz. Azt jelenti, ha szeretne hozzáadni egy másik Azure virtuális hálózat, le szeretné kérni egy másik AuthID az ExpressRoute-kapcsolatcsoport, amely kapcsolódik a HANA nagyméretű példányok az Azure-bA. 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Ha szeretné összekapcsolni az átjárót több ExpressRoute-kapcsolatcsoporttal az előfizetéséhez tartozó, szükség lehet többször végrehajtani ezt a lépést. Ha például valószínűleg fog, amely a virtuális hálózathoz kapcsolódik a helyszíni hálózat az ExpressRoute-kapcsolatcsoport a ugyanazon virtuális hálózati átjáró csatlakoztatása.

**Következő lépések**

- Tekintse meg [HLI további hálózati követelményei](hana-additional-network-requirements.md).