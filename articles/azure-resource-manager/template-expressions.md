---
title: Sablon szintaxisa és kifejezések
description: A Azure Resource Manager-sablonok deklaratív JSON-szintaxisát ismerteti.
ms.topic: conceptual
ms.date: 09/03/2019
ms.openlocfilehash: 046f7f4866e9b5933c55bc5a9d0ee96c945bff0e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149195"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Szintaxis és kifejezések a Azure Resource Manager-sablonokban

A sablon alapszintű szintaxisa a JSON. A kifejezésekkel azonban a sablonban elérhető JSON-értékeket is kiterjesztheti.  A kifejezések kezdete és vége zárójelekkel: `[` és `]`. A kifejezés értéke a sablon telepítésekor lesz kiértékelve. Egy kifejezés karakterláncot, egész számot, logikai értéket, tömböt vagy objektumot adhat vissza.

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

A kifejezésen belül a szintaxis `resourceGroup()` meghívja a Resource Manager által a sablonon belül használható függvények egyikét. Ebben az esetben ez a [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) függvény. Akárcsak a JavaScriptben, a függvények hívásait a rendszer `functionName(arg1,arg2,arg3)`formázza. A szintaxis `.location` egy tulajdonságot kér le a függvény által visszaadott objektumból.

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

## <a name="next-steps"></a>További lépések

* A sablon függvények teljes listájáért lásd: [Azure Resource Manager template functions](resource-group-template-functions.md).
* További információ a sablonfájlokről: [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](resource-group-authoring-templates.md).
