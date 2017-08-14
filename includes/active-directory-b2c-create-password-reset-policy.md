Az alkalmazáshoz tartozó új jelszó részletekbe menő létrehozási folyamatának engedélyezéséhez létre kell hoznia egy új jelszó létrehozására vonatkozó szabályzatot. Az új jelszó összes bérlőre vonatkozó létrehozási lehetősége [itt](../articles/active-directory-b2c/active-directory-b2c-reference-sspr.md) van meghatározva. Ez a szabályzat írja le, hogy mit tapasztalnak a felhasználók az új jelszó kérése során, valamint megadja azokat a jogkivonatokat, amelyeket az alkalmazás fogad a művelet sikeres elvégzésekor.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

A beállítások szabályzatokat tartalmazó részén válassza az **Új jelszó kérésére vonatkozó szabályzatok** lehetőséget, majd kattintson a **+ Hozzáadás** elemre.

![Válassza a regisztrációs vagy a bejelentkezési szabályzatokat, és kattintson a Hozzáadás gombra.](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-policy.png)

Adjon meg egy **nevet** a szabályzatnak, amelyre az alkalmazás hivatkozni fog. Adja meg például a következőt: `SSPR`.

Válassza az **Identitásszolgáltatók** lehetőséget, és jelölje be a **Jelszó visszaállítása e-mail-címmel** jelölőnégyzetet. Kattintson az **OK** gombra.

![Válassza a Jelszó visszaállítása e-mail-címmel lehetőséget identitásszolgáltatóként, és kattintson az OK gombra.](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-identity-providers.png)

Válassza az **Alkalmazásjogcímek** lehetőséget. Válassza ki azokat a jogcímeket, amelyeket szeretne szerepeltetni a sikeres jelszókérési művelet után az alkalmazásnak visszaküldött hitelesítő jogkivonatokban. Válassza például a **Felhasználó objektumazonosítója** lehetőséget.

![Válasszon ki néhány alkalmazásjogcímet, majd kattintson az OK gombra.](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-application-claims.png)

A szabályzat hozzáadásához kattintson a **Létrehozás** gombra. A szabályzat **B2C_1_SSPR** néven jelenik meg a listában. A **B2C_1_** előtagot a rendszer adja hozzá a névhez.

Nyissa meg a szabályzatot a **B2C_1_SSPR** kiválasztásával. Ellenőrizze a táblázatban megadott beállításokat, majd kattintson a **Futtatás most** gombra.

![Szabályzat kiválasztása és futtatása](media/active-directory-b2c-create-password-reset-policy/run-b2c-password-reset-policy.png)

| Beállítás      | Érték  |
| ------------ | ------ |
| **Alkalmazások** | Contoso B2C-alkalmazás |
| **Válasz URL-cím kiválasztása** | `https://localhost:44316/` |

Megnyílik egy böngészőablak, ahol ellenőrizheti, hogyan működik az alkalmazása jelszókérési felhasználói folyamata.

> [!NOTE]
> A szabályzat létrehozása és a frissítések érvénybe lépése akár egy percig is eltarthat.
>
