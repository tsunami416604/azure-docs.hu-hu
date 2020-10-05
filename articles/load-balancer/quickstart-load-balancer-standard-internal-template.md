---
title: Belső terheléselosztó létrehozása Azure Resource Manager sablon használatával (ARM-sablon)
description: Megtudhatja, hogyan hozhat létre belső Azure Load balancert Azure Resource Manager sablon (ARM-sablon) használatával.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: allensu
ms.date: 09/14/2020
ms.openlocfilehash: 287afc51aa15ed4cadba7e2d6cd389e4869d7d61
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "90532855"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>Rövid útmutató: belső terheléselosztó létrehozása a virtuális gépek terheléselosztásához ARM-sablon használatával

Ez a rövid útmutató azt ismerteti, hogyan használható egy Azure Resource Manager sablon (ARM-sablon) egy belső Azure Load Balancer létrehozásához.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/201-2-vms-internal-load-balancer)származik.

:::code language="json" source="~/quickstart-templates/201-2-vms-internal-load-balancer/azuredeploy.json":::

Több Azure-erőforrás van definiálva a sablonban:

- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): virtuálisgép-tároló fiókok a rendszerindítási diagnosztika számára.
- [**Microsoft. számítás/availabilitySets**](/azure/templates/microsoft.compute/availabilitySets): rendelkezésre állási csoport a virtuális gépekhez.
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks): Virtual Network a Load Balancerhez és a virtuális gépekhez.
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkInterfaces): hálózati adapterek virtuális gépekhez.
- [**Microsoft. Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers): belső terheléselosztó.
- [**Microsoft. számítási/virtualMachines**](/azure/templates/microsoft.compute/virtualMachines): virtuális gépek.

A Azure Load Balancer kapcsolódó további sablonok kereséséhez tekintse meg az [Azure Gyorsindítás sablonjait](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

**Azure CLI**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupLB"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza ki az **erőforráscsoportok** elemet a bal oldali ablaktáblán.

3. Válassza ki az előző szakaszban létrehozott erőforráscsoportot. Az erőforráscsoport alapértelmezett neve **myresourcegrouplb erőforráscsoportban**

4. Ellenőrizze, hogy az erőforráscsoport az alábbi erőforrásokat hozta-e létre:

:::image type="content" source="media/quickstart-load-balancer-standard-internal-template/verify-creation.png" alt-text="Felhasználói Azure Portal az erőforrások létrehozásának ellenőrzéséhez." border="true":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az az [Group delete](/cli/azure/group#az-group-delete) paranccsal eltávolíthatja az erőforráscsoportot és a benne található összes erőforrást.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>További lépések

A sablonok létrehozásának folyamatát ismertető, lépésenkénti oktatóanyagért lásd:

> [!div class="nextstepaction"]
> [Oktatóanyag: az első ARM-sablon létrehozása és üzembe helyezése](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)