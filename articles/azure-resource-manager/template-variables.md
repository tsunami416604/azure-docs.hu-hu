---
title: Változók a sablonokban
description: Leírja, hogyan határozhat meg változókat egy Azure Resource Manager sablonban.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 5bd7acd759c553e629febdb141aefd63cfa4dd4b
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149088"
---
# <a name="variables-in-azure-resource-manager-template"></a>Változók Azure Resource Manager sablonban

Ez a cikk bemutatja, hogyan definiálhat és használhat változókat a Azure Resource Manager-sablonban. Változók használatával egyszerűsítheti a sablont. Ahelyett, hogy a sablonban megismételje a bonyolult kifejezéseket, Definiáljon egy változót, amely tartalmazza a bonyolult kifejezést. Ezután a sablonban szükség szerint hivatkozhat erre a változóra.

A Resource Manager a telepítési műveletek megkezdése előtt feloldja a változókat. Ha a változót használja a sablonban, a Resource Manager lecseréli azt a megoldott értékre.

## <a name="define-variable"></a>Változó meghatározása

Az alábbi példa egy változó definícióját mutatja be. Létrehoz egy karakterlánc-értéket a Storage-fiók nevéhez. Számos sablon függvényt használ a paraméterérték beolvasásához, és összefűzi azt egy egyedi karakterlánchoz.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

A változók szakaszban nem használható a [Reference](resource-group-template-functions-resource.md#reference) függvény vagy a [List](resource-group-template-functions-resource.md#list) függvények egyike sem. Ezek a függvények egy erőforrás futásidejű állapotát kapják meg, és a változók feloldása előtt nem hajthatók végre.

## <a name="use-variable"></a>Változó használata

A sablonban a [változók](resource-group-template-functions-deployment.md#variables) függvény használatával hivatkozhat a paraméter értékére. Az alábbi példa bemutatja, hogyan használható a változó egy erőforrás-tulajdonsághoz.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="configuration-variables"></a>Konfigurációs változók

Meghatározhatja a környezet konfigurálásához kapcsolódó értékeket tároló változókat. A változót az értékekkel rendelkező objektumként definiálhatja. A következő példa egy olyan objektumot mutat be, amely két környezet, a **test** és a **Prod**értékeit tárolja.

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

A paraméterek területen létrehoz egy értéket, amely jelzi, hogy mely konfigurációs értékeket kell használni.

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

A megadott környezet beállításainak beolvasásához használja együtt a változót és a paramétert.

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="example-templates"></a>Példa sablonok

Az alábbi példák a változók használatára vonatkozó forgatókönyveket szemléltetik.

|Sablon  |Leírás  |
|---------|---------|
| [változók definíciói](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Bemutatja a különböző típusú változókat. A sablon nem telepít semmilyen erőforrást. Változó értékeket hoz létre, és visszaadja ezeket az értékeket. |
| [konfigurációs változó](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | A konfigurációs értékeket meghatározó változó használatát mutatja be. A sablon nem telepít semmilyen erőforrást. Változó értékeket hoz létre, és visszaadja ezeket az értékeket. |
| [hálózati biztonsági szabályok](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) és [paraméter fájl](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | A megfelelő formátumú tömböt hoz létre a biztonsági szabályok hálózati biztonsági csoporthoz való hozzárendeléséhez. |

## <a name="next-steps"></a>További lépések

* A változók elérhető tulajdonságainak megismeréséhez tekintse meg [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](resource-group-authoring-templates.md)című témakört.
* A változók létrehozásával kapcsolatos javaslatokért lásd: [ajánlott eljárások – változók](template-best-practices.md#variables).
