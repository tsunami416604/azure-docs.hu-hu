---
title: Azure Resource Manager sablon szintaxisa és kifejezései
description: A Azure Resource Manager-sablonok deklaratív JSON-szintaxisát ismerteti.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: tomfitz
ms.openlocfilehash: 1e7288da19e2e81d609b952e03d5143b03a65c63
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259482"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Szintaxis és kifejezések a Azure Resource Manager-sablonokban

A sablon alapszintű szintaxisa a JSON. A kifejezésekkel azonban a sablonban elérhető JSON-értékeket is kiterjesztheti.  A kifejezések kezdete és vége zárójelekkel: `[` és `]`, illetve. A kifejezés értéke a sablon telepítésekor lesz kiértékelve. Egy kifejezés karakterláncot, egész számot, logikai értéket, tömböt vagy objektumot adhat vissza.

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

A kifejezésen belül a szintaxis `resourceGroup()` meghívja az egyik olyan függvényt, amelyet a Resource Manager biztosít a sablonon belüli használatra. Ebben az esetben ez a [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) függvény. A JavaScripthez hasonlóan a függvények hívásai is a `functionName(arg1,arg2,arg3)`következőképpen vannak formázva:. A szintaxis `.location` egy tulajdonságot kérdez le a függvény által visszaadott objektumból.

A sablon függvények és azok paramétereinek kis-és nagybetűk megkülönböztetése. A Resource Manager például feloldja a **változókat ("var1")** és a **változókat ("var1")** . A kiértékeléskor, ha a függvény kifejezetten nem módosítja a kis-és nagybetűket (például toUpper vagy toLower), a függvény megőrzi a kis-és nagybetűket. Bizonyos erőforrástípusok rendelkezhetnek a függvények kiértékelésének módjától eltérő eseti követelményekkel.

Ha karakterlánc-értéket szeretne átadni paraméterként egy függvénynek, használjon aposztrófot.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

## <a name="escape-characters"></a>Escape-karakterek

Ahhoz, hogy egy literális sztring bal oldali zárójelmel `[` kezdődjön, és jobb oldali szögletes zárójelet `]`adjon meg, de nem értelmezhető kifejezésként, vegyen fel egy extra zárójelet `[[`a sztring elindításához. Például a következő változó:

```json
"demoVar1": "[[test value]"
```

Feloldás a `[test value]`következőhöz:.

Ha azonban a literál sztring nem egy szögletes zárójelre végződik, ne hagyja ki az első zárójelet. Például a következő változó:

```json
"demoVar2": "[test] value"
```

Feloldás a `[test] value`következőhöz:.

Ha az idézőjeleket egy kifejezésben szeretné kipróbálni, például egy JSON-objektum hozzáadását a sablonban, használja a fordított perjelet.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

## <a name="next-steps"></a>További lépések

* A sablon függvények teljes listájáért lásd: [Azure Resource Manager template functions](resource-group-template-functions.md).
* További információ a sablonfájlokről: [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](resource-group-authoring-templates.md).
