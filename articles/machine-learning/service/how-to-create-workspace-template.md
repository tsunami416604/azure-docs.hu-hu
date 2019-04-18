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
ms.date: 04/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: 7349998325e56d5ebb78de5ca30c0127f09102aa
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58883190"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning-service"></a>Hozzon létre egy munkaterületet, az Azure Machine Learning szolgáltatás egy Azure Resource Manager-sablon használatával

Ez a cikk bemutatja egy Azure Machine Learning szolgáltatás munkaterület használata az Azure Resource Manager-sablonok létrehozásának számos módja. A Resource Manager-sablon megkönnyíti az erőforrások létrehozása egyetlen, koordinált műveletben. A sablon egy JSON-dokumentum, amely meghatározza az erőforrások üzembe helyezéséhez szükséges. Üzembe helyezési paraméterek azt is megadhatja. Paraméterek segítségével a sablon használata esetén adja meg a bemeneti értékeket.

További információkért lásd: [alkalmazás üzembe helyezése Azure Resource Manager-sablonnal](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy **Azure-előfizetés**. Ha nem rendelkezik egy, a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

* A parancssori felületen a sablon használatához szüksége vagy [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) vagy a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Resource Manager-sablon

A következő Resource Manager-sablon segítségével hozzon létre egy Azure Machine Learning szolgáltatás munkaterület és a kapcsolódó Azure-erőforrásokhoz:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "The name of the Azure Machine Learning service workspace."
            }
        },
        "location": {
            "type": "string",
            "allowedValues": [
                "eastus",
                "eastus2",
                "southcentralus",
                "southeastasia",
                "westcentralus",
                "westeurope",
                "westus2"
            ],
            "metadata": {
                "description": "The location where the workspace will be created."
            }
        }
    },
    "variables": {
        "storageAccount": {
            "name": "[concat('sa',uniqueString(resourceGroup().id))]",
            "type": "Standard_LRS"
        },
        "keyVault": {
            "name": "[concat('kv',uniqueString(resourceGroup().id))]",
            "tenantId": "[subscription().tenantId]"
        },
        "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
        "containerRegistryName": "[concat('cr',uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "name": "[variables('storageAccount').name]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2018-07-01",
            "sku": {
                "name": "[variables('storageAccount').type]"
            },
            "kind": "StorageV2",
            "properties": {
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "supportHttpsTrafficOnly": true
            }
        },
        {
            "name": "[variables('keyVault').name]",
            "type": "Microsoft.KeyVault/vaults",
            "apiVersion": "2018-02-14",
            "location": "[parameters('location')]",
            "properties": {
                "tenantId": "[variables('keyVault').tenantId]",
                "sku": {
                    "name": "standard",
                    "family": "A"
                },
                "accessPolicies": []
            }
        },
        {
            "name": "[variables('applicationInsightsName')]",
            "type": "Microsoft.Insights/components",
            "apiVersion": "2015-05-01",
            "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
            "kind": "web",
            "properties": {
                "Application_Type": "web"
            }
        },
        {
            "name": "[variables('containerRegistryName')]",
            "type": "Microsoft.ContainerRegistry/registries",
            "apiVersion": "2017-10-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard"
            },
            "properties": {
                "adminUserEnabled": true
            }
        },
        {
            "name": "[parameters('workspaceName')]",
            "type": "Microsoft.MachineLearningServices/workspaces",
            "apiVersion": "2018-11-19",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[variables('storageAccount').name]",
                "[variables('keyVault').name]",
                "[variables('applicationInsightsName')]",
                "[variables('containerRegistryName')]"
            ],
            "identity": {
                "type": "systemAssigned"
            },
            "properties": {
                "friendlyName": "[parameters('workspaceName')]",
                "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVault').name)]",
                "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
                "containerRegistry": "[resourceId('Microsoft.ContainerRegistry/registries',variables('containerRegistryName'))]",
                "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccount').name)]"
            }
        }
    ]
}
```

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
