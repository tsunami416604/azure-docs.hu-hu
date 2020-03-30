---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 629de079f7cc7d95d10f8ff951a47b8b8fc62dad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77474109"
---
Az Azure-függvényekben előforduló hibák az alábbi eredetből származhatnak:

- Beépített Azure [Functions-eseményindítók és kötések](..\articles\azure-functions\functions-triggers-bindings.md) használata
- Az alapul szolgáló Azure-szolgáltatások API-khívásai
- REST-végpontok hívásai
- Ügyfélkódtárak, csomagok vagy külső API-k hívásai

A szilárd hibakezelési eljárások követése fontos az adatvesztés vagy a kihagyott üzenetek elkerülése érdekében. Az ajánlott hibakezelési eljárások a következő műveleteket tartalmazzák:

- [Az Application Insights engedélyezése](../articles/azure-functions/functions-monitoring.md)
- [Strukturált hibakezelés használata](#use-structured-error-handling)
- [Design idempotencia](../articles/azure-functions/functions-idempotent.md)
- [Újrapróbálkozási házirendek megvalósítása](../articles/azure-functions/functions-reliable-event-processing.md) (adott esetben)

### <a name="use-structured-error-handling"></a>Strukturált hibakezelés használata

A hibák rögzítése és közzététele kritikus fontosságú az alkalmazás állapotának figyeléséhez. Bármely függvénykód legfelső szintjének tartalmaznia kell egy próba/fogási blokkot. A fogási blokkban rögzítheti és közzéteheti a hibákat.

### <a name="retry-support"></a>Újrapróbálkozási támogatás

A következő eseményindítók beépített újrapróbálkozási támogatást kaptak:

* [Azure Blob-tárhely](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure-várólista-tárolás](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (várólista/témakör)](../articles/azure-functions/functions-bindings-service-bus.md)

Alapértelmezés szerint ezek az eseményindítók legfeljebb ötször próbálja meg újraakérelmeket. Az ötödik újrapróbálkozás után mind az Azure Queue storage, mind az Azure Service Bus elindítja az üzenetet egy [méregvárólistába.](..\articles\azure-functions\functions-bindings-storage-queue-trigger.md#poison-messages)

Manuálisan kell megvalósítania az újrapróbálkozási házirendeket bármely más eseményindítókhoz vagy kötéstípusokhoz. A manuális implementációk tartalmazhatják a hibainformációk írását a [mérgező üzenetek várólistájába.](..\articles\azure-functions\functions-bindings-storage-blob-trigger.md#poison-blobs) Ha egy méregvárólistába ír, később újrapróbálkozhat a műveleteket. Ez a megközelítés megegyezik a Blob storage eseményindító által használt.
