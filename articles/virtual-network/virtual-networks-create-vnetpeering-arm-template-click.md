<properties
   pageTitle="Virtuális hálózatok közötti társviszony létrehozása Resource Manager-sablonok használatával | Microsoft Azure"
   description="Ismerje meg, hogyan hozhat létre virtuális hálózatok közötti társviszonyt a Resource Manager sablonjai segítségével."
   services="virtual-network"
   documentationCenter=""
   authors="narayanannamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai;annahar"/>


# Virtuális hálózatok közötti társviszony létrehozása Resource Manager-sablonok használatával

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Az alábbi lépésekkel hozhat létre virtuális hálózatok közötti társviszonyt Resource Manager-sablonok használatával:

1. Ha még nem használta az Azure PowerShellt, tekintse meg [How to Install and Configure Azure PowerShell](../powershell-install-configure.md) (Az Azure PowerShell telepítése és konfigurálása) című részt, majd kövesse az utasításokat egészen az utolsó lépésig az Azure-ba való bejelentkezéshez és az előfizetése kiválasztásához.

    > [AZURE.NOTE] Az [Azure PowerShell 1.6](http://www.powershellgallery.com/packages/Azure/1.6.0) tartalmazza a virtuális hálózatok közötti társviszonyok kezeléséhez szükséges PowerShell-parancsmagot.

2. Az alábbi szöveg tartalmazza a VNet1 felől a VNet2 felé irányuló virtuális hálózatok közötti társviszony-létesítési csatolás definícióját a fenti forgatókönyv alapján. Másolja az alábbi szöveget, és mentse azt egy VNetPeeringVNet1.json nevű fájlba.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToVNet2",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet2')]"       
        }
            }
            }
        ]
        }

3. Az alábbi szakasz tartalmazza a VNet2 felől a VNet1 felé irányuló virtuális hálózatok közötti társviszony-létesítési csatolás definícióját a fenti forgatókönyv alapján.  Másolja az alábbi szöveget, és mentse azt egy VNetPeeringVNet2.json nevű fájlba.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet2/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
        ]
        }

    Ahogy az a fenti sablonban látható, a virtuális hálózatok közötti társviszony rendelkezik néhány konfigurálható tulajdonsággal:

  	|Beállítás|Leírás|Alapértelmezett|
  	|:-----|:----------|:------|
  	|AllowVirtualNetworkAccess|Megadja, hogy a társ virtuális hálózat címtere része legyen-e vagy sem a virtual_network címkének.|Igen|
  	|AllowForwardedTraffic|Lehetővé teszi a társított virtuális hálózaton kívülről érkező forgalom elfogadását vagy elutasítását.|Nem|
  	|AllowGatewayTransit|Lehetővé teszi a társ virtuális hálózat számára a virtuális hálózat átjárójának használatát.|Nem|
  	|UseRemoteGateways|A társ virtuális hálózat átjárójának használata. A társ virtuális hálózatnak rendelkeznie kell konfigurált átjáróval, és be kell jelölni az AllowGatewayTransit tulajdonságot. Nem használhatja ezt a beállítást, ha konfigurált átjárót.|Nem|

    A virtuális hálózatok közötti társviszony mindegyik csatolása rendelkezik a fenti tulajdonságokkal. Az AllowVirtualNetworkAccess értékét beállíthatja például True értékűre a VNet1 felől a VNet2 felé irányuló virtuális hálózatok közötti társviszony-létesítési csatolásra vonatkozóan, illetve beállíthatja False értékűre a másik irányba mutató társviszony-létesítési csatolásra vonatkozóan.


4. A sablonfájl üzembe helyezéséhez futtathatja a New-AzureRmResourceGroupDeployment parancsmagot, amely létrehozza vagy frissíti az üzemelő példányt. A Resource Manager-sablonok használatával kapcsolatos információkért tekintse meg ezt a [cikket](../resource-group-template-deploy.md).

        New-AzureRmResourceGroupDeployment -ResourceGroupName <resource group name> -TemplateFile <template file path> -DeploymentDebugLogLevel all

    > [AZURE.NOTE] Az erőforráscsoport nevét és a sablonfájlt szükség szerint cserélje le.

    Az alábbiakban egy példát láthat a fenti forgatókönyv alapján:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet1.json -DeploymentDebugLogLevel all

    A kimenetben a következő látható:

        DeploymentName      : VNetPeeringVNet1
        ResourceGroupName   : VNet101
        ProvisioningState       : Succeeded
        Timestamp           : 7/26/2016 9:05:03 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters          :
        Outputs         :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet2.json -DeploymentDebugLogLevel all

    A kimenetben a következő látható:

        DeploymentName      : VNetPeeringVNet2
        ResourceGroupName   : VNet101
        ProvisioningState       : Succeeded
        Timestamp           : 7/26/2016 9:07:22 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters          :
        Outputs         :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

5. Miután befejezte az üzembe helyezést, a társviszony állapotának megtekintéséhez futtathatja az alábbi parancsmagot:

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNet1 -ResourceGroupName VNet101 -Name linktoVNet2

    A kimenetben a következő látható:

        Name            : LinkToVNet2
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : VNet101
        VirtualNetworkName  : VNet1
        ProvisioningState       : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

    Miután létrejött a társviszony a forgatókönyv szerint, mindkét virtuális hálózaton képesnek kell lennie arra, hogy kapcsolatot kezdeményezzen bármelyik virtuális gépről bármelyik virtuális gépre. Alapértelmezés szerint az AllowVirtualNetworkAccess értéke True, és a virtuális hálózatok közötti társviszony osztja ki a megfelelő ACL-eket a virtuális hálózatok közötti kommunikáció engedélyezéséhez. Továbbra is alkalmazhat hálózati biztonsági csoporton (NSG) alapuló szabályokat az adott alhálózatok vagy virtuális gépek közötti kapcsolat blokkolására a két virtuális hálózat közötti hozzáférés részletes vezérlése érdekében.  Az NSG-szabályok létrehozásával kapcsolatos információkért tekintse meg ezt a [cikket](virtual-networks-create-nsg-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Az alábbi lépésekkel hozhat létre virtuális hálózatok közötti társviszonyt az előfizetésekben:

1. Jelentkezzen be az Azure-ba az A előfizetés rendszergazdai engedéllyel rendelkező User-A felhasználói fiókjával, és futtassa a következő parancsmagot:

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5

    Ez nem követelmény, társviszony akkor is létesíthető, ha a felhasználók önállóan adnak ki társviszony-létesítési kérelmet a megfelelő virtuális hálózataikra, és a kérések megegyeznek. Ha a helyi virtuális hálózat felhasználóihoz hozzáadja a másik virtuális hálózat rendszergazdai engedéllyel rendelkező felhasználóit, azzal leegyszerűsítheti a beállítást.

2. Jelentkezzen be az Azure-ba a B előfizetés rendszergazdai engedéllyel rendelkező User-B felhasználói fiókjával, és futtassa a következő parancsmagot:

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3

3. A User-A felhasználó bejelentkezési munkamenetében futtassa az alábbi parancsmagot:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet3.json -DeploymentDebugLogLevel all

    Itt látható a JSON-fájl definiálásának módja.  

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet3/LinkToVNet5",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<Subscription-B-ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet5"
                }
            }
            }
        ]
        }

4. A User-B felhasználó bejelentkezési munkamenetében futtassa az alábbi parancsmagot:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet5.json -DeploymentDebugLogLevel all

    Itt látható a JSON-fájl definiálásának módja:

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet5/LinkToVNet3",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/Subscription-A-ID /resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet3"
                }
            }
            }
        ]
        }

    Miután létrejött a társviszony a forgatókönyv szerint, mindkét virtuális hálózaton képesnek kell lennie arra, hogy kapcsolatot kezdeményezzen bármelyik virtuális gépről bármelyik virtuális gépre az egyes előfizetésekben.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Ebben a forgatókönyvben az alábbi mintasablonok üzembe helyezésével létesíthet virtuális hálózatok közötti társviszonyt.  Az AllowForwardedTraffic tulajdonságot True értékűre kell állítania, ami lehetővé teszi a társított virtuális hálózat virtuális készüléke számára, hogy forgalmat küldjön és fogadjon.

    Az alábbiakban látható a HubVNet felől a VNet1 felé irányuló virtuális hálózatok közötti társviszony létrehozásához használható sablon. Vegye figyelembe, hogy az AllowForwardedTraffic False értékűre van beállítva.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "HubVNet/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
            }
        ]
        }

2. Az alábbiakban látható a VNet1 felől a HubVNet felé irányuló virtuális hálózatok közötti társviszony létrehozásához használható sablon. Vegye figyelembe, hogy az AllowForwardedTraffic True értékűre van beállítva.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToHubVNet",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": true,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'HubVnet')]"       
                }
            }
            }
        ]
        }


3. Miután létrejött a társviszony, ezen [cikk](virtual-network-create-udr-arm-ps.md) alapján meghatározhat felhasználó által megadott útvonalat a VNet1 forgalmának egy virtuális készüléken történő átirányításához a készülék képességeinek használata érdekében. Amikor meghatározza a következő ugrás címét az útvonalban, a címet beállíthatja a virtuális készülék IP-címére a társ HubVNet virtuális hálózatban.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Ha szeretne társviszonyt létrehozni különböző üzembe helyezési modelleken alapuló virtuális hálózatok között, kövesse az alábbi lépéseket:
1. Az alábbi szöveg a VNET1 felől a VNET2 felé irányuló, virtuális hálózatok közötti társviszony-létesítési csatolás ezen forgatókönyv szerinti definícióját tartalmazza. Egy klasszikus virtuális hálózat Azure Resource Manager-alapú virtuális hálózathoz társításához csak egyetlen csatolás szükséges.

    Meg kell adnia az előfizetése azonosítóját, amelyben a klasszikus virtuális hálózat vagy VNET2 található, és a MyResourceGroup rész helyére be kell helyettesítenie a megfelelő erőforráscsoport nevét.

    {  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  "contentVersion": "1.0.0.0",  "parameters": {  },  "variables": {  },  "resources": [      {      "apiVersion": "2016-06-01",      "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",      "name": "VNET1/LinkToVNET2",      "location": "[resourceGroup().location]",      "properties": {      "allowVirtualNetworkAccess": true,      "allowForwardedTraffic": false,      "allowGatewayTransit": false,      "useRemoteGateways": false,          "remoteVirtualNetwork": {          "id": "[resourceId('Microsoft.ClassicNetwork/virtualNetworks', 'VNET2')]"  }      }      }  ]  }

2. A sablonfájl üzembe helyezéséhez futtassa a következő parancsmagot, amely létrehozza vagy frissíti az üzemelő példányt.

        New-AzureRmResourceGroupDeployment -ResourceGroupName MyResourceGroup -TemplateFile .\VnetPeering.json -DeploymentDebugLogLevel all

        Output shows:

        DeploymentName          : VnetPeering
        ResourceGroupName       : MyResourceGroup
        ProvisioningState       : Succeeded
        Timestamp               : XX/XX/YYYY 5:42:33 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
        Outputs                 :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

3. Az üzembe helyezés sikeres elvégzése után a társviszony állapotának megtekintéséhez futtathatja az alábbi parancsmagot:

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNET1 -ResourceGroupName MyResourceGroup -Name LinkToVNET2

        Output shows:

        Name                             : LinkToVNET2
        Id                               : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResource
                                   Group/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeering
                                   s/LinkToVNET2
        Etag                             : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/M
                                   yResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                   }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

Miután létrejött a társviszony a klasszikus virtuális hálózat és a Resource Manager-alapú virtuális hálózat között, a VNET1 bármely virtuális gépéről kapcsolatot kell, hogy tudjon kezdeményezni a VNET2 bármelyik virtuális gépére, illetve fordítva.



<!--HONumber=Sep16_HO4-->


