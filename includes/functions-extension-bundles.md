---
ms.openlocfilehash: 0b0d2ead4a10d037b09c2a532eb21372ffd0cb82
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178900"
---
Az Azure Functions 2.x alapértelmezett kötések hivatkozik, nyissa meg a *host.json* fájlt, és frissítse tartalmát a következő kód megfelelően.

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```