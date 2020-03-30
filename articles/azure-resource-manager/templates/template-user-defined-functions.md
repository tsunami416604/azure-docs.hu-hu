---
title: Felhasználó által definiált függvények a sablonokban
description: Bemutatja, hogyan definiálhatja és használhatja a felhasználó által definiált függvényeket egy Azure Resource Manager-sablonban.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 2c09572a460aa028b23987033d2b77e2aad8a0cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943218"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Felhasználó által definiált függvények az Azure Resource Manager-sablonban

A sablonon belül létrehozhatja saját funkcióit. Ezek a funkciók a sablonban használhatók. A felhasználó által definiált függvények elkülönülnek a sablonon belül automatikusan elérhető [szabványos sablonfüggvényektől.](template-functions.md) Hozzon létre saját függvényeket, ha bonyolult kifejezésekkel rendelkezik, amelyeket többször is használ a sablonban.

Ez a cikk ismerteti, hogyan adhat hozzá felhasználó által definiált függvényeket az Azure Resource Manager-sablonban.

## <a name="define-the-function"></a>A függvény meghatározása

A függvények névtérértéket igényelnek a sablonfüggvényekkel való ütközések elkerülése érdekében. A következő példa egy olyan függvényt mutat be, amely egyedi nevet ad vissza:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

## <a name="use-the-function"></a>A függvény használata

A következő példa egy felhasználó által definiált függvényt tartalmazó sablont mutat be. Ezt a függvényt használja egy tárfiók egyedi nevének lekérnie. A sablon egy **storageNamePrefix** nevű paraméterrel rendelkezik, amely paraméterként halad át a függvényben.

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
 "contentVersion": "1.0.0.0",
 "parameters": {
   "storageNamePrefix": {
     "type": "string",
     "maxLength": 11
   }
 },
 "functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
 "resources": [
   {
     "type": "Microsoft.Storage/storageAccounts",
     "apiVersion": "2019-04-01",
     "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
     "location": "South Central US",
     "sku": {
       "name": "Standard_LRS"
     },
     "kind": "StorageV2",
     "properties": {
       "supportsHttpsTrafficOnly": true
     }
   }
 ]
}
```

## <a name="limitations"></a>Korlátozások

A felhasználói függvény ek meghatározásakor vannak bizonyos korlátozások:

* A függvény nem tud hozzáférni a változókhoz.
* A függvény csak a függvényben definiált paramétereket használhatja. Ha a [paraméterek függvényt](template-functions-deployment.md#parameters) egy felhasználó által definiált függvényen belül használja, akkor az adott függvény paramétereire van korlátozva.
* A függvény nem hívhat meg más felhasználó által definiált függvényeket.
* A függvény nem használhatja a [referencia](template-functions-resource.md#reference) függvényt vagy a [lista](template-functions-resource.md#list) függvényeket.
* A függvény paraméterei nem rendelkezhetnek alapértelmezett értékekkel.


## <a name="next-steps"></a>További lépések

* A felhasználó által definiált függvények elérhető tulajdonságairól [az Azure Resource Manager-sablonok szerkezetének és szintaxisának megismerése című témakörben olvashat.](template-syntax.md)
* A rendelkezésre álló sablonfüggvények listáját az [Azure Resource Manager sablonfüggvényei című témakörben olvashatja.](template-functions.md)
