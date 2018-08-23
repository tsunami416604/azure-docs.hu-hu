---
title: Hozzon létre az Azure Machine Learning-kísérletezés egy Azure Resource Manager-sablonnal |} A Microsoft Docs
description: Ez a cikk bemutatja, hozhat létre egy Azure Resource Manager-sablon használatával az Azure Machine Learning-kísérletezés-fiókot.
services: machine-learning
author: hning86
ms.author: haining
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 11/14/2017
ms.openlocfilehash: 7938eaa0e06c9a33034a7388d02845d60967774e
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "42054530"
---
# <a name="configure-the-azure-machine-learning-experimentation-service"></a>Az Azure Machine Learning-kísérletezés szolgáltatás konfigurálása

## <a name="overview"></a>Áttekintés
Azure Machine Learning-kísérletezés szolgáltatás-fiókot, a munkaterületet, és a projekt az Azure-erőforrások. Ennek megfelelően telepíthetők legyenek erőforrások Manager-sablonok használatával. A Resource Manager-sablonok JSON-fájlok, melyek az adott megoldáshoz telepítendő erőforrásokat határozzák meg. Az Azure-megoldások telepítésével és kezelésével kapcsolatos fogalmak megismeréséhez lásd: [Az Azure Resource Manager áttekintése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="deploy-a-template"></a>Sablon üzembe helyezése
A sablon üzembe helyezéséhez szükséges csak néhány lépést, az Azure parancssori felület vagy az Azure Portalon.

### <a name="deploy-a-template-from-the-command-line"></a>A parancssorból-sablon üzembe helyezése
A parancssori felület használatával, egyetlen paranccsal telepítheti egy sablont egy meglévő erőforráscsoportot.
A sablon létrehozásával kapcsolatos információkat lásd a következő.

```sh
# Login and validate your are in the right subscription context
az login

# Create a new resource group (you can use an existing one)
az group create --name <resource group name> --location <supported Azure region>
az group deployment create -n testdeploy -g <resource group name> --template-file <template-file.json> --parameters <parameters.json>
```

### <a name="deploy-a-template-from-the-azure-portal"></a>Az Azure Portal-sablon üzembe helyezése
Igény szerint hozhat létre és helyezhet üzembe sablont is használhatja az Azure Portalon. Tegye az alábbiak szerint:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** , és keressen a "sablon".
3. Válassza ki **sablonok**.
4. Kattintson a **+ Hozzáadás** , és másolja a sablon adatait. 
5. Válassza ki a #4. lépésben létrehozott sablont, és kattintson a **telepítés**.


## <a name="create-a-template-from-an-existing-azure-resource-in-the-azure-portal"></a>A sablon létrehozása az Azure Portalon egy meglévő Azure-erőforrás
Ha már rendelkezik egy Azure Machine Kísérletezési fiók érhető el, a [az Azure portal](https://portal.azure.com), az adott erőforrást is létrehozhat egy sablont. 

1. Keresse meg az Azure Kísérletezési fiók [az Azure portal](https://portal.azure.com).
2. A **beállítások**, kattintson a **Automation-szkript**.
3. Töltse le a sablont. 

Másik lehetőségként kézzel szerkesztheti a sablonfájlokat. Tekintse meg a következő példa egy sablon és paraméterek fájlok. 

### <a name="template-file-example"></a>Sablon fájl például
Hozzon létre egy "sablon-file.json" nevű tartalom alatt. 

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                "description": "Name of the machine learning experimentation team account"
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location of the machine learning experimentation account and other dependent resources."
            }
        },
        "storageAccountSku": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "metadata": {
                "description": "Sku of the storage account"
            }
        }
    },
    "variables": {
        "mlexpVersion": "2017-05-01-preview",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
    },
    "resources": [
        {
            "name": "[parameters('accountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2016-12-01",
            "tags": {
                "mlteamAccount": "[parameters('accountName')]"
            },
            "sku": {
                "name": "[parameters('storageAccountSku')]"
            },
            "kind": "Storage",
            "properties": {
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            }
        },
        {
            "apiVersion": "[variables('mlexpVersion')]",
            "type": "Microsoft.MachineLearningExperimentation/accounts",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
            ],
            "properties": {
                "storageAccount": {
                    "storageAccountId": "[variables('stgResourceId')]",
                    "accessKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName')), '2016-12-01').keys[0].value]"
                }
            }
        }
    ]
}
```

### <a name="parameters"></a>Paraméterek 
Hozzon létre egy fájlt az alábbi tartalmat, és mentse < parameters.json >. 

Három értékeket módosíthatja. 
* AccountName: A Kísérletezési fiók neve.
* Hely: Az egyik a támogatott Azure-régiókban.
* Tárfiók SKU: Az Azure ML standard szintű tárolást, nem prémium szintű csak támogatja. Storage szolgáltatással kapcsolatos további információkért lásd: [storage bemutatása](https://docs.microsoft.com/azure/storage/common/storage-introduction). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "accountName": {
         "value": "MyExperimentationAccount"
     },
     "location": {
         "value": "eastus2"
     },
     "storageAccountSku": {
         "value": "Standard_LRS"
     }
  }
}
```

## <a name="next-steps"></a>További lépések
* [Hozzon létre és telepítse az Azure Machine Learning](../service/quickstart-installation.md)
