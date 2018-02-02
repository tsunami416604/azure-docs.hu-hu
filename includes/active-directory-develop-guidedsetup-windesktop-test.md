## <a name="test-your-code"></a>Tesztelheti a kódját

Futtatja a projektet a Visual Studio, jelölje be **F5**. Az alkalmazás **MainWindow** jelenik meg, ahogy az itt látható:

![Az alkalmazás tesztelése](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

Először futtassa az alkalmazást, és válassza ki a **Microsoft Graph API hívása** gomb, felszólítást kap a bejelentkezéshez. Használjon egy Azure Active Directory-fiók (munkahelyi vagy iskolai fiókkal) vagy a Microsoft-fiókkal (live.com, outlook.com) tesztelik azt.

![Jelentkezzen be az alkalmazás](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Alkalmazás-hozzáférés hozzájárulás megadása
Az első alkalommal bejelentkezik az alkalmazás is felszólítja arra, hozzájárul ahhoz, hogy az alkalmazás a profil eléréséhez, és itt látható a bejelentkezés: 

![Adja meg az Ön hozzájárul az alkalmazás-hozzáférés](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Alkalmazás-eredmények megtekintése
Miután bejelentkezik, a felhasználói profil adatait a Microsoft Graph API-hívás által visszaadott kell megjelennie. Az eredmények jelennek meg a **API-hívási eredmények** mezőbe. A jogkivonat-hívás keresztül megszerzett alapvető információkat `AcquireTokenAsync` vagy `AcquireTokenSilentAsync` megjelennek a a **Token adatait** mezőbe. Az eredmények az alábbi tulajdonságokat tartalmazzák:

|Tulajdonság  |Formátum  |Leírás |
|---------|---------|---------|
|**Name (Név)** |Felhasználó teljes neve |A felhasználó nagyapja vezeték- és keresztneve.|
|**Felhasználónév** |<span>user@domain.com</span> |A felhasználónév, amely a felhasználó azonosítására szolgál.|
|**Jogkivonat lejár** |DateTime |Az az idő, amelynél a jogkivonat lejár. MSAL szükség szerint a token megújításával terjeszti ki a lejárati dátum.|
|**Hozzáférési jogkivonat** |Karakterlánc |A lexikális elem karakterlánca küldött HTTP-kérelmek igénylő egy *Authorization fejlécet*.|

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörök és delegált jogosultságokkal sikeresen telepítették

A Microsoft Graph API megköveteli a *user.read* hatókörrel, hogy a felhasználói profil olvasása. Minden egyes, amely regisztrálva van az alkalmazásregisztrációs portálra az alkalmazás alapértelmezés szerint automatikusan megjelenik az ebben a hatókörben. Más Microsoft Graph API-k, valamint a egyéni API-k, a háttér-kiszolgáló további hatókörökkel lehet szükség. A Microsoft Graph API megköveteli a *Calendars.Read* hatókörrel, hogy a felhasználók naptáraiban lévő listából.

A felhasználók naptáraiban a környezetben, az alkalmazások eléréséhez vegye fel a *Calendars.Read* jogosultságot a az alkalmazás regisztrációs adatait. Adja hozzá a *Calendars.Read* a hatókör a `acquireTokenSilent` hívható meg. 

>[!NOTE]
>A felhasználó kérheti további hozzájárulásokat azoktól a hatókörök számának növelésével.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
