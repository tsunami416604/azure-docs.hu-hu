---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8530f4469a0c25f3c32e652e2b0752c51c28ff3f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78191036"
---
A kötési attribútumok közvetlenül a function. JSON fájlban vannak definiálva. A kötési típustól függően szükség lehet további tulajdonságokra. A [várólista kimeneti konfigurációja](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration) leírja az Azure Storage-várólista kötéséhez szükséges mezőket. A bővítmény megkönnyíti a kötések hozzáadását a function. JSON fájlhoz. 

Kötés létrehozásához kattintson a jobb gombbal (CTRL + kattintás macOS-re) a `function.json` HttpTrigger mappában található fájlra, majd válassza a **kötés hozzáadása...** lehetőséget. Kövesse az utasításokat a következő kötési tulajdonságok definiálásához az új kötéshez:

| Kérdés | Érték | Leírás |
| -------- | ----- | ----------- |
| **Kötési irány kiválasztása** | `out` | A kötés kimeneti kötés. |
| **Kötés kijelölése iránysal...** | `Azure Queue Storage` | A kötés egy Azure Storage-várólista kötése. |
| **A kódban a kötés azonosítására használt név** | `msg` | A kódban hivatkozott kötési paramétert azonosító név. |
| **Az az üzenetsor, amelybe az üzenet el lesz küldve** | `outqueue` | Annak a sornak a neve, amelyet a kötés ír. Ha a *queueName* nem létezik, a kötés létrehozza az első használatkor. |
| **Válassza a beállítás a következőből: "local. Setting. JSON"** | `AzureWebJobsStorage` | A Storage-fiókhoz tartozó kapcsolatok karakterláncát tartalmazó Alkalmazásbeállítás neve. A `AzureWebJobsStorage` beállítás tartalmazza a Function alkalmazással létrehozott Storage-fiókhoz tartozó kapcsolatok karakterláncát. |

A függvény egy kötést ad `bindings` hozzá a tömbhöz a function. JSON fájlban, amelynek a következőhöz hasonlóan kell kinéznie:

:::code language="son" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="18-24":::