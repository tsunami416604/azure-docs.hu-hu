---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: b609a708a987194398c53bdf83f0d6e1f281808d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179612"
---
Alapértelmezés szerint a mobilalkalmazások háttérrendszerében lévő API-k névtelenül hívhatók meg. Ezután csak a hitelesített ügyfelekre kell korlátoznia a hozzáférést.  

* **Node.js háttérrendszeren keresztül (az Azure Portalon keresztül)** :  

    A Mobilalkalmazások beállításaiközött kattintson a **Táblázatok egyszerű** lehetőségre, és válassza ki a táblázatot. Kattintson **az Engedélyek módosítása**gombra, válassza a Csak hitelesített **hozzáférés** lehetőséget az összes engedélyhez, majd kattintson a **Mentés gombra.**
* **.NET háttér-tartalék (C#)**:  

    A kiszolgálóprojektben keresse meg a **Vezérlők** > **TodoItemController.cs.** Adja `[Authorize]` hozzá az attribútumot a **TodoItemController** osztályhoz az alábbiak szerint. Ha csak bizonyos metódusokra szeretné korlátozni a hozzáférést, ezt az attribútumot csak ezekre a metódusokra is alkalmazhatja az osztály helyett. Tegye közzé újra a kiszolgálóprojektet.

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Node.js háttér (via Node.js kód)** :  

    A táblahozzáférés hitelesítésének megköveteléséhez adja hozzá a következő sort a Node.js kiszolgáló parancsfájljához:

        table.access = 'authenticated';

    További részletek: [Hogyan: Hitelesítés megkövetelése a táblákhoz való hozzáféréshez.](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth) A rövid útmutató kódprojektjének a webhelyről való letöltéséről a [Hogyan töltheti le a Node.js háttérkódprojektet a Git használatával című témakörben.](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)
