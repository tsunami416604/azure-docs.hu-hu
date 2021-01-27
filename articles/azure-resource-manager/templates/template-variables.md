---
title: Változók a sablonokban
description: Leírja, hogyan lehet változókat definiálni egy Azure Resource Manager sablonban (ARM-sablon).
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: feecc4b5df77e6a3bf51294cb12aabf44899dde5
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874434"
---
# <a name="variables-in-arm-template"></a>Az ARM-sablonban szereplő változók

Ez a cikk bemutatja, hogyan határozhat meg és használhat változókat a Azure Resource Manager-sablonban (ARM-sablon). Változók használatával egyszerűsítheti a sablont. Ahelyett, hogy a sablonban megismételje a bonyolult kifejezéseket, Definiáljon egy változót, amely tartalmazza a bonyolult kifejezést. Ezután a sablonban szükség szerint hivatkozhat erre a változóra.

A Resource Manager a telepítési műveletek megkezdése előtt feloldja a változókat. Mindenhol, ahol a sablonban a változót kell használni, a Resource Manager lecseréli a változót a feloldott értékre.

## <a name="define-variable"></a>Változó meghatározása

Egy változó definiálásakor adjon meg egy értéket vagy egy sablon kifejezést, amely feloldja az [adattípust](template-syntax.md#data-types). A változó létrehozásakor egy paraméter vagy egy másik változó értékét is használhatja.

A változó deklarációjában használhatja a [template functions](template-functions.md) funkciót, de nem használhatja a [Reference](template-functions-resource.md#reference) függvényt vagy a [List](template-functions-resource.md#list) függvényeket. Ezek a függvények egy erőforrás futásidejű állapotát kapják meg, és a változók feloldása előtt nem hajthatók végre.

Az alábbi példa egy változó definícióját mutatja be. Létrehoz egy karakterlánc-értéket a Storage-fiók nevéhez. Számos sablon függvényt használ a paraméterérték beolvasásához, és összefűzi azt egy egyedi karakterlánchoz.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

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

## <a name="example-template"></a>Példa sablonra

A következő sablon egyetlen erőforrást sem helyez üzembe. Ez csak a változók bevallásának bizonyos módjait mutatja be.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

## <a name="configuration-variables"></a>Konfigurációs változók

Meghatározhatja a környezet konfigurálásához kapcsolódó értékeket tároló változókat. A változót az értékekkel rendelkező objektumként definiálhatja. A következő példa egy olyan objektumot mutat be, amely két környezet, a **test** és a **Prod** értékeit tárolja. A telepítés során az alábbi értékek egyikét adja át.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

## <a name="next-steps"></a>További lépések

* A változók elérhető tulajdonságainak megismeréséhez tekintse meg [az ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető témakört.
* A változók létrehozásával kapcsolatos javaslatokért lásd: [ajánlott eljárások – változók](template-best-practices.md#variables).
* Egy hálózati biztonsági csoportra vonatkozó biztonsági szabályok hozzárendelésére szolgáló sablonhoz lásd: [hálózati biztonsági szabályok](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) és [paraméter fájl](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json).
