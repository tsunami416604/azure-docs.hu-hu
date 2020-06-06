---
title: Rövid útmutató – Azure Stream Analytics feladatok létrehozása Azure Resource Manager sablon alapján
description: Ez a rövid útmutató azt ismerteti, hogyan használható a Azure Resource Manager sablon Azure Stream Analytics feladatok létrehozásához.
services: stream-analytics
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, subject-armqs
ms.date: 05/28/2020
ms.openlocfilehash: 127a8550b7f4ca89b8b3a8e61880b295a806e5d0
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84449362"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-by-using-the-azure-resource-manager-template"></a>Gyors útmutató: Azure Stream Analytics-feladatok létrehozása a Azure Resource Manager sablon használatával

Ebben a rövid útmutatóban egy Azure Resource Manager sablonnal hoz létre egy Azure Stream Analytics feladatot. A művelet létrehozása után érvényesítse a központi telepítést.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Előfeltételek

A cikk elvégzéséhez a következőket kell tennie:

* Rendelkezzen Azure-előfizetéssel – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/).

## <a name="create-an-azure-stream-analytics-job"></a>Azure Stream Analytics-feladat létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/101-streamanalytics-create/)származik.

:::code language="json" source="~/quickstart-templates/101-streamanalytics-create/azuredeploy.json" range="1-66" highlight="41-60":::

A sablonban definiált Azure-erőforrás a [Microsoft. StreamAnalytics/StreamingJobs](/azure/templates/microsoft.streamanalytics/streamingjobs): create a Azure stream Analytics job.

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

Ebben a szakaszban egy Azure Stream Analytics feladatot hoz létre a Azure Resource Manager sablon használatával.

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy Azure Stream Analytics feladatot.

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-streamanalytics-create%2Fazuredeploy.json)

2. Adja meg a szükséges értékeket a Azure Stream Analytics-feladatok létrehozásához.

   ![Azure Stream Analytics-feladatok létrehozása Azure Resource Manager sablon használatával](./media/quick-create-azure-resource-manager/create-stream-analytics-job-resource-manager-template.png "Azure Stream Analytics-feladatok létrehozása Azure Resource Manager sablon használatával")

   Adja meg a következő értékeket:

   |Tulajdonság  |Leírás  |
   |---------|---------|
   |**Előfizetés**     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
   |**Erőforráscsoport**     | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport olyan tároló, amely egy adott Azure-megoldás kapcsolódó erőforrásait tartalmazza. További információért olvassa el az [Azure-erőforráscsoportok áttekintését](../azure-resource-manager/management/overview.md). |
   |**Régió**     | Válassza az **USA keleti régiója** lehetőséget. A további elérhető régiókért tekintse meg az [elérhető Azure-szolgáltatások régiók szerinti bontását](https://azure.microsoft.com/regions/services/).        |
   |**Stream Analytics feladatok neve**     | Adja meg a Stream Analytics feladatoknak megfelelő nevet.      |
   |**Folyamatos átviteli egységek száma**     |  Válassza ki a szükséges folyamatos átviteli egységek számát. További információt a [folyamatos átviteli egységek ismertetése és módosítása](stream-analytics-streaming-unit-consumption.md)című témakörben talál.       |

3. Válassza az **Áttekintés + létrehozás**, majd a **Létrehozás** lehetőséget.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A Azure Portal segítségével megtekintheti a Azure Stream Analytics feladatot, vagy az alábbi Azure CLI-vagy Azure PowerShell-parancsfájl használatával listázhatja az erőforrást.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
echo "Enter your Azure Stream Analytics job name:" &&
read streamAnalyticsJobName &&
echo "Enter the resource group where the Azure Stream Analytics job exists:" &&
read resourcegroupName &&
az stream-analytics job show -g $resourcegroupName -n $streamAnalyticsJobName
```

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Stream Analytics job exists"
(Get-AzResource -ResourceType "Microsoft.StreamAnalytics/StreamingJobs" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a következő oktatóanyagokat, érdemes lehet ezeket az erőforrásokat helyben hagyni. Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli a Azure Stream Analytics feladatot. Az erőforráscsoport törlése az Azure CLI vagy a Azure PowerShell használatával:

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

Ebben a rövid útmutatóban egy Azure Stream Analytics feladatot hozott létre egy Azure Resource Manager sablonnal, és ellenőrizte az üzemelő példányt. A következő cikkből megtudhatja, hogyan exportálhat egy Azure Resource Manager sablont egy meglévő feladatokhoz a VS Code használatával.

> [!div class="nextstepaction"]
> [Azure Stream Analytics-feladatok exportálása Azure Resource Manager sablon](resource-manager-export.md)
