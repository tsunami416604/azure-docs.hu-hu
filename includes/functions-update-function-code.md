---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/16/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d264477693458ff4132c1f69704a480eed2756e0
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988000"
---
## <a name="update-the-function"></a>A függvény frissítése

Alapértelmezés szerint a sablon létrehoz egy függvényt, amelynek szüksége van egy függvénykulcsra a kérések küldésekor. Hogy könnyebben lehessen tesztelni a függvényt az Azure-ban, módosítania kell a függvényt, hogy engedélyezze a névtelen hozzáférést. Ennek a módja a Functions-projekt nyelvétől függ.

### <a name="c"></a>C\#

Nyissa meg az új függvényét, vagyis a MyHttpTrigger.cs kódfájlt, módosítsa a függvénydefinícióban az **AuthorizationLevel** (engedélyezési szint) attribútumot `anonymous` értékre, majd mentse a változtatásokat.

```csharp
[FunctionName("MyHttpTrigger")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, 
            "get", "post", Route = null)]HttpRequest req, ILogger log)
```

### <a name="javascript"></a>JavaScript

Nyissa meg az új függvényét, vagyis a function.json fájlt egy szerkesztőprogramban, módosítsa a **bindings.httpTrigger** részen az **authLevel** értékét `anonymous` értékre, majd mentse a változtatásokat.

```json
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
```

Így már anélkül hívhatja meg a függvényt az Azure-ban, hogy meg kellene adnia a függvénykulcsot. Helyi futtatás esetén sosem kell megadni a függvénykulcsot.
