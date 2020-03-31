---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/29/2019
ms.author: glenga
ms.openlocfilehash: f16852cdc18053a3a8e375dc6f14e39bb069afef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72329621"
---
Az Azure Functions lehetővé teszi az Azure-szolgáltatások és egyéb erőforrások csatlakoztatását a függvényekhez anélkül, hogy saját integrációs kódot kellene írnia. Ezek *a kötések*, amelyek mind a bemeneti, mind a kimeneti kötéseket képviselik, a függvénydefinícióban vannak deklarálva. A kötések adatai a függvények számára paraméterekként vannak megadva. Az *eseményindító* a bemeneti kötés egy speciális típusa. Bár egy függvény csak egy eseményindítóval rendelkezik, több bemeneti és kimeneti kötéssel rendelkezhet. További információ: [Azure Functions triggers and bindings concepts](../articles/azure-functions/functions-triggers-bindings.md).