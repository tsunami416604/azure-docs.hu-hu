---
title: Sablon szintaxisa és kifejezések
description: A Azure Resource Manager-sablonok deklaratív JSON-szintaxisát ismerteti.
ms.topic: conceptual
ms.date: 09/03/2019
ms.openlocfilehash: b7682ba10c30290e5935bc2dd17e2a83852d92f4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75484193"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Szintaxis és kifejezések a Azure Resource Manager-sablonokban

A sablon alapszintű szintaxisa a JSON. A kifejezésekkel azonban a sablonban elérhető JSON-értékeket is kiterjesztheti.  A kifejezések szögletes zárójellel kezdődnek és végződnek: `[` és `]`. A kifejezés értékét a rendszer a sablon üzembe helyezésekor értékeli ki. A kifejezés lehet sztring, egész szám, logikai érték, tömb vagy objektum.

A sablon kifejezése nem lehet hosszabb 24 576 karakternél.

## <a name="use-functions"></a>Függvények használata

Az alábbi példa egy paraméter alapértelmezett értékében egy kifejezést mutat be:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

A kifejezésen belül a szintaxis `resourceGroup()` meghívja a Resource Manager által a sablonon belül használható függvények egyikét. Ebben az esetben ez a [resourceGroup](template-functions-resource.md#resourcegroup) függvény. Akárcsak a JavaScriptben, a függvények hívásait a rendszer `functionName(arg1,arg2,arg3)`formázza. A szintaxis `.location` egy tulajdonságot kér le a függvény által visszaadott objektumból.

A sablon függvények és azok paramétereinek kis-és nagybetűk megkülönböztetése. A Resource Manager például feloldja a **változókat ("var1")** és a **változókat ("var1")** . A kiértékeléskor, ha a függvény kifejezetten nem módosítja a kis-és nagybetűket (például toUpper vagy toLower), a függvény megőrzi a kis-és nagybetűket. Bizonyos erőforrástípusok rendelkezhetnek a függvények kiértékelésének módjától eltérő eseti követelményekkel.

Ha karakterlánc-értéket szeretne átadni paraméterként egy függvénynek, használjon aposztrófot.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

## <a name="escape-characters"></a>Escape-karakterek

Ahhoz, hogy egy literális sztring bal oldali `[` szögletes zárójelgel kezdődjön, és a jobb oldali szögletes zárójel `]`, de nem értelmezhető kifejezésként, vegyen fel egy extra zárójelet, hogy a karakterláncot `[[`használatával indítsa el. Például a következő változó:

```json
"demoVar1": "[[test value]"
```

Feloldja a `[test value]`.

Ha azonban a literál sztring nem egy szögletes zárójelre végződik, ne hagyja ki az első zárójelet. Például a következő változó:

```json
"demoVar2": "[test] value"
```

Feloldja a `[test] value`.

Ha az idézőjeleket egy kifejezésben szeretné kipróbálni, például egy JSON-objektum hozzáadását a sablonban, használja a fordított perjelet.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

## <a name="next-steps"></a>Következő lépések

* A sablon függvények teljes listájáért lásd: [Azure Resource Manager template functions](template-functions.md).
* További információ a sablonfájlokről: [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](template-syntax.md).
