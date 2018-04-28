Bejelentkezés az alkalmazásban csak engedélyezni szeretné, ha használja a **bejelentkezési** házirend. Ez a házirend az, hogy az ügyfelek végighaladnia során be- és a jogkivonatok, amelyek megkapják az alkalmazás tartalmát a sikeres bejelentkezések feladatait ismerteti.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]
Kattintson a **bejelentkezési házirendek**.

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