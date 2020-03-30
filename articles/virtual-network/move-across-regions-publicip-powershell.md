---
title: Az Azure nyilvános IP-cím áthelyezése egy másik Azure-régióba az Azure PowerShell használatával
description: Az Azure Resource Manager-sablonnal áthelyezi az Azure nyilvános IP-címét egyik Azure-régióból a másikba az Azure PowerShell használatával.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: c55b6011381d385fed7c7b8175ff02ec9be66fdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641553"
---
# <a name="move-azure-public-ip-to-another-region-using-azure-powershell"></a>Az Azure nyilvános IP-cím áthelyezése egy másik régióba az Azure PowerShell használatával

Vannak különböző forgatókönyvek, amelyekben szeretné áthelyezni a meglévő Azure nyilvános IP-k egyik régióból a másikba. Előfordulhat például, hogy egy nyilvános IP-címet szeretne létrehozni ugyanazzal a konfigurációval és termékku tesztelésre. Előfordulhat, hogy egy nyilvános IP-cím áthelyezése egy másik régióba a vész-helyreállítási tervezés részeként.

Az Azure nyilvános IP-k régióspecifikusak, és nem helyezhetők át egyik régióból a másikba. Azonban egy Azure Resource Manager sablon használatával exportálhatja a nyilvános IP-cím meglévő konfigurációját.  Ezután egy másik régióban is elhelyezheti az erőforrást úgy, hogy a nyilvános IP-címet egy sablonba exportálja, módosítja a paramétereket a célrégiónak megfelelően, majd telepíti a sablont az új régióba.  Az Erőforrás-kezelőről és a sablonokról az [Erőforráscsoportok exportálása sablonokba című](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates) témakörben olvashat bővebben.


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az Azure nyilvános IP-cím az Azure-régióban, ahonnan át szeretne helyezni.

- Az Azure nyilvános IP-k nem helyezhetők át a régiók között.  Az új nyilvános ip-címet a célrégió erőforrásaihoz kell társítania.

- Nyilvános IP-konfiguráció exportálásához és egy sablon központi telepítéséhez nyilvános IP-cím más régióban történő létrehozásához a Hálózati közreműködő szerepkörvagy magasabb szükséges.
   
- Azonosítsa a forráshálózati elrendezést és az összes jelenleg használt erőforrást. Ez az elrendezés magában foglalja, de nem kizárólagosan a terheléselosztók, hálózati biztonsági csoportok (NSG- k) és a virtuális hálózatok.

- Ellenőrizze, hogy az Azure-előfizetés lehetővé teszi-e nyilvános IP-k létrehozását a használt célrégióban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

- Győződjön meg arról, hogy az előfizetés elegendő erőforrással rendelkezik a nyilvános IP-k hozzáadásának támogatásához ehhez a folyamathoz.  Lásd: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Felkészülés és mozgás
A következő lépések bemutatják, hogyan készítse elő a nyilvános IP-cím a konfigurációs áthelyezés egy Resource Manager-sablon használatával, és helyezze át a nyilvános IP-konfigurációt a célrégióba az Azure PowerShell használatával.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>A sablon exportálása és üzembe helyezése parancsfájlból

1. Jelentkezzen be Azure-előfizetésébe a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) paranccsal, és kövesse a képernyőn megjelenő utasításokat:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Szerezze be a célterületre áthelyezni kívánt nyilvános IP-cím erőforrásazonosítóját, és helyezze el egy változóban a [Get-AzPublicIPAddress használatával:](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. A forrásvirtuális hálózat exportálása .json fájlba abba a könyvtárba, amelyen az [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)parancsot futtatja:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. A letöltött fájl az erőforrás tanelemcsoportjáról lesz elnevezve.  Keresse meg az ** \<erőforráscsoport-név** nevű parancsból exportált fájlt>.json néven, és nyissa meg egy ön által választott szerkesztőben:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
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
## <a name="discard"></a>Elvetés 

A központi telepítés után, ha szeretné újrakezdeni, vagy elveti a nyilvános ip a célban, törölje a célban létrehozott erőforráscsoportot, és az áthelyezett nyilvános IP törlődik.  Az erőforráscsoport eltávolításához használja az [Eltávolítás-AzResourceGroup csoportot:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A módosítások véglegesítéséhez és a virtuális hálózat áthelyezésének befejezéséhez törölje a forrásvirtuális hálózatot vagy erőforráscsoportot, használja az [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) vagy [az Remove-AzPublicIPAddress parancsot:](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban áthelyezte az Azure nyilvános IP-címét az egyik régióból a másikba, és megtisztította a forráserőforrásokat.  Ha többet szeretne tudni az erőforrások régiók közötti áthelyezéséről és az Azure-beli vészhelyreállításról:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
