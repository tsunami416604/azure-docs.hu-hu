---
title: Az Azure külső terheléselosztó áthelyezése egy másik Azure-régióba az Azure PowerShell használatával
description: Az Azure Resource Manager-sablonnal áthelyezhető az Azure külső terheléselosztója az egyik Azure-régióból a másikba az Azure PowerShell használatával.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: a24eb4608e7630d5b613751fa2120361eccd7672
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644817"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-azure-powershell"></a>Az Azure külső terheléselosztó áthelyezése egy másik régióba az Azure PowerShell használatával

Vannak különböző forgatókönyvek, amelyekben szeretné áthelyezni a meglévő külső terheléselosztó egyik régióból a másikba. Előfordulhat például, hogy egy külső terheléselosztót szeretne létrehozni ugyanazzal a konfigurációval a teszteléshez. Előfordulhat, hogy egy külső terheléselosztót is át szeretne helyezni egy másik régióba a vész-helyreállítási tervezés részeként.

Az Azure külső terheléselosztók nem helyezhetők át egyik régióból a másikba. Azonban egy Azure Resource Manager sablon használatával exportálhatja a meglévő konfigurációt és a külső terheléselosztó nyilvános IP-címét.  Ezután egy másik régióban is elhelyezheti az erőforrást a terheléselosztó és a nyilvános IP-cím sablonba exportálásával, a paraméterek módosításával a célrégiónak megfelelően, majd telepítheti a sablonokat az új régióba.  Az Erőforrás-kezelőről és a sablonokról az [Erőforráscsoportok exportálása sablonokba című](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates) témakörben olvashat bővebben.


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az Azure külső terheléselosztó az Azure-régióban, ahonnan át szeretne helyezni.

- Az Azure külső terheléselosztók nem helyezhetők át a régiók között.  Az új terheléselosztót a célrégió erőforrásaihoz kell társítania.

- Külső terheléselosztó konfigurációjának exportálásához és egy sablon központi telepítéséhez egy külső terheléselosztó létrehozásához egy másik régióban, szüksége van a Hálózati közreműködő szerepkörre vagy magasabb ra.
   
- Azonosítsa a forráshálózati elrendezést és az összes jelenleg használt erőforrást. Ez az elrendezés tartalmazza, de nem korlátozódik a terheléselosztók, a hálózati biztonsági csoportok, a nyilvános IP-k és a virtuális hálózatok.

- Győződjön meg arról, hogy az Azure-előfizetés lehetővé teszi, hogy külső terheléselosztók a célrégióban, amely a használt. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

- Győződjön meg arról, hogy az előfizetés elegendő erőforrással rendelkezik a folyamat terheléselosztók hozzáadásának támogatásához.  Tekintse meg [az Azure előfizetési és szolgáltatáskorlátait, kvótáit és korlátozásait](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Felkészülés és mozgás
A következő lépések bemutatják, hogyan készítse elő a külső terheléselosztót az áthelyezéshez egy Resource Manager-sablon használatával, és helyezze át a külső terheléselosztó konfigurációját a célrégióba az Azure PowerShell használatával.  Ennek a folyamatnak a részeként a külső terheléselosztó nyilvános IP-konfigurációját is bele kell foglalni, és először nekem kell elvégezni, mielőtt áthelyezi a külső terheléselosztót.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-azure-powershell"></a>A nyilvános IP-sablon exportálása és üzembe helyezése az Azure PowerShellből

1. Jelentkezzen be Azure-előfizetésébe a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) paranccsal, és kövesse a képernyőn megjelenő utasításokat:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2. Szerezze be a célterületre áthelyezni kívánt nyilvános IP-cím erőforrásazonosítóját, és helyezze el egy változóban a [Get-AzPublicIPAddress használatával:](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportálja a forrás nyilvános IP-címét egy .json fájlba abba a könyvtárba, amelyen az [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)parancsot futtatja:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. A letöltött fájl az erőforrás tanelemcsoportjáról lesz elnevezve.  Keresse meg az ** \<erőforráscsoport-név** nevű parancsból exportált fájlt>.json néven, és nyissa meg egy ön által választott szerkesztőben:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. A nyilvános IP-név paraméterének szerkesztéséhez módosítsa a forrás nyilvános IP-nevének **alapértelmezett tulajdonságátA** nyilvános IP-cím értékén a nyilvános célIP nevére, győződjön meg arról, hogy a név idézőjelek között van:
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. A nyilvános IP-címet áthelyező célrégió szerkesztéséhez módosítsa a **helytulajdonságot** az erőforrások alatt:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. A régióhelykódok beszerzéséhez használhatja az Azure PowerShell-parancsmag [get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) a következő parancs futtatásával:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Ha úgy dönt, módosíthatja a sablon egyéb paramétereit is, és a követelményektől függően nem kötelező:

    * **Sku** - Módosíthatja a nyilvános IP-cím sku a konfiguráció szabványos alapszintű vagy alapszintű szabványos megváltoztatásával a **sku** > **név** tulajdonság az ** \<erőforrás-csoport-név>.json** fájl:

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         Az alapszintű és a szabványos sku nyilvános ips közötti különbségekről a [Nyilvános IP-cím létrehozása, módosítása vagy törlése](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)című témakörben talál további információt.

    * **Nyilvános IP-foglalási módszer** és **tétlen időtúlszállás** – A sablon mindkét beállítását módosíthatja a **publicIPAllocationMethod** tulajdonság **dinamikusról** **statikusra** vagy **statikusra** dinamikusra **dinamikusra.** Az alapjárati időhosszabbítás az **idleTimeoutInMinutes** tulajdonság kívánt értékre történő módosításával módosítható.  Az alapértelmezett érték **4:**

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        A felosztási módszerekről és az alapjárati időtúlterhelési értékekről a [Nyilvános IP-cím létrehozása, módosítása vagy törlése](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)című témakörben talál további információt.


9. Mentse az ** \<erőforráscsoport-név>.json** fájlt.

10. Hozzon létre egy erőforráscsoportot a célrégióban a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)használatával telepítendő nyilvános célIP-címhez.
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Telepítse a szerkesztett ** \<erőforráscsoport-név>.json** fájlt az előző lépésben létrehozott erőforráscsoportba a [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)használatával:

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. A célrégióban létrehozott erőforrások ellenőrzéséhez használja a [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) és a [Get-AzPublicIPAddress címet:](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

### <a name="export-the-external-load-balancer-template-and-deploy-from-azure-powershell"></a>A külső terheléselosztó sablon exportálása és üzembe helyezése az Azure PowerShellből

1. Jelentkezzen be Azure-előfizetésébe a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) paranccsal, és kövesse a képernyőn megjelenő utasításokat:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Szerezze be a célterületre áthelyezni kívánt külső terheléselosztó erőforrásazonosítóját, és helyezze el egy változóban a [Get-AzLoadBalancer használatával:](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceExtLBID = (Get-AzLoadBalancer -Name <source-external-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportálja a forrás külső terheléselosztó konfigurációját egy .json fájlba abba a könyvtárba, amelyen az [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)parancsot hajtja végre:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceExtLBID -IncludeParameterDefaultValue
   ```
4. A letöltött fájl az erőforrás tanelemcsoportjáról lesz elnevezve.  Keresse meg az ** \<erőforráscsoport-név** nevű parancsból exportált fájlt>.json néven, és nyissa meg egy ön által választott szerkesztőben:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. A külső terheléselosztó nevének módosításához módosítsa a forrás külső terheléselosztó nevének **defaultValue** tulajdonságát a cél külső terheléselosztó nevére, győződjön meg arról, hogy a név idézőjelek között van:

    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "loadBalancers_myLoadbalancer_ext_name": {
        "defaultValue": "<target-external-lb-name>",
        "type": "String"
            },
        "publicIPAddresses_myPubIP_in_externalid": {
        "defaultValue": "<target-publicIP-resource-ID>",
        "type": "String"
            },

    ```

6.  A fenti nyilvános célIP-cím értékének szerkesztéséhez először be kell szereznie az erőforrás-azonosítót, majd át kell másolnia és be kell illesztenie az ** \<erőforráscsoport-név>.json** fájlba.  Az azonosító beszerzéséhez használja a [Get-AzPublicIPAddress címet:](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)

    ```azurepowershell-interactive
    $targetPubIPID = (Get-AzPublicIPaddress -Name <target-public-ip-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Írja be a változót, és nyomja meg az Enter billentyűt az erőforrás-azonosító megjelenítéséhez.  Jelölje ki az azonosító elérési útját, és másolja a vágólapra:

    ```powershell
    PS C:\> $targetPubIPID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupLB-Move/providers/Microsoft.Network/publicIPAddresses/myPubIP-in-move
    ```

7.  Az ** \<erőforráscsoport neve>.json** fájlban illessze be az **erőforrás-azonosítót** a változóból a **defaultValue** helyére a nyilvános IP külső azonosító második paraméterébe, győződjön meg arról, hogy az elérési utat idézőjelek közé foglalja:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "loadBalancers_myLoadbalancer_ext_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
            },
            "publicIPAddresses_myPubIP_in_externalid": {
            "defaultValue": "<target-publicIP-resource-ID>",
            "type": "String"
            },

    ```

8.  Ha konfigurált kimenő NAT és kimenő szabályokat a terheléselosztóhoz, akkor egy harmadik bejegyzés jelenik meg ebben a fájlban a kimenő nyilvános IP külső azonosítójának.  Ismételje meg a fenti lépéseket a **célrégióban** a kimenő nyilvános iP azonosítójának beszerzéséhez, és illessze be azt a bejegyzést az ** \<erőforráscsoport nevéhez>.json** fájlba:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "defaultValue": "<target-external-lb-name>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "defaultValue": "<target-publicIP-resource-ID>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",
                "type": "String"
            }
        },
    ```

10. A külső terheléselosztó konfigurációjának áthelyezéséhez szükséges célrégió szerkesztéséhez módosítsa a **helytulajdonságot** az ** \<erőforráscsoport neve>.json** fájl **erőforrásai** alatt:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
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
    
    * **Sku** - Módosíthatja a külső terheléselosztó termékkészletét a konfigurációban szabványosról alapszintűre vagy alapszintűre szabványosra az ** \<erőforráscsoport-név>.json** fájlban lévő **sku** > **név** tulajdonság módosításával:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
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

    * **Kimenő szabályok** – A konfigurációban hozzáadhat vagy eltávolíthat kimenő szabályokat az ** \<erőforráscsoport-név>.json** fájl **outboundRules** tulajdonságának szerkesztésével:

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         A kimenő szabályokról további információt a [Terheléselosztó kimenő szabályai című](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview) témakörben talál.

13. Mentse az ** \<erőforráscsoport-név>.json** fájlt.
    
10. Hozzon létre vagy erőforráscsoportot hozzon létre a célrégióban a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)használatával telepítendő célkülső terheléselosztóhoz. A meglévő erőforráscsoport felülről is újra felhasználható a folyamat részeként:
    
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

A központi telepítés után, ha szeretné újrakezdeni, vagy dobja el a nyilvános IP-cím és a terheléselosztó a célban, törölje a célban létrehozott erőforráscsoportot, és az áthelyezett nyilvános IP és terheléselosztó törlődik.  Az erőforráscsoport eltávolításához használja az [Eltávolítás-AzResourceGroup csoportot:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A módosítások véglegesítéséhez és az NSG áthelyezésének befejezéséhez törölje a forrás NSG-t vagy az erőforráscsoportot, használja az [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) vagy [az Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0) és [az Remove-AzLoadBalancer parancsot.](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzPublicIpAddress -Name <public-ip> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban áthelyezett egy Azure-hálózati biztonsági csoportot egyik régióból a másikba, és megtisztította a forráserőforrásokat.  Ha többet szeretne tudni az erőforrások régiók közötti áthelyezéséről és az Azure-beli vészhelyreállításról:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
