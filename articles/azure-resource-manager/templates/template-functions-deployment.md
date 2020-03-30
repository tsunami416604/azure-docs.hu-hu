---
title: Sablonfüggvények - telepítés
description: Ismerteti az Azure Resource Manager-sablonban a központi telepítési adatok lekéréséhez használandó függvényeket.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 86a1d3d7e05fedacd7a3c044ecab241ca9d059c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156327"
---
# <a name="deployment-functions-for-arm-templates"></a>Telepítési függvények ARM-sablonokhoz 

Az Erőforrás-kezelő a következő függvényeket biztosítja az Azure Resource Manager (ARM) sablon aktuális telepítéséhez kapcsolódó értékek beszerzéséhez:

* [Telepítési](#deployment)
* [Környezet](#environment)
* [Paraméterek](#parameters)
* [Változók](#variables)

Az erőforrásokból, erőforráscsoportokból vagy előfizetésekből származó értékek beolvassa az [Erőforrásfüggvények témakört.](template-functions-resource.md)

## <a name="deployment"></a>üzembe helyezés

`deployment()`

Az aktuális telepítési művelettel kapcsolatos információkat adja vissza.

### <a name="return-value"></a>Visszatérítési érték

Ez a függvény az üzembe helyezés során átadott objektumot adja vissza. A visszaadott objektum tulajdonságai attól függően eltérnek, hogy a központi telepítési objektum hivatkozásként vagy soros objektumként kerül-e átadásra. Amikor a központi telepítési objektum ot átad nak a sorban, például amikor az Azure PowerShell **-TemplateFile** paraméterét használja egy helyi fájlra való ponthoz, a visszaadott objektum formátuma a következő:

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

Ha az objektumot hivatkozásként adják át, például amikor a **-TemplateUri** paramétert használja egy távoli objektumra való ponthoz, az objektum a következő formátumban jelenik meg: 

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

Ha [egy Azure-előfizetésre telepíti,](deploy-to-subscription.md)erőforráscsoport helyett, `location` a visszatérési objektum tartalmaz egy tulajdonságot. A helytulajdonság helyi vagy külső sablon telepítésekor is szerepel.

### <a name="remarks"></a>Megjegyzések

A deployment() segítségével a szülősablon URI-ja alapján egy másik sablonra hivatkozhat.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

Ha újratelepít egy sablont a portál on-mail-előzményeiből, a sablon helyi fájlként lesz telepítve. A `templateLink` tulajdonság nem adja vissza a központi telepítési függvény. Ha a sablon `templateLink` támaszkodik egy másik sablonra mutató hivatkozás létrehozásához, ne használja a portált az újratelepítéshez. Ehelyett használja a sablon eredeti telepítéséhez használt parancsokat.

### <a name="example"></a>Példa

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) a központi telepítési objektumot adja vissza:

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

Az előző példa a következő objektumot adja vissza:

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

A központi telepítési függvényt használó előfizetési szintű sablonról az [előfizetés-telepítési funkció című témakörben olvashat.](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json) Vagy `az deployment create` `New-AzDeployment` parancsokkal van bevetve.

## <a name="environment"></a>environment

`environment()`

A központi telepítéshez használt Azure-környezetre vonatkozó információkat adja vissza.

### <a name="return-value"></a>Visszatérítési érték

Ez a függvény az aktuális Azure-környezet tulajdonságait adja vissza. A következő példa a globális Azure tulajdonságait mutatja be. A szuverén felhők kissé eltérő tulajdonságokat adhatnak vissza.

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

A következő példasablon a környezeti objektumot adja vissza.

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

Az előző példa a következő objektumot adja vissza, amikor a globális Azure-ra van telepítve:

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

Paraméterértéket ad eredményül. A megadott paraméternevet a sablon paraméterek szakaszában kell megadni.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| paraméternév |Igen |sztring |A visszaadandó paraméter neve. |

### <a name="return-value"></a>Visszatérítési érték

A megadott paraméter értéke.

### <a name="remarks"></a>Megjegyzések

Általában paramétereket használ az erőforrás-értékek beállításához. A következő példa a webhely nevét a központi telepítés során megadott paraméterértékre állítja be.

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

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) a paraméterek függvény egyszerűsített használatát mutatja be.

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| karakterlánckimenet | Sztring | 1. lehetőség |
| intOutput | Int | 1 |
| objectOutput | Objektum | {"egy": "a", "kettő": "b"} |
| tömbkimenet | Tömb | [1, 2, 3] |
| crossOutput (keresztkimenet) | Sztring | 1. lehetőség |

A paraméterek használatáról további információt a [Paraméterek az Azure Resource Manager sablonban című témakörben talál.](template-parameters.md)

## <a name="variables"></a>Változók

`variables(variableName)`

A változó értékét adja eredményül. A megadott változónevet a sablon változók szakaszában kell definiálni.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| változóNév |Igen |Sztring |A visszaadandó változó neve. |

### <a name="return-value"></a>Visszatérítési érték

A megadott változó értéke.

### <a name="remarks"></a>Megjegyzések

Általában változókkal egyszerűsítheti a sablont, ha csak egyszer hoz létre összetett értékeket. A következő példa egy tárfiók egyedi nevét hoz létre.

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

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) különböző változóértékeket ad vissza.

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| példaKimenet1 | Sztring | myVariable |
| példaKimenet2 | Tömb | [1, 2, 3, 4] |
| példaKimenet3 | Sztring | myVariable |
| példaKimenet4 |  Objektum | {"property1": "value1", "property2": "value2"} |

A változók használatáról a [Változók az Azure Resource Manager sablonban című témakörben olvashat bővebben.](template-variables.md)

## <a name="next-steps"></a>További lépések
* Az Azure Resource Manager-sablon szakaszainak leírását az [Azure Resource Manager-sablonok készítése című témakörben találja.](template-syntax.md)
* Több sablon egyesítéséhez olvassa el a [Csatolt sablonok használata az Azure Resource Manager rel.](linked-templates.md)
* Ha egy erőforrástípus létrehozásakor meghatározott számú alkalommal szeretne meghaladni, olvassa [el az Erőforrások több példányának létrehozása az Azure Resource Manager ben című témakört.](copy-resources.md)
* A létrehozott sablon központi telepítéséről az Alkalmazás üzembe helyezése az [Azure Resource Manager sablonnal című témakörben](deploy-powershell.md)olvashat.

