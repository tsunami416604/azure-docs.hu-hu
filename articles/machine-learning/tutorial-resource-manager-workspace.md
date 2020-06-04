---
title: Oktatóanyag – Azure ML-munkaterület létrehozása – Resource Manager-sablon
description: Ebben az oktatóanyagban egy Azure Resource Manager sablonnal végezheti el a gépi tanuláshoz készült Azure-munkaterület gyors üzembe helyezését.
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.openlocfilehash: 76f37beb22e28c0232efd0d62e82c8d3b60c78dc
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84345087"
---
# <a name="tutorial-deploy-an-azure-machine-learning-workspace-using-a-resource-manager-template"></a>Oktatóanyag: Azure Machine learning-munkaterület üzembe helyezése Resource Manager-sablonnal
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy Azure Machine learning-munkaterületet egy Azure Resource Manager sablon használatával. Az Azure Machine learning-munkaterületek az alapkonfigurációk által üzembe helyezett modellekbe rendezik az összes gépi tanulási eszközt. A munkaterületek egyetlen helyet biztosítanak a munkatársaival való együttműködéshez a kísérletek létrehozásához, futtatásához és áttekintéséhez, a képzés és a számítási erőforrások felügyeletéhez, valamint a telepített modellek monitorozásához és verziószámához.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLFree) a Kezdés előtt

* Ha a jelen dokumentumban a CLI-parancsokat a **helyi környezetből**szeretné használni, szüksége lesz az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) -re

## <a name="create-a-workspace"></a>Munkaterület létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/101-machine-learning-create/)származik.

:::code language="json" source="~/quickstart-templates/101-machine-learning-create/azuredeploy.json" range="1-258" highlight="224-254":::

A következő erőforrások vannak definiálva a sablonban:

* [Microsoft. MachineLearningServices/munkaterületek](/azure/templates/microsoft.machinelearningservices/workspaces): hozzon létre egy Azure ml-munkaterületet. Ebben a sablonban a hely és a név olyan paraméterek, amelyeket a felhasználó be-vagy interaktív módon adhat meg.

### <a name="deploy-the-template"></a>A sablon üzembe helyezése 

Ha az Azure CLI-vel szeretné használni a sablont, jelentkezzen be, és válassza ki az előfizetését (lásd: [Bejelentkezés az Azure CLI-vel](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)). Majd futtassa ezt:

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-machine-learning-create/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
workspaceName="${projectName}ws" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters workspaceName=$workspaceName location=$location && 
echo "Press [ENTER] to continue ..." &&
read
```

A fenti parancs futtatásakor írja be a következőket:

1. A létrehozott erőforráscsoport és az Azure ML-munkaterület nevének alapjául szolgáló projekt neve
1. Az Azure-beli hely, ahol az üzembe helyezést el szeretné végezni

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Az Azure ML-munkaterület megtekintése:

1. Nyissa meg a következőt: https://portal.azure.com 
1. Bejelentkezés 
1. Válassza ki az imént létrehozott munkaterületet

Ekkor megjelenik a Azure Machine Learning kezdőlapja: 

:::image type="content" source="media/tutorial-resource-manager-workspace/workspace-home.png" alt-text="Képernyőkép az Azure ML-munkaterületről":::

A telepítéshez társított összes erőforrás megjelenítéséhez kattintson a bal felső sarokban található hivatkozásra a munkaterület nevével (a képernyőképen `my_templated_ws` ). Ez a hivatkozás a Azure Portal található erőforráscsoporthoz viszi. Az erőforráscsoport neve, `{projectName}rg` és a munkaterület neve `{projectName}ws` .

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem szeretné használni ezt a munkaterületet, törölje azt. Mivel a munkaterület más erőforrásokhoz, például egy Storage-fiókhoz van társítva, valószínűleg törölni szeretné a teljes létrehozott erőforráscsoportot. Az erőforráscsoportot a portálon törölheti, ha a "Törlés" gombra kattint, és megerősíti a szolgáltatást. Vagy törölheti is az erőforráscsoportot a parancssori felületről a következővel: 

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy Azure Machine Learning munkaterületet egy Azure Resource Manager sablonból. Ha Azure Machine Learningt szeretne felfedezni, folytassa az Oktatóanyaggal. 

> [!div class="nextstepaction"]
> [Oktatóanyag: Ismerkedés az első ML-kísérlettel a Python SDK-val](tutorial-1st-experiment-sdk-setup.md)
