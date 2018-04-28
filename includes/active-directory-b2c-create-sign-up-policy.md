Ha szeretné csak akkor engedélyezze a regisztrációs az alkalmazásra, használja a **előfizetési** házirend. Ez a házirend a regisztráció során végighaladnia ügyfelek szolgáltatásokat írja le, illetve az alkalmazás sikeres regisztrációra által kapott jogkivonatok tartalmát.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Kattintson a **Regisztrálási szabályzatok** lehetőségre.

A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.

A **Név** az alkalmazása által használt regisztrálási szabályzat nevét határozza meg. Adja meg például a következőt: **SiUp**.

Kattintson az **Identitásszolgáltatók** lehetőségre, és válassza a **Regisztráció e-mail-címmel** elemet. Azt is megteheti, hogy közösségi identitásszolgáltatókat választ ki, ha ezek már be vannak állítva. Kattintson az **OK** gombra.

Kattintson a **Regisztrálási attribútumok** lehetőségre. Itt választhatja meg, milyen attribútumokat kell a felhasználóknak megadniuk a regisztráció során. Válassza ki például az **Ország/régió**, a **Megjelenítendő név** és az **Irányítószám** attribútumokat. Kattintson az **OK** gombra.

Kattintson az **Alkalmazásjogcímek** elemre. Itt kiválaszthatja azokat a jogcímeket, amelyeket szeretne szerepeltetni a sikeres regisztráció után az alkalmazásnak visszaküldött hitelesítő jogkivonatokban. Válassza például a **Megjelenítendő név**, az **Identitásszolgáltató**, az **Irányítószám**, az **Új felhasználó** és a **Felhasználó objektumazonosítója** lehetőségeket.

Kattintson a **Create** (Létrehozás) gombra. Az újonnan létrehozott szabályzat **B2C_1_SiUp** néven jelenik meg (a **B2C\_1\_** részt a rendszer automatikusan adja hozzá) a **Regisztrálási szabályzatok** panelen.

Nyissa meg a szabályzatot azzal, hogy a **B2C_1_SiUp** elemre kattint.

Válassza ki a **Contoso B2C-alkalmazást** az **Alkalmazások**, illetve a `https://localhost:44321/` elemet a **Válasz URL-cím/Átirányítási URI** legördülő menüben.

Kattintson a **Futtatás most** parancsra. Megnyílik egy böngészőablak, ahol ellenőrizheti, hogyan működik az alkalmazás regisztrációs felhasználói folyamata.

> [!NOTE]
> A szabályzat létrehozása és a frissítések érvénybe lépése akár egy percig is eltarthat.
>