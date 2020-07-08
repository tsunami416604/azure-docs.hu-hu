---
title: Felhasználó által definiált függvények a sablonokban
description: Ismerteti, hogyan lehet felhasználó által definiált függvényeket definiálni és használni egy Azure Resource Manager sablonban.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 69f4e98d389cc8dbe5cd3f4b628189676c501106
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84672935"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Felhasználó által definiált függvények Azure Resource Manager sablonban

A sablonon belül létrehozhat saját függvényeket is. Ezek a függvények a sablonban használhatók. A felhasználó által definiált függvények eltérhetnek a sablonon belül automatikusan elérhető [szabványos sablon-függvényektől](template-functions.md) . Saját függvényeket hozhat létre, ha olyan bonyolult kifejezésekkel rendelkezik, amelyeket a sablonban ismételten használnak.

Ez a cikk azt ismerteti, hogyan adhat hozzá felhasználó által definiált függvényeket a Azure Resource Manager-sablonhoz.

## <a name="define-the-function"></a>A függvény megadása

A függvények névtér-értéket igényelnek, így elkerülhető a sablon függvényekkel való névütközés. A következő példa egy olyan függvényt mutat be, amely egyedi nevet ad vissza:

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

A következő példa egy olyan sablont mutat be, amely egy felhasználó által definiált függvényt tartalmaz. Ezt a függvényt használja a Storage-fiók egyedi nevének beszerzéséhez. A sablon egy **storageNamePrefix** nevű paraméterrel rendelkezik, amelyet paraméterként a függvénynek továbbít.

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

A felhasználói függvények meghatározásakor bizonyos korlátozások vonatkoznak:

* A függvény nem fér hozzá a változókhoz.
* A függvény csak a függvényben definiált paramétereket tudja használni. Ha a [Parameters](template-functions-deployment.md#parameters) függvényt egy felhasználó által definiált függvényen belül használja, akkor a függvény paraméterei vannak korlátozva.
* A függvény nem hívhat meg más, felhasználó által definiált függvényeket.
* A függvény nem tudja használni a [Reference](template-functions-resource.md#reference) függvényt vagy a [List](template-functions-resource.md#list) függvények egyikét sem.
* A függvény paraméterei nem rendelkezhetnek alapértelmezett értékekkel.


## <a name="next-steps"></a>További lépések

* A felhasználó által definiált függvények elérhető tulajdonságainak megismeréséhez tekintse meg [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](template-syntax.md)című témakört.
* A sablonhoz elérhető függvények listáját itt tekintheti meg: [Azure Resource Manager template functions](template-functions.md).
