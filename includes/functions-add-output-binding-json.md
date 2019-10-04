---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 64a1062a8b73768a334277eafb663a7d2d5dd59a
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838958"
---
A kötési attribútumok közvetlenül a function. JSON fájlban vannak definiálva. A kötési típustól függően szükség lehet további tulajdonságokra. A [várólista kimeneti konfigurációja](../articles/azure-functions/functions-bindings-storage-queue.md#output---configuration) leírja az Azure Storage-várólista kötéséhez szükséges mezőket. A bővítmény megkönnyíti a kötések hozzáadását a function. JSON fájlhoz. 

Kötés létrehozásához kattintson a jobb gombbal (CTRL + kattintás macOS rendszeren) a HttpTrigger mappában található `function.json` fájlra, majd válassza a **kötés hozzáadása...** lehetőséget. Kövesse az utasításokat a következő kötési tulajdonságok definiálásához az új kötéshez:

| Kérdés | Value | Leírás |
| -------- | ----- | ----------- |
| **Kötési irány kiválasztása** | `out` | A kötés kimeneti kötés. |
| **Kötés kijelölése iránysal...** | `Azure Queue Storage` | A kötés egy Azure Storage-várólista kötése. |
| **A kódban a kötés azonosítására használt név** | `msg` | A kódban hivatkozott kötési paramétert azonosító név. |
| **Az az üzenetsor, amelybe az üzenet el lesz küldve** | `outqueue` | Annak a sornak a neve, amelyet a kötés ír. Ha a *queueName* nem létezik, a kötés létrehozza az első használatkor. |
| **Válassza a beállítás a következőből: "local. Setting. JSON"** | `AzureWebJobsStorage` | A Storage-fiókhoz tartozó kapcsolatok karakterláncát tartalmazó Alkalmazásbeállítás neve. A `AzureWebJobsStorage` beállítás a Function alkalmazással létrehozott Storage-fiókhoz tartozó kapcsolatok karakterláncát tartalmazza. |

A function. JSON fájlban egy kötés kerül a `bindings` tömbbe, amelynek most az alábbi példához hasonlóan kell kinéznie:

```json
{
   ...

  "bindings": [
    {
      "authLevel": "function",
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
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```