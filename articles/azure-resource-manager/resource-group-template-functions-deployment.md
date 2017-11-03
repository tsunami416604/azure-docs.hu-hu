---
title: "Az Azure Resource Manager sablonfüggvényei - telepítés |} Microsoft Docs"
description: "A központi telepítési információk beolvasása az Azure Resource Manager-sablonok használatára funkcióit ismerteti."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2017
ms.author: tomfitz
ms.openlocfilehash: 17fe2bc467acc5542d021961a066940dbecf6120
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Központi telepítési funkciók az Azure Resource Manager sablonokhoz 

Erőforrás-kezelő a következő funkciókat nyújt értékek lekérése a sablon és a központi telepítéshez kapcsolódó értékek szakaszait:

* [központi telepítés](#deployment)
* [Paraméterek](#parameters)
* [változók](#variables)

Értékek erőforrásokat, erőforráscsoport-sablonok vagy előfizetések megtekinteni [erőforrás funkciók](resource-group-template-functions-resource.md).

<a id="deployment" />

## <a name="deployment"></a>üzembe helyezés
`deployment()`

Az aktuális központi telepítési művelet információt ad vissza.

### <a name="return-value"></a>Visszatérési érték

Ez a funkció a telepítés során átadott objektum adja vissza. A tulajdonságokat a visszaadott objektumot az attól függően változnak, hogy a központi telepítési objektum közlekednek hivatkozásként vagy beágyazott objektumként. Ha a központi telepítési objektum átadása egysoros, többek között használata esetén a **- TemplateFile** az Azure PowerShell helyi fájlra mutat, a visszaadott objektumot paraméternek a következő formátumban:

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Ha az objektum átadása hivatkozásként, például a használatakor a **- TemplateUri** paraméterrel a távoli objektumra mutat az objektumot ad vissza a következő formátumban: 

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

### <a name="remarks"></a>Megjegyzések

Deployment() használatával kapcsolódik egy másik sablont, az URI a szülő-sablon alapján.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) a központi telepítési objektumot ad vissza:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

A fenti példa adja a következő objektumot:

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "subscriptionOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

<a id="parameters" />

## <a name="parameters"></a>paraméterek
`parameters(parameterName)`

A paraméter értékét adja vissza. A megadott paraméternév definiálni kell a sablon a Paraméterek szakaszban.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| parameterName |Igen |Karakterlánc |Vissza a paraméter neve. |

### <a name="return-value"></a>Visszatérési érték

A megadott paraméter értéke.

### <a name="remarks"></a>Megjegyzések

Általában használja a paraméterek erőforrás értékeinek beállításához. A következő példa a telepítés során az átadott paraméter értéke a webhely nevének megadása.

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2016-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) paraméterek függvény egyszerűsített felhasználási látható.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringParameter": {
            "type" : "string",
            "defaultValue": "option 1"
        },
        "intParameter": {
            "type": "int",
            "defaultValue": 1
        },
        "objectParameter": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b"}
        },
        "arrayParameter": {
            "type": "array",
            "defaultValue": [1, 2, 3]
        },
        "crossParameter": {
            "type": "string",
            "defaultValue": "[parameters('stringParameter')]"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "stringOutput": {
            "value": "[parameters('stringParameter')]",
            "type" : "string"
        },
        "intOutput": {
            "value": "[parameters('intParameter')]",
            "type" : "int"
        },
        "objectOutput": {
            "value": "[parameters('objectParameter')]",
            "type" : "object"
        },
        "arrayOutput": {
            "value": "[parameters('arrayParameter')]",
            "type" : "array"
        },
        "crossOutput": {
            "value": "[parameters('crossParameter')]",
            "type" : "string"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| stringOutput | Karakterlánc | 1. lehetőséget |
| intOutput | int | 1 |
| objectOutput | Objektum | {"egy": "a", "2": "b"} |
| arrayOutput | Tömb | [1, 2, 3] |
| crossOutput | Karakterlánc | 1. lehetőséget |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

<a id="variables" />

## <a name="variables"></a>változók
`variables(variableName)`

A változó értékét adja vissza. A változók szakaszban a sablon a megadott változónév definiálni kell.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| variableName |Igen |Karakterlánc |Vissza a változó neve. |

### <a name="return-value"></a>Visszatérési érték

A megadott változó értékét.

### <a name="remarks"></a>Megjegyzések

Általában a változókkal egyszerűbbé teheti a sablont hozhat létre csak egyszer összetett értékeket. A következő példa egy egyedi nevet a tárfiók hoz létre.

```json
"variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
    },
    {
        "type": "Microsoft.Compute/virtualMachines",
        "dependsOn": [
            "[variables('storageName')]"
        ],
        ...
    }
],
```

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) különböző változók értékeinek adja vissza.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {
        "var1": "myVariable",
        "var2": [ 1,2,3,4 ],
        "var3": "[ variables('var1') ]",
        "var4": {
            "property1": "value1",
            "property2": "value2"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput1": {
            "value": "[variables('var1')]",
            "type" : "string"
        },
        "exampleOutput2": {
            "value": "[variables('var2')]",
            "type" : "array"
        },
        "exampleOutput3": {
            "value": "[variables('var3')]",
            "type" : "string"
        },
        "exampleOutput4": {
            "value": "[variables('var4')]",
            "type" : "object"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| exampleOutput1 | Karakterlánc | myVariable |
| exampleOutput2 | Tömb | [1, 2, 3, 4] |
| exampleOutput3 | Karakterlánc | myVariable |
| exampleOutput4 |  Objektum | {"Tulajdonság1": "érték1", "Tulajdonság2": "érték2"} |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

## <a name="next-steps"></a>Következő lépések
* A szakaszok az Azure Resource Manager-sablon ismertetését lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
* Több sablon egyesíteni, lásd: [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).
* Megadott számú alkalommal felépítésének egy adott típusú erőforrás létrehozása esetén lásd: [erőforrások több példánya létrehozása az Azure Resource Manager](resource-group-create-multiple.md).
* A sablon létrehozott központi telepítéséről, olvassa el [Azure Resource Manager-sablon az alkalmazás központi telepítését](resource-group-template-deploy.md).

