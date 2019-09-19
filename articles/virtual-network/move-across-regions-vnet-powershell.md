---
title: Azure-Virtual Network áthelyezése másik Azure-régióba Azure PowerShell használatával
description: Azure Resource Manager sablonnal áthelyezheti az Azure-Virtual Network az egyik Azure-régióból a másikba Azure PowerShell használatával.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: fe4c3fe131eb763ef8875cced91ab3ae22abca08
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077658"
---
# <a name="move-azure-virtual-network-to-another-region-using-azure-powershell"></a>Azure-Virtual Network áthelyezése egy másik régióba az Azure PowerShell használatával

Különböző helyzetekben érdemes áthelyeznie meglévő Azure-beli virtuális hálózatait (virtuális hálózatok) az egyik régióból a másikba. Előfordulhat például, hogy létre szeretne hozni egy virtuális hálózatot ugyanazzal a konfigurációval a meglévő virtuális hálózat teszteléséhez és rendelkezésre állásához. Előfordulhat, hogy a vész-helyreállítási tervezés részeként egy éles virtuális hálózatot is át szeretne helyezni egy másik régióba.

A virtuális hálózat áthelyezését egy másik régióba Azure Resource Manager sablon használatával végezheti el. Ezt úgy teheti meg, hogy a virtuális hálózatot sablonba exportálja, módosítja a paramétereket, hogy azok megfeleljenek a célként megadott régiónak, majd üzembe helyezi a sablont az új régióban.  A Resource Managerrel és a sablonokkal kapcsolatos további információkért lásd: [erőforráscsoportok exportálása sablonokba](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az Azure Virtual Network abban az Azure-régióban található, amelyről át kívánja helyezni.

- Virtuális hálózat exportálásához és sablon üzembe helyezéséhez egy másik régióban lévő virtuális hálózat létrehozásához szüksége lesz a hálózati közreműködő szerepkörre vagy magasabbra.

- A virtuális hálózati társítások nem lesznek újra létrehozva, és sikertelenek lesznek, ha még jelen vannak a sablonban.  A sablon exportálása előtt el kell távolítania minden virtuális hálózati társat, majd újra létre kell hoznia a társait a virtuális hálózat áthelyezése után.
    
- Azonosítsa a forrás hálózatkezelési elrendezést és az összes éppen használt erőforrást. Ez az elrendezés tartalmaz, de nem korlátozódik a terheléselosztó, a hálózati biztonsági csoportok (NSG) és a nyilvános IP-címek számára.

- Győződjön meg arról, hogy az Azure-előfizetése lehetővé teszi, hogy virtuális hálózatokat hozzon létre a használt célként megadott régióban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

- Győződjön meg arról, hogy az előfizetése elegendő erőforrással rendelkezik a virtuális hálózatok ezen folyamathoz való hozzáadásának támogatásához.  Tekintse meg az [Azure-előfizetések és-szolgáltatások korlátozásait, kvótáit és korlátozásait](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Előkészítés és áthelyezés
A következő lépések bemutatják, hogyan készítheti elő a virtuális hálózatot az áthelyezéshez egy Resource Manager-sablon használatával, és hogyan helyezheti át a virtuális hálózatot a célként megadott régióba Azure PowerShell parancsok használatával.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-and-deploy-the-target-virtual-network-with-powershell"></a>A virtuális hálózat exportálása és a cél virtuális hálózat üzembe helyezése a PowerShell-lel

1. Jelentkezzen be az Azure-előfizetésbe a [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) paranccsal, és kövesse a képernyőn megjelenő utasításokat:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Szerezze be annak a virtuális hálózatnak az erőforrás-AZONOSÍTÓját, amelyet át szeretne helyezni a célként megadott régióba, és helyezze egy változóba a [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)használatával:

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportálja a forrásként szolgáló virtuális hálózatot egy. JSON-fájlba abba a könyvtárba, amelyben az [export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)parancsot futtatja:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. A letöltött fájl neve annak az erőforrás-csoportnak az alapján lesz elnevezve, amelyből az erőforrást exportálták.  Keresse meg az  **\<erőforrás-csoport-név >. JSON** nevű parancsból exportált fájlt, és nyissa meg egy tetszőleges szerkesztőben:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
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

## <a name="discard"></a>Elvetés 

Ha a központi telepítés után szeretné elindítani vagy elvetni a virtuális hálózatot a célhelyen, törölje a célhelyen létrehozott erőforráscsoportot, és az áthelyezett virtuális hálózat törlődni fog.  Az erőforráscsoport eltávolításához használja a [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A módosítások elvégzéséhez és a virtuális hálózat áthelyezésének befejezéséhez törölje a forrás virtuális hálózatot vagy az erőforráscsoportot a [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) vagy a [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure-Virtual Network helyezett át egyik régióból a másikba, és megtisztította a forrás erőforrásait.  Ha többet szeretne megtudni a régiók és a vész-helyreállítás között az Azure-ban, tekintse meg a következőt:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
