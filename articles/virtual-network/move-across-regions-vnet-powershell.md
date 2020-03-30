---
title: Azure-beli virtuális hálózat áthelyezése másik Azure-régióba az Azure PowerShell használatával
description: Az Azure virtuális hálózat áthelyezése az egyik Azure-régióból a másikba egy Resource Manager-sablon és az Azure PowerShell használatával.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: dc316e5bbb88359ff8b1e8a4fc35a56541a577f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646710"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>Azure virtuális hálózat áthelyezése másik régióba az Azure PowerShell használatával

Különböző forgatókönyvek egy meglévő Azure virtuális hálózat áthelyezése az egyik régióból a másikba. Előfordulhat például, hogy a meglévő virtuális hálózattal azonos tesztelési és rendelkezésre állási konfigurációval rendelkező virtuális hálózatot szeretne létrehozni. Vagy előfordulhat, hogy egy éles virtuális hálózatot át szeretne helyezni egy másik régióba a vész-helyreállítási tervezés részeként.

Használhatja az Azure Resource Manager sablont a virtuális hálózat áthelyezése egy másik régióba. Ehhez exportálja a virtuális hálózatot egy sablonba, módosítja a paramétereket a célrégiónak megfelelően, majd telepíti a sablont az új régióba. Az Erőforrás-kezelő sablonjairól az [Erőforráscsoportok exportálása sablonokba című](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)témakörben olvashat bővebben.


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy a virtuális hálózat az Azure-régióban, amelyről át szeretne helyezni.

- Virtuális hálózat exportálásához és egy sablon központi telepítéséhez egy másik régióban lévő virtuális hálózat létrehozásához a Hálózati közreműködő szerepkörrel vagy magasabb szintű szerepkörrel kell rendelkeznie.

- A virtuális hálózati társviszony-létesítések nem jönnek létre újra, és sikertelenek lesznek, ha még mindig jelen vannak a sablonban. A sablon exportálása előtt el kell távolítania a virtuális hálózati társakat. Ezután a virtuális hálózat áthelyezése után újra létrehozhatja őket.
    
- Azonosítsa a forráshálózati elrendezést és az összes jelenleg használt erőforrást. Ez az elrendezés tartalmazza, de nem kizárólagosan a terheléselosztók, hálózati biztonsági csoportok (NSG-k) és a nyilvános IP-k.

- Ellenőrizze, hogy az Azure-előfizetés lehetővé teszi-e, hogy virtuális hálózatokat hozzon létre a célrégióban. A szükséges kvóta engedélyezéséhez forduljon az ügyfélszolgálathoz.

- Győződjön meg arról, hogy az előfizetés elegendő erőforrással rendelkezik a virtuális hálózatok hozzáadásának támogatásához ehhez a folyamathoz. További információk: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-for-the-move"></a>Készüljetek a költözésre!
Ebben a szakaszban előkészíti a virtuális hálózatot az áthelyezéshez egy Erőforrás-kezelő sablon használatával. Ezután az Azure PowerShell-parancsok használatával áthelyezheti a virtuális hálózatot a célrégióba.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A virtuális hálózat exportálásához és a célvirtuális hálózat PowerShell használatával történő központi telepítéséhez tegye a következőket:

1. Jelentkezzen be Azure-előfizetésébe a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) paranccsal, majd kövesse a képernyőn megjelenő utasításokat:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Szerezze be a célterületre áthelyezni kívánt virtuális hálózat erőforrásazonosítóját, majd helyezze el egy változóban a [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)használatával:

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. A forrásvirtuális hálózat exportálása egy .json fájlba abban a könyvtárban, ahol az [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)parancsot végrehajtja:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. A letöltött fájl neve megegyezik azzal az erőforráscsoporttal, amelyből az erőforrást exportálták. Keresse meg az * \<erőforráscsoport-név nevű>.json* fájlt, amelyet a paranccsal exportált, majd nyissa meg a szerkesztőben:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. A virtuális hálózat nevének módosításához módosítsa a forrásvirtuális hálózat nevének **defaultValue** tulajdonságát a célvirtuális hálózat nevére. Ügyeljen arra, hogy a nevet idézőjelek közé tegye.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. A virtuális hálózat áthelyezésének célrégiójának szerkesztéséhez módosítsa a **helytulajdonságot** az erőforrások alatt:

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
  
1. A régióhelykódok beszerzéséhez használhatja az Azure PowerShell-parancsmag [get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) a következő parancs futtatásával:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. (Nem kötelező) Az * \<erőforráscsoport-név>.json* fájlban más paramétereket is módosíthat, a követelményektől függően:

    * **Címtér**: A fájl mentése előtt módosíthatja a virtuális hálózat címterét az **erőforrások** > **addressSpace** szakaszának módosításával és a **addressPrefixes** tulajdonság módosításával:

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

    * **Alhálózat**: Az alhálózat nevét és az alhálózati címteret a fájl alhálózati szakaszának módosításával módosíthatja vagy **hozzáadhatja.** Az alhálózat nevét a **névtulajdonság** módosításával módosíthatja. Az alhálózati címteret pedig a **addressPrefix** tulajdonság módosításával módosíthatja:

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

        A címelőtag módosításához két helyen módosítsa a fájlt: az előző szakaszban és a következő kód **típus** szakaszában. Módosítsa a **addressPrefix** tulajdonságot a következő kódban, hogy megfeleljen az előző szakaszban található kódban található **addressPrefix** tulajdonságnak.

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

1. Mentse az * \<erőforráscsoport-név>.json* fájlt.

1. Hozzon létre egy erőforráscsoportot a célrégióban a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)használatával telepítendő célvirtuális hálózathoz:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. Telepítse a szerkesztett * \<erőforráscsoport-név>.json* fájlt az előző lépésben létrehozott erőforráscsoportba a [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)használatával:

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. Annak ellenőrzéséhez, hogy az erőforrások a célrégióban jöttek-e létre, használja a [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) és a [Get-AzVirtualNetwork (Get-AzVirtualNetwork) csoportot:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>A virtuális hálózat vagy erőforráscsoport törlése 

Miután telepítette a virtuális hálózatot, hogy újrakezdje vagy elvesse a virtuális hálózatot a célrégióban, törölje a célrégióban létrehozott erőforráscsoportot, és az áthelyezett virtuális hálózat törlődik. 

Az erőforráscsoport eltávolításához használja az [Eltávolítás-AzResourceGroup csoportot:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A módosítások véglegesítéséhez és a virtuális hálózat áthelyezésének befejezéséhez tegye az alábbiak egyikét:

* Az erőforráscsoport törlése az [Eltávolítás-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)csoport használatával:

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* Törölje a forrásvirtuális hálózatot az [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0)használatával:  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a PowerShell használatával áthelyezett egy virtuális hálózatot az egyik régióból a másikba, majd megtisztította a szükségtelen forráserőforrásokat. Ha többet szeretne tudni az erőforrások régiók közötti áthelyezéséről és az Azure-beli vészhelyreállításról:

- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Az Azure virtuális gépei áthelyezése egy másik régióba](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
