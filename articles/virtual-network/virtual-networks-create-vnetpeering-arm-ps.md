<properties
   pageTitle="Társviszony-létesítés virtuális hálózatok között PowerShell-parancsmagok használatával | Microsoft Azure"
   description="Ismerje meg, hogyan hozhat létre virtuális hálózatot az Azure Portallal a Resource Managerben."
   services="virtual-network"
   documentationCenter=""
   authors="NarayanAnnamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/02/2016"
   ms.author="narayanannamalai"/>

# Társviszony-létesítés virtuális hálózatok között PowerShell-parancsmagok használatával

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Az alábbi lépésekkel hozhat létre virtuális hálózatok közötti társviszonyt a PowerShell használatával:

1. Ha még nem használta az Azure PowerShellt, tekintse meg [How to Install and Configure Azure PowerShell](../powershell-install-configure.md) (Az Azure PowerShell telepítése és konfigurálása) című részt, majd kövesse az utasításokat egészen az utolsó lépésig az Azure-ba való bejelentkezéshez és az előfizetése kiválasztásához.

        Note: PowerShell cmdlet for managing VNet peering is shipped with [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. Virtuális objektumok olvasása:

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
        $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2
    
3. A virtuális hálózatok közötti társviszony-létesítéshez két hivatkozást kell létrehoznia, egyet-egyet mindkét irányban. A következő lépés először a VNet1 felől a VNet2 felé hozza létre a virtuális hálózatok közötti társviszony-létesítési csatolást:

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.id 

        Output shows:

        Name            : LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        ProvisioningState       : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic   : False
        AllowGatewayTransit : False
        UseRemoteGateways   : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

4. Ez a lépés a VNet2 felől a VNet1 felé hoz létre egy virtuális hálózatok közötti társviszony-létesítési csatolást:

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.id 

        Output shows:

        Name            : LinkToVNet1
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet2
        ProvisioningState       : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic   : False
        AllowGatewayTransit : False
        UseRemoteGateways   : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

5. Miután létrejött a virtuális hálózatok közötti társviszony-létesítési csatolás, a csatolás állapotát az alábbiak szerint láthatja:

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2

        Output shows:

        Name            : LinkToVNet2
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        ProvisioningState       : Succeeded
        RemoteVirtualNetwork    : {
                                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

    A virtuális hálózatok közötti társviszony rendelkezik néhány konfigurálható tulajdonsággal:

  	|Beállítás|Leírás|Alapértelmezett|
  	|:-----|:----------|:------|
  	|AllowVirtualNetworkAccess|Megadja, hogy a társ virtuális hálózat címtere része legyen-e a Virtual_network címkének|Igen|
  	|AllowForwardedTraffic|Lehetővé teszi a társított virtuális hálózaton kívülről érkező forgalom elfogadását vagy elutasítását|Nem|
  	|AllowGatewayTransit|Lehetővé teszi a társ virtuális hálózat számára a virtuális hálózat átjárójának használatát|Nem|
  	|UseRemoteGateways|A társ virtuális hálózat átjárójának használata. A társ virtuális hálózatnak rendelkeznie kell konfigurált átjáróval, és be kell jelölni az AllowGatewayTransit tulajdonságot. Nem használhatja ezt a beállítást, ha konfigurált átjárót.|Nem|

    A virtuális hálózatok közötti társviszony mindegyik csatolása rendelkezik a fenti tulajdonságokkal. Az AllowVirtualNetworkAccess értékét beállíthatja például True értékűre a VNet1 felől a VNet2 felé irányuló virtuális hálózatok közötti társviszony-létesítési csatolásra vonatkozóan, illetve beállíthatja False értékűre a másik irányba mutató társviszony-létesítési csatolásra vonatkozóan.

        $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name LinkToVNet2
        $LinktoVNet2.AllowForwardedTraffic = $true
        Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2

        You can run Get-AzureRmVirtualNetworkPeering to double check the property value after the change.  From the output, you can see AllowForwardedTraffic changes set to True after running the above cmdlets. 

        Name            : LinkToVNet2
        Id          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic       : True
        AllowGatewayTransit     : False
        UseRemoteGateways       : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

    Miután létrejött a társviszony a forgatókönyv szerint, mindkét virtuális hálózaton képesnek kell lennie arra, hogy kapcsolatot kezdeményezzen bármelyik virtuális gépről bármelyik virtuális gépre. Alapértelmezés szerint az AllowVirtualNetworkAccess értéke True, és a virtuális hálózatok közötti társviszony osztja ki a megfelelő ACL-eket a virtuális hálózatok közötti kommunikáció engedélyezéséhez. Továbbra is alkalmazhat hálózati biztonsági csoporton (NSG) alapuló szabályokat az adott alhálózatok vagy virtuális gépek közötti kapcsolat blokkolására a két virtuális hálózat közötti hozzáférés részletes vezérlése érdekében.  Az NSG-szabályok létrehozásával kapcsolatos információkért tekintse meg ezt a [cikket](virtual-networks-create-nsg-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Az alábbi lépésekkel hozhat létre virtuális hálózatok közötti társviszonyt az előfizetésekben a PowerShell használatával:

1. Jelentkezzen be az Azure-ba az A előfizetés rendszergazdai engedéllyel rendelkező User-A felhasználói fiókjával, és futtassa a következő parancsmagot:

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5

        This is not a requirement, peering can be established even if users individually raise peering requests for thier respective Vnets as long as the requests match. Adding a privileged user of the other VNet as a user in the local VNet makes it easier to do the setup. 

2. Jelentkezzen be az Azure-ba a B előfizetés rendszergazdai engedéllyel rendelkező User-B felhasználói fiókjával, és futtassa a következő parancsmagot:

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3

3. A User-A felhasználó bejelentkezési munkamenetében futtassa az alábbi parancsmagot:

        $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess

4. A User-B felhasználó bejelentkezési munkamenetében futtassa az alábbi parancsmagot:

        $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess

5. Miután létrejön a társviszony, a VNet3 hálózat bármely virtuális gépének képesnek kell lennie kommunikálni a VNet5 hálózat bármelyik virtuális gépével.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Ebben a forgatókönyvben az alábbi PowerShell-parancsmagok futtatásával létesíthet virtuális hálózatok közötti társviszonyt.  Az AllowForwardedTraffic tulajdonságot True értékűre kell állítania a VNET1 és a HubVnet között, ami engedélyezi a társított virtuális hálózati címtéren kívülről érkező bejövő forgalmat. 

        $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

        Add-AzureRmVirtualNetworkPeering -name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.id -AllowForwardedTraffic

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.id

2. Miután létrejött a társviszony, ezen [cikk](virtual-network-create-udr-arm-ps.md) alapján meghatározhat felhasználó által megadott útvonalat a VNet1 forgalmának egy virtuális készüléken történő átirányításához a készülék képességeinek használata érdekében. Amikor meghatározza a következő ugrás címét az útvonalban, a címet beállíthatja a virtuális készülék IP-címére a társ HubVNet virtuális hálózatban. Az alábbiakban láthat egy példát:

        $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5

        $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1

        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1

## Virtuális hálózatok közötti társviszony eltávolítása

1.  A virtuális hálózatok közötti társviszony eltávolításához a következő parancsmagot kell futtatnia: 

        Remove-AzureRmVirtualNetworkPeering  
    
        remove both links, as shown below:

        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2 
        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2 

2. Miután eltávolított egy csatolást a virtuális hálózatok közötti társviszonyban, a társviszony-csatolás állapota leválasztottra változik. Ebben az állapotban addig nem hozhatja létre újra a csatolást, amíg a társviszony-csatolás állapota Kezdeményezettre nem változik. Javasoljuk, hogy mindkét csatolást távolítsa el, mielőtt újra létrehozza a virtuális hálózatok közötti társviszonyt. 



<!--HONumber=sep16_HO1-->


