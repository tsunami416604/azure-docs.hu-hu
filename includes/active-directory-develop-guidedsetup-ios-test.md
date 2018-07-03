## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>Tesztelje az iOS-alkalmazásból, a Microsoft Graph API lekérdezése

A kód-szimulátorban történő futtatásához nyomja le a **parancs** + **R**.

![Az alkalmazás-szimulátorban történő tesztelése](media/active-directory-develop-guidedsetup-ios-test/iostestscreenshot.png)

Amikor elkészült, teszteléséhez, válassza ki a **Microsoft Graph API meghívása**. Amikor a rendszer kéri, adja meg a felhasználónevét és jelszavát.

### <a name="provide-consent-for-application-access"></a>Alkalmazás-hozzáférési hozzájárulás megadása
Első alkalommal jelentkezik be az alkalmazás kéri, hogy Ön hozzájárul ahhoz, hogy az alkalmazás eléréséhez a profil és a bejelentkezéshez adja meg:

![Adja meg az Ön hozzájárul az alkalmazás-hozzáférés](media/active-directory-develop-guidedsetup-ios-test/iosconsentscreen.png)

### <a name="view-application-results"></a>Kérelem eredményének megtekintése
Miután bejelentkezett, megjelenik a profilok adatainak a a Microsoft Graph API-hívás által visszaadott a **naplózás** szakaszban. 

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörök és delegált engedélyek

A Microsoft Graph API megköveteli a **user.read** hatókörrel, hogy a felhasználói profil olvasása. Minden alkalmazás, amely regisztrálva van az alkalmazásregisztrációs portálon alapértelmezés szerint automatikusan megjelenik az ebben a hatókörben. Egyéb Microsoft Graph API-k, valamint a egyéni API-k, a háttér-kiszolgáló további hatókörökkel lehet szükség. A Microsoft Graph API megköveteli a **Calendars.Read** hatókörrel, hogy a felhasználók naptáraiban listázása.

A felhasználók naptáraiban keretén belül egy alkalmazás eléréséhez, adja hozzá a **Calendars.Read** delegált engedéllyel az alkalmazás regisztrációs adatok. Adja hozzá a **Calendars.Read** a hatókört a **acquireTokenSilent** hívja. 

>[!NOTE]
>A felhasználó további címtárbérlőhöz hatókörök számának növelésével kérheti.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
