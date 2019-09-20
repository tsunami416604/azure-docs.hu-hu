---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: f771b6b0416c5777c1ebde7e2cf2c4ffc6f375ff
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155300"
---
Egy Azure Functionsban felmerülő hibák a következő eredetek bármelyike esetén származhatnak:

- Beépített Azure Functions [Eseményindítók és kötések](..\articles\azure-functions\functions-triggers-bindings.md) használata
- Az alapul szolgáló Azure-szolgáltatások API-jának meghívása
- REST-végpontokra irányuló hívások
- Ügyféloldali kódtárak, csomagok vagy külső API-k hívása

Az adatvesztés és a kihagyott üzenetek elvesztése miatt fontos a folyamatos hibajavítási eljárások követése. Ajánlott hibakezelés a következő műveletekkel:

- [Application Insights engedélyezése](../articles/azure-functions/functions-monitoring.md)
- [Strukturált hibakezelés használata](#use-structured-error-handling)
- [Idempotencia tervezése](../articles/azure-functions/functions-idempotent.md)
- Újrapróbálkozási szabályzatok implementálása (ha szükséges)

### <a name="use-structured-error-handling"></a>Strukturált hibakezelés használata

A rögzítési és közzétételi hibák kritikus fontosságúak az alkalmazás állapotának monitorozásához. A függvények kódjának legfelső szintjén szerepelnie kell egy try/catch blokknak. A Catch blokkban hibákat rögzíthet és tehet közzé.

### <a name="retry-support"></a>Újrapróbálkozás támogatással

A következő eseményindítók beépített újrapróbálkozási támogatással rendelkeznek:

* [Azure Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure üzenetsor-tároló](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (Üzenetsor/témakör)](../articles/azure-functions/functions-bindings-service-bus.md)

Alapértelmezés szerint ezek az eseményindítók legfeljebb ötször kérik újra a kérelmeket. Az ötödik újrapróbálkozás után mindkét eseményindító üzenetet ír egy [Megmérgező várólistába](..\articles\azure-functions\functions-bindings-storage-queue.md#trigger---poison-messages).

Az újrapróbálkozási szabályzatokat minden más eseményindítóhoz vagy kötési típushoz manuálisan kell végrehajtania. A manuális megvalósítások tartalmazhatják a hibákra vonatkozó információkat a [Megmérgező üzenetek várólistáján](..\articles\azure-functions\functions-bindings-storage-blob.md#trigger---poison-blobs). Egy Megmérgező várólistába való írással lehetősége van arra, hogy egy későbbi időpontban újrapróbálkozjon a műveletekkel. Ez a módszer ugyanaz, mint amelyet a blob Storage-trigger használ.
