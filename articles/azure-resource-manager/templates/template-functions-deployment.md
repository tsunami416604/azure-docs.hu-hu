---
title: Sablon functions – üzembe helyezés
description: Ismerteti a Azure Resource Manager-sablonban a telepítési információk lekéréséhez használandó függvényeket.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: e8240c05cba82d5563c4b327ecbc65a9c358720f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84677814"
---
# <a name="deployment-functions-for-arm-templates"></a>Az ARM-sablonok üzembe helyezési funkciói

A Resource Manager a következő függvényeket biztosítja a Azure Resource Manager-(ARM-) sablon aktuális üzembe helyezéséhez kapcsolódó értékek beolvasásához:

* [telepítési](#deployment)
* [környezet](#environment)
* [paraméterek](#parameters)
* [változók](#variables)

Erőforrások, erőforráscsoportok vagy előfizetések értékeinek lekéréséhez lásd: [Resource functions](template-functions-resource.md).

## <a name="deployment"></a>üzembe helyezés

`deployment()`

A jelenlegi telepítési műveletre vonatkozó adatokat adja vissza.

### <a name="return-value"></a>Visszatérési érték

Ez a függvény az üzembe helyezés során átadott objektumot adja vissza. A visszaadott objektum tulajdonságai eltérnek attól függően, hogy:

* olyan sablon telepítése, amely helyi fájl, vagy olyan sablon központi telepítése, amely egy URI-n keresztül elért távoli fájl.
* egy erőforráscsoport üzembe helyezése vagy az egyik másik hatókör ([Azure-előfizetés](deploy-to-subscription.md), [felügyeleti csoport](deploy-to-management-group.md)vagy [bérlő](deploy-to-tenant.md)) üzembe helyezése.

Helyi sablon erőforráscsoporthoz való telepítésekor: a függvény a következő formátumot adja vissza:

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
        "templateHash": "",
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Távoli sablon erőforráscsoporthoz való telepítésekor: a függvény a következő formátumot adja vissza:

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
        "templateHash": "",
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Ha Azure-előfizetésre, felügyeleti csoportra vagy bérlőre végez üzembe helyezést, a Return objektum tartalmaz egy `location` tulajdonságot. Helyi sablon vagy külső sablon telepítésekor a Location tulajdonság is szerepel. A formátum:

```json
{
    "name": "",
    "location": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "resources": [],
            "outputs": {}
        },
        "templateHash": "",
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

### <a name="remarks"></a>Megjegyzések

A központi telepítés () használatával egy másik sablonra lehet hivatkozni a fölérendelt sablon URI-ja alapján.

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

Ha a portálon lévő központi telepítési előzményekből telepít újra egy sablont, a sablon helyi fájlként lesz telepítve. A `templateLink` tulajdonságot a rendszer nem adja vissza a telepítési függvényben. Ha a sablon `templateLink` egy másik sablonra mutató hivatkozást hoz létre, ne használja a portált az újbóli üzembe helyezéshez. Ehelyett használja a sablon eredeti üzembe helyezéséhez használt parancsokat.

### <a name="example"></a>Példa

A következő [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) a központi telepítési objektumot adja vissza:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "deploymentOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

Az előző példa a következő objektumot adja vissza:

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "deploymentOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "templateHash": "13135986259522608210",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

## <a name="environment"></a>environment

`environment()`

Információt ad vissza az üzembe helyezéshez használt Azure-környezetről.

### <a name="return-value"></a>Visszatérési érték

Ez a függvény az aktuális Azure-környezet tulajdonságait adja vissza. Az alábbi példa a globális Azure tulajdonságait mutatja be. A szuverén felhők némileg eltérő tulajdonságokat adhatnak vissza.

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>Példa

A következő példában szereplő sablon a környezeti objektumot adja vissza.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "environmentOutput": {
            "value": "[environment()]",
            "type" : "object"
        }
    }
}
```

Az előző példa a következő objektumot adja vissza a globális Azure-ba való üzembe helyezéskor:

```json
{
  "name": "AzureCloud",
  "gallery": "https://gallery.azure.com/",
  "graph": "https://graph.windows.net/",
  "portal": "https://portal.azure.com",
  "graphAudience": "https://graph.windows.net/",
  "activeDirectoryDataLake": "https://datalake.azure.net/",
  "batch": "https://batch.core.windows.net/",
  "media": "https://rest.media.azure.net",
  "sqlManagement": "https://management.core.windows.net:8443/",
  "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
  "resourceManager": "https://management.azure.com/",
  "authentication": {
    "loginEndpoint": "https://login.windows.net/",
    "audiences": [
      "https://management.core.windows.net/",
      "https://management.azure.com/"
    ],
    "tenant": "common",
    "identityProvider": "AAD"
  },
  "suffixes": {
    "acrLoginServer": ".azurecr.io",
    "azureDatalakeAnalyticsCatalogAndJob": "azuredatalakeanalytics.net",
    "azureDatalakeStoreFileSystem": "azuredatalakestore.net",
    "azureFrontDoorEndpointSuffix": "azurefd.net",
    "keyvaultDns": ".vault.azure.net",
    "sqlServerHostname": ".database.windows.net",
    "storage": "core.windows.net"
  }
}
```

## <a name="parameters"></a>paraméterek

`parameters(parameterName)`

Egy paraméter értékét adja vissza. A megadott paraméter nevét meg kell adni a sablon paraméterek szakaszában.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| parameterName |Yes |sztring |A visszaadni kívánt paraméter neve. |

### <a name="return-value"></a>Visszatérési érték

A megadott paraméter értéke.

### <a name="remarks"></a>Megjegyzések

Általában paraméterekkel állíthatja be az erőforrás-értékeket. A következő példa beállítja a webhely nevét az üzembe helyezés során átadott paraméterérték értékére.

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

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) a Parameters függvény egyszerűsített használatát mutatja be.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Name | Típus | Érték |
| ---- | ---- | ----- |
| stringOutput | Sztring | 1. lehetőség |
| intOutput | Int | 1 |
| objectOutput | Objektum | {"One": "a", "kettő": "b"} |
| arrayOutput | Tömb | [1, 2, 3] |
| crossOutput | Sztring | 1. lehetőség |

További információ a paraméterek használatáról: [Azure Resource Manager sablonban található paraméterek](template-parameters.md).

## <a name="variables"></a>változók

`variables(variableName)`

A változó értékét adja vissza. A megadott változó nevét meg kell adni a sablon változók szakaszában.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| variableName |Yes |Sztring |A visszaadni kívánt változó neve. |

### <a name="return-value"></a>Visszatérési érték

A megadott változó értéke.

### <a name="remarks"></a>Megjegyzések

Általában változók használatával egyszerűsítheti a sablont úgy, hogy csak egyszer hozza létre az összetett értékeket. A következő példa egy egyedi nevet hoz létre egy Storage-fiókhoz.

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

A következő [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) eltérő változó értékeket ad vissza.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Name | Típus | Érték |
| ---- | ---- | ----- |
| exampleOutput1 | Sztring | myVariable |
| exampleOutput2 | Tömb | [1, 2, 3, 4] |
| exampleOutput3 | Sztring | myVariable |
| exampleOutput4 |  Objektum | {"Tulajdonság1": "érték1", "property2": "érték2"} |

További információ a változók használatáról: [változók Azure Resource Manager sablonban](template-variables.md).

## <a name="next-steps"></a>További lépések

* Egy Azure Resource Manager sablonban található részekről az [ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető cikk nyújt tájékoztatást.
