---
title: Az Azure Firewall üzembe helyezése és konfigurálása hibrid hálózatban az Azure PowerShell használatával
description: Ebből az oktatóanyagból megtudhatja, hogyan helyezheti üzembe és konfigurálhatja az Azure Firewallt az Azure Portalon.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 10/2/2018
ms.author: victorh
ms.openlocfilehash: 27221ac4b23f52dd6976a959e6e5529eb0cc89fa
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48856071"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Oktatóanyag: Az Azure Firewall üzembe helyezése és konfigurálása hibrid hálózatban az Azure PowerShell használatával


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A hálózati környezet beállítása
> * A tűzfal konfigurálása és üzembe helyezése
> * Az útvonalak létrehozása
> * A virtuális gépek létrehozása
> * A tűzfal tesztelése

Ebben az oktatóanyagban három virtuális hálózatot hozunk létre:
- **VNet-Hub** – ezen a virtuális hálózaton található a tűzfal.
- **VNet-Spoke** – a küllő virtuális hálózat az Azure-beli számítási feladatot jelöli.
- **VNet-Onprem** – ez a virtuális hálózat egy helyszíni hálózatot jelöl. Egy valós üzemelő példányban ez VPN- vagy Express Route-kapcsolattal csatlakozhat. Az egyszerűség kedvéért ebben az oktatóanyagban VPN-átjárókapcsolatot alkalmazunk, és egy Azure-beli virtuális hálózattal jelölünk egy helyszíni hálózatot.

![Tűzfal a hibrid hálózatban](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

## <a name="key-requirements"></a>Fő követelmények

Három alapvető követelménynek kell teljesülnie, hogy ez a forgatókönyv megfelelően működjön:

- Lennie kell egy felhasználó által megadott útvonalnak a küllő alhálózaton, amely alapértelmezett átjáróként az Azure Firewall IP-címére mutat. A BGP-útvonalpropagálásnak **letiltott** állapotúnak kell lennie ebben az útválasztási táblázatban.
- Lennie kell egy felhasználó által megadott útvonalnak a központ átjáróalhálózatán, amely a tűzfal IP-címére mutat következő ugrásként a küllő hálózatok felé.
- Az Azure Firewall alhálózatán nincs szükség felhasználó által megadott útvonalra, mivel ez a BGP-től tanulja az útvonalakat.
- A VNet-Hub-tól a VNet-Spoke felé irányuló társviszony létesítésekor az **AllowGatewayTransit** beállítást, a VNet-Spoke-tól a VNet-Hub felé irányuló társviszony létesítésekor pedig a **UseRemoteGateways** beállítást adja meg.

Az útvonalak létrehozásával kapcsolatos információkért lásd az oktatóanyag [Útvonalak létrehozása](#create-routes) című szakaszát.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="declare-the-variables"></a>Változók deklarálása
Az alábbi példa az oktatóanyaghoz használt értékekkel deklarálja a változókat. A legtöbb esetben ezeket az értékeket a saját értékeire kell lecserélnie. Ezeket a változókat akkor használhatja, ha azért hajtja végre a lépéseket, hogy megismerje ezt a konfigurációtípust. Ha szükséges, módosítsa a változókat, majd másolja és illessze be őket a PowerShell-konzolra.

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

# Variables for the spoke VNet

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the OnPrem VNet

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

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy erőforráscsoportot az oktatóanyaghoz szükséges erőforrások tárolására:

```azurepowershell
  New-AzureRmResourceGroup -Name $RG1 -Location $Location1
  ```

## <a name="create-and-configure-the-firewall-hub-vnet"></a>A tűzfal központi virtuális hálózatának létrehozása és konfigurálása

Határozza meg a virtuális hálózat részét képező alhálózatokat:

```azurepowershell
$FWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

Most hozza létre a tűzfal központi virtuális hálózatát:

```azurepowershell
$VNetHub = New-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```
Kérje egy nyilvános IP-cím kiosztását a virtuális hálózat számára létrehozni kívánt VPN-átjáróhoz. Látható, hogy az *AllocationMethod* értéke **Dynamic**. A használni kívánt IP-címet nem adhatja meg. Ennek kiosztása a VPN-átjáró számára dinamikusan történik. 

  ```azurepowershell
  $gwpip1 = New-AzureRmPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```
## <a name="create-and-configure-the-spoke-vnet"></a>A küllő virtuális hálózat létrehozása és konfigurálása

Határozza meg a küllő virtuális hálózat részét képező alhálózatokat:

```azurepowershell
$Spokesub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

Hozza létre a küllő virtuális hálózatot:

```azurepowershell
$VNetSpoke = New-AzureRmVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="create-and-configure-the-onprem-vnet"></a>A helyszíni virtuális hálózat létrehozása és konfigurálása

Határozza meg a virtuális hálózat részét képező alhálózatokat:

```azurepowershell
$Onpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

Most hozza létre a helyszíni virtuális hálózatot:

```azurepowershell
$VNetOnprem = New-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```
Kérje egy nyilvános IP-cím kiosztását a virtuális hálózat számára létrehozni kívánt átjáróhoz. Látható, hogy az *AllocationMethod* értéke **Dynamic**. A használni kívánt IP-címet nem adhatja meg. Ennek kiosztása az átjáró számára dinamikusan történik. 

  ```azurepowershell
  $gwOnprempip = New-AzureRmPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="configure-and-deploy-the-firewall"></a>A tűzfal konfigurálása és üzembe helyezése

Most helyezze üzembe a tűzfalat a központi virtuális hálózaton.

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

<!--- $Rule2 = New-AzureRmFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *--->

```azurepowershell
$Rule1 = New-AzureRmFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80

$Rule3 = New-AzureRmFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzureRmFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2,$Rule3 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzureRmFirewall -AzureFirewall $Azfw
```
### <a name="configure-an-application-rule"></a>Alkalmazásszabály konfigurálása

```azurepowershell
$Rule4 = New-AzureRmFirewallApplicationRule -Name "AllowBing" -Protocol "Http:80","Https:443" `
   -SourceAddress $SNOnpremPrefix -TargetFqdn "bing.com"

$AppRuleCollection = New-AzureRmFirewallApplicationRuleCollection -Name RCApp01 -Priority 100 `
   -Rule $Rule4 -ActionType "Allow"
$Azfw.ApplicationRuleCollections = $AppRuleCollection
Set-AzureRmFirewall -AzureFirewall $Azfw

```

## <a name="create-and-connect-the-vpn-gateways"></a>A VPN-átjárók létrehozása és csatlakoztatása

A központi és a helyszíni virtuális hálózatok VPN-átjárókon keresztül csatlakoznak.

### <a name="create-a-vpn-gateway-for-the-hub-vnet"></a>VPN-átjáró létrehozása a központi virtuális hálózathoz

Hozza létre a VPN-átjáró konfigurációját. A VPN-átjáró konfigurációja határozza meg az alhálózatot és a használandó nyilvános IP-címet.

  ```azurepowershell
  $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

Most hozza létre a VPN-átjárót a központi virtuális hálózathoz. A virtuális hálózatok közötti konfigurációkhoz a VpnType paraméternek RouteBased értékűnek kell lennie. A VPN-átjáró létrehozása akár 45 percet is igénybe vehet, az átjáró kiválasztott termékváltozatától függően.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-onprem-vnet"></a>VPN-átjáró létrehozása a helyszíni virtuális hálózathoz

Hozza létre a VPN-átjáró konfigurációját. A VPN-átjáró konfigurációja határozza meg az alhálózatot és a használandó nyilvános IP-címet.

  ```azurepowershell
$vnet2 = Get-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

Most hozza létre a VPN-átjárót a helyszíni virtuális hálózathoz. A virtuális hálózatok közötti konfigurációkhoz a VpnType paraméternek RouteBased értékűnek kell lennie. A VPN-átjáró létrehozása akár 45 percet is igénybe vehet, az átjáró kiválasztott termékváltozatától függően.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```
### <a name="create-the-vpn-connections"></a>A VPN-kapcsolatok létrehozása
Most létrehozhatja a VPN-kapcsolatokat a központi és a helyszíni átjárók között.

#### <a name="get-the-vpn-gateways"></a>A VPN-átjárók lekérése
```azurepowershell
$vnetHubgw = Get-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>A kapcsolatok létrehozása

Ebben a lépésben a központi virtuális hálózat felől a helyszíni virtuális hálózat felé irányuló kapcsolatot hozzuk létre. A példák egy megosztott kulcsra is hivatkoznak. A megosztott kulcshoz használhatja a saját értékeit. Fontos, hogy a megosztott kulcs azonos legyen mindkét kapcsolathoz. A kapcsolat létrehozása egy kis időt vehet igénybe.

```azurepowershell
New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
Hozza létre a helyszíni hálózat felől a központi virtuális hálózatra irányuló kapcsolatot. Ez a lépés az előzőhöz hasonló. A különbség annyi, hogy ezúttal a helyszíni virtuális hálózat felől a központi virtuális hálózat felé irányuló kapcsolatot hozzuk létre. Ügyeljen arra, hogy a megosztott kulcsok megegyezzenek. A kapcsolat néhány perc alatt létrejön.

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



## <a name="peer-the-hub-and-spoke-vnets"></a>Társviszony létesítése a központi és a küllő virtuális hálózatok között

Létesítsen társviszonyt a központi és a küllő virtuális hálózatok között.

```azurepowershell
# Peer hub to spoke
Add-AzureRmVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzureRmVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```
## <a name="create-routes"></a>Útvonalak létrehozása

Ezután hozzon létre néhány útvonalat: 
- Egy útvonalat a központi átjáró alhálózatától a küllő alhálózatához a tűzfal IP-címén keresztül
- Egy alapértelmezett útvonalat a küllő alhálózattól a tűzfal IP-címén keresztül

> [!NOTE]
> Az Azure Firewall BGP segítségével megismeri a helyszíni hálózatokat. Ebbe beletartozhat egy alapértelmezett útvonal, amely az internetes forgalmat a helyszíni hálózaton keresztül irányítja vissza. Ha ehelyett azt szeretné, hogy az internetes forgalmat a rendszer közvetlenül a tűzfalról küldje az internetre, adjon hozzá egy felhasználó által megadott alapértelmezett útvonalat (0.0.0.0/0) az AzureFirewallSubnet alhálózaton úgy, hogy a következő ugrás típusa **Internet** legyen. A helyszíni irányított forgalom továbbra is kényszerített bújtatással halad át a VPN/ExpressRoute átjárón a BGP-ből megismert konkrétabb útvonalak használatával.

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

Most hozza létre a küllő számítási feladatát futtató és a helyszíni virtuális gépeket, és helyezze el őket a megfelelő alhálózatokon.

### <a name="create-the-workload-virtual-machine"></a>A számítási feladatot futtató virtuális gép létrehozása
Hozzon létre egy virtuális gépet a küllő virtuális hálózaton, amely IIS-t futtat, nem rendelkezik nyilvános IP-címmel, és engedélyezi a bepingeléseket.
Amikor a rendszer kéri, adjon meg egy felhasználónevet és jelszót a virtuális gép számára.

```azurepowershell
# Create an inbound network security group rule for port 3389
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


### <a name="create-the-onprem-virtual-machine"></a>A helyszíni virtuális gép létrehozása
Ez egy egyszerű virtuális gép, amelyhez a távoli asztal használatával csatlakozhat a nyilvános IP-címmel. Innen pedig a tűzfalon keresztül csatlakozhat a helyszíni kiszolgálóhoz. Amikor a rendszer kéri, adjon meg egy felhasználónevet és jelszót a virtuális gép számára.
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
Először jegyezze fel a **VM-spoke-01** virtuális gép magánhálózati IP-címét.

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

1. Az Azure Portalról csatlakozzon a **VM-Onprem** virtuális géphez.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
2. Nyisson meg egy webböngészőt a **VM-Onprem** virtuális gépen, majd lépjen a következő webhelyre: http://\<VM-spoke-01 magánhálózati IP-címe\>

   Az Internet Information Services alapértelmezett oldalának kell megjelennie.

3. A **VM-Onprem** virtuális gépről nyisson meg egy távoli asztalt a **VM-spoke-01** gépre a magánhálózati IP-címen.

   A kapcsolatnak sikeresen létre kell jönnie, és be kell tudnia jelentkezni a választott falhasználónévvel és jelszóval.

Ezzel ellenőrizte, hogy a tűzfalszabályok működnek-e:

<!---- You can ping the server on the spoke VNet.--->
- Tud böngészni a webkiszolgálón a küllő virtuális hálózaton.
- Tud csatlakozni a kiszolgálóhoz a küllő virtuális hálózaton RDP használatával.

Ezután módosítsa a tűzfal hálózati szabálygyűjteményének műveletét **Deny** (Megtagadás) értékre annak ellenőrzéséhez, hogy a tűzfalszabályok a vártnak megfelelően működnek-e. A következő szkript futtatásával módosíthatja a szabálygyűjtemény műveletét **Deny** (Megtagadás) értékre.

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

$rcApp = $azfw.GetApplicationRuleCollectionByName("RCApp01")
$rcApp.action.type = "Deny"

Set-AzureRmFirewall -AzureFirewall $azfw
```
Most futtassa újra az ellenőrzéseket. Ezúttal mindegyiknek sikertelennek kell lennie. A módosított szabályok ellenőrzése előtt zárja be a meglévő távoli asztalokat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A tűzfalhoz kapcsolódó erőforrásokat a következő oktatóanyagban is használhatja, vagy ha már nincs rájuk szükség, törölje az **FW-Hybrid-Test** erőforráscsoportot, és vele együtt a tűzfalhoz kapcsolódó összes erőforrást.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A hálózati környezet beállítása
> * A tűzfal konfigurálása és üzembe helyezése
> * Az útvonalak létrehozása
> * A virtuális gépek létrehozása
> * A tűzfal tesztelése

A következő lépésben monitorozhatja az Azure Firewall naplóit.

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Firewall naplóinak monitorozása](./tutorial-diagnostics.md)
