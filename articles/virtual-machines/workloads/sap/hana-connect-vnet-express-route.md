---
title: A virtuális hálózatról az SAP HANA-ra beállított kapcsolat az Azure-ban (nagy példányok) | Microsoft dokumentumok
description: A virtuális hálózatból beállított kapcsolat az SAP HANA azure-beli (nagy példányok) használatára.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f7ac8e69c4e149fdd0f365e19f7a0282a547af43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617185"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Virtuális hálózat csatlakoztatása hana nagy példányokhoz

Miután létrehozott egy Azure virtuális hálózatot, csatlakoztathatja a hálózatot az SAP HANA-hoz az Azure nagy példányain. Hozzon létre egy Azure ExpressRoute-átjárót a virtuális hálózaton. Ez az átjáró lehetővé teszi, hogy a virtuális hálózat ot az ExpressRoute-kapcsolathoz kapcsolja, amely a HANA nagy példány bélyegzőjének ügyfél-bérlőjéhez csatlakozik.

> [!NOTE] 
> Ez a lépés akár 30 percet is igénybe vehet. Az új átjáró jön létre a kijelölt Azure-előfizetésben, majd csatlakozik a megadott Azure virtuális hálózathoz.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Ha már létezik átjáró, ellenőrizze, hogy ExpressRoute-átjáró-e vagy sem. Ha nem ExpressRoute-átjáró, törölje az átjárót, és hozza létre újra ExpressRoute-átjáróként. Ha már létrejött egy ExpressRoute-átjáró, olvassa el a cikk következő, "Virtuális hálózatok összekapcsolása" című szakaszát. 

- Az [Azure Portalon](https://portal.azure.com/) vagy a PowerShellben hozzon létre egy, a virtuális hálózathoz csatlakoztatott ExpressRoute VPN-átjárót.
  - Ha az Azure Portalon, adjon hozzá egy új **virtuális hálózati átjárót**, majd válassza **az ExpressRoute** átjárótípust.
  - Ha PowerShellt használ, először töltse le és használja a legújabb [Azure PowerShell SDK-t.](https://azure.microsoft.com/downloads/) 
 
A következő parancsok ExpressRoute-átjárót hoznak létre. Az egy által _$_ előzze meg a szövegeket a felhasználó által definiált változók, amelyeket frissíteni kell az Ön konkrét adataival.

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
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

Ebben a példában a HighPerformance átjáró termékváltozat ot használták. A lehetőségek highperformance vagy UltraPerformance, mint az egyetlen átjáró sk, amely támogatja az SAP HANA az Azure-ban (nagy példányok).

> [!IMPORTANT]
> A Type II osztály termékváltozatának HANA nagy példányai esetén az UltraPerformance Gateway Termékváltozatot kell használnia.

## <a name="link-virtual-networks"></a>Virtuális hálózatok összekapcsolása

Az Azure virtuális hálózat most már rendelkezik egy ExpressRoute-átjáróval. A Microsoft által megadott engedélyezési adatok segítségével csatlakoztassa az ExpressRoute átjárót az SAP HANA nagypéldányok ExpressRoute-áramkörhöz. Az Azure Portalon vagy a PowerShellen keresztül csatlakozhat. A PowerShell-utasítások a következők. 

Futtassa a következő parancsokat minden ExpressRoute-átjáróhoz egy másik AuthGUID használatával minden kapcsolathoz. A következő parancsfájlban látható első két bejegyzés a Microsoft által megadott információkból származik. Emellett az AuthGUID minden virtuális hálózatra és átjárójára vonatkozik. Ha szeretne hozzáadni egy másik Azure virtuális hálózatot, be kell szereznie egy másik AuthID az ExpressRoute-kapcsolat, amely összeköti a HANA nagy példányok az Azure-ban a Microsofttól. 

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
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> A New-AzVirtualNetworkGatewayConnection parancs utolsó paramétere, az **ExpressRouteGatewayBypass** egy új paraméter, amely lehetővé teszi az ExpressRoute gyorselérési utat. Olyan funkció, amely csökkenti a hálózati késést a HANA nagy példányegységek és az Azure virtuális gépek között. A funkció 2019 májusában került hozzáadásra. További részletekért tekintse meg az [SAP HANA (Nagy példányok) hálózati architektúráját.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture) Győződjön meg arról, hogy a parancsok futtatása előtt a PowerShell-parancsmagok legújabb verzióját futtatja.

Ha az átjárót az előfizetéséhez társított egynél több ExpressRoute-kapcsolathoz szeretné csatlakoztatni, előfordulhat, hogy ezt a lépést többször is futtatnia kell. Például valószínűleg ugyanazt a virtuális hálózati átjárót fogja csatlakoztatni az ExpressRoute-kapcsolathoz, amely a virtuális hálózatot a helyszíni hálózathoz csatlakoztatja.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>ExpressRoute gyorselérési út alkalmazása meglévő HANA large instance ExpressRoute-áramkörökre
A dokumentáció eddig elmagyarázta, hogyan csatlakoztathat egy új ExpressRoute-áramkört, amely hana nagy példány központi telepítésével jött létre az egyik Azure virtuális hálózat Azure ExpressRoute-átjárójához. De sok ügyfél már rendelkezik az ExpressRoute-áramkörök beállításmár, és a virtuális hálózatok csatlakozik hana nagy példányok már. Mivel az új ExpressRoute gyors elérési út csökkenti a hálózati késést, ajánlott a módosítást alkalmazni a funkció használatára. Az új ExpreesRoute-kapcsolat csatlakoztatásához és egy meglévő ExpressRoute-áramkör módosításához szükséges parancsok megegyeznek. Ennek eredményeképpen futtatnia kell ezt a PowerShell-parancsoksorozatot egy meglévő áramkör 

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
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

Fontos, hogy az ExpressRoute gyorselérési út funkció engedélyezéséhez adja hozzá a fenti módon megjelenített utolsó paramétert.


## <a name="expressroute-global-reach"></a>Az ExpressRoute Global Reach
Mivel engedélyezni szeretné a Globális elérést a két forgatókönyv egyikénvagy mindkettőben:

 - HANA rendszerreplikáció további proxyk vagy tűzfalak nélkül
 - Biztonsági másolatok másolása a HANA nagy példányegységei között két különböző régióban a rendszermásolatok vagy rendszerfrissítések végrehajtásához

meg kell vizsgálni, hogy:

- Meg kell adnia egy /29 címtérből álló címterületet. Előfordulhat, hogy ez a címtartomány nem fedi át a hana nagypéldányok Azure-hoz való csatlakoztatása eddig használt többi címtér-tartományt, és nem fedi át az Azure-ban vagy a helyszíni környezetben máshol használt IP-címtartományokkal.
- Az ASN-ek (autonóm rendszerszám) korlátozása, amely a HANA nagy példányok helyszíni útvonalainak hirdetésére használható. A helyszíni járatok nem hirdethetnek 65000–65020 vagy 65515 közötti privát ASN-ekkel rendelkező útvonalakat. 
- A forgatókönyv a hana nagy példányokhoz való közvetlen hozzáférés csatlakoztatása esetén ki kell számítania az Azure-hoz csatlakozó áramkör díját. Az árak, ellenőrizze az árakat a [Global Reach Add-On](https://azure.microsoft.com/pricing/details/expressroute/).

Ha az egyik vagy mindkét forgatókönyvet a központi telepítésre szeretné alkalmazni, nyisson meg egy támogatási üzenetet az Azure-ral a [HANA nagy példányok támogatási kérelemének megnyitása](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances) című részében leírtak szerint.

A szükséges adatok és a Microsoft számára a kérésre történő továbbirányításhoz és végrehajtáshoz használandó kulcsszavak a következőképpen néznek ki:

- Szolgáltatás: SAP HANA nagy példány
- Probléma típusa: Konfiguráció és telepítés
- Probléma altípusa: A probléma nem szerepel a fenti felsorolásban
- Tárgy " A hálózat módosítása - globális elérés hozzáadása"
- Részletek: "Globális elérés hozzáadása hana nagy példány hana nagy példány bérlőhöz vagy "Globális elérés hozzáadása a helyszíni HANA nagy példány bérlőjéhez.
- További részletek a HANA nagy példány hana nagy példány bérlői eset: Meg kell határoznia a **két Azure-régióban,** ahol a két bérlő csatlakozni, **és** be kell küldenie a **/29 IP-címtartomány**
- A helyszíni hana nagypéldány-bérlői eset: meg kell határoznia az **Azure-régió,** ahol a HANA nagy példány bérlő települ, amelyhez közvetlenül csatlakozni szeretne. Emellett meg kell adnia az **auth GUID** és **a Circuit Peer-azonosító,** amely akkor kapott, amikor létrehozta az ExpressRoute-kapcsolat létrehozása a helyszíni és az Azure között. Ezenkívül meg kell neveznie az **ASN-t.** Az utolsó termék az ExpressRoute Globális **elérés /29 IP-címtartománya.**

> [!NOTE]
> Ha mindkét esetet kezelni szeretné, két különböző /29 IP-címtartományt kell megadnia, amelyek nem fedik át az eddig használt más IP-címtartományt. 




## <a name="next-steps"></a>További lépések

- [A HLI-re vonatkozó további hálózati követelmények](hana-additional-network-requirements.md)
