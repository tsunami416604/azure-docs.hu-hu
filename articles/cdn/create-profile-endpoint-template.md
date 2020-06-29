---
title: 'Gyors útmutató: profil és végpont – Resource Manager-sablon létrehozása'
titleSuffix: Azure Content Delivery Network
description: Ismerje meg, hogyan hozhat létre Azure Content Delivery Network-profilt és egy Resource Manager-sablont a végponton
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: allensu
ms.openlocfilehash: bdb30fed4dadef84fe012c11893661b8d9d70325
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2020
ms.locfileid: "85483040"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint---resource-manager-template"></a>Gyors útmutató: Azure CDN profil és végpont – Resource Manager-sablon létrehozása

A Azure CDN használatának első lépései Azure Resource Manager sablonnal.  Ez a sablon egy profilt és egy végpontot helyez üzembe.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-cdn-profile-and-endpoint"></a>CDN-profil és-végpont létrehozása

Ez a sablon a következő létrehozására van konfigurálva:

* Profil
* Végpont

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json) származik.

:::code language="json" source="~/quickstart-templates/101-cdn-with-custom-origin/azuredeploy.json" range="1-125" highlight="46-117":::


A sablonban egyetlen Azure-erőforrás van definiálva:

**Microsoft. CDN**

* **[Microsoft. CDN/profilok](https://docs.microsoft.com/azure/templates/microsoft.cdn/profiles)**

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

**Azure CLI**

```azurecli-interactive
read -p "Enter the location (i.e. eastus): " location
resourceGroupName="myResourceGroupCDN"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az group deployment create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

**Azure PowerShell**

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json"

$resourceGroupName = "myResourceGroupCDN"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

**Azure Portal**

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza ki az **erőforráscsoportok** elemet a bal oldali ablaktáblán.

3. Válassza ki az előző szakaszban létrehozott erőforráscsoportot. Az erőforráscsoport alapértelmezett neve **myResourceGroupCDN**

4. Ellenőrizze, hogy az erőforráscsoport az alábbi erőforrásokat hozta-e létre:

    :::image type="content" source="media/create-profile-endpoint-template/cdn-profile-template-rg.png" alt-text="Azure CDN erőforráscsoport" border="true":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

**Azure CLI**

Ha már nincs rá szükség, az az [Group delete](/cli/azure/group#az-group-delete) paranccsal eltávolíthatja az erőforráscsoportot és a benne található összes erőforrást.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupCDN
```

**Azure PowerShell**

Ha már nincs rá szükség, a [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) paranccsal távolíthatja el az erőforráscsoportot és a benne található összes erőforrást.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupCDN
```

**Azure Portal**

Ha már nincs rá szükség, törölje az erőforráscsoportot, a CDN-profilt és az összes kapcsolódó erőforrást. Válassza ki a CDN-profilt és-végpontot tartalmazó erőforráscsoport **myResourceGroupCDN** , majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozta a következőket:

* CDN-profil
* Végpont

Ha többet szeretne megtudni a Azure CDN és a Azure Resource Managerról, folytassa az alábbi cikkekkel.

* [A Azure CDN áttekintése](cdn-overview.md)
* További információ az [Azure Resource Managerről](../azure-resource-manager/management/overview.md)