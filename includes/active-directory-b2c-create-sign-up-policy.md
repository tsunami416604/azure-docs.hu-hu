---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 17c0213d63879687e9c6d5f8dca06b9113c44af8
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355692"
---
Ha azt szeretné csak engedélyezése előfizetési az alkalmazásban, használhat egy **előfizetési** felhasználói folyamatot. Ezt a felhasználói folyamatot ismerteti, hogy az ügyfelek halad át a regisztrációhoz és a jogkivonatokat, amelyeket az alkalmazás fogad a sikeres regisztrálásokkor.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

A **kezelés**válassza **felhasználókövetési adatai**.

Kattintson a +**új felhasználói folyamat** a panel tetején.

Alatt **válassza ki a felhasználói folyamat típusát**, jelölje be **összes**, majd válassza ki azt a verzióját, és **regisztráció** is használni szeretné.

A **neve** meghatározza, hogy az alkalmazása által használt előfizetés-kezelő felhasználói folyamat nevét. Adja meg például a következőt: **SiUp**.

A **Identitásszolgáltatók**válassza **regisztráció E-mail-címmel**. Azt is megteheti, hogy közösségi identitásszolgáltatókat választ ki, ha ezek már be vannak állítva.

A **felhasználói attribútumokról és jogcímekről**, kattintson a **Továbbiak megjelenítése**.

Az a **gyűjtése attribútum** oszlopban válassza ki, hogy a felhasználóknak megadniuk a regisztráció során kívánt attribútumokat. Válassza ki például az **Ország/régió**, a **Megjelenítendő név** és az **Irányítószám** attribútumokat.

Az a **visszatérési jogcím** oszlopban válassza ki a jogcímeket, amelyeket szeretne visszaküldött hitelesítő jogkivonatokban sikeres regisztráció után az alkalmazásnak. Válassza például a **Megjelenítendő név**, az **Identitásszolgáltató**, az **Irányítószám**, az **Új felhasználó** és a **Felhasználó objektumazonosítója** lehetőségeket.

Kattintson az **OK** gombra.

Kattintson a **Create** (Létrehozás) gombra. Megjelenik a létrehozott felhasználói folyamatot **B2C_1_SiUp** (a **B2C\_1\_**  részt a rendszer automatikusan adja hozzá).

Kattintson a **felhasználói folyamat futtatása**.

Válassza ki **Contoso B2C alkalmazás** a a **alkalmazás** legördülő és `https://localhost:44321/` a a **válasz URL-cím** listából.

Kattintson a **felhasználói folyamat futtatása**. Megnyílik egy böngészőablak, ahol ellenőrizheti, hogyan működik az alkalmazás regisztrációs felhasználói folyamata.

> [!NOTE]
> Vesz egy percig a felhasználói folyamat létrehozása és a frissítések érvénybe léptetéséhez.
>