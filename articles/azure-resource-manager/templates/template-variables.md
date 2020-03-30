---
title: Változók a sablonokban
description: Bemutatja, hogyan definiálhatók változók egy Azure Resource Manager-sablonban.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: cf135959d30702ea58b7a1d4fdd82625a39245d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483816"
---
# <a name="variables-in-azure-resource-manager-template"></a>Változók az Azure Resource Manager-sablonban

Ez a cikk ismerteti, hogyan definiálhatja és használhatja a változókat az Azure Resource Manager-sablonban. Változókkal egyszerűsítheti a sablont. Ahelyett, hogy bonyolult kifejezéseket ismételne meg a sablonban, definiáljon egy változót, amely tartalmazza a bonyolult kifejezést. Ezután szükség szerint hivatkozik erre a változóra a sablonban.

Az Erőforrás-kezelő a telepítési műveletek megkezdése előtt feloldja a változókat. Ahol a változót használja a sablonban, az Erőforrás-kezelő lecseréli azt a feloldott értékre.

## <a name="define-variable"></a>Változó definiálása

A következő példa egy változódefiníciót mutat be. Karakterlánc-értéket hoz létre egy tárfiók nevéhez. Számos sablonfüggvényt használ egy paraméterérték leválasztásához, és egy egyedi karakterláncba fűzi.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

A [referencia](template-functions-resource.md#reference) függvény vagy a változók szakaszban lévő [listafüggvények](template-functions-resource.md#list) nem használhatók. Ezek a függvények egy erőforrás futásidejű állapotát kapják, és nem hajthatók végre az üzembe helyezés előtt, ha a változók felvannak oldva.

## <a name="use-variable"></a>Változó használata

A sablonban a [változók](template-functions-deployment.md#variables) függvény használatával hivatkozik a paraméter értékére. A következő példa bemutatja, hogyan használható a változó egy erőforrás tulajdonsághoz.

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

A környezet konfigurálásakor olyan változókat határozhat meg, amelyek kapcsolódó értékeket tartalmaznak. A változót az értékekkel rendelkező objektumként definiálhatja. A következő példa egy olyan objektumot mutat be, amely két környezet - **teszt** és **prod**értékeit tartalmazza.

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

A paraméterekben olyan értéket hoz létre, amely jelzi, hogy mely konfigurációs értékeket kell használni.

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

Az alábbi példák bemutatják a változók használatára vonatkozó forgatókönyveket.

|Sablon  |Leírás  |
|---------|---------|
| [változó definíciók](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Bemutatja a különböző típusú változók. A sablon nem telepít erőforrásokat. Változó értékeket hoz létre, és ezeket az értékeket adja vissza. |
| [konfigurációs változó](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | A konfigurációs értékeket meghatározó változó használatát mutatja be. A sablon nem telepít erőforrásokat. Változó értékeket hoz létre, és ezeket az értékeket adja vissza. |
| [hálózati biztonsági szabályok](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) és [paraméterfájl](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | A megfelelő formátumú tömböt hoz létre a biztonsági szabályok hálózati biztonsági csoporthoz való hozzárendeléséhez. |

## <a name="next-steps"></a>További lépések

* A változók elérhető tulajdonságairól [az Azure Resource Manager-sablonok szerkezetének és szintaxisának megismerése című témakörben olvashat.](template-syntax.md)
* A változók létrehozásával kapcsolatos javaslatokról az [Ajánlott eljárások – változók](template-best-practices.md#variables)című témakörben van.
