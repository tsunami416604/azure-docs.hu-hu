---
title: Hozzon létre egy munkaterületet egy Azure Resource Manager-sablon használatával
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan hozzon létre egy új Azure Machine Learning szolgáltatás munkaterületet egy Azure Resource Manager-sablon használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 04/16/2019
ms.custom: seoapril2019
ms.openlocfilehash: abe497ed96515e8194fb2ddefd8e7f4cb9908758
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205132"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning-service"></a>Hozzon létre egy munkaterületet, az Azure Machine Learning szolgáltatás egy Azure Resource Manager-sablon használatával

Ez a cikk bemutatja egy Azure Machine Learning szolgáltatás munkaterület használata az Azure Resource Manager-sablonok létrehozásának számos módja. A Resource Manager-sablon megkönnyíti az erőforrások létrehozása egyetlen, koordinált műveletben. A sablon egy JSON-dokumentum, amely meghatározza az erőforrások üzembe helyezéséhez szükséges. Üzembe helyezési paraméterek azt is megadhatja. Paraméterek segítségével a sablon használata esetén adja meg a bemeneti értékeket.

További információkért lásd: [alkalmazás üzembe helyezése Azure Resource Manager-sablonnal](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy **Azure-előfizetés**. Ha nem rendelkezik egy, a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

* A parancssori felületen a sablon használatához szüksége vagy [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) vagy a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Resource Manager-sablon

A következő Resource Manager-sablon segítségével hozzon létre egy Azure Machine Learning szolgáltatás munkaterület és a kapcsolódó Azure-erőforrásokhoz:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Ezzel a sablonnal hoz létre a következő Azure-szolgáltatások:

* Azure-erőforráscsoport
* Azure Storage-tárfiók neve
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Azure Machine Learning-munkaterület

Az erőforráscsoport a tároló, amely a szolgáltatások. A különböző szolgáltatások által az Azure Machine Learning-munkaterület szükséges.

A példa sablon két paraméterrel rendelkezik:

* A **hely** ahol az erőforráscsoportot és a szolgáltatások létrejön.

    A sablont fogja használni, válassza ki a helyet a legtöbb erőforrást. A kivétel ez alól az Application Insights szolgáltatás, amely nem érhető el az összes, a többi szolgáltatás helyét. Ha egy helyen, ahol nem érhető el, a szolgáltatás az USA déli középső régiójában létrejön.

* A **munkaterületnév**, azaz a valódi neve az Azure Machine Learning-munkaterületet az.

    A többi szolgáltatás nevei véletlenszerűen jönnek létre.

További információ a sablonok tekintse meg a következő cikkeket:

* [Szerzői Azure Resource Manager-sablonok](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Alkalmazás üzembe helyezése Azure Resource Manager-sablonokkal](../../azure-resource-manager/resource-group-template-deploy.md)
* [Microsoft.MachineLearningServices erőforrástípusok](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. Kövesse a [erőforrások egyéni sablon üzembe helyezése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Ha akkor érkeznek a __sablon szerkesztése__ lapon illessze be a sablon a dokumentumban.
1. Válassza ki __mentése__ használhatja a sablont. Adja meg a következő adatokat, és vállalja, hogy a felsorolt feltételeket és kikötéseket:

   * Előfizetés: Válassza ki az Azure-előfizetés használni ezekhez az erőforrásokhoz.
   * Erőforráscsoport: Válassza ki vagy hozzon létre egy erőforráscsoportot a szolgáltatásokat tartalmazza.
   * Munkaterület neve: Az Azure Machine Learning-munkaterületet, amely létrehozza a használni kívánt nevet. A munkaterület neve 3 – 33 karakter hosszúnak kell lennie. Csak alfanumerikus karaktereket tartalmazhat, és '-'.
   * Hely: Válassza ki a helyet, ahol létrejön az erőforrásokat.

     ![A sablon paramétereit az Azure Portalon](media/how-to-create-workspace-template/template-parameters.png)

További információkért lásd: [erőforrások egyéni sablon üzembe helyezése](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Azure PowerShell használatával

Ez a példa feltételezi, hogy a sablon nevű fájlban mentette `azuredeploy.json` az aktuális könyvtárban:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

További információkért lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/resource-group-template-deploy.md) és [üzembe helyezés saját Resource Manager-sablon az SAS-jogkivonat és az Azure PowerShell](../../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával

Ez a példa feltételezi, hogy a sablon nevű fájlban mentette `azuredeploy.json` az aktuális könyvtárban:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace
```

További információkért lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../../azure-resource-manager/resource-group-template-deploy-cli.md) és [üzembe helyezés saját Resource Manager-sablon az SAS-jogkivonat és az Azure CLI](../../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="next-steps"></a>További lépések

* [Erőforrások üzembe helyezése Resource Manager-sablonok és a Resource Manager REST API](../../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Létrehozása és telepítése az Azure erőforráscsoport-sablonok a Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
