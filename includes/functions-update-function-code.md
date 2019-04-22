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
ms.openlocfilehash: 5b009fafc818a06bdda309b3e025251cc0997e47
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59737113"
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

Nyissa meg az új függvényt a function.json fájlt egy szövegszerkesztőben, frissítse a **authLevel** tulajdonság **kötések** való `anonymous`, és mentse a módosításokat.

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
