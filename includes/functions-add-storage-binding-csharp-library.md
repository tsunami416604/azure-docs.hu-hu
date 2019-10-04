---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 4db460a5dcefb49de3ad2b594d3957cbcf7445c3
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592807"
---
Egy C# eszközosztály projektben a kötések kötési attribútumokként vannak definiálva a Function metódusban. A *function. JSON* fájl ezután automatikusan létrejön ezen attribútumok alapján.

Nyissa meg a *HttpTrigger.cs* projektfájlt, és adja `using` hozzá a következő utasítást:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

Adja hozzá a következő paramétert `Run` a metódus definícióhoz:

```cs
[Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
```

A `msg` paraméter egy `ICollector<T>` típus, amely a függvény befejeződése után kimeneti kötésbe írt üzenetek gyűjteményét jelöli. Ebben az esetben a kimenet egy nevű `outqueue`tárolási várólista. A Storage-fiókhoz tartozó kapcsolatok karakterláncát a `StorageAccountAttribute`következő értékre állítja be:. Ez az attribútum azt a beállítást jelzi, amely a Storage-fiókhoz tartozó kapcsolatok sztringjét tartalmazza, és alkalmazható az osztály, a metódus vagy a paraméter szintjén. Ebben az esetben kihagyhatja `StorageAccountAttribute` , mert már használja az alapértelmezett Storage-fiókot.

A futtatási metódus definíciójának ekkor a következőhöz hasonlóan kell kinéznie:  

```cs
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
```
