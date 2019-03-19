---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 03/12/2019
ms.author: glenga
ms.custom: include file, fasttrack-edit
ms.openlocfilehash: 3b0be31afbce78c0de8dc919245e92bee7af04b0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57964168"
---
## <a name="update-the-function"></a>A függvény frissítése

Alapértelmezés szerint a sablon létrehoz egy függvényt, amelynek szüksége van egy függvénykulcsra a kérések küldésekor. Hogy könnyebben lehessen tesztelni a függvényt az Azure-ban, módosítania kell a függvényt, hogy engedélyezze a névtelen hozzáférést. Ennek a módja a Functions-projekt nyelvétől függ.

### <a name="c"></a>C\#

Nyissa meg az új függvényét, vagyis a MyHttpTrigger.cs kódfájlt, módosítsa a függvénydefinícióban az **AuthorizationLevel** (engedélyezési szint) attribútumot `Anonymous` értékre, majd mentse a változtatásokat.

```csharp
[FunctionName("MyHttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    ILogger log)
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
