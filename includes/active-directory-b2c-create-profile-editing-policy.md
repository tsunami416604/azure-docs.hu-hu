---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 0d9f0a24d84bd18bdf1fac84c744cc34a7d89ab3
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742378"
---
Ha szeretné engedélyezni, profilszerkesztést az alkalmazásban, használhatja a **profilszerkesztést** felhasználói folyamatot. Ez a felhasználói folyamat írja le, hogy ügyfeleink profilszerkesztést és a jogkivonatokat, amelyeket az alkalmazás fogad a sikeres végrehajtása során.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Alatt **kezelés**válassza **felhasználói folyamatok** , és kattintson a +**új felhasználói folyamat**.

![Válassza ki az új felhasználói folyamat](media/active-directory-b2c-create-profile-editing-policy/add-b2c-new-user-flow.png)

Az a **ajánlott** lapon jelölje be **profilszerkesztést**.

Adja meg a felhasználói folyamat **neve** , hogy az alkalmazás hivatkozni. Adja meg például a következőt: `SiPe`.

A **Identitásszolgáltatók**, ellenőrizze **helyi fiókba**. Azt is megteheti, hogy közösségi identitásszolgáltatókat választ ki, ha ezek már be vannak állítva.

![Válassza a Bejelentkezés helyi fiókba lehetőséget identitásszolgáltatóként, és kattintson az OK gombra.](media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-identity-providers.png)

A **felhasználói attribútumok**, kattintson a **Továbbiak megjelenítése**. Az a **gyűjtése attribútum** oszlopban válassza ki az attribútumokat, megtekintheti és szerkesztheti a fogyasztók a profilban. Például jelölje be az **Ország/régió**, a **Megjelenítendő név** és az **Irányítószám** attribútumokat.

Az a **visszatérési jogcím** oszlopban válassza ki a kívánt visszaküldött hitelesítő jogkivonatokban a sikeres Profilszerkesztési művelet után az alkalmazásnak jogcímeket. Válassza például a **Megjelenítendő név** és az **Irányítószám** lehetőséget.

Kattintson az **OK** gombra.

![Válasszon ki néhány alkalmazásjogcímet, majd kattintson az OK gombra.](media/active-directory-b2c-create-profile-editing-policy/add-b2c-user-attributes.png)

Kattintson a **létrehozás** hozzáadása a felhasználói folyamatot. A felhasználói folyamat állapottal **B2C_1_SiPe**. A **B2C_1_** előtagot a rendszer adja hozzá a névhez.

Válassza ki **felhasználói folyamat futtatása**. Ellenőrizze a táblázatban megadott beállításokat, majd kattintson a **felhasználói folyamat futtatása**.

![Válassza ki a felhasználói folyamat és futtatása](media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-run-user-flow.png)

| Beállítás      | Érték  |
| ------------ | ------ |
| **Alkalmazás** | Contoso B2C-alkalmazás |
| **Válasz URL-cím** | `https://localhost:44316/` |

Megnyílik egy böngészőablak, ahol ellenőrizheti, hogyan működik az alkalmazás profilszerkesztési felhasználói folyamata.

> [!NOTE]
> Vesz egy percig a felhasználói folyamat létrehozása és a frissítések érvénybe léptetéséhez.
>