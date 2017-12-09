---
title: "Hozzon létre egy Azure Resource Manager-sablon Azure Machine Learning kísérletezhet |} Microsoft Docs"
description: "Ez a cikk az Azure Resource Manager-sablonnal Azure Machine Learning kísérletezhet fiók létrehozására szolgáló példaként szolgál."
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/14/2017
ms.openlocfilehash: c7f4bf0fed35ffb2c03e8b983260c6093032fe79
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="configure-the-azure-machine-learning-experimentation-service"></a>Az Azure Machine Learning kísérletezhet szolgáltatás konfigurálása

## <a name="overview"></a>Áttekintés
Az Azure Machine Learning kísérletezhet szolgáltatásfiók, a munkaterületet, és a projekt az Azure-erőforrások. Így azok is telepíthető erőforrások Manager-sablonok használatával. A Resource Manager-sablonok JSON-fájlok, melyek az adott megoldáshoz telepítendő erőforrásokat határozzák meg. Az Azure-megoldások telepítésével és kezelésével kapcsolatos fogalmak megismeréséhez lásd: [Az Azure Resource Manager áttekintése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="deploy-a-template"></a>A sablon üzembe helyezése
A sablonok telepítésével szükséges csak néhány lépést, az Azure parancssori felület vagy az Azure portálon.

### <a name="deploy-a-template-from-the-command-line"></a>A parancssorból sablon telepítése
A parancssori felület használatával, egyetlen parancs telepítheti a sablon egy meglévő erőforráscsoportot.
A sablon létrehozásával kapcsolatos információért lásd a következő.

```sh
# Login and validate your are in the right subscription context
az login

# Create a new resource group (you can use an existing one)
az group create --name <resource group name> --location <supported Azure region>
az group deployment create -n testdeploy -g <resource group name> --template-file <template-file.json> --parameters <parameters.json>
```

### <a name="deploy-a-template-from-the-azure-portal"></a>Azure-portálról egy sablon üzembe helyezése
Tetszés szerint hozhat létre és telepíthet egy sablont is használhatja az Azure portálon. Tegye a következőket:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** , és keressen a "sablon".
3. Válassza ki **sablonok**.
4. Kattintson a **+ Hozzáadás** , és másolja a sablon adatait. 
5. Válassza ki a #4. lépésben létrehozott sablont, és kattintson a **telepítés**.


## <a name="create-a-template-from-an-existing-azure-resource-in-the-azure-portal"></a>Hozzon létre egy sablont az Azure portál egy meglévő Azure-erőforrás
Ha már rendelkezik egy fiók érhető el, az Azure Machine kísérletezhet [Azure-portálon](https://portal.azure.com), az adott erőforrás is létrehozhat egy sablont. 

1. Keresse meg az Azure kísérletezhet fiók [Azure-portálon](https://portal.azure.com).
2. A **beállítások**, kattintson a **automatizálási parancsfájl**.
3. A sablon letöltése. 

Azt is megteheti manuálisan módosíthatja a sablonfájlokat importálni. Tekintse meg a következő példa egy sablon és a paraméterek fájlok. 

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
* AccountName: A kísérleti fiók nevét.
* Hely: Az egyik támogatott Azure-régiókban.
* Tárfiók Termékváltozat: Az Azure ML szabványos, nem prémium szintű storage csak támogatja. Tárolási kapcsolatos további információkért lásd: [storage bemutatása](https://docs.microsoft.com/azure/storage/common/storage-introduction). 

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

## <a name="next-steps"></a>Következő lépések
* [Hozzon létre és telepítse az Azure gépi tanulás](quickstart-installation.md)
