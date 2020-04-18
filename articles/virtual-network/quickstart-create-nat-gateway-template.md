---
title: 'Rövid útmutató: NAT-átjáró létrehozása – Erőforrás-kezelő sablon'
titleSuffix: Azure Virtual Network NAT
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy NAT-átjárót az Azure Resource Manager sablon használatával.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway by using an Azure Resource Manager template so that I can provide outbound connectivity for my virtual machines.
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2020
ms.author: allensu
ms.custom: subject-armqs
ms.openlocfilehash: 3850f3f22e730e46f6d278b6cef0e17d357b126d
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618049"
---
# <a name="quickstart-create-a-nat-gateway---resource-manager-template"></a>Rövid útmutató: NAT-átjáró létrehozása – Erőforrás-kezelő sablon

Ismerkedés a virtuális hálózati nat használatával egy Azure Resource Manager sablon használatával.  Ez a sablon egy virtuális hálózatot, egy NAT átjáró-erőforrást és egy Ubuntu virtuális gépet telepít. Az Ubuntu virtuális gép egy alhálózatba van telepítve, amely a NAT átjáró-erőforráshoz van társítva.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="create-a-nat-gateway-and-supporting-resources"></a>NAT-átjáró létrehozása és támogató erőforrások létrehozása

Ez a sablon úgy van beállítva, hogy 

* Virtuális hálózat 
* NAT átjáró erőforrás
* Ubuntu virtuális gép

Az Ubuntu virtuális gép egy alhálózatra van telepítve, amely a NAT átjáró-erőforráshoz van társítva.

### <a name="review-the-template"></a>A sablon áttekintése

A rövid útmutatóban használt sablon az [Azure rövid útmutató sablonjaiból származik](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json" range="1-335" highlight="256-282":::

Kilenc Azure-erőforrás van definiálva a sablonban:

**Microsoft.Network**

* **[Microsoft.Network/natGateways](https://docs.microsoft.com/azure/templates/microsoft.network/natgateways)**: NAT átjáró-erőforrást hoz létre.

* **[Microsoft.Network/networkSecurityGroups](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)**: Hálózati biztonsági csoportot hoz létre.

    * **[Microsoft.Network/networkSecurityGroups/securityRules](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups/securityrules)**: Biztonsági szabályt hoz létre.

* **[Microsoft.Network/publicIPAddresses](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)**: Nyilvános IP-címet hoz létre.

* **[Microsoft.Network/publicIPPrefixes](https://docs.microsoft.com/azure/templates/microsoft.network/publicipprefixes)**: Nyilvános IP-előtagot hoz létre.

* **[Microsoft.Network/virtualNetworks](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)**: Virtuális hálózat létrehozása.

    * **[Microsoft.Network/virtualNetworks/subnets](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks/subnets)**: Virtuális hálózati alhálózat létrehozása.

* **[Microsoft.Network/networkinterfaces](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)**: Hálózati adapter létrehozása.

**Microsoft.Compute**

* **[Microsoft.Compute/virtualMachines](https://docs.microsoft.com/azure/templates/Microsoft.Compute/virtualMachines)**: Virtuális gépet hoz létre.

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

**Azure CLI**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupNAT"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az group deployment create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

**Azure PowerShell**

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. westcentralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json"

$resourceGroupName = "myResourceGroupNAT"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

**Azure Portal**

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza **az Erőforráscsoportok lehetőséget** a bal oldali ablaktáblából.

3. Jelölje ki az előző szakaszban létrehozott erőforráscsoportot. Az alapértelmezett erőforráscsoport neve **myResourceGroupNAT**

4. Ellenőrizze, hogy a következő erőforrások jöttek-e létre az erőforráscsoportban:

    ![Virtuális hálózati NAT erőforráscsoport](./media/quick-create-template/nat-gateway-template-rg.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

**Azure CLI**

Ha már nincs rá szükség, az [az csoport törlése](/cli/azure/group#az-group-delete) paranccsal eltávolíthatja az erőforráscsoportot és a benne lévő összes erőforrást.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

**Azure PowerShell**

Ha már nincs rá szükség, az [Eltávolítás-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) paranccsal eltávolíthatja az erőforráscsoportot és a benne lévő összes erőforrást.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

**Azure Portal**

Ha már nincs rá szükség, törölje az erőforráscsoportot, a NAT-átjárót és az összes kapcsolódó erőforrást. Jelölje ki a NAT-átjárót tartalmazó **myResourceGroupNAT** erőforráscsoportot, majd kattintson a **Törlés gombra.**

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy:

* NAT átjáró erőforrás
* Virtuális hálózat
* Ubuntu virtuális gép

A virtuális gép a NAT-átjáróhoz társított virtuális hálózati alhálózatra van telepítve. 

Ha többet szeretne megtudni a virtuális hálózati NAT-ról és az Azure Resource Managerről, folytassa az alábbi cikkekkel.

* Olvassa [el a virtuális hálózati Hálózati hálózati att áttekintését](nat-overview.md)
* További információ a [NAT Gateway erőforrásról](nat-gateway-resource.md)
* További információ az [Azure Resource Managerről](../azure-resource-manager/management/overview.md)
