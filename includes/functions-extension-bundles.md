---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: 8110d0a9d574c6691322df2162ca877b031cbc59
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442288"
---
A kötési bővítményeket telepítésének legegyszerűbb módja az, hogy engedélyezze [bővítmény csomagjaiból](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Csomagok csomagok előre meghatározott bővítmény engedélyezve van, automatikusan települ.

Ahhoz, hogy a bővítmény csomagjaiból, nyissa meg a *host.json* fájlt, és frissítse a tartalmát a felel meg a következő kódot:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```