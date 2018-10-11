---
title: Erőforráscsoport létrehozása az Azure Resource Manager-sablonnal |} A Microsoft Docs
description: Ismerteti, hogyan lehet egy Azure Resource Manager-sablonban hozzon létre egy új erőforráscsoportot.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: 87f81c6b6568cd63eaf10840043511669d634062
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079327"
---
# <a name="create-resource-groups-in-azure-resource-manager-templates"></a>Erőforráscsoportok létrehozása az Azure Resource Manager-sablonok

Hozzon létre egy erőforráscsoportot az Azure Resource Manager-sablon, adjon meg egy **Microsoft.Resources/resourceGroups** erőforrás nevét és az erőforráscsoport helyét. A sablon üzembe helyezése az Azure-előfizetéshez. Előfizetés-szintű telepítések kapcsolatos további információkért lásd: [erőforrások üzembe helyezése az Azure-előfizetés](deploy-to-subscription.md).

Az adott erőforráscsoporton ugyanazt a sablont az erőforrások is telepítheti.

Ez a cikk az Azure CLI és PowerShell használatával a sablonok üzembe helyezése.

## <a name="create-empty-resource-group"></a>Üres erőforráscsoport létrehozása

A következő példában létrehozunk egy üres erőforráscsoportot.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Ez a sablon Azure CLI-vel üzembe helyezéséhez használja:

```azurecli-interactive
az deployment create \
  -n demoEmptyRG \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoRG rgLocation=northcentralus
```

Ez a PowerShell használatával a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzureRmDeployment `
  -Name demoEmptyRG `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demogroup `
  -rgLocation northcentralus
```

## <a name="create-several-resource-groups"></a>Számos erőforrás-csoportok létrehozása

Használja a [másolási eleme](resource-group-create-multiple.md) erőforráscsoportokkal több erőforráscsoport létrehozásához. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgNamePrefix": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "instanceCount": {
            "type": "int"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
            "copy": {
                "name": "rgCopy",
                "count": "[parameters('instanceCount')]"
            },
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Telepítse ezt a sablont az Azure CLI-vel, és három erőforráscsoportokat létrehozni, használja:

```azurecli-interactive
az deployment create \
  -n demoCopyRG \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/copyRG.json \
  --parameters rgNamePrefix=demoRG rgLocation=northcentralus instanceCount=3
```

Ez a PowerShell használatával a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzureRmDeployment `
  -Name demoCopyRG `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/copyRG.json `
  -rgNamePrefix demogroup `
  -rgLocation northcentralus `
  -instanceCount 3
```

## <a name="create-resource-group-and-deploy-resource"></a>Erőforráscsoport létrehozása és üzembe helyezése erőforrás

Hozhat létre az erőforráscsoport és erőforrások történő üzembe helyezéséhez használjon a beágyazott sablont. A beágyazott sablont határozza meg, az erőforráscsoport üzembe helyezendő erőforrásokat. A beágyazott sablont állítja az erőforráscsoport, hogy az erőforráscsoport létezik az erőforrások üzembe helyezése előtt függ.

Az alábbi példa létrehoz egy erőforráscsoportot, és a egy storage-fiók telepíti az erőforráscsoport.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "storageDeployment",
            "resourceGroup": "[parameters('rgName')]",
            "dependsOn": [
                "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2017-10-01",
                            "name": "[variables('storageName')]",
                            "location": "[parameters('rgLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ],
    "outputs": {}
}
```

Ez a sablon Azure CLI-vel üzembe helyezéséhez használja:

```azurecli-interactive
az deployment create \
  -n demoRGStorage \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/newRGWithStorage.json \
  --parameters rgName=rgStorage rgLocation=northcentralus storagePrefix=storage
```

Ez a PowerShell használatával a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzureRmDeployment `
  -Name demoRGStorage `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/newRGWithStorage.json `
  -rgName rgStorage `
  -rgLocation northcentralus `
  -storagePrefix storage
```

## <a name="next-steps"></a>További lépések
* Előfizetés-szintű telepítések kapcsolatos további információkért lásd: [erőforrások üzembe helyezése az Azure-előfizetés](deploy-to-subscription.md).
* Üzembe helyezés során függőségek hibaelhárítással kapcsolatos tudnivalókért lásd: [hibáinak elhárítása a közös Azure-beli hibák az Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Az Azure Resource Manager-sablonok létrehozásával kapcsolatos további információkért lásd: [sablonok készítése](resource-group-authoring-templates.md). 
* A sablonban rendelkezésre álló függvények listája: [sablonfüggvények](resource-group-template-functions.md).

