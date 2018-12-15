---
title: Az Azure Resource Manager sablonfüggvényei – üzembe helyezés |} A Microsoft Docs
description: A funkciók az Azure Resource Manager-sablon használatával lekérheti az üzembe helyezés információkat ismerteti.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2018
ms.author: tomfitz
ms.openlocfilehash: d802af1d48405518f26f4b52ecc3023cbb15caff
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407354"
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Központi telepítési funkciók az Azure Resource Manager-sablonok 

Erőforrás-kezelő az alábbi funkciókat biztosít értékek lekérése a sablont és az értékek üzembe helyezésével kapcsolatos szakaszait:

* [Üzembe helyezés](#deployment)
* [paraméterek](#parameters)
* [Változók](#variables)

Erőforrások, erőforráscsoportok vagy előfizetések lekérjük az értékeket, lásd: [erőforrásfüggvények](resource-group-template-functions-resource.md).

<a id="deployment" />

## <a name="deployment"></a>üzembe helyezés
`deployment()`

A jelenlegi üzembe helyezési művelet adatait adja vissza.

### <a name="return-value"></a>Vrácená hodnota

Ez a függvény üzembe helyezése során átadott objektumot adja vissza. A visszaadott objektum tulajdonság e a telepítési objektum átadása egy hivatkozást, vagy egy beágyazott objektumként függően eltérőek lehetnek. Ha a központi telepítési objektum átadása a sor végét, mint például használatakor a **- TemplateFile** paramétert az Azure PowerShell helyi fájlra mutat, a visszaadott objektum rendelkezik a következő formátumban:

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

Ha az objektum átadása egy hivatkozással, mint például a használatakor a **- TemplateUri** paramétert, hogy egy távoli objektumot, mutasson az objektum a következő formátumban adja vissza: 

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

Ha Ön [üzembe helyezése Azure-előfizetéssel](deploy-to-subscription.md), helyett egy erőforráscsoport, a visszaadott objektum tartalmaz egy `location` tulajdonság. A location tulajdonság szerepel, vagy egy helyi sablont, vagy egy külső sablon üzembe helyezésekor.

### <a name="remarks"></a>Megjegyzések

Használhatja a deployment() összekapcsolása egy másik sablon URI-ját a fölérendelt sablon alapján.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

### <a name="example"></a>Példa

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) a központi telepítési objektumot ad vissza:

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

Az előző példában a következő objektumot ad vissza:

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

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

Egy előfizetés-szintű sablont, amely a központi telepítési függvényt használja, lásd: [előfizetés üzembe helyezési funkció](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json). A következők egyikével telepítették `az deployment create` vagy `New-AzureRmDeployment` parancsokat.

<a id="parameters" />

## <a name="parameters"></a>paraméterek
`parameters(parameterName)`

A paraméter értékét adja vissza. A megadott paraméternév a sablon a Paraméterek szakaszban kell definiálni.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| parameterName |Igen |sztring |A visszaadandó paraméter neve. |

### <a name="return-value"></a>Vrácená hodnota

A megadott paraméter értékét.

### <a name="remarks"></a>Megjegyzések

Általában használni a paraméterek erőforrás értékeinek beállításához. Az alábbi példában a webhely nevét a telepítés során átadott paraméter értéke állítja.

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

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) egy egyszerűsített a paraméterek függvény használatát mutatja be.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| stringOutput | Karakterlánc | 1. lehetőséget |
| intOutput | Int | 1 |
| objectOutput | Objektum | {"egy": "a", "2": "b"} |
| arrayOutput | Tömb | [1, 2, 3] |
| crossOutput | Karakterlánc | 1. lehetőséget |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

<a id="variables" />

## <a name="variables"></a>Változók
`variables(variableName)`

A változó értékét adja vissza. A megadott változónév a sablon a változók szakaszban kell definiálni.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| variableName |Igen |Karakterlánc |A visszaadandó változó neve. |

### <a name="return-value"></a>Vrácená hodnota

A megadott változó értékét.

### <a name="remarks"></a>Megjegyzések

Általában a változókkal egyszerűsítése érdekében a sablont hozhat létre komplex értékek csak egyszer. Az alábbi példa létrehoz egy storage-fiók egy egyedi nevet.

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

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) különböző változó értékeit adja vissza.

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| exampleOutput1 | Karakterlánc | myVariable |
| exampleOutput2 | Tömb | [1, 2, 3, 4] |
| exampleOutput3 | Karakterlánc | myVariable |
| exampleOutput4 |  Objektum | {"Tulajdonság1": "érték1", "Tulajdonság2": "érték2"} |

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

## <a name="next-steps"></a>További lépések
* A szakaszok az Azure Resource Manager-sablon ismertetését lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
* Egyesíthet több sablont, tekintse meg [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).
* A megadott számú alkalommal újrafuttathatja egy adott típusú erőforrás létrehozásakor, lásd: [több erőforráspéldány létrehozása az Azure Resource Manager](resource-group-create-multiple.md).
* A létrehozott sablon üzembe helyezése, olvassa el [alkalmazás üzembe helyezése Azure Resource Manager-sablonnal](resource-group-template-deploy.md).

