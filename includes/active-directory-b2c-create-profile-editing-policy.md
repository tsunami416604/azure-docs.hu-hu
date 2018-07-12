Ha szeretné engedélyezni, profilszerkesztést az alkalmazásban, használhatja a **profilszerkesztést** házirend. Ez a szabályzat írja le, hogy ügyfeleink profilszerkesztést és a jogkivonatokat, amelyeket az alkalmazás fogad a sikeres végrehajtása során.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

A beállítások szabályzatokat tartalmazó részén válassza a **Profilszerkesztési szabályzatok** lehetőséget, majd kattintson a **+ Hozzáadás** elemre.

![Válassza a Profilszerkesztési szabályzatok elemet, és kattintson a Hozzáadás gombra.](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-policy.png)

Adjon meg egy **nevet** a szabályzatnak, amelyre az alkalmazás hivatkozni fog. Adja meg például a következőt: `SiPe`.

Válassza az **Identitásszolgáltatók** lehetőséget, és jelölje be a **Bejelentkezés helyi fiókba** jelölőnégyzetet. Azt is megteheti, hogy közösségi identitásszolgáltatókat választ ki, ha ezek már be vannak állítva. Kattintson az **OK** gombra.

![Válassza a Bejelentkezés helyi fiókba lehetőséget identitásszolgáltatóként, és kattintson az OK gombra.](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-identity-providers.png)

Válassza a **Profilattribútumok** elemet. Válassza ki azokat az attribútumokat, amelyeket a felhasználó megtekinthet és szerkeszthet a profilján belül. Például jelölje be az **Ország/régió**, a **Megjelenítendő név** és az **Irányítószám** attribútumokat. Kattintson az **OK** gombra.

![Válaszon ki néhány attribútumot, majd kattintson az OK gombra.](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-attributes.png)

Válassza az **Alkalmazásjogcímek** lehetőséget. Válassza ki azokat a jogcímeket, amelyeket szeretne szerepeltetni a sikeres profilszerkesztési művelet után az alkalmazásnak visszaküldött hitelesítő jogkivonatokban. Válassza például a **Megjelenítendő név** és az **Irányítószám** lehetőséget.

![Válasszon ki néhány alkalmazásjogcímet, majd kattintson az OK gombra.](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-application-claims.png)

A szabályzat hozzáadásához kattintson a **Létrehozás** gombra. A szabályzat **B2C_1_SiPe** néven jelenik meg a listában. A **B2C_1_** előtagot a rendszer adja hozzá a névhez.

Nyissa meg a szabályzatot a **B2C_1_SiPe** kiválasztásával. Ellenőrizze a táblázatban megadott beállításokat, majd kattintson a **Futtatás most** gombra.

![Szabályzat kiválasztása és futtatása](media/active-directory-b2c-create-profile-editing-policy/run-b2c-editing-policy.png)

| Beállítás      | Érték  |
| ------------ | ------ |
| **Alkalmazások** | Contoso B2C-alkalmazás |
| **Válasz URL-cím kiválasztása** | `https://localhost:44316/` |

Megnyílik egy böngészőablak, ahol ellenőrizheti, hogyan működik az alkalmazás profilszerkesztési felhasználói folyamata.

> [!NOTE]
> A szabályzat létrehozása és a frissítések érvénybe lépése akár egy percig is eltarthat.
>