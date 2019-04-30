---
ms.openlocfilehash: b609a708a987194398c53bdf83f0d6e1f281808d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62113530"
---
Alapértelmezés szerint a API-k a Mobile Apps-háttéralkalmazás névtelenül meghívható. Ezután meg kell korlátozza a hozzáférést csak a hitelesített ügyfelek.  

* **NODE.js vissza (az Azure Portalon) záró** :  

    Kattintson a Mobile Apps között **könnyen kezelhető táblák** , és válassza ki a táblát. Kattintson a **engedélyeinek módosítása**válassza **hitelesített hozzáférés csak** összes engedélyeket, és kattintson a **mentése**.
* **.NET-háttéralkalmazás (C#)**:  

    A kiszolgálói projektet, lépjen a **tartományvezérlők** > **TodoItemController.cs**. Adja hozzá a `[Authorize]` attribútumot a **TodoItemController** osztályhoz az alábbiak szerint. Csak a megadott metódusok való hozzáférés korlátozása, is alkalmazhat az attribútum csak azokat a módszereket az osztály helyett. Tegye közzé újra a kiszolgálói projektet.

    ```
    [Authorize]
    public class TodoItemController : TableController<TodoItem>
    ```

* **NODE.js háttérrendszer (a Node.js-kód) keresztül** :  

    A tábla hozzáféréshez hitelesítés szükséges, adja hozzá a következő sort a Node.js server parancsfájlt:

    ```
    table.access = 'authenticated';
    ```

    További részletekért lásd: [hogyan: Hitelesítés megkövetelése a hozzáféréshez át a táblákig](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). A kód gyorsútmutató-projekt letöltése a hely kapcsolatban lásd: [hogyan: Töltse le a Node.js háttérrendszer kód példaprojekthez Git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).