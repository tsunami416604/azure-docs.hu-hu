## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>Lekérdezi a Microsoft Graph API az iOS-alkalmazás tesztelése

A kód-szimulátorban történő elindításához nyomja le az **parancs** + **R**.

![Az alkalmazás-szimulátorban történő tesztelése](media/active-directory-develop-guidedsetup-ios-test/iostestscreenshot.png)

Ha elkészült, teszteléséhez, válassza ki a **Microsoft Graph API hívása**. Amikor a rendszer kéri, írja be a felhasználónevét és jelszavát.

### <a name="provide-consent-for-application-access"></a>Alkalmazás-hozzáférés hozzájárulás megadása
Jelentkezzen be az alkalmazás első futtatásakor kéri a hozzájárulását Itt engedélyezheti az alkalmazásnak a profilja eléréséhez és a bejelentkezés:

![Adja meg az Ön hozzájárul az alkalmazás-hozzáférés](media/active-directory-develop-guidedsetup-ios-test/iosconsentscreen.png)

### <a name="view-application-results"></a>Alkalmazás-eredmények megtekintése
Miután bejelentkezik, a felhasználói profil adatait a a Microsoft Graph API-hívás által visszaadott láthatja a **naplózás** szakasz. 

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörök és delegált jogosultságokkal sikeresen telepítették

A Microsoft Graph API megköveteli a **user.read** hatókörrel, hogy a felhasználói profil olvasása. Ebben a hatókörben alapértelmezés szerint minden egyes alkalmazás, amely a regisztrációs portál regisztrálva van a rendszer automatikusan. Más Microsoft Graph API-k, valamint a egyéni API-k, a háttér-kiszolgáló további hatókörökkel lehet szükség. A Microsoft Graph API megköveteli a **Calendars.Read** hatókörrel, hogy a felhasználók naptáraiban lévő listából.

A felhasználók naptáraiban a környezetben, az alkalmazások eléréséhez vegye fel a **Calendars.Read** jogosultságot a az alkalmazás regisztrációs adatait. Adja hozzá a **Calendars.Read** a hatókör a **acquireTokenSilent** hívható meg. 

>[!NOTE]
>A felhasználó kérheti további hozzájárulásokat azoktól a hatókörök számának növelésével.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
