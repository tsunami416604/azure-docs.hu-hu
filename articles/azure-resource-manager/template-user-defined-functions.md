---
title: Felhasználó által definiált függvények a sablonokban
description: Ismerteti, hogyan lehet felhasználó által definiált függvényeket definiálni és használni egy Azure Resource Manager sablonban.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 94fdf80ffc309645a4bc10109a5e8dd4450731e2
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143703"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Felhasználó által definiált függvények Azure Resource Manager sablonban

A sablonon belül létrehozhat saját függvényeket is. Ezek a függvények a sablonban használhatók. A felhasználó által definiált függvények eltérhetnek a sablonon belül automatikusan elérhető [szabványos sablon-függvényektől](resource-group-template-functions.md) . Saját függvényeket hozhat létre, ha olyan bonyolult kifejezésekkel rendelkezik, amelyeket a sablonban ismételten használnak.

Ez a cikk azt ismerteti, hogyan adhat hozzá felhasználó által definiált függvényeket a Azure Resource Manager-sablonhoz.

## <a name="define-the-function"></a>A függvény megadása

A függvények névtér-értéket igényelnek, így elkerülhető a sablon függvényekkel való névütközés. A következő példa egy olyan függvényt mutat be, amely egy Storage-fiók nevét adja vissza:

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

Az alábbi példa bemutatja, hogyan hívhatja meg a függvényt.

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="limitations"></a>Korlátozások

A felhasználói függvények meghatározásakor bizonyos korlátozások vonatkoznak:

* A függvény nem fér hozzá a változókhoz.
* A függvény csak a függvényben definiált paramétereket tudja használni. Ha a [Parameters](resource-group-template-functions-deployment.md#parameters) függvényt egy felhasználó által definiált függvényen belül használja, akkor a függvény paraméterei vannak korlátozva.
* A függvény nem hívhat meg más, felhasználó által definiált függvényeket.
* A függvény nem tudja használni a [Reference](resource-group-template-functions-resource.md#reference) függvényt vagy a [List](resource-group-template-functions-resource.md#list) függvények egyikét sem.
* A függvény paraméterei nem rendelkezhetnek alapértelmezett értékekkel.


## <a name="next-steps"></a>További lépések

* A felhasználó által definiált függvények elérhető tulajdonságainak megismeréséhez tekintse meg [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](resource-group-authoring-templates.md)című témakört.
* A sablonhoz elérhető függvények listáját itt tekintheti meg: [Azure Resource Manager template functions](resource-group-template-functions.md).
