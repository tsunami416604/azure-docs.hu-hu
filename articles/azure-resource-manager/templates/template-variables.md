---
title: Változók a sablonokban
description: Leírja, hogyan lehet változókat definiálni egy Azure Resource Manager sablonban (ARM-sablon).
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 7f782f9c7d3107472a74fcab73290c4cebf73693
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934662"
---
# <a name="variables-in-arm-template"></a>Az ARM-sablonban szereplő változók

Ez a cikk bemutatja, hogyan határozhat meg és használhat változókat a Azure Resource Manager-sablonban (ARM-sablon). Változók használatával egyszerűsítheti a sablont. Ahelyett, hogy a sablonban megismételje a bonyolult kifejezéseket, Definiáljon egy változót, amely tartalmazza a bonyolult kifejezést. Ezután a sablonban szükség szerint hivatkozhat erre a változóra.

A Resource Manager a telepítési műveletek megkezdése előtt feloldja a változókat. Mindenhol, ahol a sablonban a változót kell használni, a Resource Manager lecseréli a változót a feloldott értékre.

Az egyes változók formátumának meg kell egyeznie az egyik [adattípussal](template-syntax.md#data-types).

## <a name="define-variable"></a>Változó meghatározása

Az alábbi példa egy változó definícióját mutatja be. Létrehoz egy karakterlánc-értéket a Storage-fiók nevéhez. Számos sablon függvényt használ a paraméterérték beolvasásához, és összefűzi azt egy egyedi karakterlánchoz.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

A () szakaszban nem használható a [hivatkozási](template-functions-resource.md#reference) függvény vagy a [List](template-functions-resource.md#list) függvények egyike sem `variables` . Ezek a függvények egy erőforrás futásidejű állapotát kapják meg, és a változók feloldása előtt nem hajthatók végre.

## <a name="use-variable"></a>Változó használata

A sablonban a [változók](template-functions-deployment.md#variables) függvény használatával hivatkozhat a paraméter értékére. Az alábbi példa bemutatja, hogyan használható a változó egy erőforrás-tulajdonsághoz.

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

Meghatározhatja a környezet konfigurálásához kapcsolódó értékeket tároló változókat. A változót az értékekkel rendelkező objektumként definiálhatja. A következő példa egy olyan objektumot mutat be, amely két környezet, a **test** és a **Prod** értékeit tárolja.

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

A-ben `parameters` létrehoz egy értéket, amely jelzi, hogy mely konfigurációs értékeket kívánja használni.

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

## <a name="example-templates"></a>Példák sablonokra

Az alábbi példák a változók használatára vonatkozó forgatókönyveket szemléltetik.

|Sablon  |Leírás  |
|---------|---------|
| [változók definíciói](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Bemutatja a különböző típusú változókat. A sablon nem telepít semmilyen erőforrást. Változó értékeket hoz létre, és visszaadja ezeket az értékeket. |
| [konfigurációs változó](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | A konfigurációs értékeket meghatározó változó használatát mutatja be. A sablon nem telepít semmilyen erőforrást. Változó értékeket hoz létre, és visszaadja ezeket az értékeket. |
| [hálózati biztonsági szabályok](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) és [paraméter fájl](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | A megfelelő formátumú tömböt hoz létre a biztonsági szabályok hálózati biztonsági csoporthoz való hozzárendeléséhez. |

## <a name="next-steps"></a>További lépések

* A változók elérhető tulajdonságainak megismeréséhez tekintse meg [az ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető témakört.
* A változók létrehozásával kapcsolatos javaslatokért lásd: [ajánlott eljárások – változók](template-best-practices.md#variables).
