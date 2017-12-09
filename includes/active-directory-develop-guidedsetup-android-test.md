
## <a name="test-your-code"></a>Tesztelheti a kódját

1. Az eszköz/emulátor telepíteni a kódot.
2. Amikor készen áll az alkalmazás teszteléséhez, a Microsoft Azure Active Directory-fiók (munkahelyi vagy iskolai fiókkal) vagy egy Microsoft-fiók (live.com, Outlook.com-on) való bejelentkezéshez használt. 

    ![Az alkalmazás tesztelése](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![Adja meg a felhasználónév és jelszó](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="provide-consent-for-application-access"></a>Alkalmazás-hozzáférés hozzájárulás megadása
Jelentkezzen be az alkalmazás első futtatásakor kéri a hozzájárulását Itt engedélyezheti az alkalmazásnak a profilja eléréséhez és a bejelentkezés: 

![Adja meg az Ön hozzájárul az alkalmazás-hozzáférés](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)


### <a name="view-application-results"></a>Alkalmazás-eredmények megtekintése
Miután bejelentkezik, meg kell jelennie a Microsoft Graph API-hívás által visszaadott eredmények. A Microsoft Graph API-hívás **me** végpont a felhasználói profil https://graph.microsoft.com/v1.0/me adja vissza. Általános Microsoft Graph-végpontok listáját lásd: [a Microsoft Graph API fejlesztői dokumentáció](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörök és delegált jogosultságokkal sikeresen telepítették

A Microsoft Graph API megköveteli a **user.read** hatókörrel, hogy a felhasználói profil olvasása. Ebben a hatókörben alapértelmezés szerint minden egyes alkalmazás, amely a regisztrációs portál regisztrálva van a rendszer automatikusan. Más Microsoft Graph API-k, valamint a egyéni API-k, a háttér-kiszolgáló további hatókörökkel lehet szükség. A Microsoft Graph API megköveteli a **Calendars.Read** hatókörrel, hogy a felhasználók naptáraiban lévő listából.

A felhasználók naptáraiban a környezetben, az alkalmazások eléréséhez vegye fel a **Calendars.Read** jogosultságot a az alkalmazás regisztrációs adatait. Adja hozzá a **Calendars.Read** a hatókör a **acquireTokenSilent** hívható meg. 

>[!NOTE]
>A felhasználó kérheti további hozzájárulásokat azoktól a hatókörök számának növelésével.

<!--end-collapse-->

[!INCLUDE  [Help and support](active-directory-develop-help-support-include.md)]
