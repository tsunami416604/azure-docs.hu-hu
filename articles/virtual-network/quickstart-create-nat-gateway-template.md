---
title: 'Gyors útmutató: NAT-átjáró létrehozása – Resource Manager-sablon'
titleSuffix: Azure Virtual Network NAT
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre NAT-átjárót a Azure Resource Manager sablon használatával.
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
ms.openlocfilehash: 0f6a303bfa42538bf56efa7e9f669588c8bff66c
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082727"
---
# <a name="quickstart-create-a-nat-gateway---resource-manager-template"></a>Gyors útmutató: NAT-átjáró létrehozása – Resource Manager-sablon

Virtual Network NAT Azure Resource Manager sablon használatával történő használatának első lépései.  Ez a sablon egy virtuális hálózatot, egy NAT-átjárót és egy Ubuntu virtuális gépet helyez üzembe. Az Ubuntu virtuális gép a NAT-átjáróhoz társított alhálózatra van telepítve.

>[!NOTE] 
>Az Azure Virtual Network NAT jelenleg nyilvános előzetes verzióként érhető el, és korlátozott számú [régióban](https://azure.microsoft.com/global-infrastructure/regions/)érhető el. Ez az előzetes verzió szolgáltatási szintű szerződés nélkül érhető el, ezért nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-nat-gateway-and-supporting-resources"></a>NAT-átjáró és támogató erőforrások létrehozása

Ez a sablon úgy van konfigurálva, hogy létrehozzon egy 

* Virtuális hálózat 
* NAT-átjáró erőforrása
* Ubuntu virtuális gép

Az Ubuntu virtuális gép a NAT-átjáró erőforrásához társított alhálózatra van telepítve.

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json) származik.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json" range="1-335" highlight="256-282":::

A sablonban kilenc Azure-erőforrás van definiálva:

**Microsoft. Network**

* **[Microsoft. Network/natGateways](https://docs.microsoft.com/azure/templates/microsoft.network/natgateways)** : létrehoz egy NAT Gateway-erőforrást.

* **[Microsoft. Network/networkSecurityGroups](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)** : létrehoz egy hálózati biztonsági csoportot.

    * **[Microsoft. Network/networkSecurityGroups/securityRules](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups/securityrules)** : biztonsági szabály létrehozása.

* **[Microsoft. Network/nyilvános IP](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)** : létrehoz egy nyilvános IP-címet.

* **[Microsoft. Network/publicIPPrefixes](https://docs.microsoft.com/azure/templates/microsoft.network/publicipprefixes)** : létrehoz egy nyilvános IP-előtagot.

* **[Microsoft. Network/virtualNetworks](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)** : létrehoz egy virtuális hálózatot.

    * **[Microsoft. Network/virtualNetworks/Subnets](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks/subnets)** : létrehoz egy virtuális hálózati alhálózatot.

* **[Microsoft. Network/networkinterfaces](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)** : létrehoz egy hálózati adaptert.

**Microsoft. számítás**

* **[Microsoft. számítás/virtualMachines](https://docs.microsoft.com/azure/templates/Microsoft.Compute/virtualMachines)** : létrehoz egy virtuális gépet.

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

**Azure Portalra**

[![Üzembe helyezés az Azure-ban](./media/quick-create-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).

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

**Azure Portalra**

Ha már nincs rá szükség, törölje az erőforráscsoportot, a NAT-átjárót és az összes kapcsolódó erőforrást. Válassza ki a NAT-átjárót tartalmazó erőforráscsoport- **myResourceGroupNAT** , majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozta a következőket:

* NAT-átjáró erőforrása
* Virtuális hálózat
* Ubuntu virtuális gép

A virtuális gép üzembe helyezése a NAT-átjáróhoz társított virtuális hálózati alhálózaton történik. 

Ha többet szeretne megtudni a Virtual Network NAT-ról és Azure Resource Managerról, folytassa az alábbi cikkekkel.

* [Virtual Network NAT áttekintése](nat-overview.md)
* További információ a [NAT-átjáró erőforrásáról](nat-gateway-resource.md)
* További információ a [Azure Resource Manager](../azure-resource-manager/management/overview.md)
