---
title: "Társviszony-létesítés virtuális hálózatok között PowerShell-parancsmagok használatával | Microsoft Docs"
description: "Ismerje meg, hogyan hozhat létre virtuális hálózatot az Azure Portallal a Resource Managerben."
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
tags: azure-resource-manager
ms.assetid: dac579bd-7545-461a-bdac-301c87434c84
ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayanannamalai; annahar
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 348b23b277c80867f600a408736e13b8ceb665f4


---
# <a name="create-vnet-peering-using-powershell-cmdlets"></a>Társviszony-létesítés virtuális hálózatok között PowerShell-parancsmagok használatával
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Az alábbi lépésekkel hozhat létre virtuális hálózatok közötti társviszonyt a PowerShell használatával:

1. Ha még nem használta az Azure PowerShellt, tekintse meg [How to Install and Configure Azure PowerShell](../powershell-install-configure.md) (Az Azure PowerShell telepítése és konfigurálása) című részt, majd kövesse az utasításokat egészen az utolsó lépésig az Azure-ba való bejelentkezéshez és az előfizetése kiválasztásához.

> [!NOTE]
> Az [Azure PowerShell 1.6](http://www.powershellgallery.com/packages/Azure/1.6.0) tartalmazza a virtuális hálózatok közötti társviszonyok kezeléséhez szükséges PowerShell-parancsmagot.
> 
> 

1. Virtuális objektumok olvasása:
   
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
        $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2
2. A virtuális hálózatok közötti társviszony-létesítéshez két hivatkozást kell létrehoznia, egyet-egyet mindkét irányban. A következő lépés először a VNet1 felől a VNet2 felé hozza létre a virtuális hálózatok közötti társviszony-létesítési csatolást:
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.Id
   
    A kimenetben a következő látható:
   
        Name            : LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Initiated
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
        UseRemoteGateways    : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null
3. Ez a lépés a VNet2 felől a VNet1 felé hoz létre egy virtuális hálózatok közötti társviszony-létesítési csatolást:
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.Id
   
    A kimenetben a következő látható:
   
        Name            : LinkToVNet1
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet2
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
        UseRemoteGateways    : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null
4. Miután létrejött a virtuális hálózatok közötti társviszony-létesítési csatolás, a csatolás állapotát az alábbiak szerint láthatja:
   
        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2
   
    A kimenetben a következő látható:
   
        Name            : LinkToVNet2
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null
   
    A virtuális hálózatok közötti társviszony rendelkezik néhány konfigurálható tulajdonsággal:
   
   | Beállítás | Leírás | Alapértelmezett |
   |:--- |:--- |:--- |
   | AllowVirtualNetworkAccess |Megadja, hogy a társ virtuális hálózat címtere része legyen-e a Virtual_network címkének |Igen |
   | AllowForwardedTraffic |Lehetővé teszi a társított virtuális hálózaton kívülről érkező forgalom elfogadását vagy elutasítását |Nem |
   | AllowGatewayTransit |Lehetővé teszi a társ virtuális hálózat számára a virtuális hálózat átjárójának használatát |Nem |
   | UseRemoteGateways |A társ virtuális hálózat átjárójának használata. A társ virtuális hálózatnak rendelkeznie kell konfigurált átjáróval, és be kell jelölni az AllowGatewayTransit tulajdonságot. Nem használhatja ezt a beállítást, ha konfigurált átjárót. |Nem |
   
    A virtuális hálózatok közötti társviszony mindegyik csatolása rendelkezik a fenti tulajdonságokkal. Az AllowVirtualNetworkAccess értékét beállíthatja például True értékűre a VNet1 felől a VNet2 felé irányuló virtuális hálózatok közötti társviszony-létesítési csatolásra vonatkozóan, illetve beállíthatja False értékűre a másik irányba mutató társviszony-létesítési csatolásra vonatkozóan.
   
        $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name LinkToVNet2
        $LinktoVNet2.AllowForwardedTraffic = $true
        Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2
   
    Az Get-AzureRmVirtualNetworkPeering futtatásával duplán ellenőrizheti a tulajdonság értékét a módosítás után. A fenti parancsmagok futtatása után a kimenetben látható, hogy az AllowForwardedTraffic értéke True (Igaz) lesz.
   
        Name            : LinkToVNet2
        Id            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic        : True
        AllowGatewayTransit        : False
        UseRemoteGateways        : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null
   
    Miután létrejött a társviszony a forgatókönyv szerint, mindkét virtuális hálózaton képesnek kell lennie arra, hogy kapcsolatot kezdeményezzen bármelyik virtuális gépről bármelyik virtuális gépre. Alapértelmezés szerint az AllowVirtualNetworkAccess értéke True, és a virtuális hálózatok közötti társviszony osztja ki a megfelelő ACL-eket a virtuális hálózatok közötti kommunikáció engedélyezéséhez. Továbbra is alkalmazhat hálózati biztonsági csoporton (NSG) alapuló szabályokat az adott alhálózatok vagy virtuális gépek közötti kapcsolat blokkolására a két virtuális hálózat közötti hozzáférés részletes vezérlése érdekében.  Az NSG-szabályok létrehozásával kapcsolatos információkért tekintse meg ezt a [cikket](virtual-networks-create-nsg-arm-ps.md).

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Az alábbi lépésekkel hozhat létre virtuális hálózatok közötti társviszonyt az előfizetésekben a PowerShell használatával:

1. Jelentkezzen be az Azure-ba az A előfizetés rendszergazdai engedéllyel rendelkező User-A felhasználói fiókjával, és futtassa a következő parancsmagot:
   
        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet5
   
    Ez nem követelmény, társviszony akkor is létesíthető, ha a felhasználók önállóan adnak ki társviszony-létesítési kérelmet a megfelelő virtuális hálózataikra, és a kérések megegyeznek. Ha a helyi virtuális hálózat felhasználóihoz hozzáadja a másik virtuális hálózat egy rendszergazdai engedéllyel rendelkező felhasználóját, azzal leegyszerűsítheti a beállítást.
2. Jelentkezzen be az Azure-ba a B előfizetés rendszergazdai engedéllyel rendelkező User-B felhasználói fiókjával, és futtassa a következő parancsmagot:
   
        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet3
3. A User-A felhasználó bejelentkezési munkamenetében futtassa az alábbi parancsmagot:
   
        $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscription-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess
4. A User-B felhasználó bejelentkezési munkamenetében futtassa az alábbi parancsmagot:
   
        $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess
5. Miután létrejön a társviszony, a VNet3 hálózat bármely virtuális gépének képesnek kell lennie kommunikálni a VNet5 hálózat bármelyik virtuális gépével.

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Ebben a forgatókönyvben az alábbi PowerShell-parancsmagok futtatásával létesíthet virtuális hálózatok közötti társviszonyt.  Az AllowForwardedTraffic tulajdonságot True értékűre kell állítania a VNET1 és a HubVnet között, ami engedélyezi a társított virtuális hálózati címtéren kívülről érkező bejövő forgalmat.
   
        $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.Id -AllowForwardedTraffic
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.Id
2. Miután létrejött a társviszony, ezen [cikk](virtual-network-create-udr-arm-ps.md) alapján meghatározhat felhasználó által megadott útvonalat a VNet1 forgalmának egy virtuális készüléken történő átirányításához a készülék képességeinek használata érdekében. Amikor meghatározza a következő ugrás címét az útvonalban, a címet beállíthatja a virtuális készülék IP-címére a társ HubVNet virtuális hálózatban. Az alábbiakban láthat egy példát:
   
        $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5
   
        $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route
   
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1
   
        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable
   
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Az alábbi lépésekkel hozhat létre társviszonyt egy klasszikus virtuális hálózat és egy Azure Resource Manager-virtuálishálózat között:

1. A **VNET1**-hez, az Azure Resource Manager-virtuális hálózathoz tartozó objektum a következő:
   
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
2. Ebben a forgatókönyvben a virtuális hálózatok társviszonyának létrehozásához csak egyetlen hivatkozás szükséges, mégpedig egy hivatkozás a **VNET1**-ről a **VNET2**-re. Ehhez a lépéshez ismerni kell a klasszikus virtuális hálózat erőforrás-azonosítóját. Az erőforráscsoport-azonosító formátuma a következő:
   
        /subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.ClassicNetwork/virtualNetworks/{VirtualNetworkName}
   
    Ne felejtse el a SubscriptionID, ResourceGroupName és VirtualNetworkName elemeket a megfelelő nevekkel behelyettesíteni.
   
    Ezt a következőképp teheti meg:
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2
3. Miután létrejött a virtuális hálózatok közötti társviszony-létesítési csatolás, a csatolás állapotát az alábbi kimenetben láthatja:
   
        Name                             : LinkToVNet2
        Id                               : /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeerings/LinkToVNet2
        Etag                             : W/"acecbd0f-766c-46be-aa7e-d03e41c46b16"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                         "Id": "/subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                       }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

## <a name="remove-vnet-peering"></a>Virtuális hálózatok közötti társviszony eltávolítása
1. A virtuális hálózatok közötti társviszony eltávolításához a következő parancsmagot kell futtatnia:
   
     Remove-AzureRmVirtualNetworkPeering  
   
     Távolítsa el a mindkét csatolást a következő parancsokkal:
   
     Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2   Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
2. Miután eltávolított egy csatolást a virtuális hálózatok közötti társviszonyban, a társviszony-csatolás állapota leválasztottra változik. Ebben az állapotban addig nem hozhatja létre újra a csatolást, amíg a társviszony-csatolás állapota Kezdeményezettre nem változik. Javasoljuk, hogy mindkét csatolást távolítsa el, mielőtt újra létrehozza a virtuális hálózatok közötti társviszonyt.




<!--HONumber=Nov16_HO2-->


