---
title: Sablon szintaxisa és kifejezések
description: A Azure Resource Manager-sablonok deklaratív JSON-szintaxisát ismerteti.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 7bca3125f80225d2180734f483194a63e39d9cf5
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207400"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Szintaxis és kifejezések a Azure Resource Manager-sablonokban

A sablon alapszintű szintaxisa a JSON. A kifejezésekkel azonban a sablonban elérhető JSON-értékeket is kiterjesztheti.  A kifejezések kezdete és vége zárójelekkel: `[` és `]`. A kifejezés értéke a sablon telepítésekor lesz kiértékelve. Egy kifejezés karakterláncot, egész számot, logikai értéket, tömböt vagy objektumot adhat vissza.

A sablon kifejezése nem lehet hosszabb 24 576 karakternél.

## <a name="use-functions"></a>Függvények használata

A Azure Resource Manager a sablonban használható [függvényeket](template-functions.md) biztosít. Az alábbi példa egy olyan kifejezést mutat be, amely egy függvényt használ egy paraméter alapértelmezett értékében:

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

A legtöbb függvény ugyanúgy működik, hogy az erőforráscsoport, az előfizetés, a felügyeleti csoport vagy a bérlő számára van-e telepítve. A következő függvények korlátozásai a hatókörön alapulnak:

* [resourceGroup](template-functions-resource.md#resourcegroup) – csak az erőforráscsoporthoz történő központi telepítések esetén használható.
* [resourceId](template-functions-resource.md#resourceid) – bármely hatókörben használható, de az érvényes paraméterek a hatókörtől függően változnak.
* [előfizetés](template-functions-resource.md#subscription) – csak erőforráscsoporthoz vagy előfizetéshez használható központi telepítések esetén.

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

## <a name="null-values"></a>Null értékek

Ha a tulajdonságot NULL értékre kívánja beállítani, használhatja a null vagy a **[JSON ("NULL")]** **értéket** . A [JSON-függvény](template-functions-array.md#json) üres objektumot ad vissza, amikor paraméterként megadja `null`. A Resource Manager-sablonok mindkét esetben úgy bánnak, mintha a tulajdonság nem szerepel.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>Következő lépések

* A sablon függvények teljes listájáért lásd: [Azure Resource Manager template functions](template-functions.md).
* További információ a sablonfájlokről: [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](template-syntax.md).
