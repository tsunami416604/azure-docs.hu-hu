## <a name="test-your-code"></a>Tesztelheti a kódját

A Visual Studióban az alkalmazás teszteléséhez nyomja le az **F5** a projekt futtatásához. A **MainWindow** az alkalmazás megjelenik:

![Az alkalmazás tesztelése](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

Ha készen áll a teszt futtatása, a Microsoft Azure Active Directory-fiók (munkahelyi vagy iskolai fiókkal) vagy a Microsoft-fiókkal (live.com, outlook.com) való bejelentkezéshez használt. Az első alkalommal futtatja az alkalmazást kéri, jelentkezzen be a:

![Jelentkezzen be az alkalmazás](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Alkalmazás-hozzáférés hozzájárulás megadása
Jelentkezzen be az alkalmazás első futtatásakor kéri a hozzájárulását Itt engedélyezheti az alkalmazásnak a profilja eléréséhez és a bejelentkezés: 

![Adja meg az Ön hozzájárul az alkalmazás-hozzáférés](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Alkalmazás-eredmények megtekintése
Miután bejelentkezik, a felhasználói profil adatait a Microsoft Graph API-hívás által visszaadott kell megjelennie. Az eredmények jelennek meg a **API-hívási eredmények** mezőbe. A jogkivonat-hívás keresztül megszerzett alapvető információkat **AcquireTokenAsync** vagy **AcquireTokenSilentAsync** megjelennek a a **Token adatait** mezőbe. Az eredmények az alábbi tulajdonságokat tartalmazzák:

|Tulajdonság  |Formátum  |Leírás |
|---------|---------|---------|
|**Name (Név)** |Felhasználó teljes neve |A felhasználó nagyapja vezeték- és keresztneve.|
|**Felhasználónév** |<span>user@domain.com</span> |A felhasználónév, amely a felhasználó azonosítására szolgál.|
|**Jogkivonat lejár** |Dátum és idő |Az az idő, amelynél a jogkivonat lejár. MSAL szükség szerint a token megújításával terjeszti ki a lejárati dátum.|
|**Hozzáférési jogkivonat** |Karakterlánc |A lexikális elem karakterlánca küldött HTTP-kérelmek igénylő egy **engedélyezési** fejléc.|

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörök és delegált jogosultságokkal sikeresen telepítették

A Microsoft Graph API megköveteli a **user.read** hatókörrel, hogy a felhasználói profil olvasása. Ebben a hatókörben alapértelmezés szerint minden egyes alkalmazás, amely a regisztrációs portál regisztrálva van a rendszer automatikusan. Más Microsoft Graph API-k, valamint a egyéni API-k, a háttér-kiszolgáló további hatókörökkel lehet szükség. A Microsoft Graph API megköveteli a **Calendars.Read** hatókörrel, hogy a felhasználók naptáraiban lévő listából.

A felhasználók naptáraiban a környezetben, az alkalmazások eléréséhez vegye fel a **Calendars.Read** jogosultságot a az alkalmazás regisztrációs adatait. Adja hozzá a **Calendars.Read** a hatókör a **acquireTokenSilent** hívható meg. 

>[!NOTE]
>A felhasználó kérheti további hozzájárulásokat azoktól a hatókörök számának növelésével.

<!--end-collapse-->

[!INCLUDE  [Help and support](./active-directory-develop-help-support-include.md)]
