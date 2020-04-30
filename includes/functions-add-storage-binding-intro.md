---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/29/2019
ms.author: glenga
ms.openlocfilehash: f16852cdc18053a3a8e375dc6f14e39bb069afef
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "72329621"
---
Azure Functions lehetővé teszi az Azure-szolgáltatások és egyéb erőforrások összekapcsolását a funkciókhoz anélkül, hogy saját integrációs kódot kellene írnia. Ezek a *kötések*, amelyek a bemeneti és a kimeneti adatokat jelölik, a függvény definíciójában vannak deklarálva. A kötések adatai a függvények számára paraméterekként vannak megadva. Az *trigger* egy speciális típusú bemeneti kötés. Bár a függvénynek csak egy triggere van, több bemeneti és kimeneti kötés is lehet. További információ: [Azure functions triggerek és kötések fogalmai](../articles/azure-functions/functions-triggers-bindings.md).