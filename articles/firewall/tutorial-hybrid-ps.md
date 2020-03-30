---
title: Az Azure firewall telepítése & konfigurálása hibrid hálózatban a PowerShell használatával
description: Ebben a cikkben megtudhatja, hogyan telepítheti és konfigurálhatja az Azure Firewall az Azure PowerShell használatával.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/08/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 37bb28419f23fee2c179171a2e5c0e4e851ac9a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471754"
---
# <a name="deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Az Azure Firewall üzembe helyezése és konfigurálása hibrid hálózatban az Azure PowerShell használatával

Amikor csatlakoztatja a helyszíni hálózatot egy Azure virtuális hálózathoz egy hibrid hálózat létrehozásához, az Azure hálózati erőforrásokhoz való hozzáférés szabályozásának képessége fontos része egy átfogó biztonsági tervnek.

Az Azure Firewall segítségével szabályozhatja a hálózati hozzáférést egy hibrid hálózatban az engedélyezett és elutasított hálózati forgalmat meghatározó szabályok használatával.

Ebben a cikkben három virtuális hálózatot hoz létre:

- **VNet-Hub** – a tűzfal ebben a virtuális hálózatban található.
- **VNet-Küllős** – a küllővirtuális hálózat az Azure-ban található számítási feladatokat jelöli.
- **VNet-Onprem** – A helyszíni virtuális hálózat egy helyszíni hálózatot képvisel. Egy tényleges központi telepítésben VPN- vagy ExpressRoute-kapcsolattal is csatlakoztatható. Az egyszerűség kedvéért ez a cikk VPN-átjárókapcsolatot használ, és egy Azure-ban található virtuális hálózatot használ a helyszíni hálózat ábrázolására.

![Tűzfal a hibrid hálózatban](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Változók deklarálása
> * A tűzfalközpont virtuális hálózatának létrehozása
> * A küllővirtuális hálózat létrehozása
> * A helyszíni virtuális hálózat létrehozása
> * A tűzfal konfigurálása és üzembe helyezése
> * A VPN-átjárók létrehozása és csatlakoztatása
> * Társa a hub és a küllővirtuális hálózatok
> * Az útvonalak létrehozása
> * A virtuális gépek létrehozása
> * A tűzfal tesztelése

Ha az oktatóanyag befejezéséhez inkább az Azure Portalt szeretné használni, olvassa el [az Oktatóanyag: Az Azure tűzfal üzembe helyezése és konfigurálása hibrid hálózatban az Azure Portal használatával című témakört.](tutorial-hybrid-portal.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk megköveteli, hogy a PowerShell helyileg futtassa. Az Azure PowerShell-modul telepítve kell lennie. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](https://docs.microsoft.com/powershell/azure/install-Az-ps) ismertető cikket. A PowerShell-verzió ellenőrzése után futtassa az `Login-AzAccount` parancsot az Azure-hoz való kapcsolódáshoz.

Három alapvető követelménynek kell teljesülnie, hogy ez a forgatókönyv megfelelően működjön:

- A küllős alhálózaton egy felhasználó által definiált útvonal (UDR), amely az Azure tűzfal IP-címére mutat alapértelmezett átjáróként. A virtuális hálózati átjáró útvonalának propagálásának le kell **tiltani** ezen az útvonaltáblán.
- A központi átjáró alhálózatának UDR-jének a küllőhálózatok következő ugrásaként a tűzfal IP-címére kell mutatnia.

   Nincs szükség UDR-re az Azure Tűzfal alhálózaton, mivel a BGP-től tanul útvonalakat.
- A VNet-Hub-tól a VNet-Spoke felé irányuló társviszony létesítésekor az **AllowGatewayTransit** beállítást, a VNet-Spoke-tól a VNet-Hub felé irányuló társviszony létesítésekor pedig a **UseRemoteGateways** beállítást adja meg.

Tekintse meg az [Útvonalak létrehozása](#create-the-routes) szakasz ebben a cikkben, hogy hogyan jönnek létre ezek az útvonalak.

>[!NOTE]
>Az Azure tűzfalnak közvetlen internetkapcsolattal kell rendelkeznie. Ha az AzureFirewallSubnet megtanulja az alapértelmezett útvonalat a helyszíni hálózatra a BGP-n keresztül, felül kell bírnia ezt egy 0.0.0.0/0 UDR-rel, amelyen a **NextHopType** érték **internetként** van beállítva a közvetlen internetkapcsolat fenntartásához.
>
>Az Azure Firewall konfigurálható a kényszerített bújtatás támogatására. További információ: [Azure Firewall kényszerített bújtatás.](forced-tunneling.md)

>[!NOTE]
>A közvetlenül társviszonyba vett virtuális hálózatok közötti forgalom közvetlenül akkor is közvetlenül továbbítható, ha az UDR az Azure Tűzfalra mutat alapértelmezett átjáróként. Ebben az esetben az alhálózat nak a tűzfalnak való elküldéséhez az UDR-nek mindkét alhálózaton kifejezetten tartalmaznia kell a cél alhálózati hálózati előtagot.

A kapcsolódó Azure PowerShell-referenciadokumentáció áttekintéséhez olvassa el az [Azure PowerShell-referencia című témakört.](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="declare-the-variables"></a>Változók deklarálása

A következő példa deklarálja a változókat a cikk értékei alapján. Bizonyos esetekben előfordulhat, hogy le kell cserélnie bizonyos értékeket a sajátjára, hogy az előfizetésben dolgozhasson. Ha szükséges, módosítsa a változókat, majd másolja és illessze be őket a PowerShell-konzolra.

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


## <a name="create-the-firewall-hub-virtual-network"></a>A tűzfalközpont virtuális hálózatának létrehozása

Először hozza létre az erőforráscsoportot, amely tartalmazza a cikk hez szükséges erőforrásokat:

```azurepowershell
  New-AzResourceGroup -Name $RG1 -Location $Location1
  ```

Határozza meg a virtuális hálózatba bevonandó alhálózatokat:

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

Most hozza létre a tűzfalhub virtuális hálózatát:

```azurepowershell
$VNetHub = New-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```

Kérjen nyilvános IP-címet a virtuális hálózathoz létrehozandó VPN-átjáróhoz. Látható, hogy az *AllocationMethod* értéke **Dynamic**. A használni kívánt IP-címet nem adhatja meg. Ennek kiosztása a VPN-átjáró számára dinamikusan történik.

  ```azurepowershell
  $gwpip1 = New-AzPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="create-the-spoke-virtual-network"></a>A küllővirtuális hálózat létrehozása

Határozza meg a küllővirtuális hálózatba bevonandó alhálózatokat:

```azurepowershell
$Spokesub = New-AzVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

Hozza létre a küllős virtuális hálózatot:

```azurepowershell
$VNetSpoke = New-AzVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="create-the-on-premises-virtual-network"></a>A helyszíni virtuális hálózat létrehozása

Határozza meg a virtuális hálózatba bevonandó alhálózatokat:

```azurepowershell
$Onpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

Most hozza létre a helyszíni virtuális hálózatot:

```azurepowershell
$VNetOnprem = New-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```

Kérjen nyilvános IP-címet a virtuális hálózathoz létrehozandó átjáróhoz. Látható, hogy az *AllocationMethod* értéke **Dynamic**. A használni kívánt IP-címet nem adhatja meg. Ennek kiosztása az átjáró számára dinamikusan történik.

  ```azurepowershell
  $gwOnprempip = New-AzPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="configure-and-deploy-the-firewall"></a>A tűzfal konfigurálása és üzembe helyezése

Most telepítse a tűzfalat a központi virtuális hálózatba.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>Hálózati szabályok konfigurálása

<!--- $Rule3 = New-AzFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *--->

```azurepowershell
$Rule1 = New-AzFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80

$Rule2 = New-AzFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzFirewall -AzureFirewall $Azfw
```

## <a name="create-and-connect-the-vpn-gateways"></a>A VPN-átjárók létrehozása és csatlakoztatása

A hub és a helyszíni virtuális hálózatok VPN-átjárókon keresztül csatlakoznak.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>VPN-átjáró létrehozása a központi virtuális hálózathoz

Hozza létre a VPN-átjáró konfigurációját. A VPN-átjáró konfigurációja határozza meg az alhálózatot és a használandó nyilvános IP-címet.

  ```azurepowershell
  $vnet1 = Get-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

Most hozza létre a VPN-átjárót a központi virtuális hálózathoz. A hálózat-hálózat konfigurációkhoz RouteBased VpnType szükséges. A VPN-átjáró létrehozása akár 45 percet is igénybe vehet, az átjáró kiválasztott termékváltozatától függően.

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>VPN-átjáró létrehozása a helyszíni virtuális hálózathoz

Hozza létre a VPN-átjáró konfigurációját. A VPN-átjáró konfigurációja határozza meg az alhálózatot és a használandó nyilvános IP-címet.

  ```azurepowershell
$vnet2 = Get-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

Most hozza létre a VPN-átjárót a helyszíni virtuális hálózathoz. A hálózat-hálózat konfigurációkhoz RouteBased VpnType szükséges. A VPN-átjáró létrehozása akár 45 percet is igénybe vehet, az átjáró kiválasztott termékváltozatától függően.

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-the-vpn-connections"></a>A VPN-kapcsolatok létrehozása

Most már létrehozhatja a VPN-kapcsolatokat a hub és a helyszíni átjárók között

#### <a name="get-the-vpn-gateways"></a>A VPN-átjárók lekérése

```azurepowershell
$vnetHubgw = Get-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>A kapcsolatok létrehozása

Ebben a lépésben hozza létre a kapcsolatot a központi virtuális hálózatról a helyszíni virtuális hálózathoz. A példák egy megosztott kulcsra is hivatkoznak. A megosztott kulcshoz használhatja a saját értékeit. Fontos, hogy a megosztott kulcs azonos legyen mindkét kapcsolathoz. A kapcsolat létrehozása egy kis időt vehet igénybe.

```azurepowershell
New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
Hozza létre a helyszíni virtuális hálózati kapcsolat központi. Ez a lépés hasonló az előzőhez, kivéve, hogy a kapcsolatot a VNet-Onprem és a VNet-hub között hozza létre. Ügyeljen arra, hogy a megosztott kulcsok megegyezzenek. A kapcsolat néhány perc alatt létrejön.

  ```azurepowershell
  New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

#### <a name="verify-the-connection"></a>A kapcsolat ellenőrzése

A sikeres kapcsolat ellenőrzéséhez használja a *Get-AzVirtualNetworkConnection* parancsmaggal, *a -Debug*használatával vagy anélkül. A következő parancsmag-példával az értékeket a sajátjaival megegyezően konfigurálhatja. Ha a rendszer arra kéri, válassza az **A** lehetőséget az összes (**All**) futtatásához. A példában a *-Name* a tesztelni kívánt kapcsolat nevére hivatkozik.

```azurepowershell
Get-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

A parancsmag futtatása után tekintse meg az értékeket. Az alábbi példában a kapcsolati állapot *Csatlakoztatva*, és láthatja a bemenő és kimenő bájtokat.

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Társa a hub és a küllővirtuális hálózatok

Most peer a hub és a küllővirtuális hálózatok.

```azurepowershell
# Peer hub to spoke
Add-AzVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```

## <a name="create-the-routes"></a>Az útvonalak létrehozása

Ezután hozzon létre néhány útvonalat:

- Egy útvonalat a központi átjáró alhálózatától a küllő alhálózatához a tűzfal IP-címén keresztül
- Egy alapértelmezett útvonalat a küllő alhálózattól a tűzfal IP-címén keresztül

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled. The property is now called "Virtual network gateway route propagation," but the API still refers to the parameter as "DisableBgpRoutePropagation."
$routeTableSpokeDG = New-AzRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzRouteConfig `
  -Name "ToFirewall" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Most hozza létre a küllős számítási feladatok és a helyszíni virtuális gépek, és helyezze őket a megfelelő alhálózatok.

### <a name="create-the-workload-virtual-machine"></a>A számítási feladatot futtató virtuális gép létrehozása

Hozzon létre egy virtuális gépet a küllős virtuális hálózatban, amely iIS-t futtat nyilvános IP-cím nélkül, és engedélyezi a pingeket.
Amikor a rendszer kéri, adjon meg egy felhasználónevet és jelszót a virtuális gép számára.

```azurepowershell
# Create an inbound network security group rule for ports 3389 and 80
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzVMExtension `
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
Set-AzVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4"}' `
    -Location $Location1--->

### <a name="create-the-on-premises-virtual-machine"></a>A helyszíni virtuális gép létrehozása

Ez egy egyszerű virtuális gép, amellyel a Távoli asztal használatával csatlakozhat a nyilvános IP-címhez. Innen a tűzfalon keresztül csatlakozhat a helyszíni kiszolgálóhoz. Amikor a rendszer kéri, adjon meg egy felhasználónevet és jelszót a virtuális gép számára.

```azurepowershell
New-AzVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>A tűzfal tesztelése

Először is, kap, majd vegye figyelembe a virtuális **gép-küllős-01** virtuális gép privát IP-címét.

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

Az Azure Portalról csatlakozzon a **VM-Onprem** virtuális géphez.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
Nyisson meg egy webböngészőt a **VM-Onprem-en,** és keresse meg http://\<VM-küllős-01 privát IP-cím.\>

Az Internet Information Services alapértelmezett oldalának kell megjelennie.

A **VM-Onprem** virtuális gépről nyisson meg egy távoli asztalt a **VM-spoke-01** gépre a magánhálózati IP-címen.

A kapcsolatnak sikeresen létre kell jönnie, és be kell tudnia jelentkezni a választott falhasználónévvel és jelszóval.

Így most már ellenőrizte, hogy a tűzfalszabályok működnek:

<!---- You can ping the server on the spoke VNet.--->
- A küllővirtuális hálózaton böngészhet a webkiszolgáló között.
- A küllővirtuális hálózat kiszolgálójához rdp használatával csatlakozhat.

Ezután módosítsa a tűzfal hálózati szabálygyűjteményének műveletét **Deny** (Megtagadás) értékre annak ellenőrzéséhez, hogy a tűzfalszabályok a vártnak megfelelően működnek-e. A következő szkript futtatásával módosíthatja a szabálygyűjtemény műveletét **Deny** (Megtagadás) értékre.

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

Set-AzFirewall -AzureFirewall $azfw
```

Most futtassa újra az ellenőrzéseket. Ezúttal mindegyiknek sikertelennek kell lennie. A módosított szabályok ellenőrzése előtt zárja be a meglévő távoli asztalokat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A tűzfalhoz kapcsolódó erőforrásokat a következő oktatóanyagban is használhatja, vagy ha már nincs rájuk szükség, törölje az **FW-Hybrid-Test** erőforráscsoportot, és vele együtt a tűzfalhoz kapcsolódó összes erőforrást.

## <a name="next-steps"></a>További lépések

A következő lépésben monitorozhatja az Azure Firewall naplóit.

[Oktatóanyag: Az Azure Firewall naplóinak monitorozása](./tutorial-diagnostics.md)
