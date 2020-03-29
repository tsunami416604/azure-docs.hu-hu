---
title: Az Azure hálózati biztonsági csoportjának (NSG) áthelyezése egy másik Azure-régióba az Azure PowerShell használatával
description: Az Azure Resource Manager-sablonnal áthelyezi az Azure hálózati biztonsági csoportját egyik Azure-régióból a másikba az Azure PowerShell használatával.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 0cbd8f61cb1b4cb8eae6b30625fb3039ff75adde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641468"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>Az Azure hálózati biztonsági csoportjának (NSG) áthelyezése egy másik régióba az Azure PowerShell használatával

Vannak különböző forgatókönyvek, amelyekben szeretné áthelyezni a meglévő NSG-k egyik régióból a másikba. Előfordulhat például, hogy azonos konfigurációs és biztonsági szabályokkal rendelkező NSG-t szeretne létrehozni a teszteléshez. Előfordulhat, hogy egy NSG-t is át szeretne helyezni egy másik régióba a vész-helyreállítási tervezés részeként.

Az Azure biztonsági csoportok nem helyezhetők át egyik régióból a másikba. Azonban egy Azure Resource Manager sablon használatával exportálhatja az NSG meglévő konfigurációs és biztonsági szabályait.  Ezután egy másik régióban is elhelyezheti az erőforrást úgy, hogy az NSG-t egy sablonba exportálja, módosítja a paramétereket a célrégiónak megfelelően, majd telepíti a sablont az új régióba.  Az Erőforrás-kezelőről és a sablonokról az [Erőforráscsoportok exportálása sablonokba című](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)témakörben olvashat bővebben.


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az Azure hálózati biztonsági csoport az Azure-régióban, ahonnan át szeretne helyezni.

- Az Azure hálózati biztonsági csoportok nem helyezhetők át a régiók között.  Az új NSG-t a célrégió erőforrásaihoz kell társítania.

- NSG-konfiguráció exportálásához és egy sablon központi telepítéséhez nsg-t hozhat létre egy másik régióban, szüksége van a hálózati közreműködőszerepkörvagy magasabb.
   
- Azonosítsa a forráshálózati elrendezést és az összes jelenleg használt erőforrást. Ez az elrendezés tartalmazza, de nem korlátozódik a terheléselosztók, nyilvános IP-k és a virtuális hálózatok.

- Ellenőrizze, hogy az Azure-előfizetés lehetővé teszi-e, hogy nsg-ket hozzon létre a használt célrégióban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

- Győződjön meg arról, hogy az előfizetés elegendő erőforrással rendelkezik az NSG-k hozzáadásának támogatásához ehhez a folyamathoz.  Lásd: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Felkészülés és mozgás
A következő lépések bemutatják, hogyan készítse elő a hálózati biztonsági csoportot a konfigurációs és biztonsági szabály áthelyezése egy Resource Manager sablon használatával, és helyezze át az NSG konfigurációs és biztonsági szabályokat a célrégióba az Azure PowerShell használatával.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>A sablon exportálása és üzembe helyezése parancsfájlból

1. Jelentkezzen be Azure-előfizetésébe a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) paranccsal, és kövesse a képernyőn megjelenő utasításokat:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Szerezze be a célrégióba áthelyezni kívánt NSG erőforrásazonosítóját, és helyezze el egy változóban a [Get-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0)használatával:

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportálja a forrás NSG-t egy .json fájlba abba a könyvtárba, amelyen az [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)parancsot futtatja:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. A letöltött fájl az erőforrás tanelemcsoportjáról lesz elnevezve.  Keresse meg az ** \<erőforráscsoport-név** nevű parancsból exportált fájlt>.json néven, és nyissa meg egy ön által választott szerkesztőben:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Az NSG-név paraméterének szerkesztéséhez módosítsa a forrás NSG-nevének **alapértelmezett értékét** a cél NSG nevére, győződjön meg arról, hogy a név idézőjelek között van:
    
    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
            "defaultValue": "<target-nsg-name>",
            "type": "String"
            }
        }

    ```


6. Az NSG konfigurációs és biztonsági szabályainak áthelyezésére vonatkozó célrégió szerkesztéséhez módosítsa a **helytulajdonságot** az **erőforrások**alatt:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78", 
             }
            }
    ```
  
7. A régióhelykódok beszerzéséhez használhatja az Azure PowerShell-parancsmag [get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) a következő parancs futtatásával:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Az erőforráscsoport nevének más paramétereit is módosíthatja ** \<>.json,** ha úgy dönt, és a követelményektől függően nem kötelező:

    * **Biztonsági szabályok** – Az ** \<erőforráscsoport név>.json** fájl **securityRules** szakaszához való hozzáadással vagy annak eltávolításával szerkesztheti, hogy mely szabályok vannak telepítve a cél NSG-ben:

        ```json
           "resources": [
                  {
                  "type": "Microsoft.Network/networkSecurityGroups",
                  "apiVersion": "2019-06-01",
                  "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
                  "location": "TARGET REGION",
                  "properties": {
                       "provisioningState": "Succeeded",
                       "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                  "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                             "provisioningState": "Succeeded",
                             "protocol": "TCP",
                             "sourcePortRange": "*",
                             "destinationPortRange": "3389",
                             "sourceAddressPrefix": "*",
                             "destinationAddressPrefix": "*",
                             "access": "Allow",
                             "priority": 300,
                             "direction": "Inbound",
                             "sourcePortRanges": [],
                             "destinationPortRanges": [],
                             "sourceAddressPrefixes": [],
                             "destinationAddressPrefixes": []
                            }
                        ]
            }  
            
        ```

        A célNSG-ben lévő szabályok hozzáadásának vagy eltávolításának befejezéséhez az ** \<erőforráscsoport-név>.json** fájl végén található egyéni szabálytípusokat is az alábbi példa formátumában kell szerkesztenie:

        ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
        ```

9. Mentse az ** \<erőforráscsoport-név>.json** fájlt.

10. Hozzon létre egy erőforráscsoportot a célrégióban a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)használatával telepítendő célNSG számára:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Telepítse a szerkesztett ** \<erőforráscsoport-név>.json** fájlt az előző lépésben létrehozott erőforráscsoportba a [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)használatával:

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. A célrégióban létrehozott erőforrások ellenőrzéséhez használja a [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) és a [Get-AzNetworkSecurityGroup csoportot:](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Elvetés 

A központi telepítés után, ha szeretné újrakezdeni, vagy elveti az NSG a cél, törölje a célban létrehozott erőforráscsoportot, és az áthelyezett NSG törlődik.  Az erőforráscsoport eltávolításához használja az [Eltávolítás-AzResourceGroup csoportot:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A módosítások véglegesítéséhez és az NSG áthelyezésének befejezéséhez törölje a forrásNSG-t vagy az erőforráscsoportot, használja az [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) vagy [az Remove-AzNetworkSecurityGroup parancsot:](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworksecuritygroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban áthelyezett egy Azure-hálózati biztonsági csoportot egyik régióból a másikba, és megtisztította a forráserőforrásokat.  Ha többet szeretne tudni az erőforrások régiók közötti áthelyezéséről és az Azure-beli vészhelyreállításról:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
