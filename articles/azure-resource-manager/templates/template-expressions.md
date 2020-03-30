---
title: Sablon szintaxisa és kifejezései
description: Az Azure Resource Manager-sablonok deklaratív JSON-szintaxisát ismerteti.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 172838fa24709eb60fbcb6a68277f44bbd42f01e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460109"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Szintaxis és kifejezések az Azure Resource Manager-sablonokban

A sablon alapszintaxisa JSON. A kifejezések segítségével azonban kiterjesztheti a sablonon belül elérhető JSON-értékeket.  A kifejezések szögletes zárójellel kezdődnek és végződnek: `[` és `]`. A kifejezés értékét a rendszer a sablon üzembe helyezésekor értékeli ki. A kifejezés lehet sztring, egész szám, logikai érték, tömb vagy objektum.

Egy sablonkifejezés nem haladhatja meg a 24 576 karaktert.

## <a name="use-functions"></a>Függvények használata

Az Azure Resource Manager [olyan funkciókat](template-functions.md) biztosít, amelyeket egy sablonban használhat. A következő példa egy olyan kifejezést mutat be, amely egy paraméter alapértelmezett értékében egy függvényt használ:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

A kifejezésen belül `resourceGroup()` a szintaxis meghívja az Erőforrás-kezelő által a sablonon belüli használatra biztosított függvények egyikét. Ebben az esetben ez a [resourceGroup](template-functions-resource.md#resourcegroup) függvény. Csakúgy, mint a JavaScript, `functionName(arg1,arg2,arg3)`függvény hívások formázott . A szintaxis `.location` egy tulajdonságot olvas be a függvény által visszaadott objektumból.

A sablonfüggvények és paramétereik nem különböznek a kis- és nagybetűktől. Az Erőforrás-kezelő például a **változókat('var1')** és a **VARIABLES('VAR1')** azonos értékeket oldja fel. Kiértékeléskor, kivéve, ha a függvény kifejezetten módosítja a kis- és nagybetűket (például toUpper vagy toLower), a függvény megőrzi az esetet. Bizonyos erőforrástípusok nak előfordulhatnak esetkövetelményei, amelyek elkülönülnek a függvények kiértékelésétől.

Ha egy karakterlánc-értéket paraméterként szeretne átadni egy függvénynek, használjon egyszeres idézőjeleket.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

A legtöbb függvény ugyanúgy működik, függetlenül attól, hogy egy erőforráscsoport, előfizetés, felügyeleti csoport vagy bérlő üzembe helyezése. A következő függvények a hatókörön alapuló korlátozásokkal rendelkeznek:

* [resourceGroup](template-functions-resource.md#resourcegroup) – csak egy erőforráscsoportban történő központi telepítésben használható.
* [resourceId](template-functions-resource.md#resourceid) - bármely hatókörben használható, de az érvényes paraméterek a hatókörtől függően változnak.
* [előfizetés](template-functions-resource.md#subscription) – csak erőforráscsoportvagy előfizetés központi telepítéseiben használható.

## <a name="escape-characters"></a>Escape karakterek

Ha azt szeretné, hogy a `[` literális karakterlánc `]`bal oldali zárójelgel kezdődjön, és jobb oldali zárójelgel `[[`végződjön , de ne legyen kifejezésként értelmezve, adjon hozzá egy további zárójelet a karakterlánc elindításához. Például a változó:

```json
"demoVar1": "[[test value]"
```

A megoldás `[test value]`a.

Ha azonban a literális karakterlánc nem ér véget zárójelgel, ne kerüljön el az első zárójelből. Például a változó:

```json
"demoVar2": "[test] value"
```

A megoldás `[test] value`a.

Ha el szeretné kerülni a dupla idézőjeleket egy kifejezésben, például json-objektumot szeretne hozzáadni a sablonhoz, használja a fordított perjelet.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

Paraméterértékek beadásakor a escape karakterek használata attól függ, hogy hol adja meg a paraméter értékét. Ha alapértelmezett értéket állít be a sablonban, akkor a bal oldali zárójelre van szükség.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1":{
            "type": "string",
            "defaultValue": "[[test value]"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput": {
            "type": "string",
            "value": "[parameters('demoParam1')]"
        }
    }
}
```

Ha az alapértelmezett értéket használja, `[test value]`a sablon a értéket adja vissza.

Ha azonban egy paraméterértéket a parancssoron keresztül ad át, a karaktereket a rendszer szó szerint értelmezi. Az előző sablon telepítése a következővel:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[[test value]"
```

Visszatér `[[test value]`. Ehelyett használja:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[test value]"
```

Ugyanez a formázás érvényes, ha értékeket ad át egy paraméterfájlból. A karaktereket szó szerint értelmezik. Ha az előző sablonnal használja, `[test value]`a következő paraméterfájl ad vissza:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1": {
            "value": "[test value]"
        }
   }
}
```

## <a name="null-values"></a>Null értékek

Ha egy tulajdonságot null értékűre szeretne állítani, **használhat null** vagy **[json('null')]**. A [json függvény](template-functions-array.md#json) egy üres `null` objektumot ad vissza paraméterként. Az Erőforrás-kezelő sablonjai mindkét esetben úgy kezelik, mintha a tulajdonság nincs jelen.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>További lépések

* A sablonfüggvények teljes listáját az [Azure Resource Manager sablonfüggvényei című témakörben olvashatja.](template-functions.md)
* A sablonfájlokról az [Azure Resource Manager-sablonok szerkezetének és szintaxisának megismerése című témakörben olvashat bővebben.](template-syntax.md)
