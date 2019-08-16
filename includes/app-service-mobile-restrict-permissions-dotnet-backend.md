---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: b609a708a987194398c53bdf83f0d6e1f281808d
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "67179612"
---
Alapértelmezés szerint a Mobile Apps háttérbeli API-k névtelenül hívhatók meg. Ezután korlátoznia kell a hozzáférést csak a hitelesített ügyfelekre.  

* **Node. js-háttér (a Azure Portalon keresztül)** :  

    A Mobile Apps beállításaiban kattintson az **egyszerű táblák** elemre, és válassza ki a táblát. Kattintson az **engedélyek módosítása**elemre, válassza a **hitelesített hozzáférés csak** az összes engedélyhez lehetőséget, majd kattintson a **Mentés**gombra.
* **.Net-háttér (C#)** :  

    A kiszolgáló projektben navigáljon a **vezérlők** > **TodoItemController.cs**. Adja hozzá `[Authorize]` az attribútumot a **TodoItemController** osztályhoz az alábbiak szerint. Ha csak bizonyos módszerekre szeretné korlátozni a hozzáférést, ezt az attribútumot csak az osztály helyett a metódusokra is alkalmazhatja. A kiszolgálói projekt ismételt közzététele.

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Node. js-háttér (node. js-kód használatával)** :  

    A Table Access hitelesítésének megköveteléséhez adja hozzá a következő sort a Node. js-kiszolgáló parancsfájlhoz:

        table.access = 'authenticated';

    További részleteket a következő témakörben talál [: Hitelesítés megkövetelése a táblákhoz](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth)való hozzáféréshez. Ha szeretné megtudni, hogyan töltheti le a gyors üzembe helyezési kód projektet [a webhelyről, olvassa el a következő témakört: Töltse le a Node. js-háttérbeli Gyorsindítás kódot](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)a git használatával.
