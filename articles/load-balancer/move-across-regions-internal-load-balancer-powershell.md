---
title: Az Azure belső Load Balancer áthelyezése másik Azure-régióba Azure PowerShell
description: Azure Resource Manager sablon használatával áthelyezheti az Azure belső Load Balancer egyik Azure-régióból a másikba Azure PowerShell
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 52a43dff5d2e740633675b71d5177d0df876d3cd
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71092208"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-powershell"></a>Azure belső Load Balancer áthelyezése másik régióba a PowerShell használatával

Különböző helyzetekben érdemes áthelyezni a meglévő belső terheléselosztó egyik régióból a másikba. Előfordulhat például, hogy egy belső terheléselosztó-t szeretne létrehozni ugyanazzal a konfigurációval a teszteléshez. A vész-helyreállítási tervezés részeként érdemes áthelyezni egy belső terheléselosztó egy másik régióba is.

Az Azure belső terheléselosztó nem helyezhető át egyik régióból a másikba. A belső terheléselosztó meglévő konfigurációjának és virtuális hálózatának exportálásához azonban Azure Resource Manager sablont is használhat.  Ezt követően az erőforrást egy másik régióban is elvégezheti, ha a terheléselosztó és a virtuális hálózat sablonba való exportálásával módosítja a paramétereket, hogy azok megfeleljenek a célként megadott régiónak, majd telepítse a sablonokat az új régióba.  A Resource Managerrel és a sablonokkal kapcsolatos további információkért lásd: [erőforráscsoportok exportálása sablonokba](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az Azure belső terheléselosztó abban az Azure-régióban található, amelyről át kívánja helyezni.

- Az Azure belső terheléselosztó nem helyezhetők át a régiók között.  Az új Load balancert a célként megadott régióban lévő erőforrásokhoz kell rendelnie.

- A belső terheléselosztó konfigurációjának exportálásához és egy olyan sablon üzembe helyezéséhez, amely egy belső terheléselosztó egy másik régióban való létrehozásához szükséges, szüksége lesz a hálózati közreműködő szerepkörre vagy magasabbra.
   
- Azonosítsa a forrás hálózatkezelési elrendezést és az összes éppen használt erőforrást. Ez az elrendezés magában foglalja, de nem korlátozódik a terheléselosztó, a hálózati biztonsági csoportok, a virtuális gépek és a virtuális hálózatok számára.

- Ellenőrizze, hogy az Azure-előfizetése lehetővé teszi-e belső terheléselosztó létrehozását a használt célcsoportban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

- Ellenőrizze, hogy az előfizetése rendelkezik-e elegendő erőforrással a folyamathoz tartozó terheléselosztó hozzáadásának támogatásához.  Tekintse meg az [Azure-előfizetések és-szolgáltatások korlátozásait, kvótáit és korlátozásait](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Előkészítés és áthelyezés
A következő lépések bemutatják, hogyan készítse elő a belső terheléselosztó az áthelyezéshez Resource Manager-sablonnal, és a belső terheléselosztó konfigurációját helyezze át a célként megadott régióba Azure PowerShell használatával.  Ennek a folyamatnak a részeként a belső terheléselosztó virtuális hálózati konfigurációját is tartalmaznia kell, és először a belső Load Balancer áthelyezése előtt kell elvégezni.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-azure-powershell"></a>A virtuális hálózati sablon exportálása és üzembe helyezése Azure PowerShell

1. Jelentkezzen be az Azure-előfizetésbe a [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) paranccsal, és kövesse a képernyőn megjelenő utasításokat:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2.  Szerezze be annak a virtuális hálózatnak az erőforrás-AZONOSÍTÓját, amelyet át szeretne helyezni a célként megadott régióba, és helyezze egy változóba a [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)használatával:

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportálja a forrásként szolgáló virtuális hálózatot egy. JSON-fájlba abba a könyvtárba, amelyben az [export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)parancsot futtatja:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. A letöltött fájl neve annak az erőforrás-csoportnak az alapján lesz elnevezve, amelyből az erőforrást exportálták.  Keresse meg az  **\<erőforrás-csoport-név >. JSON** nevű parancsból exportált fájlt, és nyissa meg egy tetszőleges szerkesztőben:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. A virtuális hálózat nevének a paraméterének szerkesztéséhez módosítsa a forrás virtuális hálózat **nevének tulajdonságát** a célként megadott virtuális hálózat nevére, és győződjön meg arról, hogy a név idézőjelek közé esik:
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  Ha módosítani szeretné a VNET áthelyezni kívánt célpontot, módosítsa a **Location (hely** ) tulajdonságot az erőforrások területen.

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```
  
7. A régióbeli hely kódjának beszerzéséhez használja a [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) Azure PowerShell parancsmagot a következő parancs futtatásával:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  **Az\<erőforrás-csoport neve >. JSON** fájlban más paramétereket is megadhat, ha Ön a következőt választja, és a követelményektől függően nem kötelező:

    * **Címterület** – a VNET a Mentés előtt módosítható, ha módosítja az **erőforrások** > **addressSpace** szakaszt, és megváltoztatja a **addressPrefixes** tulajdonságot a  **\< erőforrás-csoport – név >. JSON** fájl:

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **Alhálózat** – az alhálózat neve és az alhálózati címtartomány módosítható, vagy hozzáadható az  **\<erőforrás-csoport neve >. JSON** fájl **alhálózatok** szakaszának módosításával. Az alhálózat neve módosítható a **Name** tulajdonság megváltoztatásával. Az alhálózati címtartomány módosítható úgy, hogy módosítja a **addressPrefix** tulajdonságot az  **\<erőforrás-csoport neve >. JSON** fájlban:

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

         Az **Erőforrás-csoport neve >. JSON fájlban a címzési előtag módosításához két helyen kell szerkesztenie, a fent felsorolt szakaszt és az alább felsorolt típusok szakaszát. \<**  Módosítsa a **addressPrefix** tulajdonságot úgy, hogy az megfeleljen a fentieknek:

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

9.  Mentse az  **\<erőforrás-csoport neve >. JSON** fájlt.

10. Hozzon létre egy erőforráscsoportot a VNET a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) használatával telepítendő célként megadott régióban.
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Telepítse a szerkesztett  **\<erőforrás-csoport neve >. JSON** fájlt az előző lépésben létrehozott erőforráscsoporthoz a [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)használatával:

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```
12. A cél régióban létrehozott erőforrások ellenőrzéséhez használja a [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) és a [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```
### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>A belső terheléselosztó sablon exportálása és üzembe helyezése Azure PowerShell

1. Jelentkezzen be az Azure-előfizetésbe a [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) paranccsal, és kövesse a képernyőn megjelenő utasításokat:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Szerezze be annak a belső terheléselosztó erőforrás-AZONOSÍTÓját, amelyet át szeretne helyezni a célként megadott régióba, és helyezze egy változóba a [Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)használatával:

    ```azurepowershell-interactive
    $sourceIntLBID = (Get-AzLoadBalancer -Name <source-internal-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportálja a forrás belső terheléselosztó konfigurációját egy. JSON-fájlba abba a könyvtárba, amelybe az [export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)parancsot futtatja:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceIntLBID -IncludeParameterDefaultValue
   ```
4. A letöltött fájl neve annak az erőforrás-csoportnak az alapján lesz elnevezve, amelyből az erőforrást exportálták.  Keresse meg az  **\<erőforrás-csoport-név >. JSON** nevű parancsból exportált fájlt, és nyissa meg egy tetszőleges szerkesztőben:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. A belső terheléselosztó nevének a paraméterének szerkesztéséhez módosítsa a forrás belső terheléselosztó **neve tulajdonságát** a cél belső terheléselosztó nevére, győződjön meg arról, hogy a név idézőjelek közé esik:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```
 
6. A fent áthelyezett cél virtuális hálózat értékének szerkesztéséhez először be kell szereznie az erőforrás-azonosítót, majd be kell másolnia és beillesztenie az  **\<erőforrás-csoport neve >. JSON** fájlba.  Az azonosító beszerzéséhez használja a [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
   
   ```azurepowershell-interactive
    $targetVNETID = (Get-AzVirtualNetwork -Name <target-vnet-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Írja be a változót, és nyomja meg az ENTER billentyűt az erőforrás-azonosító megjelenítéséhez.  Jelölje ki az azonosító elérési útját, és másolja a vágólapra:

    ```powershell
    PS C:\> $targetVNETID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupVNET-Move/providers/Microsoft.Network/virtualNetworks/myVNET2-Move
    ```

7.  Az erőforrás- **csoport neve >. JSON fájlban illessze be az erőforrás-azonosítót a második paraméterben található "defaultValue" érték helyett a cél virtuális hálózati azonosítóhoz, és győződjön meg arról, hogy az elérési utat idézőjelek közé helyezi \<** :
   
    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

8. Ha módosítani szeretné a belső terheléselosztó konfigurációját áthelyező célhelyet, akkor módosítsa a **Location** (hely ) tulajdonságot az  **\<erőforrás-csoport neve >. JSON** fájljában:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

11. A régióbeli hely kódjának beszerzéséhez használja a [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) Azure PowerShell parancsmagot a következő parancs futtatásával:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. A sablon egyéb paramétereit is módosíthatja, és a követelményektől függően választható:
    
    * **SKU** – módosíthatja a belső terheléselosztó SKU-jának konfigurációját a standard és az alap közötti értékről a standard értékre, ha megváltoztatja az **SKU** > **Name** tulajdonságot az **\<erőforrás-csoport neve >. JSON fájlban.** fájl:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Az alapszintű és standard SKU-terheléselosztó közötti különbségekről az [Azure standard Load Balancer áttekintése](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) című témakörben olvashat bővebben.

    * Terheléselosztási **szabályok** – a konfigurációban hozzáadhat vagy eltávolíthat terheléselosztási szabályokat, ha bejegyzéseket ad hozzá vagy távolít el az  **\<erőforrás-csoport neve >. JSON** fájl **loadBalancingRules** részéhez:

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       További információ a terheléselosztási szabályokról: [Mi az Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Mintavételek – hozzáadhat** vagy eltávolíthat egy mintavételt a terheléselosztó számára a konfigurációban, ha bejegyzéseket ad hozzá vagy távolít el az  **\<erőforrás-csoport neve >. JSON** fájl mintavételek **szakaszához** :

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Azure Load Balancer állapot-mintavételsel kapcsolatos további információkért lásd: [Load Balancer Health Szondák](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **Bejövő NAT-szabályok** – a terheléselosztó bejövő NAT-szabályainak hozzáadásához vagy eltávolításához adja hozzá a bejegyzéseket az  **\<erőforráscsoport-név >. JSON** fájl **inboundNatRules** szakaszához:

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        Egy bejövő NAT-szabály hozzáadásának vagy eltávolításának befejezéséhez a szabálynak jelen kell lennie, vagy el kell távolítania az  **\<erőforrás-csoport neve >. JSON** fájl végén található **Type (típus** ) tulajdonságként:

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        További információ a bejövő NAT-szabályokról: [Mi az Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)
    
13. Mentse az  **\<erőforrás-csoport neve >. JSON** fájlt.
    
10. Hozzon létre vagy egy erőforráscsoportot a cél régióban ahhoz, hogy a cél belső terheléselosztó a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)használatával legyen üzembe helyezhető. A fent említett meglévő erőforráscsoport a folyamat részeként is újra felhasználható:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Telepítse a szerkesztett  **\<erőforrás-csoport neve >. JSON** fájlt az előző lépésben létrehozott erőforráscsoporthoz a [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)használatával:

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. A cél régióban létrehozott erőforrások ellenőrzéséhez használja a [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) és a [Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Elvetés 

Ha a központi telepítés után szeretné elindítani vagy elvetni a virtuális hálózatot és a terheléselosztó-t a célhelyen, törölje a célhelyen létrehozott erőforráscsoportot, és az áthelyezett virtuális hálózatot és a terheléselosztó törlődni fog.  Az erőforráscsoport eltávolításához használja a [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A módosítások végrehajtásához és a NSG áthelyezésének befejezéséhez törölje a forrás NSG vagy az erőforráscsoportot, használja a [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) vagy a [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0) és a [Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzVirtualNetwork -Name <virtual-network-name> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy belső Azure Load balancert helyezett át egyik régióból a másikba, és megtisztította a forrás erőforrásait.  Ha többet szeretne megtudni a régiók és a vész-helyreállítás között az Azure-ban, tekintse meg a következőt:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
