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
ms.openlocfilehash: 071db2d9aeda2373c85ae62c47bbef175dcb7678
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58483403"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Egy virtuális hálózat csatlakoztatása nagyméretű HANA-példányok

Miután létrehozott egy Azure virtuális hálózatra, a nagyméretű példányok az Azure ezt a hálózatot csatlakozhatnak az SAP Hana-hoz. Hozzon létre egy Azure ExpressRoute-átjárót a virtuális hálózaton. Ez az átjáró lehetővé teszi, hogy a virtuális hálózat, amely csatlakozik az ügyfélbérlőn, a nagyméretű szolgáltatáspéldányban az ExpressRoute-kapcsolatcsoporthoz.

> [!NOTE] 
> Ez a lépés akár 30 percet is igénybe vehet. Az új átjáró a kijelölt Azure-előfizetésében létrehozott, és ezután a megadott Azure virtuális hálózathoz csatlakozik.

Ha már létezik egy átjárót, ellenőrizze, hogy azt egy ExpressRoute-átjáróval vagy sem. Ha nem, törölje az átjárót, és újra hozza létre egy ExpressRoute-átjárót. Ha már létrejött egy ExpressRoute-átjárót, tekintse meg a következő szakaszban Ez a cikk "Hivatkozás virtuális hálózatokhoz." 

- Használja a [az Azure portal](https://portal.azure.com/) vagy a PowerShell használatával egy ExpressRoute-VPN-átjáró létrehozása a virtuális hálózathoz csatlakozik.
  - Ha használja az Azure Portalon, vegyen fel egy új **virtuális hálózati átjáró**, majd válassza ki **ExpressRoute** , az átjáró típusa.
  - Ha a PowerShell segítségével, először töltse le és használja a legújabb [Azure PowerShell SDK](https://azure.microsoft.com/downloads/). A következő parancsok hozzon létre egy ExpressRoute-átjárót. A szövegek utasításnak egy _$_ vannak a felhasználó által definiált változókat, amelyek információkat frissíteni kell.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

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

Ebben a példában a nagy teljesítményű átjárók Termékváltozatainak lett megadva. A beállítások, az SAP HANA az Azure-ban (nagyméretű példányok) által támogatott egyetlen átjáró-termékváltozatok: az HighPerformance vagy UltraPerformance.

> [!IMPORTANT]
> HANA nagyméretű példányok az II. típusú osztály Termékváltozat az UltraPerformance átjáró-Termékváltozatot kell használnia.

## <a name="link-virtual-networks"></a>Hivatkozás virtuális hálózatok

Az Azure virtuális hálózat most már rendelkezik egy ExpressRoute-átjárót. A Microsoft által biztosított engedélyezési adatok használatával kapcsolódhat az ExpressRoute-átjárót, az SAP HANA az Azure-ban (nagyméretű példányok) ExpressRoute-kapcsolatcsoportot. Csatlakoztathatja az Azure portal vagy a PowerShell használatával. A portál használata ajánlott, de ha azt szeretné, ha a PowerShell segítségével, az utasításokat a következők. 

Futtassa a következő parancsokat minden egyes virtuális hálózati átjáró egy másik AuthGUID minden kapcsolat használatával. Az első két bejegyzés az alábbi szkriptben látható a Microsoft által biztosított adatokat származnak. Emellett a AuthGUID a jellemző minden virtuális hálózat és az átjáróhoz. Ha szeretne egy másik Azure virtuális hálózat hozzáadása, kell egy másik AuthID lekérése az ExpressRoute-kapcsolatcsoport, amely kapcsolódik a HANA nagyméretű példányok az Azure-bA. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
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

Csatlakozhat az átjáró egynél több ExpressRoute-kapcsolatcsoporthoz az előfizetéséhez tartozó, szüksége lehet többször futtassa ezt a lépést. Ha például valószínűleg fog, amely a virtuális hálózat csatlakozik a helyszíni hálózat az ExpressRoute-kapcsolatcsoport a ugyanazon virtuális hálózati átjáró csatlakoztatása.

## <a name="next-steps"></a>További lépések

- [HLI további hálózati követelményei](hana-additional-network-requirements.md)