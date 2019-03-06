---
title: 'Oktatóanyag: Az Azure Firewall üzembe helyezése és konfigurálása hibrid hálózatban az Azure PowerShell használatával'
description: Ebben az oktatóanyagban megismerheti, hogyan telepítheti és konfigurálhatja az Azure PowerShell-lel az Azure-tűzfalon.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 1/30/2019
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 3a1edde2f51abbe60370eefee1b0c141f772c547
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57405462"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Oktatóanyag: Az Azure Firewall üzembe helyezése és konfigurálása hibrid hálózatban az Azure PowerShell használatával

A helyszíni hálózathoz való csatlakozáskor hibrid hálózat létrehozása az Azure virtuális hálózat, az Azure-hálózat erőforrásaihoz való hozzáférés szabályozásához el egy teljes biztonsági csomag fontos része.

Azure-tűzfal segítségével szabályozhatja a hálózati hozzáférést a szabályokat, amelyek meghatározzák az engedélyezett és letiltott hálózati forgalom használó hibrid hálózatot.

Ebben az oktatóanyagban három virtuális hálózatot hoz létre:

- **VNet-Hub** – Ez a virtuális hálózat van a tűzfal.
- **VNet-küllő** -küllő virtuális hálózat a számítási feladatok Azure-ban található jelöli.
- **VNet-rendszert** – a helyi virtuális hálózat egy helyszíni hálózat jelöli. -Tényleges telepítés megjeleníthet egy VPN vagy ExpressRoute-kapcsolat. Az egyszerűség kedvéért ez az oktatóanyag a VPN gateway-kapcsolatot használ, és az Azure-található virtuális hálózat egy helyszíni hálózat megjelenítésére szolgál.

![Tűzfal a hibrid hálózatban](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Változók deklarálása
> * A tűzfal-központ virtuális hálózat létrehozása
> * A küllő virtuális hálózat létrehozása
> * A helyszíni virtuális hálózat létrehozása
> * A tűzfal konfigurálása és üzembe helyezése
> * A VPN-átjárók létrehozása és csatlakoztatása
> * Az eseményközpont és a küllő virtuális hálózatok
> * Az útvonalak létrehozása
> * A virtuális gépek létrehozása
> * A tűzfal tesztelése

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban a PowerShell helyi futtatása szükséges. Rendelkeznie kell az Azure PowerShell-modul verzióját 6.12.0 vagy újabb verziója van telepítve. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket. A PowerShell-verzió ellenőrzése után futtassa az `Login-AzureRmAccount` parancsot az Azure-hoz való kapcsolódáshoz.

Három alapvető követelménynek kell teljesülnie, hogy ez a forgatókönyv megfelelően működjön:

- Egy felhasználó által megadott útvonal (UDR), amely az alapértelmezett átjáró Azure tűzfal IP-címre mutat küllő az alhálózaton. A BGP-útvonalpropagálásnak **letiltott** állapotúnak kell lennie ebben az útválasztási táblázatban.
- Egy a hub átjáró alhálózatán található UDR küllő-hálózatokhoz a következő ugrás a tűzfal IP-címre kell mutatnia.
- Nincs UDR módon, megtanulja az útvonalakat a BGP az Azure-tűzfal alhálózat szükséges.
- A VNet-Hub-tól a VNet-Spoke felé irányuló társviszony létesítésekor az **AllowGatewayTransit** beállítást, a VNet-Spoke-tól a VNet-Hub felé irányuló társviszony létesítésekor pedig a **UseRemoteGateways** beállítást adja meg.

Útvonalak létrehozása című ebben az oktatóanyagban, hogy hogyan jönnek létre az ezeket az útvonalakat.

>[!NOTE]
>Az Azure tűzfal közvetlen internetkapcsolattal kell rendelkeznie. Ha engedélyezte a kényszerített bújtatás a helyi ExpressRoute- vagy Alkalmazásátjáró-n keresztül, szeretné-e az UDR 0.0.0.0/0 konfigurálása a **NextHopType** értéket állítja be **Internet**, majd rendelje hozzá  **AzureFirewallSubnet**.

>[!NOTE]
>Közvetlenül társviszonyban lévő virtuális hálózatok közötti adatforgalmat közvetlenül akkor is, ha egy udr-t az alapértelmezett átjáróként Azure tűzfal mutat. Ebben a forgatókönyvben a tűzfalon az alhálózat alhálózati forgalom küldésére, egy UDR a cél alhálózat hálózati előtagot mindkét alhálózat a explicit módon kell tartalmaznia.

Tekintse át a kapcsolódó Azure PowerShell-referenciáinak dokumentációja, lásd: [Azure PowerShell-referencia](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="declare-the-variables"></a>Változók deklarálása

Az alábbi példa az oktatóanyaghoz használt értékekkel deklarálja a változókat. Bizonyos esetekben szükség lehet néhány értéket lecseréli a saját működéséhez az előfizetésében. Ha szükséges, módosítsa a változókat, majd másolja és illessze be őket a PowerShell-konzolra.

```azurepowershell
$RG1 = "FW-Hybrid-Test"
$Location1 = "East US"

# Variables for the firewall hub VNet

$VNetnameHub = "VNet-hub"
$SNnameHub = "AzureFirewallSubnet"
$VNetHubPrefix = "10.5.0.0/16"
$SNHubPrefix = "10.5.0.0/24"
$SNGWHubPrefix = "10.5.1.0/24"
$GWHubName = "GW-hub"
$GWHubpipName = "VNet-hub-GW-pip"
$GWIPconfNameHub = "GW-ipconf-hub"
$ConnectionNameHub = "hub-to-Onprem"

# Variables for the spoke virtual network

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the on-premises virtual network

$VNetnameOnprem = "Vnet-Onprem"
$SNNameOnprem = "SN-Corp"
$VNetOnpremPrefix = "192.168.0.0/16"
$SNOnpremPrefix = "192.168.1.0/24"
$SNGWOnpremPrefix = "192.168.2.0/24"
$GWOnpremName = "GW-Onprem"
$GWIPconfNameOnprem = "GW-ipconf-Onprem"
$ConnectionNameOnprem = "Onprem-to-hub"
$GWOnprempipName = "VNet-Onprem-GW-pip"

$SNnameGW = "GatewaySubnet"
```


## <a name="create-the-firewall-hub-virtual-network"></a>A tűzfal-központ virtuális hálózat létrehozása

Először hozzon létre ehhez az oktatóanyaghoz az erőforrásokat tartalmazó az erőforráscsoport:

```azurepowershell
  New-AzureRmResourceGroup -Name $RG1 -Location $Location1
  ```

Adja meg az alhálózatok a virtuális hálózatban kell megfelelniük:

```azurepowershell
$FWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

A tűzfal központi virtuális hálózaton hozza létre:

```azurepowershell
$VNetHub = New-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```

Kérjen egy nyilvános IP-cím kiosztását a virtuális hálózat számára létrehozni kívánt VPN-átjáróhoz. Látható, hogy az *AllocationMethod* értéke **Dynamic**. A használni kívánt IP-címet nem adhatja meg. Ennek kiosztása a VPN-átjáró számára dinamikusan történik. 

  ```azurepowershell
  $gwpip1 = New-AzureRmPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="create-the-spoke-virtual-network"></a>A küllő virtuális hálózat létrehozása

Adja meg az alhálózatot kell foglalni a küllő virtuális hálózat:

```azurepowershell
$Spokesub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

A küllő virtuális hálózat létrehozása:

```azurepowershell
$VNetSpoke = New-AzureRmVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="create-the-on-premises-virtual-network"></a>A helyszíni virtuális hálózat létrehozása

Adja meg az alhálózatok a virtuális hálózatban kell megfelelniük:

```azurepowershell
$Onpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

Hozza létre a helyszíni virtuális hálózat:

```azurepowershell
$VNetOnprem = New-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```

Kérjen egy nyilvános IP-cím kiosztását a virtuális hálózat számára létrehozni kívánt átjáróhoz. Látható, hogy az *AllocationMethod* értéke **Dynamic**. A használni kívánt IP-címet nem adhatja meg. Ennek kiosztása az átjáró számára dinamikusan történik. 

  ```azurepowershell
  $gwOnprempip = New-AzureRmPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="configure-and-deploy-the-firewall"></a>A tűzfal konfigurálása és üzembe helyezése

Most már a központi virtuális hálózaton üzembe helyezése a a tűzfalon.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzureRmPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzureRmFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>Hálózati szabályok konfigurálása

<!--- $Rule3 = New-AzureRmFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *--->

```azurepowershell
$Rule1 = New-AzureRmFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80

$Rule2 = New-AzureRmFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzureRmFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzureRmFirewall -AzureFirewall $Azfw
```

## <a name="create-and-connect-the-vpn-gateways"></a>A VPN-átjárók létrehozása és csatlakoztatása

A hub és a helyszíni virtuális hálózatok VPN-átjárókon keresztül vannak csatlakoztatva.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>A központi virtuális hálózaton a VPN-átjáró létrehozása

Hozza létre a VPN-átjáró konfigurációját. A VPN-átjáró konfigurációja határozza meg az alhálózatot és a használandó nyilvános IP-címet.

  ```azurepowershell
  $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

Most hozzon létre a VPN-átjáró számára a központi virtuális hálózaton. Hálózatok közötti konfigurációk paraméternek RouteBased értékűnek kell. A VPN-átjáró létrehozása akár 45 percet is igénybe vehet, az átjáró kiválasztott termékváltozatától függően.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>A helyszíni virtuális hálózat VPN-átjáró létrehozása

Hozza létre a VPN-átjáró konfigurációját. A VPN-átjáró konfigurációja határozza meg az alhálózatot és a használandó nyilvános IP-címet.

  ```azurepowershell
$vnet2 = Get-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

Most hozzon létre a VPN-átjáró a helyszíni virtuális hálózathoz. Hálózatok közötti konfigurációk paraméternek RouteBased értékűnek kell. A VPN-átjáró létrehozása akár 45 percet is igénybe vehet, az átjáró kiválasztott termékváltozatától függően.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-the-vpn-connections"></a>A VPN-kapcsolatok létrehozása

Most már létrehozhatja a hub és a helyszíni átjárók közötti VPN-kapcsolatok

#### <a name="get-the-vpn-gateways"></a>A VPN-átjárók lekérése

```azurepowershell
$vnetHubgw = Get-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>A kapcsolatok létrehozása

Ebben a lépésben, a kapcsolat létrehozása a hub virtuális hálózatból a helyszíni virtuális hálózathoz. A példák egy megosztott kulcsra is hivatkoznak. A megosztott kulcshoz használhatja a saját értékeit. Fontos, hogy a megosztott kulcs azonos legyen mindkét kapcsolathoz. A kapcsolat létrehozása egy kis időt vehet igénybe.

```azurepowershell
New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
Hozza létre a helyszíni hub virtuális hálózati kapcsolat. Ebben a lépésben hasonlít az előzőre, kivéve a VNet-rendszert VNet-hub létrehozta a kapcsolatot. Ügyeljen arra, hogy a megosztott kulcsok megegyezzenek. A kapcsolat néhány perc alatt létrejön.

  ```azurepowershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

#### <a name="verify-the-connection"></a>A kapcsolat ellenőrzése

A sikeres kapcsolat ellenőrzéséhez használja a *Get-AzureRmVirtualNetworkGatewayConnection* parancsmagot a *-Debug* argumentummal vagy anélkül. A következő parancsmag-példával az értékeket a sajátjaival megegyezően konfigurálhatja. Ha a rendszer arra kéri, válassza az **A** lehetőséget az összes (**All**) futtatásához. A példában a *-Name* a tesztelni kívánt kapcsolat nevére utal.

```azurepowershell
Get-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

A parancsmag futtatása után tekintse meg az értékeket. Az alábbi példában a kapcsolati állapot *Csatlakoztatva*, és láthatja a bemenő és kimenő bájtokat.

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Az eseményközpont és a küllő virtuális hálózatok

Most már a hubot és a küllő virtuális hálózatok.

```azurepowershell
# Peer hub to spoke
Add-AzureRmVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzureRmVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```

## <a name="create-the-routes"></a>Az útvonalak létrehozása

Ezután hozzon létre néhány útvonalat:

- Egy útvonalat a központi átjáró alhálózatától a küllő alhálózatához a tűzfal IP-címén keresztül
- Egy alapértelmezett útvonalat a küllő alhálózattól a tűzfal IP-címén keresztül

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzureRmRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzureRmVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled
$routeTableSpokeDG = New-AzureRmRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Most már a küllő számítási feladatok és a helyszíni virtuális gépek létrehozásához, és helyezze el őket a megfelelő alhálózatokat.

### <a name="create-the-workload-virtual-machine"></a>A számítási feladatot futtató virtuális gép létrehozása

A küllő virtuális hálózat nincs nyilvános IP-címmel, IIS-t futtató virtuális gép létrehozása, és lehetővé teszi, hogy a pingelésre.
Amikor a rendszer kéri, adjon meg egy felhasználónevet és jelszót a virtuális gép számára.

```azurepowershell
# Create an inbound network security group rule for ports 3389 and 80
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzureRmNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzureRmVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzureRmVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
    -Location $Location1
```

<!---#Create a host firewall rule to allow ping in
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4"}' `
    -Location $Location1--->

### <a name="create-the-on-premises-virtual-machine"></a>A helyszíni virtuális gép létrehozása

Ez az egy egyszerű virtuális gépet, amely kapcsolódik a távoli asztal használatával a nyilvános IP-címet. Itt majd csatlakozik a helyi kiszolgálóhoz a tűzfalon keresztül. Amikor a rendszer kéri, adjon meg egy felhasználónevet és jelszót a virtuális gép számára.

```azurepowershell
New-AzureRmVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>A tűzfal tesztelése

Először lekérése, és jegyezze fel a magánhálózati IP-cím **VM-küllő-01** virtuális gépet.

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

Az Azure Portalról csatlakozzon a **VM-Onprem** virtuális géphez.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
Nyisson meg egy webböngészőt a **VM-rendszert**, és keresse fel a http://\<VM-küllő-01 privát IP\>.

Az Internet Information Services alapértelmezett oldalának kell megjelennie.

A **VM-Onprem** virtuális gépről nyisson meg egy távoli asztalt a **VM-spoke-01** gépre a magánhálózati IP-címen.

A kapcsolatnak sikeresen létre kell jönnie, és be kell tudnia jelentkezni a választott falhasználónévvel és jelszóval.

Ezzel ellenőrizte, hogy a tűzfalszabályok működnek-e:

<!---- You can ping the server on the spoke VNet.--->
- Megkeresheti a küllő virtuális hálózat a webkiszolgálón.
- A kiszolgáló a küllő virtuális hálózat RDP használatával csatlakozhat.

Ezután módosítsa a tűzfal hálózati szabálygyűjteményének műveletét **Deny** (Megtagadás) értékre annak ellenőrzéséhez, hogy a tűzfalszabályok a vártnak megfelelően működnek-e. A következő szkript futtatásával módosíthatja a szabálygyűjtemény műveletét **Deny** (Megtagadás) értékre.

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

Set-AzureRmFirewall -AzureFirewall $azfw
```

Most futtassa újra az ellenőrzéseket. Ezúttal mindegyiknek sikertelennek kell lennie. A módosított szabályok ellenőrzése előtt zárja be a meglévő távoli asztalokat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A tűzfalhoz kapcsolódó erőforrásokat a következő oktatóanyagban is használhatja, vagy ha már nincs rájuk szükség, törölje az **FW-Hybrid-Test** erőforráscsoportot, és vele együtt a tűzfalhoz kapcsolódó összes erőforrást.

## <a name="next-steps"></a>További lépések

A következő lépésben monitorozhatja az Azure Firewall naplóit.

> [!div class="nextstepaction"]
> [Oktatóanyag: A figyelő Azure tűzfal-naplókon](./tutorial-diagnostics.md)
