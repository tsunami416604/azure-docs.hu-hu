---
title: Az Azure külső Load Balancer áthelyezése másik Azure-régióba Azure PowerShell
description: Azure Resource Manager sablon használatával áthelyezheti az Azure külső Load Balancer az egyik Azure-régióból a másikba Azure PowerShell használatával.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: d8dfbf3f86a2233571a99c4ad832ef7bd3c3ed48
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077580"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-azure-powershell"></a>Az Azure külső Load Balancer áthelyezése másik régióba Azure PowerShell

Különböző helyzetekben érdemes áthelyezni a meglévő külső terheléselosztó egyik régióból a másikba. Előfordulhat például, hogy létre szeretne hozni egy külső terheléselosztó-t ugyanazzal a konfigurációval a teszteléshez. Előfordulhat, hogy a vész-helyreállítási tervezés részeként másik régióba is át szeretné helyezni a külső terheléselosztó-t.

Az Azure külső Load Balancer nem helyezhető át egyik régióból a másikba. A külső terheléselosztó meglévő konfigurációjának és nyilvános IP-címének exportálásához azonban Azure Resource Manager sablont is használhat.  Az erőforrást egy másik régióban is elvégezheti, ha a terheléselosztó és a nyilvános IP-címet egy sablonba exportálja, módosítja a paramétereket, hogy azok megfeleljenek a célként megadott régiónak, majd telepítse a sablonokat az új régióba.  A Resource Managerrel és a sablonokkal kapcsolatos további információkért lásd: [erőforráscsoportok exportálása sablonokba](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az Azure külső terheléselosztó abban az Azure-régióban található, amelyről át kívánja helyezni.

- Az Azure külső terheléselosztó nem helyezhető át a régiók között.  Az új Load balancert a célként megadott régióban lévő erőforrásokhoz kell rendelnie.

- Külső terheléselosztó konfigurációjának exportálásához és sablon üzembe helyezéséhez külső terheléselosztó egy másik régióban való létrehozásához szüksége lesz a hálózati közreműködő szerepkörre vagy magasabbra.
   
- Azonosítsa a forrás hálózatkezelési elrendezést és az összes éppen használt erőforrást. Ez az elrendezés tartalmaz, de nem korlátozódik a terheléselosztó, a hálózati biztonsági csoportok, a nyilvános IP-címek és a virtuális hálózatok számára.

- Ellenőrizze, hogy az Azure-előfizetése lehetővé teszi-e külső terheléselosztó létrehozását a használt célcsoportban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

- Ellenőrizze, hogy az előfizetése rendelkezik-e elegendő erőforrással a folyamathoz tartozó terheléselosztó hozzáadásának támogatásához.  Tekintse meg az [Azure-előfizetések és-szolgáltatások korlátozásait, kvótáit és korlátozásait](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Előkészítés és áthelyezés
A következő lépések bemutatják, hogyan készítse elő a külső terheléselosztó az áthelyezéshez Resource Manager-sablonnal, és a külső terheléselosztó konfigurációját helyezze át a célként megadott régióba Azure PowerShell használatával.  Ennek a folyamatnak a részeként a külső terheléselosztó nyilvános IP-konfigurációját is tartalmaznia kell, és először a külső Load Balancer áthelyezése előtt kell elvégezni.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-azure-powershell"></a>A nyilvános IP-sablon exportálása és üzembe helyezése Azure PowerShell

1. Jelentkezzen be az Azure-előfizetésbe a [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) paranccsal, és kövesse a képernyőn megjelenő utasításokat:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2. Szerezze be annak a nyilvános IP-nek az erőforrás-AZONOSÍTÓját, amelyet át szeretne helyezni a célként megadott régióba, és helyezze egy változóba a [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)használatával:

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportálja a forrás nyilvános IP-címet egy. JSON-fájlba abba a könyvtárba, amelybe az [export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)parancsot futtatja:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. A letöltött fájl neve annak az erőforrás-csoportnak az alapján lesz elnevezve, amelyből az erőforrást exportálták.  Keresse meg az  **\<erőforrás-csoport-név >. JSON** nevű parancsból exportált fájlt, és nyissa meg egy tetszőleges szerkesztőben:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. A nyilvános IP-cím paraméterének szerkesztéséhez módosítsa a forrás nyilvános IP-cím tulajdonságának **alapértelmezett** értékét a célként megadott nyilvános IP-cím nevére, győződjön meg arról, hogy a név idézőjelek közé esik:
    
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

6. A nyilvános IP-címet áthelyező cél régió szerkesztéséhez módosítsa a **Location (hely** ) tulajdonságot az erőforrások területen.

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
  
7. A régióbeli hely kódjának beszerzéséhez használja a [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) Azure PowerShell parancsmagot a következő parancs futtatásával:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. A sablon egyéb paramétereit is módosíthatja, és a követelményektől függően választható:

    * **SKU** – a konfigurációban a nyilvános IP-cím SKU-jának a standard és az alap közötti értékről a standard típusra módosítható úgy, hogy az  **\<erőforrás-csoport neve >. JSON** fájlban módosítja az **SKU** > **Name** tulajdonságot:

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

         Az alapszintű és a standard SKU nyilvános IP-címei közötti különbségekkel kapcsolatos további információkért lásd: [nyilvános IP-cím létrehozása, módosítása vagy törlése](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

    * **Nyilvános IP-kiosztási módszer** és **Üresjárati időkorlát** – a sablon mindkét beállítását módosíthatja úgy, hogy a **PublicIPAllocationMethod** tulajdonságot **dinamikusról** **statikusra** vagy statikusra változtatja **a** dinamikus értékre. Az Üresjárat időkorlátja módosítható úgy, hogy módosítja a **idleTimeoutInMinutes** tulajdonságot a kívánt értékre.  Az alapértelmezett érték **4**:

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

        A kiosztási módszerekkel és az üresjárati időtúllépési értékekkel kapcsolatos további információkért lásd: [nyilvános IP-cím létrehozása, módosítása vagy törlése](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).


9. Mentse az  **\<erőforrás-csoport neve >. JSON** fájlt.

10. Hozzon létre egy erőforráscsoportot a céltartományban a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)használatával telepítendő cél nyilvános IP-címhez.
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Telepítse a szerkesztett  **\<erőforrás-csoport neve >. JSON** fájlt az előző lépésben létrehozott erőforráscsoporthoz a [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)használatával:

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. A cél régióban létrehozott erőforrások ellenőrzéséhez használja a [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) és a [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

### <a name="export-the-external-load-balancer-template-and-deploy-from-azure-powershell"></a>A külső terheléselosztó sablon exportálása és üzembe helyezése Azure PowerShell

1. Jelentkezzen be az Azure-előfizetésbe a [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) paranccsal, és kövesse a képernyőn megjelenő utasításokat:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Szerezze be annak a külső terheléselosztó erőforrás-AZONOSÍTÓját, amelyet át szeretne helyezni a célként megadott régióba, és helyezze egy változóba a [Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)használatával:

    ```azurepowershell-interactive
    $sourceExtLBID = (Get-AzLoadBalancer -Name <source-external-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportálja a forrás külső terheléselosztó konfigurációját egy. JSON-fájlba abba a könyvtárba, amelybe az [export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)parancsot futtatja:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceExtLBID -IncludeParameterDefaultValue
   ```
4. A letöltött fájl neve annak az erőforrás-csoportnak az alapján lesz elnevezve, amelyből az erőforrást exportálták.  Keresse meg az  **\<erőforrás-csoport-név >. JSON** nevű parancsból exportált fájlt, és nyissa meg egy tetszőleges szerkesztőben:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. A külső terheléselosztó nevének a paraméterének szerkesztéséhez módosítsa a forrás külső terheléselosztó **neve tulajdonságát** a cél külső terheléselosztó nevére, és ellenőrizze, hogy a név idézőjelek közé esik-e:

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

6.  A fent áthelyezett cél nyilvános IP-cím értékének szerkesztéséhez először be kell szereznie az erőforrás-azonosítót, majd be kell másolnia és beillesztenie az  **\<erőforrás-csoport neve >. JSON** fájlba.  Az azonosító beszerzéséhez használja a [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):

    ```azurepowershell-interactive
    $targetPubIPID = (Get-AzPublicIPaddress -Name <target-public-ip-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Írja be a változót, és nyomja meg az ENTER billentyűt az erőforrás-azonosító megjelenítéséhez.  Jelölje ki az azonosító elérési útját, és másolja a vágólapra:

    ```powershell
    PS C:\> $targetPubIPID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupLB-Move/providers/Microsoft.Network/publicIPAddresses/myPubIP-in-move
    ```

7.  Az erőforrás- **csoport neve >. JSON fájlban illessze be az erőforrás-azonosítót a második paraméterben található "defaultValue" érték helyett a nyilvános IP-címhez tartozó külső azonosítóhoz, és győződjön meg arról, hogy az elérési utat idézőjelek közé helyezi \<** :

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

8.  Ha a terheléselosztó kimenő NAT-és kimenő szabályait konfigurálta, akkor a rendszer egy harmadik bejegyzést fog tartalmazni ebben a fájlban a kimenő nyilvános IP-címhez tartozó külső AZONOSÍTÓhoz.  Ismételje meg a fenti lépéseket a **cél régióban** a kimenő nyilvános IP-cím azonosítójának beszerzéséhez, és illessze be a bejegyzést az  **\<erőforrás-csoport-név >. JSON** fájlba:

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

10. A külső terheléselosztó konfigurációját áthelyező célobjektum módosításához módosítsa a **Location** tulajdonságot az  **\<erőforrás-csoport neve >. JSON** fájlban:

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

11. A régióbeli hely kódjának beszerzéséhez használja a [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) Azure PowerShell parancsmagot a következő parancs futtatásával:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. A sablon egyéb paramétereit is módosíthatja, és a követelményektől függően választható:
    
    * **SKU** – a külső Load Balancer a konfigurációban a standard és az alapszintű értékről a standard > értékre való módosításával módosíthatja az **SKU** **Name** tulajdonságot az **\<erőforrás-csoport neve >. JSON fájlban.** fájl:

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

    * **Kimenő szabályok** – a konfigurációban hozzáadhat vagy eltávolíthat kimenő szabályokat a **outboundRules** tulajdonság szerkesztésével az  **\<erőforrás-csoport neve >. JSON** fájlban:

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

         További információ a kimenő szabályokról: [Load Balancer kimenő szabályok](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)

13. Mentse az  **\<erőforrás-csoport neve >. JSON** fájlt.
    
10. Hozzon létre vagy egy erőforráscsoportot a cél régióban ahhoz, hogy a cél külső terheléselosztó a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)használatával legyen üzembe helyezhető. A fent említett meglévő erőforráscsoport a folyamat részeként is újra felhasználható:
    
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

Ha a központi telepítés után szeretné elindítani vagy elvetni a nyilvános IP-címet és a terheléselosztó-t a célhelyen, törölje a célhelyen létrehozott erőforráscsoportot, és az áthelyezett nyilvános IP-címet és terheléselosztó törölve lesz.  Az erőforráscsoport eltávolításához használja a [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A módosítások végrehajtásához és a NSG áthelyezésének befejezéséhez törölje a forrás NSG vagy az erőforráscsoportot, használja a [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) vagy a [Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0) és a [Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzPublicIpAddress -Name <public-ip> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure-beli hálózati biztonsági csoportot helyezett át az egyik régióból a másikba, és megtisztította a forrás erőforrásait.  Ha többet szeretne megtudni a régiók és a vész-helyreállítás között az Azure-ban, tekintse meg a következőt:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
