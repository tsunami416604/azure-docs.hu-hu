---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: f23d2b02bc2a23c5333a48a50532c03f3aa6a031
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355613"
---
[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Alatt **kezelés**válassza **felhasználói folyamatok** , és kattintson a +**új felhasználói folyamat**.

![Válassza ki az új felhasználói folyamat](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow.png)

Az a **ajánlott** lapon jelölje be **jelentkezzen be, és jelentkezzen be a**.

![Válassza ki a jelentkezzen be, és jelentkezzen be a felhasználói folyamat](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow-type.png)

Adja meg a felhasználói folyamat **neve** , hogy az alkalmazás hivatkozni. Adja meg például a következőt: `SiUpIn`.

A **Identitásszolgáltatók** , és ellenőrizze **regisztráció E-mail-címmel**. Azt is megteheti, hogy közösségi identitásszolgáltatókat választ ki, ha ezek már be vannak állítva.

A **többtényezős hitelesítés**, válasszon **engedélyezve** vagy **letiltott**.

![Adjon meg egy nevet, és válassza ki a regisztráció E-mail-címmel lehetőséget Identitásszolgáltatóként](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-name-identity-providers.png)

A **felhasználói attribútumokról és jogcímekről**válassza **Továbbiak megjelenítése** attribútumokról és jogcímekről közül választhat a teljes listájának megtekintéséhez.

Az a **gyűjtése attribútum** oszlopban válassza ki az attribútumokat, a felhasználóknak megadniuk a regisztráció során szeretné. Például jelölje be az **Ország/régió**, a **Megjelenítendő név** és az **Irányítószám** attribútumokat.

Az a **visszatérési jogcím** oszlopban válassza ki a kívánt visszaküldött hitelesítő jogkivonatokban vissza a sikeres regisztrációs vagy bejelentkezési után az alkalmazásnak a jogcímeket. Válassza például a **Megjelenítendő név**, az **Identitásszolgáltató**, az **Irányítószám**, az **Új felhasználó** és a **Felhasználó objektumazonosítója** lehetőséget.

Kattintson az **OK** gombra.

![Válassza ki az egyes felhasználói attribútumokról és jogcímekről, és kattintson az OK gombra.](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-sign-up-all-attributes.png)

Kattintson a **létrehozás** hozzáadása a felhasználói folyamatot. A felhasználói folyamat állapottal **B2C_1_SiUpIn**. A **B2C_1_** előtagot a rendszer adja hozzá a névhez.

Válassza ki **felhasználói folyamat futtatása**. Ellenőrizze a táblázatban megadott beállításokat, majd kattintson a **felhasználói folyamat futtatása**.

![Válassza ki a felhasználói folyamat futtatása](media/active-directory-b2c-create-sign-in-sign-up-policy/run-user-flow-b2c-signup-signin.png)

| Beállítás      | Érték  |
| ------------ | ------ |
| **Alkalmazás** | Contoso B2C-alkalmazás |
| **Válasz URL-cím** | `https://localhost:44316/` |

Megnyílik egy böngészőablak, ahol ellenőrizheti, hogyan működik az alkalmazás regisztrációs vagy bejelentkezési felhasználói folyamata.

> [!NOTE]
> Vesz egy percig a felhasználói folyamat létrehozása és a frissítések érvénybe léptetéséhez.
>