---
title: Az Azure belső terheléselosztó áthelyezése egy másik Azure-régióba az Azure PowerShell használatával
description: Az Azure Resource Manager-sablon használatával áthelyezi az Azure belső terheléselosztóját az egyik Azure-régióból a másikba az Azure PowerShell használatával
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: f8e431124155fe23853fe61e985fe4db522c3f77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644273"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-powershell"></a>Az Azure belső terheléselosztó áthelyezése egy másik régióba a PowerShell használatával

Vannak különböző forgatókönyvek, amelyben szeretné áthelyezni a meglévő belső terheléselosztó egyik régióból a másikba. Előfordulhat például, hogy egy belső terheléselosztót szeretne létrehozni ugyanazzal a konfigurációval a teszteléshez. Előfordulhat, hogy egy belső terheléselosztót is át szeretne helyezni egy másik régióba a vész-helyreállítási tervezés részeként.

Az Azure belső terheléselosztók nem helyezhetők át egyik régióból a másikba. Azonban egy Azure Resource Manager sablon használatával exportálhatja a belső terheléselosztó meglévő konfigurációját és virtuális hálózatát.  Ezután egy másik régióban elhelyezheti az erőforrást úgy, hogy a terheléselosztót és a virtuális hálózatot egy sablonba exportálja, módosítja a paramétereket a célrégiónak megfelelően, majd telepíti a sablonokat az új régióba.  Az Erőforrás-kezelőről és a sablonokról az [Erőforráscsoportok exportálása sablonokba című](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates) témakörben olvashat bővebben.


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az Azure belső terheléselosztó az Azure-régióban, ahonnan át szeretne helyezni.

- Az Azure belső terheléselosztók nem helyezhetők át a régiók között.  Az új terheléselosztót a célrégió erőforrásaihoz kell társítania.

- Belső terheléselosztó konfiguráció exportálásához és egy sablon központi telepítéséhez egy belső terheléselosztó létrehozásához egy másik régióban, szüksége lesz a Hálózati közreműködő szerepkörvagy magasabb.
   
- Azonosítsa a forráshálózati elrendezést és az összes jelenleg használt erőforrást. Ez az elrendezés magában foglalja, de nem kizárólagosan a terheléselosztók, hálózati biztonsági csoportok, virtuális gépek és a virtuális hálózatok.

- Ellenőrizze, hogy az Azure-előfizetés lehetővé teszi-e belső terheléselosztók létrehozását a használt célrégióban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

- Győződjön meg arról, hogy az előfizetés elegendő erőforrással rendelkezik a folyamat terheléselosztók hozzáadásának támogatásához.  Tekintse meg [az Azure előfizetési és szolgáltatáskorlátait, kvótáit és korlátozásait](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Felkészülés és mozgás
A következő lépések bemutatják, hogyan készítse elő a belső terheléselosztót az áthelyezéshez egy Resource Manager-sablon használatával, és helyezze át a belső terheléselosztó konfigurációját a célrégióba az Azure PowerShell használatával.  Ennek a folyamatnak a részeként a belső terheléselosztó virtuális hálózati konfigurációját is bele kell foglalni, és először el kell végezni, mielőtt áthelyezi a belső terheléselosztót.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-azure-powershell"></a>A virtuális hálózati sablon exportálása és üzembe helyezése az Azure PowerShellből

1. Jelentkezzen be Azure-előfizetésébe a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) paranccsal, és kövesse a képernyőn megjelenő utasításokat:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2.  Szerezze be annak a virtuális hálózatnak az erőforrásazonosítóját, amelyet a célterületre szeretne áthelyezni, és a [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)használatával változóba helyezni:

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. A forrásvirtuális hálózat exportálása .json fájlba abba a könyvtárba, amelyen az [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)parancsot futtatja:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. A letöltött fájl az erőforrás tanelemcsoportjáról lesz elnevezve.  Keresse meg az ** \<erőforráscsoport-név** nevű parancsból exportált fájlt>.json néven, és nyissa meg egy ön által választott szerkesztőben:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. A virtuális hálózat nevének módosításához módosítsa a forrásvirtuális hálózat nevének **alapértelmezett tulajdonságátA** virtuális forrásnevének neve a célvirtuális hálózat nevére, győződjön meg arról, hogy a név idézőjelek között van:
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  A virtuális hálózat áthelyezésének célrégiójának szerkesztéséhez módosítsa a **helytulajdonságot** az erőforrások alatt:

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
  
7. A régióhelykódok beszerzéséhez használhatja az Azure PowerShell-parancsmag [get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) a következő parancs futtatásával:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  Az ** \<erőforráscsoport-név>.json** fájlban más paramétereket is módosíthat, ha úgy dönt, és a követelményektől függően nem kötelező:

    * **Címtér** – A virtuális hálózat címtere a mentés előtt módosítható az **erőforrások** > **addressSpace** szakaszának módosításával és az ** \<erőforráscsoport név>.json** fájlban lévő **addressPrefixes** tulajdonság módosításával:

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

    * **Alhálózat** – Az alhálózat neve és az alhálózati címtér az ** \<erőforráscsoport-név>.json** fájl **alhálózati** szakaszának módosításával módosítható vagy hozzáadható. Az alhálózat neve a **névtulajdonság** módosításával módosítható. Az alhálózati címtér az ** \<erőforráscsoport-név>.json** **fájlcímprefix** tulajdonságának módosításával módosítható:

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

         Az ** \<erőforráscsoport-név>.json** fájlban a címelőtag módosításához két helyen kell módosítani, a fent felsorolt szakaszt és az alább felsorolt **típusszakaszt.**  Módosítsa a **addressPrefix** tulajdonságot a fentinek megfelelően:

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

9.  Mentse az ** \<erőforráscsoport-név>.json** fájlt.

10. Erőforráscsoport létrehozása a célrégióban a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) használatával telepítendő célvirtuális hálózatszámára
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Telepítse a szerkesztett ** \<erőforráscsoport-név>.json** fájlt az előző lépésben létrehozott erőforráscsoportba a [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)használatával:

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```
12. A célrégióban létrehozott erőforrások ellenőrzéséhez használja a [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) és a [Get-AzVirtualNetwork (Get-AzVirtualNetwork) csoportot:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```
### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>A belső terheléselosztó sablon exportálása és üzembe helyezése az Azure PowerShellből

1. Jelentkezzen be Azure-előfizetésébe a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) paranccsal, és kövesse a képernyőn megjelenő utasításokat:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Szerezze be a célterületre áthelyezni kívánt belső terheléselosztó erőforrásazonosítóját, és helyezze el egy változóban a [Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)használatával:

    ```azurepowershell-interactive
    $sourceIntLBID = (Get-AzLoadBalancer -Name <source-internal-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportálja a forrás belső terheléselosztó konfigurációját egy .json fájlba abba a könyvtárba, amelyen az [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)parancsot hajtja végre:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceIntLBID -IncludeParameterDefaultValue
   ```
4. A letöltött fájl az erőforrás tanelemcsoportjáról lesz elnevezve.  Keresse meg az ** \<erőforráscsoport-név** nevű parancsból exportált fájlt>.json néven, és nyissa meg egy ön által választott szerkesztőben:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. A belső terheléselosztó nevének módosításához módosítsa a forrás belső terheléselosztó nevének **defaultValue** tulajdonságát a cél belső terheléselosztó nevére, győződjön meg arról, hogy a név idézőjelek között van:

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
 
6. A fent áthelyezett célvirtuális hálózat értékének szerkesztéséhez először be kell szereznie az erőforrás-azonosítót, majd át kell másolnia és be kell illesztenie az ** \<erőforráscsoport-név>.json** fájlba.  Az azonosító beszerzéséhez használja a [Get-AzVirtualNetwork (Get-AzVirtualNetwork) (Get-AzVirtualNetwork) (Get-AzVirtualNetwork) (Get-AzVirtualNetwork) (Get](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)
   
   ```azurepowershell-interactive
    $targetVNETID = (Get-AzVirtualNetwork -Name <target-vnet-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Írja be a változót, és nyomja meg az Enter billentyűt az erőforrás-azonosító megjelenítéséhez.  Jelölje ki az azonosító elérési útját, és másolja a vágólapra:

    ```powershell
    PS C:\> $targetVNETID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupVNET-Move/providers/Microsoft.Network/virtualNetworks/myVNET2-Move
    ```

7.  Az ** \<erőforráscsoport neve>.json** fájlban illessze be az **erőforrás-azonosítót** a változóból a **defaultValue** helyére a célvirtuális hálózati azonosító második paraméterébe, győződjön meg arról, hogy az elérési utat idézőjelek közé foglalja:
   
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

8. A belső terheléselosztó konfigurációjának áthelyezésére irányuló célterület szerkesztéséhez módosítsa a **helytulajdonságot** az ** \<erőforráscsoport neve>.json** fájl **erőforrásai** alatt:

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

11. A régióhelykódok beszerzéséhez használhatja az Azure PowerShell-parancsmag [get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) a következő parancs futtatásával:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. Ha úgy dönt, módosíthatja a sablon egyéb paramétereit is, és a követelményektől függően nem kötelező:
    
    * **Sku** - A belső terheléselosztó termékkészletét a konfigurációban szabványosról alapszintűre vagy alapszintűre módosíthatja az ** \<erőforráscsoport-név>.json** fájlban lévő **sku** > **név** tulajdonság módosításával:

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
      Az alapszintű és a szabványos termékváltozat terheléselosztók közötti különbségekről az [Azure Standard Load Balancer áttekintése című témakörben olvashat bővebben.](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **Terheléselosztási szabályok** – A konfigurációban hozzáadhat vagy eltávolíthat terheléselosztási szabályokat, ha bejegyzéseket ad hozzá vagy távolít el az ** \<erőforráscsoport-név>.json** fájl **loadBalancingRules** szakaszához:

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
       A terheléselosztási szabályokról a [Mi az Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Mintavételek** – Hozzáadhat vagy eltávolíthat egy mintavételt a konfigurációban a terheléselosztóhoz, ha bejegyzéseket ad hozzá vagy távolít el az ** \<erőforráscsoport-név>.json** fájl **mintavételezési** szakaszában:

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
       Az Azure Load Balancer állapotmintáiról további információt a [Terheléselosztó állapotmintái című témakörben talál.](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **Bejövő NAT-szabályok** – A terheléselosztó bejövő NAT-szabályait úgy veheti fel vagy távolíthatja el, hogy bejegyzéseket ad hozzá vagy távolít el az ** \<erőforráscsoport-név>.json** fájl **bejövőNatRules** szakaszához:

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
        A bejövő NAT-szabály hozzáadásának vagy eltávolításának befejezéséhez a szabálynak az ** \<erőforráscsoport-név>.json** fájl végén jelen lévőnek vagy **típustulajdonságként** kell lennie:

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
        A bejövő NAT-szabályokról a [Mi az Azure Load Balancer című](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) témakörben talál további információt?
    
13. Mentse az ** \<erőforráscsoport-név>.json** fájlt.
    
10. Hozzon létre vagy erőforráscsoportot hozzon létre a célrégióban a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)használatával telepítendő célbelső terheléselosztóhoz. A meglévő erőforráscsoport felülről is újra felhasználható a folyamat részeként:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Telepítse a szerkesztett ** \<erőforráscsoport-név>.json** fájlt az előző lépésben létrehozott erőforráscsoportba a [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)használatával:

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Az erőforrások célrégióban való létrehozásának ellenőrzéséhez használja a [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) és a [Get-AzLoadBalancer csoportot:](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Elvetés 

A telepítés után, ha szeretné újrakezdeni, vagy elveti a virtuális hálózat és a terheléselosztó a cél, törölje a cél létrehozott erőforráscsoportot, és az áthelyezett virtuális hálózat és a terheléselosztó törlődik.  Az erőforráscsoport eltávolításához használja az [Eltávolítás-AzResourceGroup csoportot:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A módosítások véglegesítéséhez és az NSG áthelyezésének befejezéséhez törölje a forrás NSG-t vagy az erőforráscsoportot, használja az [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) vagy [az Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0) és [az Remove-AzLoadBalancer parancsot.](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzVirtualNetwork -Name <virtual-network-name> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban áthelyezett egy Azure belső terheléselosztó egyik régióból a másikba, és megtisztította a forráserőforrásokat.  Ha többet szeretne tudni az erőforrások régiók közötti áthelyezéséről és az Azure-beli vészhelyreállításról:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
