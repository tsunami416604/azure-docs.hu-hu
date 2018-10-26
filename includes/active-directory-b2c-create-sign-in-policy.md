---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: 19e7c919345c0f56b274737840f8150f7d710501
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133591"
---
Ha azt szeretné, csak jelentkezzen be az alkalmazás engedélyezéséhez használja a **bejelentkezési** házirend. Ez a szabályzat írja le, hogy ügyfelek során be- és a tartalom jogkivonatokat, amelyeket az alkalmazás fogad a sikeres bejelentkezésekkor.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]
Kattintson a **bejelentkezési szabályzatok**.

A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.

A **Név** az alkalmazása által használt bejelentkezésiszabályzat-nevet határozza meg. Adja meg például a következőt: **SiIn**.

Kattintson az **Identitásszolgáltatók** elemre, és válassza a **Bejelentkezés helyi fiókba** lehetőséget. Azt is megteheti, hogy közösségi identitásszolgáltatókat választ ki, ha ezek már be vannak állítva. Kattintson az **OK** gombra.

Kattintson az **Alkalmazásjogcímek** elemre. Itt kiválaszthatja azokat a jogcímeket, amelyeket szeretne szerepeltetni a sikeres bejelentkezés után az alkalmazásnak visszaküldött hitelesítő jogkivonatokban. Válassza például a **Megjelenítendő név**, az **Identitásszolgáltató**, az **Irányítószám** és a **Felhasználó objektumazonosítója** lehetőségeket. Kattintson az **OK** gombra.

Kattintson a **Create** (Létrehozás) gombra. Vegye figyelembe, hogy az újonnan létrehozott szabályzat **B2C_1_SiIn** néven jelenik meg (a **B2C\_1\_** részt a rendszer automatikusan adja hozzá) a **Bejelentkezési szabályzatok** panelen.

Nyissa meg a szabályzatot a **B2C_1_SSPR** kiválasztásával.

Válassza ki a **Contoso B2C-alkalmazást** az **Alkalmazások**, illetve a `https://localhost:44321/` elemet a **Válasz URL-cím/Átirányítási URI** legördülő menüben.

Kattintson a **Futtatás most** parancsra. Megnyílik egy böngészőablak, ahol ellenőrizheti, hogyan működik az alkalmazása bejelentkezési felhasználói folyamata.

> [!NOTE]
> A szabályzat létrehozása és a frissítések érvénybe lépése akár egy percig is eltarthat.
>