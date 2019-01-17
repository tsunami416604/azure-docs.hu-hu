---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 0ab34d6234db9c13ffe82ccd0e8580217085f631
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355592"
---
Ha azt szeretné, csak jelentkezzen be az alkalmazás engedélyezéséhez használja a **bejelentkezési** felhasználói folyamatot. Ezt a felhasználói folyamatot ismerteti, hogy az ügyfelek fog áthaladni során be- és a tartalom jogkivonatokat, amelyeket az alkalmazás fogad a sikeres bejelentkezésekkor.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]
A **kezelés**válassza **felhasználókövetési adatai**.

Kattintson a +**új felhasználói folyamat** a panel tetején.

Alatt **válassza ki a felhasználói folyamat típusát**válassza **minden**, majd válassza ki azt a verzióját, és **jelentkezzen be a** is használni szeretné.

A **neve** határozza meg a bejelentkezési felhasználói folyamat nevét, az alkalmazása által használt. Adja meg például a következőt: **SiIn**.

A **Identitásszolgáltatók**, jelöljön ki egy lehetőséget. Közösségi Identitásszolgáltatók, azt is választhatja, ha már be van állítva. Kattintson az **OK** gombra.

A **alkalmazásjogcímek**, kattintson a **Továbbiak megjelenítése**.

Az a **visszatérési jogcím** oszlopban válassza ki a jogcímeket, amelyeket szeretne visszaküldött hitelesítő jogkivonatokban sikeres után jelentkezzen be az alkalmazásnak. Válassza például a **Megjelenítendő név**, az **Identitásszolgáltató**, az **Irányítószám** és a **Felhasználó objektumazonosítója** lehetőségeket. Kattintson az **OK** gombra.

Kattintson a **Create** (Létrehozás) gombra. Vegye figyelembe, hogy megjelenik az imént létrehozott felhasználói folyamat **B2C_1_SiIn** (a **B2C\_1\_**  részt a rendszer automatikusan adja hozzá).

Kattintson a **felhasználói folyamat futtatása**.

Válassza ki **Contoso B2C alkalmazás** a a **alkalmazás** legördülő és `https://localhost:44321/` a a **válasz URL-cím** listából.

Kattintson a **felhasználói folyamat futtatása**. Megnyílik egy böngészőablak, ahol ellenőrizheti, hogyan működik az alkalmazása bejelentkezési felhasználói folyamata.

> [!NOTE]
> Vesz egy percig a felhasználói folyamat létrehozása és a frissítések érvénybe léptetéséhez.
>