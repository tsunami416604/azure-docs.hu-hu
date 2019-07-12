---
title: Kapcsolat beállítása a virtuális hálózatról az Azure-ban (nagyméretű példányok) SAP Hana-hoz |} A Microsoft Docs
description: Kapcsolat beállítása a virtuális hálózat használatához az SAP HANA az Azure-ban (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ebe303b24c497fe8ac52ac90a236a23c279ea2e9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709678"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Egy virtuális hálózat csatlakoztatása nagyméretű HANA-példányok

Miután létrehozott egy Azure virtuális hálózatra, a nagyméretű példányok az Azure ezt a hálózatot csatlakozhatnak az SAP Hana-hoz. Hozzon létre egy Azure ExpressRoute-átjárót a virtuális hálózaton. Az átjáró lehetővé teszi, hogy a virtuális hálózat az ExpressRoute-kapcsolatcsoporthoz, amely csatlakozik az ügyfélbérlőn, a HANA nagyméretű szolgáltatáspéldányban.

> [!NOTE] 
> Ez a lépés akár 30 percet is igénybe vehet. Az új átjáró a kijelölt Azure-előfizetésében létrehozott, és ezután a megadott Azure virtuális hálózathoz csatlakozik.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Ha már létezik egy átjárót, ellenőrizze, hogy azt egy ExpressRoute-átjáróval vagy sem. Ha nem az ExpressRoute-átjárót, törölje az átjárót, és újra hozza létre egy ExpressRoute-átjárót. Ha már létrejött egy ExpressRoute-átjárót, tekintse meg a következő szakaszban Ez a cikk "Hivatkozás virtuális hálózatokhoz." 

- Használja a [az Azure portal](https://portal.azure.com/) vagy a PowerShell használatával egy ExpressRoute-VPN-átjáró létrehozása a virtuális hálózathoz csatlakozik.
  - Ha használja az Azure Portalon, vegyen fel egy új **virtuális hálózati átjáró**, majd válassza ki **ExpressRoute** , az átjáró típusa.
  - Ha a PowerShell segítségével, először töltse le és használja a legújabb [Azure PowerShell SDK](https://azure.microsoft.com/downloads/). 
 
A következő parancsok hozzon létre egy ExpressRoute-átjárót. A szövegek utasításnak egy _$_ olyan felhasználói változók, információkat kell frissíteni.

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

Ebben a példában a nagy teljesítményű átjárók Termékváltozatainak lett megadva. A beállítások, az SAP HANA az Azure-ban (nagyméretű példányok) által támogatott egyetlen átjáró-termékváltozatok: az HighPerformance vagy UltraPerformance.

> [!IMPORTANT]
> HANA nagyméretű példányok az II. típusú osztály Termékváltozat az UltraPerformance átjáró-Termékváltozatot kell használnia.

## <a name="link-virtual-networks"></a>Hivatkozás virtuális hálózatok

Az Azure virtuális hálózat most már rendelkezik egy ExpressRoute-átjárót. A Microsoft által biztosított engedélyezési adatok használatával kapcsolódhat az ExpressRoute-átjárót, az SAP HANA nagyméretű példányok az ExpressRoute-kapcsolatcsoporthoz. Csatlakoztathatja az Azure portal vagy a PowerShell használatával. A PowerShell-utasításokat az alábbiak szerint. 

Futtassa a következő parancsokat minden egyes ExpressRoute-átjáró egy másik AuthGUID minden kapcsolat használatával. Az első két bejegyzés az alábbi szkriptben látható a Microsoft által biztosított adatokat származnak. Emellett a AuthGUID a jellemző minden virtuális hálózat és az átjáróhoz. Ha szeretne hozzáadni egy másik Azure virtuális hálózat, az ExpressRoute-kapcsolatcsoport, amely kapcsolódik a HANA nagyméretű példányok az Azure-bA a Microsoft egy másik AuthID lekérése szeretné. 

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
> Az utolsó paraméter a New-AzVirtualNetworkGatewayConnection, parancsban **ExpressRouteGatewayBypass** paraméter, amely lehetővé teszi, hogy az ExpressRoute gyors elérési útja. Ez a funkció csökkenti a hálózati késést a nagyméretű HANA-példány egységek és az Azure virtuális gépek között. A funkció a 2019. május van hozzá. További részletekért ellenőrizze a cikk [SAP HANA (nagyméretű példányok) hálózati architektúra](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture). Győződjön meg arról, hogy futtatja a parancsok futtatása előtt a PowerShell-parancsmagok legújabb verzióját.

Csatlakozhat az átjáró egynél több ExpressRoute-kapcsolatcsoporthoz az előfizetéséhez tartozó, szüksége lehet többször futtassa ezt a lépést. Ha például valószínűleg fog, amely a virtuális hálózat csatlakozik a helyszíni hálózat az ExpressRoute-kapcsolatcsoport a ugyanazon virtuális hálózati átjáró csatlakoztatása.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Az ExpressRoute gyors elérési út alkalmazására meglévő HANA nagyméretű példány ExpressRoute-Kapcsolatcsoportok
Eddig a dokumentációt, amely az Azure ExpressRoute-átjárónak, az Azure virtuális hálózatok egyike egy nagyméretű HANA-példány központi telepítéshez létrehozott ExpressRoute-kapcsolatcsoporttal csatlakozhat magyarázta el. Azonban számos ügyfél már az ExpressRoute-Kapcsolatcsoportok telepítés után már, és saját virtuális hálózataikon csatlakozott HANA nagyméretű példányok már. A hálózati késés csökkenthető az új ExpressRoute gyors elérési utat, javasoljuk, hogy a változtatás a funkció használatához. A parancsok ExpreesRoute új kapcsolatcsoport csatlakozni, valamint módosíthatja egy meglévő ExpressRoute-kapcsolatcsoport azonosak. Ennek eredményeképpen kell futtatnia a használandó meglévő kapcsolatcsoport módosítása a PowerShell-parancsok sorozatát 

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

Fontos, az utolsó paraméter hozzáadása, az ExpressRoute gyors elérési út funkcióinak fent látható


## <a name="expressroute-global-reach"></a>Az ExpressRoute globális elérhetőséggel
Globális elérhetőségű engedélyezése egyikét vagy mindkettőt a két esetben szeretne:

 - HANA-Rendszerreplikálást további proxyk és tűzfalak nélkül
 - Hajtsa végre a rendszer másolja vagy a rendszer frissíti a két különböző régiókban lévő nagyméretű HANA-példány egység közötti másolását biztonsági mentések

meg kell vegye figyelembe, hogy:

- Meg kell adnia egy/29 terület címtartománnyal címtér. Hogy címtartomány előfordulhat, hogy ne legyen átfedésben azokkal a további címtartományt, amelyet használt eddig csatlakozás HANA nagyméretű példányok az Azure-ba, és nem lehet átfedés azokkal az IP-címtartományok, akkor valahol máshol Azure-ban vagy a helyszínen használt.
- Nincs meg korlátozást a ASN (autonóm rendszer száma), a helyszíni felé haladó útvonalak meghirdetéséhez nagyméretű HANA-példányokhoz használható. A helyszíni kell ne hirdesse meg a privát ASN-eket 65000 – 65020 tartományán az összes olyan esetleges útvonalat, vagy a 65515. 
- A közvetlen hozzáférést a helyszíni csatlakozás HANA nagyméretű példányokhoz forgatókönyvhöz a Díjszámítás a kapcsolatcsoport, amely csatlakoztatja az Azure-bA kell. Az árak, ellenőrizze a árai [globális elérni bővítmény](https://azure.microsoft.com/pricing/details/expressroute/).

Egyikét vagy mindkettőt a alkalmazni az üzemelő példány forgatókönyvek beszerzéséhez nyissa meg a támogatási üzenetet az Azure-ral leírtak szerint [nyisson egy támogatási kérelmet a nagyméretű HANA-példányok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)

Adatokhoz, és a kulcsszavak, amelyekre szüksége van a Microsoft használatával irányíthatja, és hajtsa végre a kérést a következőhöz hasonló:

- Szolgáltatás: Nagyméretű SAP HANA-példány
- Probléma típusa: Konfigurálás és beállítás
- A probléma altípus: Olyan problémát tapasztaltam, amely nem szerepel a listán
- "A hálózat módosítása – globális elérhetőségű hozzáadása" tárgy
- Részletek: "Hozzá globális elérhetőségű nagyméretű HANA-példány nagyméretű HANA-példány-bérlőhöz vagy" globális elérhetőségű hozzáadása helyi nagyméretű HANA-példány bérlőhöz.
- A nagyméretű HANA-példány nagyméretű HANA-példány bérlői esethez tartozó további részletek: Meg kell határoznia a **két Azure-régió** hol találhatók a két bérlők kapcsolódni **és** küldheti be a   **/29 IP-címtartomány**
- További részletek a helyszíni nagyméretű HANA-példány bérlői esethez: Meg kell határoznia a **Azure-régió** a nagyméretű HANA-példány bérlő üzemel, ahol szeretné-e közvetlenül kapcsolódni. Emellett meg kell adnia a **Auth GUID** és **Circuit ID-társ** kapott, a helyszíni és az Azure között ExpressRoute-kapcsolatcsoport létrejöttekor. Ezenkívül szüksége nevezze el a **ASN**. Az utolsó termékhez van egy **/29 IP-címtartomány** az ExpressRoute globális elérhetőségű.

> [!NOTE]
> Mindkét esetben kezelni szeretne használni, ha meg kell adnia két különböző/29 IP-címtartományok ne legyenek átfedésben bármely más IP-címtartomány eddig használt. 




## <a name="next-steps"></a>További lépések

- [HLI további hálózati követelményei](hana-additional-network-requirements.md)
