---
title: Rövid útmutató – Azure Databricks munkaterület létrehozása Azure Resource Manager sablon alapján
description: Ez a rövid útmutató azt ismerteti, hogyan használható a Azure Resource Manager sablon Azure Databricks munkaterület létrehozásához.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, subject-armqs
ms.date: 05/27/2020
ms.openlocfilehash: c8503aee5cefbe197fd63cd6406006a1cdf8afa9
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2020
ms.locfileid: "84463150"
---
# <a name="quickstart-create-an-azure-databricks-workspace-by-using-the-azure-resource-manager-template"></a>Gyors útmutató: Azure Databricks munkaterület létrehozása a Azure Resource Manager sablon használatával

Ebben a rövid útmutatóban egy Azure Resource Manager sablonnal hoz létre egy Azure Databricks-munkaterületet. A munkaterület létrehozása után érvényesítse az üzemelő példányt.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Előfeltételek

A cikk elvégzéséhez a következőket kell tennie:

* Azure-előfizetéssel [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks-munkaterület létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/101-databricks-workspace/)származik.

:::code language="json" source="~/quickstart-templates/101-databricks-workspace/azuredeploy.json" range="1-53" highlight="33-46":::

A sablonban definiált Azure-erőforrás [Microsoft. Databricks/workspaces](/azure/templates/microsoft.databricks/workspaces): Azure Databricks munkaterület létrehozása.

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

Ebben a szakaszban egy Azure Databricks-munkaterületet hoz létre az Azure Resource Manager-sablon használatával.

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy Azure Databricks munkaterületet.

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-databricks-workspace%2Fazuredeploy.json)

2. Adja meg az Azure Databricks-munkaterület létrehozásához szükséges értékeket

   ![Azure Databricks munkaterület létrehozása Azure Resource Manager sablon használatával](./media/quickstart-create-databricks-workspace-resource-manager-template/create-databricks-workspace-using-resource-manager-template.png "Azure Databricks munkaterület létrehozása Azure Resource Manager sablon használatával")

   Adja meg a következő értékeket:

   |Tulajdonság  |Leírás  |
   |---------|---------|
   |**Előfizetés**     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
   |**Erőforráscsoport**     | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport olyan tároló, amely egy adott Azure-megoldás kapcsolódó erőforrásait tartalmazza. További információért olvassa el az [Azure-erőforráscsoportok áttekintését](../azure-resource-manager/management/overview.md). |
   |**Hely**     | Válassza az **USA 2. keleti régiója** lehetőséget. A további elérhető régiókért tekintse meg az [elérhető Azure-szolgáltatások régiók szerinti bontását](https://azure.microsoft.com/regions/services/).        |
   |**Munkaterület neve**     | Adja meg a Databricks-munkaterület nevét.        |
   |**Díjszabási csomag**     |  Válassza a **Standard** vagy a **Prémium** előfizetést. További információkért a csomagokkal kapcsolatban tekintse meg a [Databricks díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/databricks/).       |

3. Válassza az **Áttekintés + létrehozás**, majd a **Létrehozás** lehetőséget.

4. A munkaterület létrehozása eltarthat néhány percig. Ha a munkaterület telepítése meghiúsul, a munkaterület még sikertelen állapotban jön létre. Törölje a sikertelen munkaterületet, és hozzon létre egy új munkaterületet, amely feloldja a telepítési hibákat. A sikertelen munkaterület törlésekor a felügyelt erőforráscsoport és a sikeresen telepített erőforrások is törlődnek.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A Azure Portal segítségével megtekintheti a Azure Databricks munkaterületet, vagy az alábbi Azure CLI-vagy Azure PowerShell-parancsfájl használatával listázhatja az erőforrást.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
echo "Enter your Azure Databricks workspace name:" &&
read databricksWorkspaceName &&
echo "Enter the resource group where the Azure Databricks workspace exists:" &&
read resourcegroupName &&
az databricks workspace show -g $resourcegroupName -n $databricksWorkspaceName
```

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Databricks workspace exists"
(Get-AzResource -ResourceType "Microsoft.Databricks/workspaces" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a következő oktatóanyagokat, érdemes lehet ezeket az erőforrásokat helyben hagyni. Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli a Azure Databricks munkaterületet és a kapcsolódó felügyelt erőforrásokat. Az erőforráscsoport törlése az Azure CLI vagy a Azure PowerShell használatával:

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy Azure Databricks munkaterületet hozott létre egy Azure Resource Manager sablonnal, és ellenőrizte az üzemelő példányt. Folytassa a következő cikkel annak megismeréséhez, hogyan végezhet ETL-műveletet (adatok kinyerését, átalakítását és betöltését) az Azure Databricks használatával.

> [!div class="nextstepaction"]
> [Adatok kinyerése, átalakítása és betöltése az Azure Databricks használatával](databricks-extract-load-sql-data-warehouse.md)
