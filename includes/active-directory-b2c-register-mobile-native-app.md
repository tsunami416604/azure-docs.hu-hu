[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Mobil vagy natív alkalmazásának regisztrálásához használja a táblázatban megadott beállításokat.

![Példák az új mobil vagy natív alkalmazások regisztrációs beállításaira](./media/active-directory-b2c-register-mobile-native-app/b2c-new-mobile-native-app-settings.png)

| Beállítás      | Mintaérték  | Leírás                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Name (Név)** | Contoso B2C-alkalmazás | Adjon meg egy olyan **nevet**, amely ismerteti az alkalmazást a felhasználók számára. |
| **Natív ügyfél** | Igen | Válassza az **Igen** lehetőséget mobil vagy natív alkalmazás esetén. |
| **Egyéni átirányítási URI** | `com.onmicrosoft.contoso.appname://redirect/path` | Adjon meg egy egyéni sémával rendelkező átirányítási URI-t. Ügyeljen arra, hogy [megfelelő átirányítási URI-t](../articles/active-directory-b2c/active-directory-b2c-app-registration.md#choosing-a-native-application-redirect-uri) válasszon, és különleges karakterek, például aláhúzásjelek ne szerepeljenek benne. |

Kattintson a **Create** (Létrehozás) gombra az alkalmazás regisztrálásához.

Az újonnan regisztrált alkalmazás a B2C-bérlő alkalmazásainak listájában jelenik meg. Válassza ki mobil vagy natív alkalmazását a listáról. Ekkor megjelenik az alkalmazás tulajdonságpanelje.

![Az alkalmazás tulajdonságai](./media/active-directory-b2c-register-mobile-native-app/b2c-mobile-native-app-properties.png)

Jegyezze fel az alkalmazás globálisan egyedi **ügyfél-azonosítóját**. Ezt az azonosítót használja az alkalmazás kódjában.

Ha natív alkalmazása az Azure AD B2C által védett webes API-t hív meg, kövesse a következő lépéseket:
   1. Hozzon létre egy alkalmazáskulcsot a **Kulcsok** panel **Kulcs létrehozása** gombjára kattintva. Jegyezze fel az **Alkalmazáskulcs** értékét. Ezt az értéket fogja használni az alkalmazás kódjában alkalmazáskulcsként.
   2. Kattintson az **API-hozzáférés**, majd a **Hozzáadás**, lehetőségre, és válassza ki webes API-ját és a hatóköröket (engedélyeket).

> [!NOTE]
> Az **Application Secret** (Alkalmazástitok) fontos biztonsági hitelesítő adat, amelynek megfelelő biztonságáról gondoskodni kell.
> 
