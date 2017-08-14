Az alkalmazásba való bejelentkezés engedélyezéséhez létre kell hoznia egy bejelentkezési szabályzatot. Ez a szabályzat írja le, hogy mit tapasztalnak a felhasználók a bejelentkezés során, valamint megadja azokat a jogkivonatokat, amelyeket az alkalmazás fogad a sikeres bejelentkezésekkor.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

A beállítások szabályzatokat tartalmazó részén válassza a **Regisztrálási vagy bejelentkezési szabályzatok** lehetőséget, majd kattintson a **+ Hozzáadás** gombra.

![Válassza ki a regisztrálási vagy a bejelentkezési szabályzatokat, és kattintson a Hozzáadás gombra.](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-policy.png)

Adjon meg egy **nevet** a szabályzatnak, amelyre az alkalmazás hivatkozni fog. Adja meg például a következőt: `SiUpIn`.

Válassza az **Identitásszolgáltatók** lehetőséget, és jelölje be a **Regisztráció e-mail-címmel** jelölőnégyzetet. Azt is megteheti, hogy közösségi identitásszolgáltatókat választ ki, ha ezek már be vannak állítva. Kattintson az **OK** gombra.

![Válassza a Regisztráció e-mail-címmel lehetőséget identitásszolgáltatóként, és kattintson az OK gombra.](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-identity-providers.png)

Válassza a **Regisztrálási attribútumok** lehetőséget. Válassza ki, milyen attribútumokat kell a felhasználóknak megadniuk a regisztráció során. Például jelölje be az **Ország/régió**, a **Megjelenítendő név** és az **Irányítószám** attribútumokat. Kattintson az **OK** gombra.

![Válaszon ki néhány attribútumot, majd kattintson az OK gombra.](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-sign-up-attributes.png)

Válassza az **Alkalmazásjogcímek** lehetőséget. Válassza ki azokat a jogcímeket, amelyeket szeretne szerepeltetni a sikeres regisztrációs vagy bejelentkezési művelet után az alkalmazásnak visszaküldött hitelesítő jogkivonatokban. Válassza például a **Megjelenítendő név**, az **Identitásszolgáltató**, az **Irányítószám**, az **Új felhasználó** és a **Felhasználó objektumazonosítója** lehetőséget.

![Válasszon ki néhány alkalmazásjogcímet, majd kattintson az OK gombra.](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-application-claims.png)

A szabályzat hozzáadásához kattintson a **Létrehozás** gombra. A szabályzat **B2C_1_SiUpIn** néven jelenik meg a listában. A **B2C_1_** előtagot a rendszer adja hozzá a névhez.

Nyissa meg a szabályzatot a **B2C_1_SiUpIn** kiválasztásával. Ellenőrizze a táblázatban megadott beállításokat, majd kattintson a **Futtatás most** gombra.

![Szabályzat kiválasztása és futtatása](media/active-directory-b2c-create-sign-in-sign-up-policy/run-b2c-signup-signin-policy.png)

| Beállítás      | Érték  |
| ------------ | ------ |
| **Alkalmazások** | Contoso B2C-alkalmazás |
| **Válasz URL-cím kiválasztása** | `https://localhost:44316/` |

Megnyílik egy böngészőablak, ahol ellenőrizheti, hogyan működik az alkalmazás regisztrációs vagy bejelentkezési felhasználói folyamata.

> [!NOTE]
> A szabályzat létrehozása és a frissítések érvénybe lépése akár egy percig is eltarthat.
>