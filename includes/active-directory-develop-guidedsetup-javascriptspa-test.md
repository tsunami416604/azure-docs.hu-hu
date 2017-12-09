
## <a name="test-your-code"></a>Tesztelheti a kódját

### <a name="test-with-visual-studio"></a>A Visual Studio tesztelése
Ha a Visual Studio használata esetén nyomja le az **F5** a projekt futtatásához. A böngésző megnyitja a http://<span></span>localhost: {port} helyre, és tekintse meg a **Microsoft Graph API hívása** gombra.

<p/><!-- --> 

### <a name="test-with-python-or-other-web-server"></a>A Python-teszt során vagy más webkiszolgáló
Ha a Visual Studio nem használ, győződjön meg arról a webalkalmazás-kiszolgáló elindult. Konfigurálja a kiszolgálót, amely a helye alapján TCP-port figyelésére a **index.html** fájlt. Python indítsa el a port figyelésére az alkalmazás mappájában terminál parancssorból való futtatásával:
 
```bash
python -m http.server 8080
```
Nyissa meg a böngészőt, és írja be a http://<span></span>localhost:8080 vagy http://<span></span>localhost: {port} ahol **port** a port, amelyet a webkiszolgáló figyel. Láthatja, hogy a index.html fájl tartalmát, és a **Microsoft Graph API hívása** gombra.

## <a name="test-your-application"></a>Az alkalmazás tesztelése

A böngészőben a index.html fájl betöltése után válassza ki a **Microsoft Graph API hívása**. Az első alkalommal futtatja az alkalmazást, a böngésző átirányítja Önt a Microsoft Azure Active Directory (Azure AD) v2.0-végpontra, és jelentkezzen be kell:
 
![Jelentkezzen be a JavaScript SPA-fiókba](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="provide-consent-for-application-access"></a>Alkalmazás-hozzáférés hozzájárulás megadása

Jelentkezzen be az alkalmazás első futtatásakor kéri a hozzájárulását Itt engedélyezheti az alkalmazásnak a profilja eléréséhez és a bejelentkezés:

![Adja meg az Ön hozzájárul az alkalmazás-hozzáférés](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Alkalmazás-eredmények megtekintése
Miután bejelentkezik, megjelenik a felhasználói profil adatait a a **Graph API-hívás válasz** mezőbe.
 
![A Microsoft Graph API-hívás a várt eredményt](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

Emellett meg kell jelennie a jogkivonatot, a megszerzett alapvető adatait a **Access Token** és **azonosító Token jogcímek** jelölőnégyzetéből.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörök és delegált jogosultságokkal sikeresen telepítették

A Microsoft Graph API megköveteli a **user.read** hatókörrel, hogy a felhasználói profil olvasása. Ebben a hatókörben alapértelmezés szerint minden egyes alkalmazás, amely a regisztrációs portál regisztrálva van a rendszer automatikusan. Más Microsoft Graph API-k, valamint a egyéni API-k, a háttér-kiszolgáló további hatókörökkel lehet szükség. A Microsoft Graph API megköveteli a **Calendars.Read** hatókörrel, hogy a felhasználók naptáraiban lévő listából.

A felhasználók naptáraiban a környezetben, az alkalmazások eléréséhez vegye fel a **Calendars.Read** jogosultságot a az alkalmazás regisztrációs adatait. Adja hozzá a **Calendars.Read** a hatókör a **acquireTokenSilent** hívható meg. 

>[!NOTE]
>A felhasználó kérheti további hozzájárulásokat azoktól a hatókörök számának növelésével.

Ha egy háttér-API (nem ajánlott) hatókör nem igényel, használhatja a **clientId** a hatóköreként a **acquireTokenSilent** és **acquireTokenRedirect** hívások.

<!--end-collapse-->

[!INCLUDE  [Help and support](./active-directory-develop-help-support-include.md)]
