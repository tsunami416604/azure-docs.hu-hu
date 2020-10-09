---
title: NAT-átjáró létrehozása – Resource Manager-sablon
titleSuffix: Azure Virtual Network NAT
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre NAT-átjárót a Azure Resource Manager sablon használatával.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway by using an Azure Resource Manager template so that I can provide outbound connectivity for my virtual machines.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2020
ms.author: allensu
ms.custom: subject-armqs
ms.openlocfilehash: fc4804070e0fa4ca6e9e54dcf6e04aafcc17f91a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88053898"
---
# <a name="create-a-nat-gateway---resource-manager-template"></a>NAT-átjáró létrehozása – Resource Manager-sablon

Virtual Network NAT Azure Resource Manager sablon használatával történő használatának első lépései.  Ez a sablon egy virtuális hálózatot, egy NAT-átjáró erőforrást és egy Ubuntu virtuális gépet helyez üzembe. Az Ubuntu virtuális gép a NAT-átjáró erőforrásához társított alhálózatra van telepítve.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

## <a name="create-a-nat-gateway-and-supporting-resources"></a>NAT-átjáró és támogató erőforrások létrehozása

Ez a sablon úgy van konfigurálva, hogy létrehozzon egy 

* Virtuális hálózat 
* NAT-átjáró-erőforrás
* Ubuntu virtuális gép

Az Ubuntu virtuális gép a NAT-átjáró erőforrásához társított alhálózatra van telepítve.

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json) származik.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json" range="1-335" highlight="256-282":::

A sablonban kilenc Azure-erőforrás van definiálva:

**Microsoft.Network**

* **[Microsoft. Network/natGateways](https://docs.microsoft.com/azure/templates/microsoft.network/natgateways)**: létrehoz egy NAT Gateway-erőforrást.

* **[Microsoft. Network/networkSecurityGroups](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)**: létrehoz egy hálózati biztonsági csoportot.

    * **[Microsoft. Network/networkSecurityGroups/securityRules](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups/securityrules)**: biztonsági szabály létrehozása.

* **[Microsoft. Network/nyilvános IP](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)**: létrehoz egy nyilvános IP-címet.

* **[Microsoft. Network/publicIPPrefixes](https://docs.microsoft.com/azure/templates/microsoft.network/publicipprefixes)**: létrehoz egy nyilvános IP-előtagot.

* **[Microsoft. Network/virtualNetworks](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)**: létrehoz egy virtuális hálózatot.

    * **[Microsoft. Network/virtualNetworks/Subnets](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks/subnets)**: létrehoz egy virtuális hálózati alhálózatot.

* **[Microsoft. Network/networkinterfaces](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)**: létrehoz egy hálózati adaptert.

**Microsoft.Compute**

* **[Microsoft. számítás/virtualMachines](https://docs.microsoft.com/azure/templates/Microsoft.Compute/virtualMachines)**: létrehoz egy virtuális gépet.

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

2. Válassza ki az **erőforráscsoportok** elemet a bal oldali ablaktáblán.

3. Válassza ki az előző szakaszban létrehozott erőforráscsoportot. Az erőforráscsoport alapértelmezett neve **myResourceGroupNAT**

4. Ellenőrizze, hogy az erőforráscsoport az alábbi erőforrásokat hozta-e létre:

    ![NAT-erőforráscsoport Virtual Network](./media/quick-create-template/nat-gateway-template-rg.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

**Azure CLI**

Ha már nincs rá szükség, az az [Group delete](/cli/azure/group#az-group-delete) paranccsal eltávolíthatja az erőforráscsoportot és a benne található összes erőforrást.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

**Azure PowerShell**

Ha már nincs rá szükség, a [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) paranccsal távolíthatja el az erőforráscsoportot és a benne található összes erőforrást.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

**Azure Portal**

Ha már nincs rá szükség, törölje az erőforráscsoportot, a NAT-átjárót és az összes kapcsolódó erőforrást. Válassza ki a NAT-átjárót tartalmazó erőforráscsoport- **myResourceGroupNAT** , majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozta a következőket:

* NAT-átjáró-erőforrás
* Virtuális hálózat
* Ubuntu virtuális gép

A virtuális gép üzembe helyezése a NAT-átjáróhoz társított virtuális hálózati alhálózaton történik. 

Ha többet szeretne megtudni a Virtual Network NAT-ról és Azure Resource Managerról, folytassa az alábbi cikkekkel.

* [Virtual Network NAT áttekintése](nat-overview.md)
* További információ a [NAT-átjáró erőforrásáról](nat-gateway-resource.md)
* További információ az [Azure Resource Managerről](../azure-resource-manager/management/overview.md)
